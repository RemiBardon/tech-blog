---
title: I just added Obsidian-style Callouts to my blog 👨🏻‍🎨
summary: A quick article to show you the different callouts I added to my blog and how they look like.
date: 2023-12-13
lastmod: 2023-12-29
draft: false
---

I just added [Obsidian-style Callouts](https://help.obsidian.md/Editing+and+formatting/Callouts) to my blog theme!
It will make some articles look a lot better 😍
[Hugo](https://gohugo.io/) is missing a `blockquote` [render hook](https://gohugo.io/templates/render-hooks/) so I had to write a custom [shortcode](https://gohugo.io/templates/shortcode-templates/).
You can see it and how I use it in my Gist "[Obsidian-style callouts in Hugo](https://gist.github.com/RemiBardon/0156d028e83137011ac38b22d3fbef26)".

---

Here is what it looks like (taking examples from [Callouts - Obsidian Help](https://help.obsidian.md/Editing+and+formatting/Callouts)):

{{< callout "info" >}}

  Here's a callout block.
  It supports **Markdown**, [Wikilinks](https://help.obsidian.md/Linking+notes+and+files/Internal+links), and [embeds](https://help.obsidian.md/Linking+notes+and+files/Embedding+files)!

  ![Engelbart.jpg](https://publish-01.obsidian.md/access/f786db9fac45774fa4f0d8112e232d67/Attachments/Engelbart.jpg)

{{< /callout >}}

{{< callout type="tip" title="Callouts can have custom titles" >}}

  Like this one

{{< /callout >}}

{{< callout type="tip" title="Title-only callout" />}}

{{< callout type="faq" fold="-" title="Are callouts foldable?" >}}

  Yes! In a foldable callout, the contents are hidden when the callout is collapsed

{{< /callout >}}

{{< callout type="question" title="Can callouts be nested?" >}}

  {{< callout type="todo" title="Yes!, they can." >}}

    {{< callout type="example" title="You can even use multiple layers of nesting." />}}

  {{< /callout >}}

{{< /callout >}}

{{< callout type="done" title="Callouts with **markdown**" >}}

  _Before_
  {{< callout type="todo" title="Yes!, they can." >}}

    **Before**
    {{< callout type="example" title="You can even use multiple layers of nesting." >}}

      **_Inside_**
    {{< /callout >}}

    **After**
  {{< /callout >}}

  _After_

{{< /callout >}}

---

{{< callout "note" "+" >}}

  A `note` callout

{{< /callout >}}

---

{{< callout "abstract" "+" >}}

  A `abstract` callout

{{< /callout >}}

{{< callout "summary" "+" >}}

  A `summary` callout

{{< /callout >}}

{{< callout "tldr" "+" >}}

  A `tldr` callout

{{< /callout >}}

---

{{< callout "info" "+" >}}

  A `info` callout

{{< /callout >}}

---

{{< callout "todo" "+" >}}

  A `todo` callout

{{< /callout >}}

---

{{< callout "tip" "+" >}}

  A `tip` callout

{{< /callout >}}

{{< callout "hint" "+" >}}

  A `hint` callout

{{< /callout >}}

{{< callout "important" "+" >}}

  A `important` callout

{{< /callout >}}

---

{{< callout "success" "+" >}}

  A `success` callout

{{< /callout >}}

{{< callout "check" "+" >}}

  A `check` callout

{{< /callout >}}

{{< callout "done" "+" >}}

  A `done` callout

{{< /callout >}}

---

{{< callout "question" "+" >}}

  A `question` callout

{{< /callout >}}

{{< callout "help" "+" >}}

  A `help` callout

{{< /callout >}}

{{< callout "faq" "+" >}}

  A `faq` callout

{{< /callout >}}

---

{{< callout "warning" "+" >}}

  A `warning` callout

{{< /callout >}}

{{< callout "caution" "+" >}}

  A `caution` callout

{{< /callout >}}

{{< callout "attention" "+" >}}

  A `attention` callout

{{< /callout >}}

---

{{< callout "failure" "+" >}}

  A `failure` callout

{{< /callout >}}

{{< callout "fail" "+" >}}

  A `fail` callout

{{< /callout >}}

{{< callout "missing" "+" >}}

  A `missing` callout

{{< /callout >}}

---

{{< callout "danger" "+" >}}

  A `danger` callout

{{< /callout >}}

{{< callout "error" "+" >}}

  A `error` callout

{{< /callout >}}

---

{{< callout "bug" "+" >}}

  A `bug` callout

{{< /callout >}}

---

{{< callout "example" "+" >}}

  A `example` callout

{{< /callout >}}

---

{{< callout "quote" "+" >}}

  A `quote` callout

{{< /callout >}}

{{< callout "cite" "+" >}}

  A `cite` callout

{{< /callout >}}
