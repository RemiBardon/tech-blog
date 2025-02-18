---
title: How to deploy Prose on a Raspberry Pi
summary: TODO
date: 2024-05-28
lastmod: 2024-08-03
tags:
  - Prose
draft: true
---

I’ve been working for [Prose](https://prose.org) for a few months now, and things are getting more and more real. As I explained in TODO, I’m currently working on the administration dashboard which allows one to configure their Prose Pod.

{{< callout type="tip" title="Tip for the nerds" >}}
This article starts with a lot of context and explanations, if you’re here for the nitty-gritty stuff, you can jump to TODO.
{{< /callout >}}

## Why would one want to host a Prose Pod themselves? {#why-self-hosting}

I strongly advise you to read TODO if you don’t already know the answer to this question. Anyway, here is a summary of what you need to understand:

- Messages exchanged via Instant Messaging apps need to be stored somewhere[^1] so users can see the conversation history.
- Most often, this data is stored on servers in big data centers.
- In most cases, those servers are owned and operated by private companies (e.g. Amazon Web Services, Google Cloud Platform…).
- Both individuals and companies often use Instant Messaging apps owned by private companies (e.g. Meta for WhatsApp, Facebook Messenger and Instagram; Microsoft for Microsoft Teams; Salesforce for Slack…) too.
- In all of the examples cited above, the services are hosted on US soil and messages are stored there too, making it subject to US legislation.
- Unless the conversations are end-to-end encrypted (which is almost never the case for internal enterprise communications[^2]), those conversations can be read by both the service provider and the infrastructure operator[^intelligence-services].
  - I will explain this in a separate article one day™, but keep in mind that end-to-end encryption does not stop companies from spying on you, inferring the contents of your conversations and making money on you via targeted advertising[^e2e-spying].
- If a company or an individual hosts their own Instant Messaging server and the Instant Messaging client (the app) doesn’t spy on its users, then Internet Service Providers become the only ones who can still exploit the communications[^not-just-isps].
- Prose is designed to be self-hosted, meaning most companies using Prose will likely host their own Prose Pod “on premise”.

{{< callout "info" "+" >}}
“On premise” hosting (informally “self-hosting”) means the client company operates the hardware on which the software is executed.

{{< callout "example" "+" >}}
We develop Prose but companies self-hosting it will grab a free copy of the software and use it without us ever having a hand on it.
{{< /callout >}}
{{< /callout >}}

So to answer the initial question, while Prose will never spy on users, someone would want to self-host a Prose Pod if they want full control over their data and ensure secrecy.

## What it means to host a Prose Pod {#what-hosting-a-prose-pod-means}

A Prose Pod is made of three main parts: the Prose Pod Server —the Instant Messaging server—, the Prose Pod Dashboard —a web app allowing one to configure their Prose Pod Server— and the Prose Pod API —what allows the dashboard to communicate with the server.

Hosting a Prose Pod means running those three programs, connecting them together and exposing the parts which need to be accessible to the outside world (meaning *the internet* for us developers).

## Action plan {#action-plan}

### Install the OS {#install-os}

1. [Intall Raspberry Pi Imager](https://www.raspberrypi.com/software/)
2. Connect the RPi's SD card to your computer
   1. Backup the SD card's contents if needed, as everything will be erased
3. Open RPi Imaer
   1. "Choose device" → "Raspberry Pi 3" in my case
   2. "Choose OS" → "Raspberry Pi OS (other)" → "Raspberry Pi OS (Legacy, 32-bit) Lite" (0.4 GB)
      - A port of Debian Bullseye with security updates and no desktop environment
      - 32 bits because the RPi 3 has a 32-bit architecture
      - We don't need a UI
   3. "Choose Storage" → The RPi's SD card
   4. "Use OS customization?" → "Edit settings"
      1. "General"
         - "Set hostname" → "prose-pod"(.local)
         - "Set username and password" → "prose", any password
         - "Configure wireless LAN" → No
           - We will use a wired connection
         - "Set locale settings" → Choose what fits you best
      2. "Services"
         - "Enable SSH" -> Yes
           - "Allow public-key authentication only"
             - Run `ssh-add -L` in your terminal then copy-paste the public key you will want to use
               - Me I'm using a SSH key derived from a GPG key stored on a [YubiKey](https://www.yubico.com/products/), see my article [“Using GPG keys for SSH”]({{< ref "tech/yubikey-setup/ssh-using-gpg" >}}) if you're curious about how it works
      3. "Options"
         - "Eject media when finished" -> Yes
         - "Enable telemetry" -> No
           - We don't need it
      4. "Save"
      5. "Yes"
      6. "All existing data will be erased" -> "Yes"
      7. Enter admin password if your session is not administrator
      8. ""Raspberry Pi Imager.app" would like to access files on a removable volume." -> "Allow"
      9. Wait 5–10 minutes
      10. "Write successful" -> "Continue"

### Start the Raspberry Pi

1. Plug the SD card into the RPi
2. Turn on the RPi by connecting it to a power source
3. After a few seconds, using a keyboard connected to the RPi, write your username ("prose"), hit <kbd>Enter</kbd>, then write your password and hit <kbd>Enter</kbd> again

The RPi should now be running and accepting SSH connections.

### Connect the Raspberry Pi to the internet

{{< callout "note" >}}
While we could keep the Raspberry Pi [air-gapped] to use a Prose Pod, it will greatly simplify the setup if it is connected to the public internet (e.g. to install Docker).
{{< /callout >}}

1. Connect an ethernet cable from your router to the RPi

This should be enough, as the RPi should automatically request an IP address when connected via a wired connection.

### SSH into the Raspberry Pi {#ssh-into-rpi}

1. Find the RPi's IP address:

   ```bash
   sudo nmap -sn 192.168.1.0/24;
   ```

   ```txt
   Starting Nmap 7.95 ( https://nmap.org ) at 2024-07-30 20:21 CEST
   [...]
   Nmap scan report for 192.168.1.126
   Host is up (0.015s latency).
   MAC Address: B8:27:EB:XX:XX:XX (Raspberry Pi Foundation)
   [...]
   Nmap done: 256 IP addresses (9 hosts up) scanned in 1.92 seconds
   ```

   Search for "Raspberry Pi Foundation", then the IPv4 address should be just above (here `192.168.1.126`). For convenience, you can save it in the `RPI_IP` environment variable:

   ```bash
   RPI_IP='<THE_IP_ADDRESS>';
   ```

2. ```bash
   ssh prose@"${RPI_IP:?}";
   ```

   (`prose` being the username you set in [Install the OS](#install-os)).

### Install Docker

See [Install Docker Engine on Debian > Install using the `apt` repository | Docker Docs](https://docs.docker.com/engine/install/debian/#install-using-the-repository).

### Run a Prose Pod {#run-prose-pod}

#### From the official releases {#run-prose-pod-from-releases}

{{< callout type="missing" title="Not supported yet" >}}
At the time of writing this article, Prose is still in its very early stages and we don't release Docker images to run a Prose Pod. I will update this section when we provide support for it.
{{< /callout >}}

<!-- TODO -->

#### From the source code {#run-prose-pod-from-source}

docker buildx create --use

```bash
# Install the Rust toolchain for the ARMv7 architecture
rustup target add armv7-unknown-linux-gnueabihf
# Install the cross-compilation toolchain
brew install arm-linux-gnueabihf-binutils
```

Then we need to tell Cargo which linker to use when cross-compiling for the ARMv7 architecture. Create or edit `.cargo/config.toml` to include the following:

```toml
[target.armv7-unknown-linux-gnueabihf]
linker = "arm-linux-gnueabihf-gcc"
```

1. Build the Docker images:
   - The Prose Pod Server:

     ```bash
     # On your machine
     docker buildx build --platform linux/arm/v7 -t proseim/prose-pod-server:latest "<PATH_TO_PROSE_POD_SERVER_REPO>";
     ```

   - The Prose Pod API:

     ```bash
     # On your machine
     docker buildx build --platform linux/arm/v7 -t proseim/prose-pod-api:latest "<PATH_TO_PROSE_POD_API_REPO>";
     ```

     {{< callout "tip" "+" "Build in debug mode" >}}
     To build the Prose Pod API in debug mode, you can pass `--build-arg CARGO_INSTALL_EXTRA_ARGS='--debug'` to the `docker buildx build` command.
     {{< /callout >}}

2. Save the Docker images to [tar files][tar]:

   ```bash
   # On your machine
   docker save -o prose-pod-server.tar proseim/prose-pod-server:latest;
   docker save -o prose-pod-api.tar proseim/prose-pod-api:latest;
   ```

3. Send the [tar files][tar] to the Raspberry Pi using [SFTP] (which should be enabled and running without further configuration, as authentication is done via [SSH]):

   ```bash
   # On your machine
   sftp prose@"${RPI_IP:?}";
   ```

   ```ftp
   put prose-pod-server.tar /var/tmp/
   put prose-pod-api.tar /var/tmp/
   bye
   ```

   {{< callout "info" "-" "Basic Commands in SFTP" >}}
   - List Files: `ls` or `dir`
   - Change Directory: `cd <remote_directory>`
   - Upload Files: `put <local_file> <remote_file>`
   - Download Files: `get <remote_file> <local_file>`
   - Quit SFTP: `exit` or `bye`

   [SFTP]: https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol "SSH File Transfer Protocol — Wikipedia"
   {{< /callout >}}

4. [SSH into the Raspberry Pi](#ssh-into-rpi)
5. Load the Docker images on the Raspberry Pi:

   ```bash
   # On the RPi
   docker load -i /var/tmp/prose-pod-server.tar
   docker load -i /var/tmp/prose-pod-api.tar
   ```

6. Rename the Docker images to get the prefixes back:

   ```bash
   # On the RPi
   docker tag prose-pod-server proseim/prose-pod-server:latest;
   docker tag prose-pod-api proseim/prose-pod-api:latest;
   ```

7. Copy the Prose Pod System repository:

   ```bash
   # On your machine
   sftp prose@"${RPI_IP:?}";
   put "<PATH_TO_PROSE_POD_SYSTEM>" ~/prose-pod-system;
   exit;
   ```

8. Write some configuration:
   - The Prose Pod configuration:

     ```bash
     # On the RPi
     pushd ~/prose-pod-system;
     cp Prose-template.toml Prose.toml;
     nano Prose.toml;
     ```

     Then replace all values between angle brackets (`<…>`).

   - The environment variables:

     ```bash
     # On the RPi, in ~/prose-pod-system
     nano .env;
     ```

     Then define the required variables:

     ```bash
     # TIP: Use <https://jwtsecret.com/> to generate a strong key.
     export JWT_SIGNING_KEY='<INSERT_A_STRONG_KEY>';
     # TIP: Use <https://passwordsgenerator.net/> to generate a strong password.
     # INFO: This password will be rotated for a very strong random password as soon as the Prose Pod API starts.
     #   Don't try to log in using this password, it will never work.
     export PROSE_BOOTSTRAP__PROSE_POD_API_XMPP_PASSWORD='<INSERT_A_STRONG_PASSWORD>';
     # INFO: Defining `RUST_LOG` is not required, but it's preferable that you set it to suit your needs.
     # TIP: See <https://docs.rs/env_logger/latest/env_logger/#enabling-logging> for information
     #   about the `RUST_LOG` environment variable's format.
     export RUST_LOG='debug,sqlx=warn,hyper=warn,hyper_util=warn,service=trace,prose_pod_api=trace';
     ```

8. Run the Prose Pod System:

   ```bash
   # On the RPi, in ~/prose-pod-system
   source .env;
   docker compose up --detach;
   ```

### Access the Prose Pod over the public internet

A Prose Pod comprises 4 parts: the Prose Pod Server, the Prose web application, the Prose Pod Dashboard and the Prose Pod API.
The Prose Pod Server is an XMPP server, which needs to expose port `5222` for client-to-server connections and port `5269` for server-to-server connections.
The Prose web application and Prose Pod Dashboard are web applications, which are accessed using port `80` (HTTP) or `443` (HTTPS).
Finally, the Prose Pod API is an internal RESTful HTTP API used by the Prose Pod Dashboard. It doesn't need to be accessible via the public internet, but it can be exposed via the ports `80` (HTTP) and `443` (HTTPS).

{{< callout "note" >}}
At the time of writing this article, the Prose Pod Dashboard hasn't been developed therefore we need to expose the  Prose Pod API publicly in order to configure the Prose Pod.
{{< /callout >}}

#### Install and launch nginx

[nginx](https://nginx.org/en/) is a [reverse proxy] server, which we will use to redirect traffic from outside the Raspberry Pi to the appropriate Prose Pod service.

```bash
sudo apt install nginx;
sudo systemctl start nginx;
# Automatically start nginx at boot
sudo systemctl enable nginx;
```

#### Forward router ports to the Raspberry Pi

Enable [port forwarding] on your router and forward ports `80`, `443`, `5222` and `5269` to the RPi.

To test that it works correctly, try opening your public IPv4 address in a web browser.

{{< callout "tip" "+" "Find your public IP address easily" >}}
To find your public IPv4 and IPv6 addresses, the easiest way is to head over to [whatismyipaddress.com](https://whatismyipaddress.com/).
{{< /callout >}}

If it works, you should see something like:

<iframe width="100%" height="256" srcdoc="<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href=&quot;http://nginx.org/&quot;>nginx.org</a>.<br/>
Commercial support is available at
<a href=&quot;http://nginx.com/&quot;>nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>"></iframe>

#### Add DNS entries to your domain

{{< callout "tip" "+" "Find your public IP address easily" >}}
To find your public IPv4 and IPv6 addresses, the easiest way is to head over to [whatismyipaddress.com](https://whatismyipaddress.com/).
{{< /callout >}}

```dns
; The Prose web application
prose 600 IN A <YOUR_RPI_PUBLIC_IPv4_ADDRESS>
prose 600 IN AAAA <YOUR_RPI_PUBLIC_IPv6_ADDRESS>
; The Prose Pod Server
@ 3600 IN SRV 0 5 5222 prose.<YOUR_DOMAIN>.
@ 3600 IN SRV 0 5 5269 prose.<YOUR_DOMAIN>.
; The Prose Pod Dashboard
admin.prose 600 IN A <YOUR_RPI_PUBLIC_IPv4_ADDRESS>
admin.prose 600 IN AAAA <YOUR_RPI_PUBLIC_IPv6_ADDRESS>
; The Prose Pod API
api.prose 600 IN A <YOUR_RPI_PUBLIC_IPv4_ADDRESS>
api.prose 600 IN AAAA <YOUR_RPI_PUBLIC_IPv6_ADDRESS>
```

#### Configure nginx

Now that the traffic is redirected to the RPi, we need to point it to the correct Prose Pod services. To do this, we need to tell [nginx] what to do with incoming requests and where to forward them.



TODO

### Update the Prose Pod {#update-prose-pod}

#### From the official releases {#update-prose-pod-from-releases}

{{< callout type="missing" title="Not supported yet" >}}
At the time of writing this article, Prose is still in its very early stages and we don't release Docker images to run a Prose Pod. I will update this section when we provide support for it.
{{< /callout >}}

<!-- TODO -->

#### From the source code {#update-prose-pod-from-source}

TODO



[^1]: Unless the conversation is ephemeral, but I consciously omitted this situation as it’s a rare usage in the general public.
[^2]: Because companies often want their employees to be able to see the history of messages sent before their recruitment too, and by design end-to-end encryption prevents that.
[^intelligence-services]: Not even mentioning laws allowing intelligence services to access data without ever noticing its owner.
[^e2e-spying]: In my dedicated article, I will also mention some privacy and security concerns.
[^not-just-isps]: I’m aware they’re not the only ones, but in this scenario networking represents the biggest threat.

[SSH]: https://en.wikipedia.org/wiki/Secure_Shell "Secure Shell — Wikipedia"
[SFTP]: https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol "SSH File Transfer Protocol — Wikipedia"
[air-gapped]: https://en.wikipedia.org/wiki/Air_gap_(networking) "Air gap (networking) — Wikipedia"
[tar]: https://en.wikipedia.org/wiki/Tar_(computing) "tar (computing) — Wikipedia"
[port forwarding]: https://en.wikipedia.org/wiki/Port_forwarding "Port forwarding — Wikipedia"
[reverse proxy]: https://en.wikipedia.org/wiki/Reverse_proxy "Reverse proxy — Wikipedia"
