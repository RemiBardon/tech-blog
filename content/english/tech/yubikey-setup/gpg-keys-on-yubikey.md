---
title: Linking GPG keys to a YubiKey
summary: &summary |
  Having private GPG keys on your machine can be unsafe, because it could be stolen
  by a malicious program. If you have a [YubiKey], you can move those private keys
  to it, ensuring they're stored in a secure place and protected by a passcode.
  This article explains how to move GPG subkeys to a [YubiKey] and require card taps
  when signing, encrypting and/or authenticating.

  [YubiKey]: https://www.yubico.com/products/yubikey/ "“YubiKeys | Two-Factor Authentication for Secure Login” on yubico.com"
description: *summary
date: 2023-12-10
# publishDate: 2023-12-12
lastmod: 2024-12-06
draft: false
weight: 80
tags:
  - GPG
  - OpenPGP
  - Cryptography
  - Security
  - YubiKey
slug: gpg-on-yubikey
---

{{< callout type="help" title="What are GPG keys?" >}}
If you're not entirely familiar with all concepts surrouding GPG, you should read my article "[What are GPG keys?]({{< relref "what-are-gpg-keys" >}})" first. It will give you all the necessary concepts and vocabulary you will need to understand this article.
{{< /callout >}}

## Configuring the YubiKey {#configuring-the-yubikey}

Before we move the keys to the YubiKey, let's make sure it is configured and safe to use. To edit the GPG configuration on the Yubikey, we can use `gpg --card-edit`:

```bash
gpg --card-edit
```

{{< callout "example" "-" "Command output" >}}
  Like `gpg --edit-key`, this opens a console-like environment where we can interact with the GPG card:

  ```txt
  Reader ...........: Yubico YubiKey FIDO CCID
  Application ID ...: D276000124010000000620141XXX0000
  Application type .: OpenPGP
  Version ..........: 3.4
  Manufacturer .....: Yubico
  Serial number ....: 20141XXX
  Name of cardholder: [not set]
  Language prefs ...: [not set]
  Salutation .......:
  URL of public key : [not set]
  Login data .......: [not set]
  Signature PIN ....: not forced
  Key attributes ...: rsa2048 rsa2048 rsa2048
  Max. PIN lengths .: 127 127 127
  PIN retry counter : 3 0 3
  Signature counter : 0
  KDF setting ......: off
  UIF setting ......: Sign=off Decrypt=off Auth=off
  Signature key ....: [none]
  Encryption key....: [none]
  Authentication key: [none]
  General key info..: [none]

  gpg/card>
  ```

{{< /callout >}}

{{< callout "help" "-" "How to list available commands" >}}

  The available commands can be listed by running `help`:

  ```txt
  gpg/card> help
  quit           quit this menu
  admin          show admin commands
  help           show this help
  list           list all available data
  fetch          fetch the key specified in the card URL
  passwd         menu to change or unblock the PIN
  verify         verify the PIN and list all data
  unblock        unblock the PIN using a Reset Code
  openpgp        switch to the OpenPGP app

  gpg/card>
  ```

{{< /callout >}}

### Configuring the PIN

First things first, let's change the factory default PIN and Admin PIN so only ourselves can administrate the key. We could use `gpg --change-pin` but since we're already in card edit, we can just run `passwd`:

```txt
gpg/card> passwd
```

{{< callout "example" "-" "Command output" >}}

  ```txt {hl_lines=[1]}
  gpg/card> passwd
  gpg: OpenPGP card no. D276000124010000000620141XXX0000 detected
  ```

  `gpg` then shows a series of [PINEntry] dialogs to change the PIN:

  1. ```txt
     ┌──────────────────────────────────────────────┐
     │ Please enter the PIN                         │
     │                                              │
     │ PIN ________________________________________ │
     │                                              │
     │      <OK>                        <Cancel>    │
     └──────────────────────────────────────────────┘
     ```

     > As stated in [Using Your YubiKey with OpenPGP – Yubico]:
     >
     > > Note: If you haven't set a User PIN or an Admin PIN for OpenPGP, the default values are 123456 and 12345678, respectively. If the User PIN and/or Admin PIN have been changed and are not known, the OpenPGP Applet can be reset by following [this article](https://support.yubico.com/support/solutions/articles/15000006421-resetting-the-openpgp-applet-on-the-yubikey).

  2. ```txt
     ┌──────────────────────────────────────────────┐
     │ New PIN                                      │
     │                                              │
     │ PIN ________________________________________ │
     │                                              │
     │      <OK>                        <Cancel>    │
     └──────────────────────────────────────────────┘
     ```

  3. ```txt
     ┌──────────────────────────────────────────────┐
     │ Repeat this PIN                              │
     │                                              │
     │ PIN ________________________________________ │
     │                                              │
     │      <OK>                        <Cancel>    │
     └──────────────────────────────────────────────┘
     ```

  Then it goes back to the console-like environment:

  ```txt
  PIN changed.

  gpg/card>
  ```

  [PINEntry]: https://github.com/gpg/pinentry "gpg/pinentry: The standard pinentry collection."
  [Using Your YubiKey with OpenPGP – Yubico]: https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP "Using Your YubiKey with OpenPGP – Yubico"
{{< /callout >}}

### Configuring the Admin PIN

By default, only a subset of commands are available, so as we did with the `--expert`[^expert-flag] flag for `gpg --edit-key`, here we need to run `admin`:

```txt
gpg/card> admin
```

{{< callout "example" "-" "Command output" >}}

  ```txt {hl_lines=[1,4]}
  gpg/card> admin
  Admin commands are allowed

  gpg/card> help
  quit           quit this menu
  admin          show admin commands
  help           show this help
  list           list all available data
  name           change card holder's name
  url            change URL to retrieve key
  fetch          fetch the key specified in the card URL
  login          change the login name
  lang           change the language preferences
  salutation     change card holder's salutation
  cafpr          change a CA fingerprint
  forcesig       toggle the signature force PIN flag
  generate       generate new keys
  passwd         menu to change or unblock the PIN
  verify         verify the PIN and list all data
  unblock        unblock the PIN using a Reset Code
  factory-reset  destroy all keys and data
  kdf-setup      setup KDF for PIN authentication (on/single/off)
  key-attr       change the key attribute
  uif            change the User Interaction Flag
  openpgp        switch to the OpenPGP app

  gpg/card>
  ```
{{< /callout >}}

This also changes the behavior of `passwd`, which can now be used to change the Admin PIN:

```txt {hl_lines=[1,10]}
gpg/card> passwd
gpg: OpenPGP card no. D276000124010000000620141XXX0000 detected

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 3
```

{{< callout "example" "-" "Command output" >}}
  1. ```txt
     ┌────────────────────────────────────────────────────┐
     │ Please enter the Admin PIN                         │
     │                                                    │
     │ Number: 20 141 XXX                                 │
     │ Holder:                                            │
     │                                                    │
     │ Admin PIN ________________________________________ │
     │                                                    │
     │       <OK>                            <Cancel>     │
     └────────────────────────────────────────────────────┘
     ```

     > As stated in [Using Your YubiKey with OpenPGP – Yubico]:
     >
     > > Note: If you haven't set a User PIN or an Admin PIN for OpenPGP, the default values are 123456 and 12345678, respectively. If the User PIN and/or Admin PIN have been changed and are not known, the OpenPGP Applet can be reset by following [this article](https://support.yubico.com/support/solutions/articles/15000006421-resetting-the-openpgp-applet-on-the-yubikey).

  2. ```txt
     ┌────────────────────────────────────────────────────┐
     │ New Admin PIN                                      │
     │                                                    │
     │ Admin PIN ________________________________________ │
     │                                                    │
     │       <OK>                            <Cancel>     │
     └────────────────────────────────────────────────────┘
     ```

  3. ```txt
     ┌────────────────────────────────────────────────────┐
     │ Repeat this PIN                                    │
     │                                                    │
     │ Admin PIN ________________________________________ │
     │                                                    │
     │       <OK>                            <Cancel>     │
     └────────────────────────────────────────────────────┘
     ```

  [Using Your YubiKey with OpenPGP – Yubico]: https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP "Using Your YubiKey with OpenPGP – Yubico"
{{< /callout >}}

After the Admin PIN is changed, quit to go back to the console-like environment:

```txt {hl_lines=[9]}
PIN changed.

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? Q

gpg/card>
```

### Configuring your personal details

Now let's customize the key:

```txt {hl_lines=[1,2,3,15,16]}
gpg/card> name
Cardholder's surname: John
Cardholder's given name: Doe
┌────────────────────────────────────────────────────┐
│ Please enter the Admin PIN                         │
│                                                    │
│ Number: 20 141 XXX                                 │
│ Holder:                                            │
│                                                    │
│ Admin PIN ________________________________________ │
│                                                    │
│       <OK>                            <Cancel>     │
└────────────────────────────────────────────────────┘

gpg/card> lang
Language preferences: fr

gpg/card>
```

We could also change `login` and `url` if we want to. There is also `salutation` but I have no idea how it should be used.

## Moving the private subkeys to the YubiKey {#moving-keys-to-the-yubikey}

Our OpenPGP smartcard (the YubiKey) is now ours and secured. Let's move our private keys to it.

{{< callout "danger" >}}
WAIT! Make sure you [backup your master key]({{< ref "gpg-keys-the-right-way" >}}#backup), at least. Once a private key is moved to a YubiKey, it can never be extracted from it (by design). If you loose this YubiKey, you will never be able to generate new subkeys.
{{< /callout >}}

```bash
gpg --edit-key "${KEY_ID:?}"
```

{{< callout "example" "-" "Command output" >}}
  ```txt
  gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
  This is free software: you are free to change and redistribute it.
  There is NO WARRANTY, to the extent permitted by law.

  Secret key is available.

  sec  ed25519/A6D3E049FD2A88C4
      created: 2023-12-12  expires: never       usage: C
      trust: ultimate      validity: ultimate
  ssb  ed25519/A2E618E20456625F
      created: 2023-12-12  expires: 2024-12-11  usage: S
  ssb  cv25519/52477AF6A631E77F
      created: 2023-12-12  expires: 2024-12-11  usage: E
  ssb  ed25519/37A902F030B44E05
      created: 2023-12-12  expires: 2024-12-11  usage: A
  [ultimate] (1). John Doe (General purpose personal key) <john.doe@example.org>

  gpg>
  ```
{{< /callout >}}

Now let's select the [Sign] key ("usage: S"), of index `1`:

```txt {hl_lines=[1]}
gpg> key 1

sec  ed25519/A6D3E049FD2A88C4
     created: 2023-12-12  expires: never       usage: C
     trust: ultimate      validity: ultimate
ssb* ed25519/A2E618E20456625F
     created: 2023-12-12  expires: 2024-12-11  usage: S
ssb  cv25519/52477AF6A631E77F
     created: 2023-12-12  expires: 2024-12-11  usage: E
ssb  ed25519/37A902F030B44E05
     created: 2023-12-12  expires: 2024-12-11  usage: A
[ultimate] (1). John Doe (General purpose personal key) <john.doe@example.org>

gpg>
```

Notice the star (`*`) after `ssb`. This means the key is selected. We can then run `keytocard` to move it to our card:

```txt {hl_lines=[1]}
gpg> keytocard
Please select where to store the key:
   (1) Signature key
   (3) Authentication key
Your selection?
```

{{< callout "tip" >}}
We could also use `addcardkey` instead to keep the private keys locally after `save`.
{{< /callout >}}

We want to move it to the "Signature" slot of the OpenPGP smartcard, so we choose `1`:

```txt
Your selection? 1
```

{{< callout "example" "-" "Command output" >}}
  ```txt {hl_lines=[1]}
  Your selection? 1

  ┌──────────────────────────────────────────────────────────────────────  ─────────────────┐
  │ Please enter your passphrase, so that the secret key can be unlocked for this session │
  │                                                                                       │
  │ Passphrase: _________________________________________________________________________ │
  │                                                                                       │
  │             <OK>                                                   <Cancel>           │
  └───────────────────────────────────────────────────────────────────────────────────────┘

  ┌────────────────────────────────────────────────────┐
  │ Please enter the Admin PIN                         │
  │                                                    │
  │ Number: 20 141 XXX                                 │
  │ Holder: Doe John                                   │
  │                                                    │
  │ Admin PIN ________________________________________ │
  │                                                    │
  │       <OK>                            <Cancel>     │
  └────────────────────────────────────────────────────┘

  sec  ed25519/A6D3E049FD2A88C4
  created: 2023-12-12  expires: never       usage: C
  trust: ultimate      validity: ultimate
  ssb* ed25519/A2E618E20456625F
  created: 2023-12-12  expires: 2024-12-11  usage: S
  ssb  cv25519/52477AF6A631E77F
  created: 2023-12-12  expires: 2024-12-11  usage: E
  ssb  ed25519/37A902F030B44E05
  created: 2023-12-12  expires: 2024-12-11  usage: A
  [ultimate] (1). John Doe (General purpose personal key) <john.doe@example.org>

  Note: the local copy of the secret key will only be deleted with "save".
  gpg>
  ```
{{< /callout >}}

Note the comment at the end:

> `Note: the local copy of the secret key will only be deleted with "save".`

Let's move the two other keys. Make sure to deselect the selected key before running `keytocard` (it won't work otherwise anyway), but other than that it is very straightforward:

```txt {hl_lines=[1,14,27,30,35,39,43,46]}
gpg> key 1

sec  ed25519/A6D3E049FD2A88C4
     created: 2023-12-12  expires: never       usage: C
     trust: ultimate      validity: ultimate
ssb  ed25519/A2E618E20456625F
     created: 2023-12-12  expires: 2024-12-11  usage: S
ssb  cv25519/52477AF6A631E77F
     created: 2023-12-12  expires: 2024-12-11  usage: E
ssb  ed25519/37A902F030B44E05
     created: 2023-12-12  expires: 2024-12-11  usage: A
[ultimate] (1). John Doe (General purpose personal key) <john.doe@example.org>

gpg> key 2

sec  ed25519/A6D3E049FD2A88C4
     created: 2023-12-12  expires: never       usage: C
     trust: ultimate      validity: ultimate
ssb  ed25519/A2E618E20456625F
     created: 2023-12-12  expires: 2024-12-11  usage: S
ssb* cv25519/52477AF6A631E77F
     created: 2023-12-12  expires: 2024-12-11  usage: E
ssb  ed25519/37A902F030B44E05
     created: 2023-12-12  expires: 2024-12-11  usage: A
[ultimate] (1). John Doe (General purpose personal key) <john.doe@example.org>

gpg> keytocard
Please select where to store the key:
   (2) Encryption key
Your selection? 2

[...]

Note: the local copy of the secret key will only be deleted with "save".
gpg> key 2

[...]

gpg> key 3

[...]

gpg> keytocard
Please select where to store the key:
   (3) Authentication key
Your selection? 3

[...]

Note: the local copy of the secret key will only be deleted with "save".
gpg>
```

Now is the critical part: saving. [Using Your YubiKey with OpenPGP – Yubico] says you should quit without saving, but this leaves the private keys on the computer. Since [we have backed up the private keys](#backup), we can safely save and delete the local copy. If you haven't backed up your keys, you can always open a new terminal tab or `quit` instead of `save` to do it.

> Double check that you backed up the keys correctly, as you will never be able to get them out of the YubiKey!

```txt
gpg> save
```

### Checking that it worked {#checking-moving-worked}

Let's check that this worked.

We can first have a look at the OpenPGP smartcard details to see if the keys are configured:

```bash
gpg --card-status
```

```txt {hl_lines=["19-25","26-32"]}
Reader ...........: Yubico YubiKey FIDO CCID
Application ID ...: D276000124010000000620141XXX0000
Application type .: OpenPGP
Version ..........: 3.4
Manufacturer .....: Yubico
Serial number ....: 20141XXX
Name of cardholder: Doe John
Language prefs ...: fr
Salutation .......:
URL of public key : [not set]
Login data .......: [not set]
Signature PIN ....: not forced
Key attributes ...: ed25519 cv25519 ed25519
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
Signature counter : 3
KDF setting ......: off
UIF setting ......: Sign=off Decrypt=off Auth=off
Signature key ....: DBCC F949 18B2 4D1A 0053  AD43 A2E6 18E2 0456 625F
      created ....: 2023-12-12 22:01:54
Encryption key....: B5FF 206E 44D0 20AE 6CC5  30C1 5247 7AF6 A631 E77F
      created ....: 2023-12-12 22:18:06
Authentication key: 5706 1CDF 8DC3 15ED 81A4  1DCC 37A9 02F0 30B4 4E05
      created ....: 2023-12-12 22:20:20
General key info..: sub  ed25519/A2E618E20456625F 2023-12-12 John Doe (General purpose personal key) <john.doe@example.org>
sec   ed25519/A6D3E049FD2A88C4  created: 2023-12-12  expires: never
ssb>  ed25519/A2E618E20456625F  created: 2023-12-12  expires: 2024-12-11
                                card-no: 0006 20141XXX
ssb>  cv25519/52477AF6A631E77F  created: 2023-12-12  expires: 2024-12-11
                                card-no: 0006 20141XXX
ssb>  ed25519/37A902F030B44E05  created: 2023-12-12  expires: 2024-12-11
                                card-no: 0006 20141XXX
```

With the YubiKey plugged in, signing a file should work as expected:

```bash
gpg --local-user "${KEY_ID:?}" --armor --output - --sign <(echo 'Test')
```

```txt
┌──────────────────────────────────────────────┐
│ Please unlock the card                       │
│                                              │
│ Number: 20 141 XXX                           │
│ Holder: Doe John                             │
│ Counter: 0                                   │
│                                              │
│ PIN ________________________________________ │
│                                              │
│      <OK>                        <Cancel>    │
└──────────────────────────────────────────────┘
```

```txt
-----BEGIN PGP MESSAGE-----

owGbwMvMwCW26JnEI5awpHjG07xJTIbGqZVMy0JSi0u4OkpZGMS4GGTFFFlun/np
KbHJV4oheK0zTDkrE0glAxenAExERojhn87f3kM6IS7PP4vektaYnLB9++LnCx6d
eLdxnY79OxP1WHFGhtOn/nPvdGLij/r7KW/hm7j932TX6294sNRp50n92V/ts3gB
=F13E
-----END PGP MESSAGE-----
```

It outputs a PGP message, which means it worked.

Now let's unplug the YubiKey and try again:

```bash
gpg --local-user "${KEY_ID:?}" --armor --output - --sign <(echo 'Test')
```

`gpg` can't sign the payload as it can't access the private key:

```txt
┌────────────────────────────────────────────┐
│ Please insert the card with serial number: │
│                                            │
│ 20 141 XXX                                 │
│                                            │
│                                            │
│      <OK>                      <Cancel>    │
└────────────────────────────────────────────┘
```

Now plug the YubiKey again and hit <kbd>Enter</kbd> (having `<OK>` selected). It asks you for the card PIN:

```txt
┌──────────────────────────────────────────────┐
│ Please unlock the card                       │
│                                              │
│ Number: 20 141 XXX                           │
│ Holder: Doe John                             │
│ Counter: 2                                   │
│                                              │
│ PIN ________________________________________ │
│                                              │
│      <OK>                        <Cancel>    │
└──────────────────────────────────────────────┘
```

```txt
-----BEGIN PGP MESSAGE-----

owGbwMvMwCW26JnEI5awpHjG07xJTIbGqZXMbCGpxSVcHaUsDGJcDLJiiiy3z/z0
lNjkK8UQvNYZppyVCaSSgYtTACby5jHD/5T4jjZj/rpOhjXxbEFdNkxBt+/n7jW2
DMm6eks2zemXCCPDSe1MqyMpsQtZ067PfNUf5n/thOiewj+GLkmdfUt8M+5wAgA=
=uDWm
-----END PGP MESSAGE-----
```

It outputs a PGP message, which means the YubiKey really is necessary to sign a payload.

I won't go over encryption and authentication, but it's the same.

## Adding extra security {#extra-security}

### Requiring card taps {#requiring-card-taps}

Having to plug and unlock the YubiKey is a secure requirement. However, what happens if a malicious background process tries to access a machine using SSH or more generally authenticate you somewhere with your authentication key for example? The same goes for signing and encryption, even though it's not as good an example. Well, configured like it is right now, our YubiKey will authorize any operation as long as its timeout is not reached. Let's change that.

One solution is to require "card taps" on every operation, which means you have to physically touch the YubiKey every time you sign, encrypt or authenticate. This can be configured via the "User Interaction Flag" (or "UIF") using `uif`[^uif] in `gpg --card-edit`:

```bash
gpg --card-edit
```

{{< callout "example" "-" "Command output" >}}
  ```txt
  Reader ...........: Yubico YubiKey FIDO CCID
  Application ID ...: D276000124010000000620141XXX0000
  Application type .: OpenPGP
  Version ..........: 3.4
  Manufacturer .....: Yubico
  Serial number ....: 20141XXX
  Name of cardholder: Doe John
  Language prefs ...: fr
  Salutation .......:
  URL of public key : [not set]
  Login data .......: [not set]
  Signature PIN ....: not forced
  Key attributes ...: ed25519 cv25519 ed25519
  Max. PIN lengths .: 127 127 127
  PIN retry counter : 3 0 3
  Signature counter : 3
  KDF setting ......: off
  UIF setting ......: Sign=off Decrypt=off Auth=off
  Signature key ....: DBCC F949 18B2 4D1A 0053  AD43 A2E6 18E2 0456 625F
        created ....: 2023-12-12 22:01:54
  Encryption key....: B5FF 206E 44D0 20AE 6CC5  30C1 5247 7AF6 A631 E77F
        created ....: 2023-12-12 22:18:06
  Authentication key: 5706 1CDF 8DC3 15ED 81A4  1DCC 37A9 02F0 30B4 4E05
        created ....: 2023-12-12 22:20:20
  General key info..:
  sub  ed25519/A2E618E20456625F 2023-12-12 John Doe (General purpose personal key) <john.doe@example.org>
  sec   ed25519/A6D3E049FD2A88C4  created: 2023-12-12  expires: never
  ssb>  ed25519/A2E618E20456625F  created: 2023-12-12  expires: 2024-12-11
                                  card-no: 0006 20141XXX
  ssb>  cv25519/52477AF6A631E77F  created: 2023-12-12  expires: 2024-12-11
                                  card-no: 0006 20141XXX
  ssb>  ed25519/37A902F030B44E05  created: 2023-12-12  expires: 2024-12-11
                                  card-no: 0006 20141XXX
  ```
{{< /callout >}}

Here is how to use the `uif` command:

```txt {hl_lines=[1,4]}
gpg/card> admin
Admin commands are allowed

gpg/card> uif
usage: uif N [on|off|permanent]
       1 <= N <= 3

gpg/card>
```

- `N` is the index of the key we want to update (Sign=1, Decrypt=2, Auth=3)
- `on` means the card will require a tap
- `off` means the card will not require a tap
- `permanent` is the same as `on` but you won't be able to change the flag anymore

I have no reason to keep one off, so let's set the three to `on`:

```txt {hl_lines=[1,14,16]}
gpg/card> uif 1 on

┌────────────────────────────────────────────────────┐
│ Please enter the Admin PIN                         │
│                                                    │
│ Number: 20 141 702                                 │
│ Holder: Doe John                                   │
│                                                    │
│ Admin PIN ________________________________________ │
│                                                    │
│       <OK>                            <Cancel>     │
└────────────────────────────────────────────────────┘

gpg/card> uif 2 on

gpg/card> uif 3 on
```

#### Checking that it worked {#checking-uif-worked}

We can make sure this worked by first checking the card data:

```txt
gpg/card> list

[...]
UIF setting ......: Sign=on Decrypt=on Auth=on
[...]

gpg/card> quit
```

They are all set to `on` which means it worked.

We can also check that signing a file requires a tap:

```bash
gpg --local-user "${KEY_ID:?}" --armor --output - --sign <(echo 'Test')
```

- When tapping the card after running the command:

  ```txt
  -----BEGIN PGP MESSAGE-----

  owGbwMvMwCW26JnEI5awpHjG07xJTIbGqZV8uiGpxSVcHaUsDGJcDLJiiiy3z/z0
  lNjkK8UQvNYZppyVCaSSgYtTACaybzsjw6yg+as77Kv/ckR52Pu4XFDtapyhXcny
  RG6XjL7H6zlVBxgZrtq4/53QeHxFyDrH18sWb9odlsr15bqi/c4rTt1fJ3zxYwYA
  =D0sU
  -----END PGP MESSAGE-----
  ```

- When not tapping the card:

  ```txt
  gpg: signing failed: Timeout
  -----BEGIN PGP MESSAGE-----

  gpg: signing failed: Timeout
  ```

I'm suprised I even see `-----BEGIN PGP MESSAGE-----` when not tapping, but it timed out so it worked anyway.

Note that having the card timeout also causes it to require a PIN the next time. This way, a background process can't loop infinitely until you tap the card for something else. It will be blocked by a [PINEntry]. Also, if your card always asks for a PIN, then there might be something shady happening that timeouts the card.

<!-- ### Customizing timeouts

TODO -->

## Miscellaneous useful commands

### List connected YubiKeys and get information about them

```bash
brew install ykman # YubiKey configuration manager
ykman list
```

```txt
YubiKey 5C Nano (5.4.3) [FIDO+CCID] Serial: 20141XXX
```

### Reset the OpenPGP application on the YubiKey

See [Resetting the OpenPGP Application on the YubiKey – Yubico](https://support.yubico.com/hc/en-us/articles/360013761339-Resetting-the-OpenPGP-Application-on-the-YubiKey "Resetting the OpenPGP Application on the YubiKey – Yubico").

`gpg --card-edit` then `admin`, `factory-reset` and `y`.

{{< callout "example" "-" "Example output" >}}

  ```bash
  gpg --card-edit
  ```

  ```txt
  Reader ...........: Yubico YubiKey FIDO CCID
  Application ID ...: D276000124010000000620141XXX0000
  Application type .: OpenPGP
  Version ..........: 3.4
  Manufacturer .....: Yubico
  Serial number ....: 20141XXX
  Name of cardholder: Doe John
  Language prefs ...: fr
  Salutation .......:
  URL of public key : [not set]
  Login data .......: [not set]
  Signature PIN ....: not forced
  Key attributes ...: ed25519 cv25519 ed25519
  Max. PIN lengths .: 127 127 127
  PIN retry counter : 3 0 3
  Signature counter : 3
  KDF setting ......: off
  UIF setting ......: Sign=on Decrypt=on Auth=on
  Signature key ....: DBCC F949 18B2 4D1A 0053  AD43 A2E6 18E2 0456 625F
        created ....: 2023-12-12 22:01:54
  Encryption key....: B5FF 206E 44D0 20AE 6CC5  30C1 5247 7AF6 A631 E77F
        created ....: 2023-12-12 22:18:06
  Authentication key: 5706 1CDF 8DC3 15ED 81A4  1DCC 37A9 02F0 30B4 4E05
        created ....: 2023-12-12 22:20:20
  General key info..: sub  ed25519/A2E618E20456625F 2023-12-12 John Doe (General purpose personal key) <john.doe@example.org>
  sec   ed25519/A6D3E049FD2A88C4  created: 2023-12-12  expires: never
  ssb>  ed25519/A2E618E20456625F  created: 2023-12-12  expires: 2024-12-11
                                  card-no: 0006 20141XXX
  ssb>  cv25519/52477AF6A631E77F  created: 2023-12-12  expires: 2024-12-11
                                  card-no: 0006 20141XXX
  ssb>  ed25519/37A902F030B44E05  created: 2023-12-12  expires: 2024-12-11
                                  card-no: 0006 20141XXX

  gpg/card> admin
  Admin commands are allowed

  gpg/card> factory-reset
  gpg: OpenPGP card no. D276000124010000000620141XXX0000 detected

  gpg: Note: This command destroys all keys stored on the card!

  Continue? (y/N) y
  Really do a factory reset? (enter "yes") yes

  gpg/card> list

  Reader ...........: Yubico YubiKey FIDO CCID
  Application ID ...: D276000124010000000620141XXX0000
  Application type .: OpenPGP
  Version ..........: 3.4
  Manufacturer .....: Yubico
  Serial number ....: 20141XXX
  Name of cardholder: [not set]
  Language prefs ...: [not set]
  Salutation .......:
  URL of public key : [not set]
  Login data .......: [not set]
  Signature PIN ....: not forced
  Key attributes ...: rsa2048 rsa2048 rsa2048
  Max. PIN lengths .: 127 127 127
  PIN retry counter : 3 0 3
  Signature counter : 0
  KDF setting ......: off
  UIF setting ......: Sign=off Decrypt=off Auth=off
  Signature key ....: [none]
  Encryption key....: [none]
  Authentication key: [none]
  General key info..: [none]

  gpg/card>
  ```

{{< /callout >}}

## References {#references}

To write this article, I used various sources:

- [Using Your YubiKey with OpenPGP – Yubico]
  - Lists compatible devices and explains the core logic of the process described in this article
- [drduh/YubiKey-Guide: Guide to using YubiKey for GPG and SSH](https://github.com/drduh/YubiKey-Guide "drduh/YubiKey-Guide: Guide to using YubiKey for GPG and SSH · GitHub")
  - Explains everything for every platform
- [Setting up GnuPG + Yubikey on NixOS for SSH authentication][rzetterberg-article]
  - Very complete, lots of good explanations, very close to this article finally (wish I had found it sooner)

and manuals:

- [GPG Configuration Options]

[PINEntry]: https://github.com/gpg/pinentry "gpg/pinentry: The standard pinentry collection."
[rzetterberg-article]: https://rzetterberg.github.io/yubikey-gpg-nixos.html "Setting up GnuPG + Yubikey on NixOS for SSH authentication"
[GPG Configuration Options]: https://www.gnupg.org/documentation/manuals/gnupg/GPG-Configuration-Options.html "GPG Configuration Options (Using the GNU Privacy Guard)"
[Using Your YubiKey with OpenPGP – Yubico]: https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP "Using Your YubiKey with OpenPGP – Yubico"
[YubiKey]: https://www.yubico.com/products/yubikey/ "“YubiKeys | Two-Factor Authentication for Secure Login” on yubico.com"

[Certify]: {{< relref "what-are-gpg-keys" >}}#capability-certify
[Sign]: {{< relref "what-are-gpg-keys" >}}#capability-sign
[Encrypt]: {{< relref "what-are-gpg-keys" >}}#capability-encrypt
[Authenticate]: {{< relref "what-are-gpg-keys" >}}#capability-authenticate
[primary key pair]: {{< relref "what-are-gpg-keys" >}}#primary-key-pair
[primary identity]: {{< relref "what-are-gpg-keys" >}}#uid
[subkey pairs]: {{< relref "what-are-gpg-keys" >}}#subkey-pairs
[Creating the primary key pair]: #creating-the-primary-key-pair

[^expert-flag]: From [GPG Configuration Options]: <blockquote>`--expert`, `--no-expert`<br>Allow the user to do certain nonsensical or "silly" things like signing an expired or revoked key, or certain potentially incompatible things like generating unusual key types. This also disables certain warning messages about potentially incompatible actions. As the name implies, this option is for experts only. If you don’t fully understand the implications of what it allows you to do, leave this off. `--no-expert` disables this option.</blockquote>
[^uif]: From [gpg-card](https://www.gnupg.org/documentation/manuals/gnupg/gpg_002dcard.html): <blockquote>`UIF N [on|off|permanent]`<br>Change the User Interaction Flag. That flags tells whether the confirmation button of a token shall be used. n must in the range 1 to 3. "permanent" is the same as "on" but the flag can’t be changed anmore.</blockquote>
