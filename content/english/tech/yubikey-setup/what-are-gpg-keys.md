---
title: What are GPG keys?
summary: &summary >
  In this article, I explain what GnuPG is and its differences with PGP, `gpg` and OpenPGP.
  I aslo go into the details of how a GnuPG key is structured, what you can find inside it and explain some of its core concepts.
description: *summary
date: 2023-12-10
# publishDate: 2023-12-10
draft: false
weight: 100
tags:
  - GPG
  - OpenPGP
  - Cryptography
  - Security
slug: what-is-gpg
---

{{< callout "info" >}}
I haven’t finished writing this blog post, but I published it so the already-written information can be read by you. I will improve this post one day™.
{{< /callout >}}

## PGP, gpg, GnuPG, OpenPGP… what is it? {#pgp-gpg-gnupg-openpgp}

{{< callout "tldr" "-" >}}

- PGP (Pretty Good Privacy) is the original proprietary software package
- OpenPGP is the open standard defined by [RFC4880](https://www.ietf.org/rfc/rfc4880.txt)
- GnuPG (GNU Privacy Guard), often abbreviated as GPG, is a complete and free implementation of the OpenPGP standard
- `gpg` is the command used on Unix systems to work with GnuPG

{{< /callout >}}

{{< callout "cite" "" "[The GNU Privacy Guard · gnupg.org](https://gnupg.org/)" >}}

  GnuPG is a complete and free implementation of the OpenPGP standard as defined by [RFC4880](https://www.ietf.org/rfc/rfc4880.txt) (also known as _PGP_). GnuPG allows you to encrypt and sign your data and communications; it features a versatile key management system, along with access modules for all kinds of public key directories. GnuPG, also known as _GPG_, is a command line tool with features for easy integration with other applications. A wealth of [frontend applications](https://gnupg.org/software/frontends.html) and [libraries](https://gnupg.org/software/libraries.html) are available. GnuPG also provides support for S/MIME and Secure Shell (ssh).

  Since its introduction in 1997, GnuPG is [Free Software](https://www.gnu.org/philosophy/free-sw.html) (meaning that it respects your freedom). It can be freely used, modified and distributed under the terms of the [GNU General Public License](https://www.gnu.org/copyleft/gpl.html).

{{< /callout >}}

[RFC4880 "OpenPGP Message Format"](https://www.ietf.org/rfc/rfc4880.txt), mentioned by GnuPG, was only published in November 2007 but it obsoletes older [RFC2440 "OpenPGP Message Format"](https://www.ietf.org/rfc/rfc2440.txt) (November 1998) and [RFC1991 "PGP Message Exchange Formats"](https://www.ietf.org/rfc/rfc1991.txt)(August 1996). PGP is a long-living standard.

{{< callout "cite" "" "GPT-3.5, 2023-12-10 (with some formatting improvements)" >}}

GPG (GNU Privacy Guard) and PGP (Pretty Good Privacy) are both cryptographic software used for encryption, decryption, digital signatures, and securing communication. They provide similar functionalities and share common goals of ensuring data confidentiality and integrity, but they have different origins and implementations.

### PGP (Pretty Good Privacy)

- **Origin**: PGP was developed by Phil Zimmermann in the early 1990s. It was initially released as a proprietary software package for encrypting and securing email communications.
- **Features**: PGP offers various cryptographic functionalities such as encryption, decryption, digital signatures, and secure email communication. It gained popularity for its strong encryption capabilities and was widely used for securing emails and files.
- **Ownership**: PGP was initially a proprietary software owned by various companies over the years. Symantec Corporation acquired PGP Corporation, which further developed and maintained the PGP software suite.

### GPG (GNU Privacy Guard)

- **Origin**: GPG, on the other hand, is an open-source implementation of the OpenPGP standard. It was developed by Werner Koch and released as free software as part of the GNU Project.
- **Compatibility**: GPG aims to be compatible with the OpenPGP standard defined in RFC 4880. It implements the OpenPGP protocol for secure communication and encryption.
- **Open Source**: GPG is distributed under the GNU General Public License (GPL) and is free to use, modify, and distribute. Being open-source, GPG is transparent and subject to community-driven development and scrutiny.

### Differences

1. **Licensing and Ownership**: PGP started as a proprietary software with various ownerships over time, while GPG is an open-source implementation distributed under a free software license.
2. **Development Model**: GPG's development is open and community-driven, while PGP's development was initially proprietary and later continued under commercial entities.
3. **Compatibility**: Both GPG and PGP adhere to the OpenPGP standard, which means they are designed to be interoperable with each other and other software that follows the same standard.

In summary, while GPG and PGP serve similar purposes and follow the same OpenPGP standard, the key differences lie in their origins, licensing, development models, and ownership history. GPG is a free and open-source implementation of the OpenPGP standard, while PGP was initially a proprietary software later maintained by commercial entities.

{{< /callout >}}

## Some GPG-related cryptography vocabulary {#crypto-vocabulary}

{{< callout "tip" >}}
If you're familiar with cryptography, you can jump to [the next section](#inside-a-gpg-key).
{{< /callout >}}

### Asymmetric cryptography {#asymmetric-cryptography}

{{< callout "cite" "" "[Public-key cryptography · Wikipedia](https://en.wikipedia.org/wiki/Public-key_cryptography)" >}}

  **Public-key cryptography**, or **asymmetric cryptography**, is the field of cryptographic systems that use pairs of related keys. Each key pair consists of a **public key** and a corresponding **private key**. Key pairs are generated with [cryptographic](https://en.wikipedia.org/wiki/Cryptographic "Cryptographic") [algorithms](https://en.wikipedia.org/wiki/Algorithms "Algorithms") based on [mathematical](https://en.wikipedia.org/wiki/Mathematical "Mathematical") problems termed [one-way functions](https://en.wikipedia.org/wiki/One-way_function "One-way function"). Security of public-key cryptography depends on keeping the private key secret; the public key can be openly distributed without compromising security.

  In a **public-key encryption** system, anyone with a public key can [encrypt](https://en.wikipedia.org/wiki/Encryption "Encryption") a message, yielding a **ciphertext**, but only those who know the corresponding private key can decrypt the ciphertext to obtain the original message.

{{< /callout >}}

## What's inside a GPG key? {#inside-a-gpg-key}

A GPG key is not just a single key, as it consists of four parts:

![GPG key anatomy](https://rzetterberg.github.io/assets/yubikey-gpg-nixos/key-anatomy1.png)

This image, coming from "[Setting up GnuPG + Yubikey on NixOS for SSH authentication](https://rzetterberg.github.io/yubikey-gpg-nixos.html "Setting up GnuPG + Yubikey on NixOS for SSH authentication")", explains quite well what a GPG key contains, but let's get a bit more in-depth:

1. ### Primary key pair {#primary-key-pair}

   The primary key pair, also called "master key", is the root key used to create other key pairs, called [subkey pairs](#subkey-pairs).

2. ### Primary identity {#uid}

   The primary identity, also called "user identity" or "uid", contains information about the owner of the key (name and email).

   {{< callout "info" "+" >}}
   From what I know, identities are not verified as keys are generated offline. However, some key servers might perform additional checks to ensure a key is published by the entity described by the primary identity.
   {{< /callout >}}

3. ### Extra identities {#extra-ids}

   A GPG key can contain extra identities linked to the primary key pair. It can be used to define multiple email addresses for example.

   {{< callout "info" "+" >}}
   From what I know, identities are not verified as keys are generated offline. However, some key servers might perform additional checks to ensure a key is published by the entity described by the primary identity.
   {{< /callout >}}

4. ### Subkey pairs {#subkey-pairs}

   {{< callout "cite" "" "[Setting up GnuPG + Yubikey on NixOS for SSH authentication](https://rzetterberg.github.io/yubikey-gpg-nixos.html \"Setting up GnuPG + Yubikey on NixOS for SSH authentication\")" >}}
   Subkey pairs are basically normal key pairs that have a signature that associates them to the primary key pair. When a subkey pair is created, the primary private key is used to create this signature. The reason why you would want to create subkey pairs is because in GPG you can specify what actions a key pair is allowed to perform.
   {{< /callout >}}

## Some GPG concepts

### Capabilities {#capabilities}

There are 4 different actions a key pair can perform: **Certify**, **Sign**, **Encrypt** and **Authenticate**:

- #### Certify (`C`) {#capability-certify}

  {{< callout "cite" "" "[Setting up GnuPG + Yubikey on NixOS for SSH authentication](https://rzetterberg.github.io/yubikey-gpg-nixos.html \"Setting up GnuPG + Yubikey on NixOS for SSH authentication\")" >}}
  The most powerful action is called **Certify** (`C`), which allows a key pair to create subkey pairs from itself. A subkey pair is just a key pair but with a signature that associates it to another key pair. The primary key pair will therefore always have the **Certify** capability, so that it can create subkey pairs.
  {{< /callout >}}

- #### Sign (`S`) {#capability-sign}

  Signing files, emails, etc.

- #### Encrypt (`E`) {#capability-encrypt}

  Encrypting files, emails, etc.

- #### Authenticate (`A`) {#capability-authenticate}

  Authenticating against external services, such as SSH

### GPG key ID {#key-id}

TODO: <https://unix.stackexchange.com/a/613909>

### Expiry

TODO

### Refresh

TODO
