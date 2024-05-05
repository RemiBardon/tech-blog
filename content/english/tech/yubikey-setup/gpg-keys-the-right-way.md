---
title: Creating GPG keys the right way
summary: &summary |
  WIP
description: *summary
date: 2023-12-10
# publishDate: 2023-12-11
draft: false
weight: 90
tags:
  - GPG
  - OpenPGP
  - Cryptography
  - Security
  - WIP
slug: creating-gpg-keys
---

{{< callout "info" >}}
I haven’t finished writing this blog post, but I published it so the already-written information can be read by you. I will improve this post one day™.
{{< /callout >}}

{{< callout "info" >}}
If you're not entirely familiar with all concepts surrouding GPG, you should read my article "[What are GPG keys?]({{< relref "what-are-gpg-keys" >}})" first. It will give you all the necessary concepts and vocabulary you will need to understand this article.
{{< /callout >}}

## Why all this? {#why}

The first reason is that I just started as a freelance developer and I created a new session especially for it.

- I had too many SSH keys
- I changed my email address
- GPG keys contain identity information

## What I want {#what-i-want}

- A GPG key
  - With a [primary key pair] that can only [Certify]
  - With my personal contact info as the [primary identity]
  - With a subkey pair that can only [Sign], valid for 1 year
  - With a subkey pair that can only [Encrypt], valid for 1 year
  - With a subkey pair that can only [Authenticate], valid for 1 year
- A backup of the private keys ([primary key pair] and [subkey pairs])
- My YubiKey configured so my name appears on it and with a custom PIN / admin PIN
- No private key on my computer
- The 3 private subkeys on my YubiKey
- My GPG keys used for SSH authentication and git signing
  - Require a "card tap" on every SSH authentication
  - Require a "card tap" on every git signing operation

My YubiKey supports it, and I don't use any tool that don't so I'm going to use the state-of-the-art [ed25519] signature scheme for my keys instead of heavier 4096 bits [RSA] keys.

## The whole process {#process}

### Installing required tools

On macOS, run:

```bash
brew install gnupg
```

Otherwise, go to [GnuPG - Download](https://www.gnupg.org/download/index.html "GnuPG - Download").

{{< callout "info" >}}
This article has been written using `gnupg: stable 2.4.3`.
{{< /callout >}}

### Creating the primary key pair {#creating-the-primary-key-pair}

To create a GnuPG key, you need to run `gpg --full-gen-key`. However, it only shows a limited number of options, which do not provide the opportunity to create a [Certify]-only [primary key pair]:

{{< callout "example" "-" "The options available by default" >}}

  ```txt
  gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
  This is free software: you are free to change and redistribute it.
  There is NO WARRANTY, to the extent permitted by law.

  Please select what kind of key you want:
    (1) RSA and RSA
    (2) DSA and Elgamal
    (3) DSA (sign only)
    (4) RSA (sign only)
    (9) ECC (sign and encrypt) *default*
    (10) ECC (sign only)
    (14) Existing key from card
  Your selection? 
  ```

{{< /callout >}}

For this reason, we will have to use the `--expert` flag[^expert-flag]:

```bash
gpg --full-gen-key --expert
```

{{< callout "example" "+" "Command output" >}}

  ```txt
  gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
  This is free software: you are free to change and redistribute it.
  There is NO WARRANTY, to the extent permitted by law.

  Please select what kind of key you want:
    (1) RSA and RSA
    (2) DSA and Elgamal
    (3) DSA (sign only)
    (4) RSA (sign only)
    (7) DSA (set your own capabilities)
    (8) RSA (set your own capabilities)
    (9) ECC (sign and encrypt) *default*
    (10) ECC (sign only)
    (11) ECC (set your own capabilities)
    (13) Existing key
    (14) Existing key from card
  Your selection?
  ```

{{< /callout >}}

We want to set our own capabilities to choose [Certify] only and I want to use [ed25519] so when prompted what kind of key I want I'm going to choose `11` ("ECC" means "Elliptic Curve Cryptography"):

{{< callout "example" "+" "Terminal output" >}}

  ```txt {hl_lines=[1,4]}
  Your selection? 11

  Possible actions for this ECC key: Sign Certify Authenticate
  Current allowed actions: Sign Certify

    (S) Toggle the sign capability
    (A) Toggle the authenticate capability
    (Q) Finished

  Your selection? 
  ```

{{< /callout >}}

We want [Certify] only, so let's choose `S` (`s` works too) and then `Q`:

{{< callout "example" "+" "Terminal output" >}}

  ```txt {hl_lines=[1,4,10]}
  Your selection? S

  Possible actions for this ECC key: Sign Certify Authenticate
  Current allowed actions: Certify

    (S) Toggle the sign capability
    (A) Toggle the authenticate capability
    (Q) Finished

  Your selection? Q
  Please select which elliptic curve you want:
    (1) Curve 25519 *default*
    (2) Curve 448
    (3) NIST P-256
    (4) NIST P-384
    (5) NIST P-521
    (6) Brainpool P-256
    (7) Brainpool P-384
    (8) Brainpool P-512
    (9) secp256k1
  Your selection?
  ```

{{< /callout >}}

We want to use the "Curve 25519" signature scheme so let's choose `1` (or just hit <kbd>Enter</kbd> as it's the default):

{{< callout "example" "+" "Terminal output" >}}

  ```txt {hl_lines=[1]}
  Your selection? 1
  Please specify how long the key should be valid.
          0 = key does not expire
        <n>  = key expires in n days
        <n>w = key expires in n weeks
        <n>m = key expires in n months
        <n>y = key expires in n years
  Key is valid for? (0)
  ```

{{< /callout >}}

The [primary key pair] will be kept somewhere safe and we will delete it locally so we can make it never expire:

{{< callout "example" "+" "Terminal output" >}}

  ```txt {hl_lines=[1,3]}
  Key is valid for? (0) 0
  Key does not expire at all
  Is this correct? (y/N) y

  GnuPG needs to construct a user ID to identify your key.

  Real name: 
  ```

{{< /callout >}}

Now is the moment where we create the [primary identity]. For example purposes and to avoid you copy-pasting my information, I'm going to call myself John Doe. You should put your real name, not a nickname.

{{< callout "example" "+" "Terminal output" >}}

  ```txt {hl_lines=["1-3",7]}
  Real name: John Doe
  Email address: john.doe@example.org
  Comment: General purpose personal key
  You selected this USER-ID:
      "John Doe (General purpose personal key) <john.doe@example.org>"

  Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
  ```

{{< /callout >}}

Now `gpg` will ask you for a passphrase. You should make it very complex and store it very safely as it is what will ensure your key is never misused. `gpg` uses a [PINEntry] dialog to ask you for the passphrase, making it more secure than asking it inline.

{{< callout "example" "-" "What the PINEntries look like" >}}

  ```txt
  ┌──────────────────────────────────────────────────────┐
  │ Please enter the passphrase to                       │
  │ protect your new key                                 │
  │                                                      │
  │ Passphrase: ________________________________________ │
  │                                                      │
  │       <OK>                              <Cancel>     │
  └──────────────────────────────────────────────────────┘
  ```

  ```txt
  ┌──────────────────────────────────────────────────────┐
  │ Please re-enter this passphrase                      │
  │                                                      │
  │ Passphrase: ________________________________________ │
  │                                                      │
  │       <OK>                              <Cancel>     │
  └──────────────────────────────────────────────────────┘
  ```

{{< /callout >}}

Once those credentials entered, `gpg` will show this message:

```txt
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
```

On my machine the key generation is very fast so you could start streaming a video or something like that in the background to ensure there is enough [entropy].

After key generation succeeds, `gpg` prints different information:

{{< callout "example" "+" "Terminal output" >}}

  ```txt
  gpg: revocation certificate stored as '/Users/johndoe/.gnupg/openpgp-revocs.d/DFF5C72AE657A8361A57D937A6D3E049FD2A88C4.rev'
  public and secret key created and signed.

  pub   ed25519 2023-12-12 [C]
        DFF5C72AE657A8361A57D937A6D3E049FD2A88C4
  uid                      John Doe (General purpose personal key) <john.doe@example.org>
  ```

{{< /callout >}}

- First, it informs that there is a revocation certificate (stored in `/Users/johndoe/.gnupg/openpgp-revocs.d/DFF5C72AE657A8361A57D937A6D3E049FD2A88C4.rev` here).

  {{< callout "example" "-" "The content of this certificate" >}}

  
    ```txt
    This is a revocation certificate for the OpenPGP key:
    
    pub   ed25519 2023-12-12 []
          DFF5C72AE657A8361A57D937A6D3E049FD2A88C4
    uid          John Doe (General purpose personal key) <john.doe@example.org>
    
    A revocation certificate is a kind of "kill switch" to publicly
    declare that a key shall not anymore be used.  It is not possible
    to retract such a revocation certificate once it has been published.
    
    Use it to revoke this key in case of a compromise or loss of
    the secret key.  However, if the secret key is still accessible,
    it is better to generate a new revocation certificate and give
    a reason for the revocation.  For details see the description of
    of the gpg command "--generate-revocation" in the GnuPG manual.
    
    To avoid an accidental use of this file, a colon has been inserted
    before the 5 dashes below.  Remove this colon with a text editor
    before importing and publishing this revocation certificate.
    
    :-----BEGIN PGP PUBLIC KEY BLOCK-----
    Comment: This is a revocation certificate
    
    iHgEIBYKACAWIQTf9ccq5leoNhpX2Tem0+BJ/SqIxAUCZXixhgIdAAAKCRCm0+BJ
    /SqIxIzvAQDtNTas4P/meRRwX9Cl4gdHGsEQWvFVuEeiDKhSn8AR1AEA/m53PfeR
    wv2JWrZuQy8DRSjsSRGqYVb1fWODFZ8MDAs=
    =zdjH
    -----END PGP PUBLIC KEY BLOCK-----
    ```

  {{< /callout >}}

  {{< callout "important" >}}

    You should save it somewhere safe, in case you need to revoke the entire GPG key someday.
  {{< /callout >}}

- It also prints information about the key:
  
  ```txt
  pub   ed25519 2023-12-12 [C]
        DFF5C72AE657A8361A57D937A6D3E049FD2A88C4
  uid                      John Doe (General purpose personal key) <john.doe@example.org>
  ```
  
  All of this information is public, and I broke down what it means in [After generating a key](#gpg-output-key-generation).

To make things easier later, let's save the key fingerprint in an environment variable:

```bash
KEY_ID='DFF5C72AE657A8361A57D937A6D3E049FD2A88C4'
```

This value can then be used for every `gpg` operation to identify the key.

{{< callout "tip" >}}

  You could also use the email address or even just part of the name (like "John") if it is unique. I prefer using the fingerprint to ensure uniqueness.
{{< /callout >}}

### Creating the subkey pairs {#creating-the-subkey-pairs}

Now that we have a [primary key pair], we can derive the three subkeys from it. For that, we use `gpg --edit-key` with the `--expert` flag to be sure we can select the desired capabilities:

```bash
gpg --edit-key --expert "${KEY_ID:?}"
```

{{< callout "tip" >}}

  `:?` after the environment variable name ensures you didn't forget to set it 😉 If you did, it will abort saying "KEY_ID: parameter not set".
{{< /callout >}}

We now arrive in a console-like environment where we can interact with the GPG key.

{{< callout "example" "-" "What the `gpg` console looks like" >}}

  ```txt
  gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
  This is free software: you are free to change and redistribute it.
  There is NO WARRANTY, to the extent permitted by law.

  Secret key is available.

  sec  ed25519/A6D3E049FD2A88C4
      created: 2023-12-12  expires: never       usage: C
      trust: ultimate      validity: ultimate
  [ultimate] (1). John Doe (General purpose personal key) <john.doe@example.org>

  gpg> 
  ```

{{< /callout >}}

{{< callout "tip" >}}

  You can find all operations available by running `help` or via [edit-key · The GNU Privacy Handbook].
{{< /callout >}}

Since we want to add a new subkey, we will use `addkey`[^addkey]:

{{< callout "example" "+" "Terminal output" >}}

  ```txt {hl_lines=[1]}
  gpg> addkey
  Please select what kind of key you want:
    (3) DSA (sign only)
    (4) RSA (sign only)
    (5) Elgamal (encrypt only)
    (6) RSA (encrypt only)
    (7) DSA (set your own capabilities)
    (8) RSA (set your own capabilities)
    (10) ECC (sign only)
    (11) ECC (set your own capabilities)
    (12) ECC (encrypt only)
    (13) Existing key
    (14) Existing key from card
  Your selection?
  ```

{{< /callout >}}

For this first key, we want the [Sign] capability only, so let's enter `10` and then `1` for "Curve 25519":

{{< callout "example" "+" "Terminal output" >}}

  ```txt {hl_lines=[1,12]}
  Your selection? 10
  Please select which elliptic curve you want:
    (1) Curve 25519 *default*
    (2) Curve 448
    (3) NIST P-256
    (4) NIST P-384
    (5) NIST P-521
    (6) Brainpool P-256
    (7) Brainpool P-384
    (8) Brainpool P-512
    (9) secp256k1
  Your selection? 1
  Please specify how long the key should be valid.
          0 = key does not expire
        <n>  = key expires in n days
        <n>w = key expires in n weeks
        <n>m = key expires in n months
        <n>y = key expires in n years
  Key is valid for? (0)
  ```

{{< /callout >}}

Having keys that expire is a good thing because it reduces the chance of having a leaked private key be used for malicious purposes. However, constantly changing (or "rotating") the keys is quite tedious so I think having keys valid for 1 year is a good in-between. Let's enter `1y` then:

```txt
Key is valid for? (0) 1y
Key expires at Mer 11 déc 16:02:05 2024 CST
Is this correct? (y/N) y
Really create? (y/N) y
```

`gpg` now asks for the passphrase so it can read the private key and derive a subkey from it.

{{< callout "example" "-" "What the PINEntry looks like" >}}

  ```txt
  ┌──────────────────────────────────────────────────────────────────┐
  │ Please enter the passphrase to unlock the OpenPGP secret key:    │
  │ "John Doe (General purpose personal key) <john.doe@example.org>" │
  │ 255-bit EDDSA key, ID A6D3E049FD2A88C4,                          │
  │ created 2023-12-12.                                              │
  │                                                                  │
  │                                                                  │
  │ Passphrase: ____________________________________________________ │
  │                                                                  │
  │         <OK>                                      <Cancel>       │
  └──────────────────────────────────────────────────────────────────┘
  ```

{{< /callout >}}

After that, `gpg` generates a new key and prints this time information about the private keys (`sec`, `ssb`).

{{< callout "example" "-" "Terminal output" >}}

  ```txt
  We need to generate a lot of random bytes. It is a good idea to perform
  some other action (type on the keyboard, move the mouse, utilize the
  disks) during the prime generation; this gives the random number
  generator a better chance to gain enough entropy.

  sec  ed25519/A6D3E049FD2A88C4
      created: 2023-12-12  expires: never       usage: C
      trust: ultimate      validity: ultimate
  ssb  ed25519/A2E618E20456625F
      created: 2023-12-12  expires: 2024-12-11  usage: S
  [ultimate] (1). John Doe (General purpose personal key) <john.doe@example.org>

  gpg> 
  ```

{{< /callout >}}

Now that we have the [Sign] (`S`) subkey, we can add an [Encrypt] subkey by doing the same but choosing `12` ("ECC (encrypt only)") as the kind:

{{< callout "example" "-" "Terminal output" >}}

  ```txt {hl_lines=[1,14,25,32,"34-35"]}
  gpg> addkey
  Please select what kind of key you want:
    (3) DSA (sign only)
    (4) RSA (sign only)
    (5) Elgamal (encrypt only)
    (6) RSA (encrypt only)
    (7) DSA (set your own capabilities)
    (8) RSA (set your own capabilities)
    (10) ECC (sign only)
    (11) ECC (set your own capabilities)
    (12) ECC (encrypt only)
    (13) Existing key
    (14) Existing key from card
  Your selection? 12
  Please select which elliptic curve you want:
    (1) Curve 25519 *default*
    (2) Curve 448
    (3) NIST P-256
    (4) NIST P-384
    (5) NIST P-521
    (6) Brainpool P-256
    (7) Brainpool P-384
    (8) Brainpool P-512
    (9) secp256k1
  Your selection? 1
  Please specify how long the key should be valid.
          0 = key does not expire
        <n>  = key expires in n days
        <n>w = key expires in n weeks
        <n>m = key expires in n months
        <n>y = key expires in n years
  Key is valid for? (0) 1y
  Key expires at Mer 11 déc 16:18:15 2024 CST
  Is this correct? (y/N) y
  Really create? (y/N) y
  We need to generate a lot of random bytes. It is a good idea to perform
  some other action (type on the keyboard, move the mouse, utilize the
  disks) during the prime generation; this gives the random number
  generator a better chance to gain enough entropy.

  sec  ed25519/A6D3E049FD2A88C4
      created: 2023-12-12  expires: never       usage: C
      trust: ultimate      validity: ultimate
  ssb  ed25519/A2E618E20456625F
      created: 2023-12-12  expires: 2024-12-11  usage: S
  ssb  cv25519/52477AF6A631E77F
      created: 2023-12-12  expires: 2024-12-11  usage: E
  [ultimate] (1). John Doe (General purpose personal key) <john.doe@example.org>

  gpg> 
  ```

{{< /callout >}}

We now see a second secret subkey (`ssb`) with the [Encrypt] capability (`E`).

Finally, let's create the third and last subkey, with [Authenticate] capability:

{{< callout "example" "+" "Terminal output" >}}

  ```txt {hl_lines=[1]}
  gpg> addkey
  Please select what kind of key you want:
    (3) DSA (sign only)
    (4) RSA (sign only)
    (5) Elgamal (encrypt only)
    (6) RSA (encrypt only)
    (7) DSA (set your own capabilities)
    (8) RSA (set your own capabilities)
    (10) ECC (sign only)
    (11) ECC (set your own capabilities)
    (12) ECC (encrypt only)
    (13) Existing key
    (14) Existing key from card
  Your selection? 
  ```

{{< /callout >}}

As you can see, there is no "ECC (authenticate only)" option, so we will need to choose `11` ("ECC (set your own capabilities)") and toggle the capabilities as we did when [Creating the primary key pair]:

{{< callout "example" "-" "Terminal output" >}}

  ```txt {hl_lines=[1,10,19,28,39,46,"48-49"]}
  Your selection? 11

  Possible actions for this ECC key: Sign Authenticate
  Current allowed actions: Sign

    (S) Toggle the sign capability
    (A) Toggle the authenticate capability
    (Q) Finished

  Your selection? S

  Possible actions for this ECC key: Sign Authenticate
  Current allowed actions:

    (S) Toggle the sign capability
    (A) Toggle the authenticate capability
    (Q) Finished

  Your selection? A

  Possible actions for this ECC key: Sign Authenticate
  Current allowed actions: Authenticate

    (S) Toggle the sign capability
    (A) Toggle the authenticate capability
    (Q) Finished

  Your selection? Q
  Please select which elliptic curve you want:
    (1) Curve 25519 *default*
    (2) Curve 448
    (3) NIST P-256
    (4) NIST P-384
    (5) NIST P-521
    (6) Brainpool P-256
    (7) Brainpool P-384
    (8) Brainpool P-512
    (9) secp256k1
  Your selection? 1
  Please specify how long the key should be valid.
          0 = key does not expire
        <n>  = key expires in n days
        <n>w = key expires in n weeks
        <n>m = key expires in n months
        <n>y = key expires in n years
  Key is valid for? (0) 1y
  Key expires at Mer 11 déc 16:22:35 2024 CST
  Is this correct? (y/N) y
  Really create? (y/N) y
  We need to generate a lot of random bytes. It is a good idea to perform
  some other action (type on the keyboard, move the mouse, utilize the
  disks) during the prime generation; this gives the random number
  generator a better chance to gain enough entropy.

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

We can see that we successfully created the [Sign], [Encrypt] and [Authenticate] keys, in addiction to the primary [Certify] key. We now have to save:

```txt
gpg> save
```

This saves the changes (which would not be saved on `quit` or <kbd>Ctrl</kbd>+<kbd>C</kbd>) and gets us back to the terminal.

### Backing up the keys {#backup}

Now we want to create backups of the secret keys before we delete them. This can be done using `--export-secret-*`[^export-secret] commands. By default, `gpg` exports in binary OpenPGP format so we use the `--armor` flag to generate an ASCII-armored output. You can find more information about this format in [What is ASCII-armored format?](#ascii-armored), but basically its a format that uses only printable [ASCII] characters. To avoid having a trace of the output of this command stored on my computer[^pbcopy-can-leave-traces], I'm piping the output to `pbcopy` which copies the sensitive information to my clipboard:

```bash
gpg --export-secret-keys --armor "${KEY_ID}" | pbcopy
```

Now we can save this backup in a very safe place, like an external drive then kept offline or a password manager for convenience (even though it's not as safe).

We can also generate a less sensitive backup with only the private keys of the [subkey pairs], and a placeholder for the [primary key pair] private key:

```bash
gpg --export-secret-subkeys --armor "${KEY_ID}" | pbcopy
```

{{< callout "caution" >}}

  Keep in mind that this does not replace the `--export-secret-keys` backup, as the [primary key pair] private key is vital!
{{< /callout >}}

## Q&A

### What is ASCII-armored format? {#ascii-armored}

{{< callout "cite" "+" "GPT-3.5, 2023-12-10" >}}

  ASCII-armored (also known as ASCII-armoured or ASCII-armour) refers to a method of representing binary data (such as cryptographic keys) using only printable ASCII characters. It's a way to encode binary data into a text format that can be easily transported via text-based communication channels like email or plaintext files without corruption or loss of information.

  In the context of GnuPG (GPG) or PGP (Pretty Good Privacy), ASCII-armored format is commonly used to export and share keys. When you export a GPG key in ASCII-armored format, the binary data of the key (which includes your public or private key) is converted into a text-based representation. This representation contains only printable ASCII characters (letters, numbers, and symbols commonly found in plain text) that can be safely transmitted or stored without corruption.

  ASCII-armored files typically have file extensions like `.asc` or `.gpg`, and they contain the encoded key data that can be imported back into GPG or PGP systems when needed.

  For example, when you export a GPG key to an ASCII-armored file, the resulting file will look something like this:

  ```txt
  -----BEGIN PGP PRIVATE KEY BLOCK-----
  mQENBF5QwYwBCADm36ZLDl4JcnJ0Iyv/xTXbweAbCf09gODTk/8E+JZrG9zSyF1N
  ...
  ...
  ...
  wXd1xUDquOqwDe8q/QFwUBoPqQ==
  =B1g3
  -----END PGP PRIVATE KEY BLOCK-----
  ```

  The `-----BEGIN PGP...-----` and `-----END PGP...-----` delimiters mark the beginning and end of the ASCII-armored data, respectively.

  ASCII-armored keys are human-readable and can be copied and pasted, transmitted via email, or stored in text-based files while retaining their integrity and allowing for easy sharing and importation into GPG or PGP software.

{{< /callout >}}

### What do `gpg` outputs mean?

#### After generating a key {#gpg-output-key-generation}

After generating a key, `gpg` prints the same information as `gpg --list-keys <KEY_ID>`:

```txt
pub   ed25519 2023-12-12 [C]
      DFF5C72AE657A8361A57D937A6D3E049FD2A88C4
uid                      John Doe (General purpose personal key) <john.doe@example.org>
```

Here is a breakdown of what it means:

- `pub` introduces the public key of the [primary key pair]
  - `ed25519` is the scheme
  - `2023-12-12` is the creation date
  - `[C]` means this key has [Certify] capabilities only
  - `DFF5C72AE657A8361A57D937A6D3E049FD2A88C4` is the [key fingerprint]
- `uid` introduces the [primary identity]
  - `John Doe` is the real name
  - `General purpose personal key` is the comment
  - `john.doe@example.org` is the email address

## Common issues {#common-issues}

### I don't see the "set your own capabilities" options {#own-capabilities-not-available}

If you don't see the "set your own capabilities" options, you probably forgot to add the `--expert` flag[^expert-flag] to `gpg --edit-key`.

## Miscellaneous useful commands

### Delete a subkey pair

To delete a [subkey pair] from a GPG key, you need to edit the key:

```bash
gpg --edit-key <KEY_ID>
```

(replacing `<KEY_ID>` by an unique identifier for your GPG key (fingerprint, key ID, email, name…))

Then select, a [subkey pair] by its index (`<n>`, starting from `0`) and then run `delkey`:

```txt
gpg> key <n>
gpg> delkey
```

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

## References {#references}

To write this article, I used various sources:

- [Using Your YubiKey with OpenPGP – Yubico]
  - Lists compatible devices and explains the core logic of the process described in this article
- [drduh/YubiKey-Guide: Guide to using YubiKey for GPG and SSH](https://github.com/drduh/YubiKey-Guide "drduh/YubiKey-Guide: Guide to using YubiKey for GPG and SSH · GitHub")
  - Explains everything for every platform
- [Setting up GnuPG + Yubikey on NixOS for SSH authentication][rzetterberg-article]
  - Very complete, lots of good explanations, very close to this article finally (wish I had found it sooner)
- [GPG Cheat Sheet | Andy Gock](https://gock.net/blog/2020/gpg-cheat-sheet/ "GPG Cheat Sheet | Andy Gock")
  - Haven't used but might be interesting to keep around

and manuals:

- [GPG Configuration Options]

<!-- *Example1 -->

[PINEntry]: https://github.com/gpg/pinentry "gpg/pinentry: The standard pinentry collection."
[rzetterberg-article]: https://rzetterberg.github.io/yubikey-gpg-nixos.html "Setting up GnuPG + Yubikey on NixOS for SSH authentication"
[GPG Configuration Options]: https://www.gnupg.org/documentation/manuals/gnupg/GPG-Configuration-Options.html "GPG Configuration Options (Using the GNU Privacy Guard)"
[ed25519]: https://en.wikipedia.org/wiki/EdDSA#Ed25519 "EdDSA · Wikipedia"
[RSA]: https://en.wikipedia.org/wiki/RSA_(cryptosystem) "RSA (cryptosystem) · Wikipedia"
[entropy]: https://en.wikipedia.org/wiki/Entropy "Entropy · Wikipedia"
[key fingerprint]: https://en.wikipedia.org/wiki/Public_key_fingerprint "Public key fingerprint · Wikipedia"
[edit-key · The GNU Privacy Handbook]: https://www.gnupg.org/gph/en/manual/r899.html "edit-key · The GNU Privacy Handbook"
[ASCII]: https://en.wikipedia.org/wiki/ASCII "ASCII · Wikipedia"
[Using Your YubiKey with OpenPGP – Yubico]: https://support.yubico.com/hc/en-us/articles/360013790259-Using-Your-YubiKey-with-OpenPGP "Using Your YubiKey with OpenPGP – Yubico"

[Certify]: {{< relref "what-are-gpg-keys" >}}#capability-certify
[Sign]: {{< relref "what-are-gpg-keys" >}}#capability-sign
[Encrypt]: {{< relref "what-are-gpg-keys" >}}#capability-encrypt
[Authenticate]: {{< relref "what-are-gpg-keys" >}}#capability-authenticate
[primary key pair]: {{< relref "what-are-gpg-keys" >}}#primary-key-pair
[primary identity]: {{< relref "what-are-gpg-keys" >}}#uid
[subkey pairs]: {{< relref "what-are-gpg-keys" >}}#subkey-pairs
[Creating the primary key pair]: #creating-the-primary-key-pair

[^expert-flag]: From [GPG Configuration Options]: <blockquote>`--expert`, `--no-expert`<br>Allow the user to do certain nonsensical or "silly" things like signing an expired or revoked key, or certain potentially incompatible things like generating unusual key types. This also disables certain warning messages about potentially incompatible actions. As the name implies, this option is for experts only. If you don’t fully understand the implications of what it allows you to do, leave this off. `--no-expert` disables this option.</blockquote>
[^addkey]: From [edit-key · The GNU Privacy Handbook]: <blockquote>`addkey`<br>Add a new subkey to the current key</blockquote>
[^export-secret]: From [`man gpg`](https://manpages.org/gpg): <blockquote>`--export-secret-keys`, `--export-secret-subkeys`<br>Same as `--export`, but exports the secret keys instead.  The exported keys are written to STDOUT or to the file given with option `--output`.  This command is often used along with the option `--armor` to allow for easy printing of the key for paper backup; however the external tool `paperkey` does a better job of creating backups on paper.  Note that exporting a secret key can be a security risk if the exported keys are sent over an insecure channel.<br>The second form of the command has the special property to render the secret part of the primary key useless; this is a GNU extension to OpenPGP and other implementations can not be expected to successfully import such a key.  Its intended use is in generating a full key with an additional signing subkey on a dedicated machine.  This command then exports the key without the primary key to the main machine.<br>GnuPG may ask you to enter the passphrase for the key.  This is required, because the internal protection method of the secret key is different from the one specified by the OpenPGP protocol.</blockquote>
[^pbcopy-can-leave-traces]: I know there are some clipboard managers which keep a history of copied text, but I don't think this is happening with Apple's clipboard and it's always better than a regular file.
