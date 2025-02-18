---
title: YubiKey Setup
_summary: TODO
date: 2023-12-10
draft: false
---

In this series of articles, I detail how I managed to configure my [YubiKey] security key to handle signing, encryption and authentication on my machine, removing the need for any other [cryptographic key] like SSH keys. I configured my git client to use GPG for signing and my SSH agent to use it for authentication. All of this can happen if I have my physical key inserted into my computer, otherwise it is impossible. I even added extra security by requiring a "card tap" (touching my security key) when signing and having to accept a [PINEntry] dialog when initializing SSH connections.

[YubiKey]: https://www.yubico.com/products/yubikey/ "“YubiKeys | Two-Factor Authentication for Secure Login” on yubico.com"
[cryptographic key]: https://www.cloudflare.com/learning/ssl/what-is-a-cryptographic-key/ "What is a cryptographic key? | Keys and SSL encryption | Cloudflare"
[PINEntry]: https://github.com/gpg/pinentry "gpg/pinentry: The standard pinentry collection."
