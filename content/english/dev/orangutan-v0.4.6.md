---
title: Adding admin pages to my blog ⚙️
summary: My blog server now exposes a route for generating access links, which will save me a lot of time. I also added ephemeral error and access logs to help me diagnose unexpected behaviors.
date: 2024-06-09
draft: true
tags:
  - Orangutan
  - Rust
  - Security
  - Access logs
---

{{< callout "info" "+" "Context: What Orangutan is" >}}
As you might already know, my blog isn't entirely public. Some pages, paragraphs and phrases can only be read by a few people whom I gave access to. To support such feature on a static site, I added a custom layer of authentication and authorization which I called [Orangutan]. This article is a summary of a lot of improvements I made to this project.

[Orangutan]: https://github.com/remibardon/orangutan "GitHub - RemiBardon/Orangutan: Lightweight authorization layer for static sites"
{{< /callout >}}

## First things first: let’s remove clutter {#spring-cleaning}

Before implementing new features, I decided to give my blog a thorough spring cleaning.

### Centralize dependencies management and delete redundant lock files {#workspace-dependencies}

[Orangutan] contains multiple sub-projects like a [CLI] for generating access tokens. Before [v0.4.1], each of them specified versions for every dependencies they used. While I was meticulous updating dependencies everywhere at the same time, this resulted in **each [crate] having their own `Cargo.lock` file**. At some point I started using [Cargo workspaces] but I didn’t clean dedicated lock files so in addition to adding **thousands of lines in the [repository]**, those **lock files became outdated** and would trigger erroneous [Dependabot] alerts. **By using [Cargo workspace’s dependencies table], I centralized dependency management and fixed all of those issues.**

### Remove error boilerplate {#thiserror}

In Rust, errors need to implement `std::fmt::Display` and `std::error::Error`, which can add a lot of code with no business value. For example, this is what it would look like:

```rust
#[derive(Debug)]
pub enum Error {
    CannotExecuteCommand(String, io::Error),
    CommandExecutionFailed { command: String, code: Option<i32> },
    CannotGenerateWebsite(Box<Error>),
    CannotEmptyIndexJson(io::Error),
    CannotCreateHugoConfigFile(io::Error),
    IOError(io::Error),
}

impl std::fmt::Display for Error {
    fn fmt(
        &self,
        f: &mut std::fmt::Formatter<'_>,
    ) -> std::fmt::Result {
        match self {
            Error::CannotExecuteCommand(command, err) => {
                write!(f, "Could not execute command `{command}`: {err}")
            },
         Error::CommandExecutionFailed { command, code } => {
                write!(f, "Command `{command}` failed with exit code {:?}", code)
            },
            Error::CannotGenerateWebsite(err) => write!(f, "Could not generate website: {err}"),
            Error::CannotEmptyIndexJson(err) => {
                write!(f, "Could not empty <index.json> file: {err}")
            },
            Error::CannotCreateHugoConfigFile(err) => {
                write!(f, "Could create hugo config file: {err}")
            },
            Error::IOError(err) => write!(f, "IO error: {err}"),
        }
    }
}

impl std::error::Error for Error {}
```

By using [`thiserror`], we can reduce it to:

```rust
#[derive(Debug, thiserror::Error)]
pub enum Error {
    #[error("Could not execute command `{0}`: {1}")]
    CannotExecuteCommand(String, io::Error),
    #[error("Command `{command}` failed with exit code {code:?}")]
    CommandExecutionFailed { command: String, code: Option<i32> },
    #[error("Could not generate website: {0}")]
    CannotGenerateWebsite(Box<Error>),
    #[error("Could not empty <index.json> file: {0}")]
    CannotEmptyIndexJson(io::Error),
    #[error("Could create hugo config file: {0}")]
    CannotCreateHugoConfigFile(io::Error),
    #[error("IO error: {0}")]
    IOError(#[from] io::Error),
}
```

Even better, I hadn’t taken the time to implement conversions like `impl From<io::Error> for Error` (which would take 5 lines) but now that it is as easy as adding `#[from]`, I could remove most [`map_err`]s, essentially replacing things like `.map_err(Error::IOError)?` to just `?`. This makes the code much clearer and thus nicer to read.

To see how a full example of refactoring to [`thiserror`], see my commit [`8b1eeb7` “🧹 Remove boilerplate thanks to `thiserror`”][`8b1eeb7`].

## Recover old files if the new website doesn't generate properly {#recover-old-files-if-deploy-fails}

In “[Improving my website so changes deploy in 5 seconds ⚡️]({{< ref "dev/orangutan-v0.3" >}})”, I introduced the fact that my blog is not just a static site anymore, it’s an always-running server which rebuilds the static pages when the source files are updated. However, as I mentioned in “[A few issues I still have to fix]({{< ref "orangutan-v0.3" >}}#issues)”, on updates the server would delete all files then regenerate it. Unfortunately, if generation failed (e.g. because of a badly formatted [front matter] or a [broken link]), all file would be gone and the server would not serve anything (returning [404] to all requests).



TODO: [Recover old files if new website doesn't generate properly](https://github.com/RemiBardon/Orangutan/commit/fb2c9c74a324f598d87b835892d4d0471a27561a)

## Adding admin routes {#admin-routes}

### Ephemeral error logs {#error-logs}

My blog is hosted on [Clever Cloud]. I can access the server logs via their console, but it’s a bit cumbersome to access

https://github.com/RemiBardon/Orangutan/commit/f087705205ff9486cef81f4db233380160e0091b

### Ephemeral access logs {#access-logs}

### A way to generate access tokens from the website {#token-generator}

TODO: Evolution idea

### Making admin features optional {#feature-flags}

TODO: Feature flags

[broken link]: https://gohugo.io/content-management/shortcodes/#ref-and-relref "Shortcodes | Hugo"
[Cargo workspaces]: https://doc.rust-lang.org/cargo/reference/workspaces.html
[Cargo workspace’s dependencies table]: https://doc.rust-lang.org/cargo/reference/workspaces.html#the-dependencies-table
[CLI]: https://en.wikipedia.org/wiki/Command-line_interface "Command-line interface | Wikipedia"
[Clever Cloud]: https://clever-cloud.com "Clever Cloud homepage"
[crate]: https://doc.rust-lang.org/book/ch07-01-packages-and-crates.html "Packages and Crates - The Rust Programming Language"
[repository]: https://en.wikipedia.org/wiki/Software_repository "Software repository | Wikipedia"
[`map_err`]: https://doc.rust-lang.org/std/result/enum.Result.html#method.map_err
[v0.4.1]: https://github.com/RemiBardon/Orangutan/releases/tag/v0.4.1
[Dependabot]: https://docs.github.com/en/code-security/dependabot/dependabot-alerts/about-dependabot-alerts "About Dependabot alerts | GitHub Docs"
[`thiserror`]: https://crates.io/crates/thiserror "thiserror - crates.io: Rust Package Registry"
[`8b1eeb7`]: https://github.com/RemiBardon/Orangutan/commit/8b1eeb79036aaeb4de1684cf77d66970b585ca7e
[front matter]: https://gohugo.io/content-management/front-matter/ "Front matter | Hugo"
[404]: https://httpstatuses.io/404 "404 Not Found — httpstatuses.io"
[Orangutan]: https://github.com/remibardon/orangutan "GitHub - RemiBardon/Orangutan: Lightweight authorization layer for static sites"
