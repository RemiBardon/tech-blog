---
title: Refresh GPG keys
summary: &summary |
  How to refresh GPG keys before they expire.
description: *summary
date: 2024-12-06
lastmod: 2024-12-06
draft: false
weight: 70
tags:
  - GPG
  - OpenPGP
  - Cryptography
  - Security
  - YubiKey
  - WIP
---

{{< callout "info" >}}
I haven’t finished writing this blog post, but I published it so the already-written information can be read by you. I will improve this post one day™.
{{< /callout >}}

```bash
gpg --import "${KEY_ID:?}"-master.asc
```

```txt
gpg> addkey
Your selection? 10
Your selection? 1
Key is valid for? (0) 13m
Is this correct? (y/N) y
Really create? (y/N) y

gpg> addkey
Your selection? 12
Your selection? 1
Key is valid for? (0) 13m
Is this correct? (y/N) y
Really create? (y/N) y

gpg> addkey
Your selection? 11
Your selection? S
Your selection? A
Your selection? Q
Your selection? 1
Key is valid for? (0) 13m
Is this correct? (y/N) y
Really create? (y/N) y
```

```bash
gpg --edit-key "${KEY_ID:?}"
```

```txt
gpg> key 7
gpg> keytocard

gpg> key 7
gpg> key 8
gpg> keytocard

gpg> key 8
gpg> key 9
gpg> keytocard

gpg> save
```

```bash
gpg --delete-secret-keys "${KEY_ID:?}"
```

```txt
gpg (GnuPG) 2.4.6; Copyright (C) 2024 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


sec  ed25519/A6D3E049FD2A88C4 2023-12-12 John Doe (General purpose personal key) <john.doe@example.org>

Delete this key from the keyring? (y/N) y
This is a secret key! - really delete? (y/N) y
```

gpg --card-edit
fetch

```bash
gpg --card-status
```

uif 2 on
uif 3 on

https://keys.openpgp.org/manage
GitHub


https://digitalnotions.net/using-yubikey-pgp-keys-on-new-computer/
https://www.g-loaded.eu/2010/11/01/change-expiration-date-gpg-key/
