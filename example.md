---
title: Example
section: 2
layout: default
summary:
    "Look carefully at the source files for the ccsc-example site."
---

# Example

Let's look at the source files from which the ccsc-example
site is generated.

## index.md

1.  This is the file from which `index.html` is generated.  "md" stands for
    [Markdown](https://daringfireball.net/projects/markdown/), a simple
    plaintext format designed to be easy to write and easy to read (much
    easier to write and read than HTML source files, for example).  The
    original Markdown software tool generates HTML from Markdown source
    files; several alternative tools exist for generating HTML, PDF, or
    other output formats.  Among them is
    [kramdown](http://kramdown.gettalong.org/), which is the default
    Markdown tool for [Jekyll](https://jekyllrb.com/), the static website
    generator used by GitHub Pages.

    So let's take a look at basic Markdown syntax, as illustrated by
    index.md:

    ```markdown
    # CCSC Example

    A (very short) paragraph,
    followed by a list:

    -   An item
    -   An italicized *item*

    -   A third
        item.
    ```

    A line beginning with `#` is a title, a line beginning with `-` is a
    list item, etc.  The strange formatting at the end of the list is just
    to show that you can leave a blank line between list items, and you can
    break a line without that indicating a new list item.  For more about
    Markdown, specifically kramdown, go to the reference page for
    [kramdown's version of Markdown syntax](http://kramdown.gettalong.org/syntax.html).

2.  `index.md` continues with a second level title (preceded by `##`) and a code block.
    Unlike the original Markdown tool, kramdown supports syntax highlighting
    for source code blocks in a variety of languages (via
    [Rouge](http://rouge.jneen.net/)).

    ~~~markdown
    ## Subtitle, Source Code

    ```java
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
    ```
    ~~~

    As you can see, the beginning and end of a code block can be marked by
    three consecutive backticks.  The language is given right after the first
    set of backticks.  In the HTML generated by kramdown, spacing will be
    preserved and we'll get a monospace font.  But we won't actually get
    colors---here's what the generated HTML would look like:

    ```html
    <div class="language-java highlighter-rouge"><pre class="highlight"><code><span class="kd">public</span> <span class="kd">static</span> <span class="kt">void</span> <span class="nf">main</span><span class="o">(</span><span class="n">String</span><span class="o">[]</span> <span class="n">args</span><span class="o">)</span> <span class="o">{</span>...
    ```

    What you get is code divided into span elements with CSS classes assigned
    in a way that makes highlighting possible.  But how do you get the CSS
    file that actually specifices the colors?  You can use Rouge to generate
    it for you:

    <pre>
    <span class="muted">user:~/workspace $</span> rougify help style
    <span class="muted">usage: rougify style [&lt;theme-name&gt;] [&lt;options&gt;]

    Print CSS styles for the given theme.  Extra options are
    passed to the theme.  Theme defaults to thankful_eyes.

    options:
      --scope       (default: .highlight) a css selector to scope by

    available themes:
      base16, base16.dark, base16.monokai, base16.monokai.light, base16.solarized, base16.solarized.dark, colorful, github, gruvbox, gruvbox.light, molokai, monokai, monokai.sublime, thankful_eyes
    user:~/workspace $</span> rougify style gruvbox.light &gt; gruvbox.css
    </pre>

3.  `index.md` includes one more section, meant to show that you can
    include mathematical notation, rendered (nicely) by
    [MathJax](https://www.mathjax.org/):

    ```
    ## Another Subtitle, and Some Math

    \\( \cos (\phi + \theta) = \cos \phi \cos \theta - \sin \phi \sin \theta \\)
    ```

    Aside from the one extra slash at the beginning (`\\(` instead of
    `\(`) and the one extra at the end, this is Latex input syntax.
    [MathJax basic tutorial and quick reference](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference)
    is a great resource for help with Latex math input (and it's
    specifically focused on MathJax).

## _layouts/default.html

1.  In the section above, I skipped the beginning of `index.md`:

    ```markdown
    ---
    layout: default
    ---
    ```

    What these lines mean, if you put them at the beginning of a Markdown
    file that's part of your site, is that a file called `default.html`,
    in the special folder `_layouts`, should be used as a template when
    HTML is generated from the Markdown file.

2.  One more thing before we
    look at `_layouts/default.html`---the special file `_config.yml`, which contains the
    following line:

    ```yaml
    baseurl: /ccsc-example
    ```

    With `baseurl` set in the special file `_config.yml`, we'll be able
    to use `site.baseurl` in other files in place of the base URL for
    the site...so that we only have to set that in one place, `_config.yml`.

3.  Here's what's in
    `_layouts/default.html`:

    {% raw %}
    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <meta charset="UTF-8">
        <link rel="stylesheet" href="{{ site.baseurl }}/css/gruvbox.css" />
        <script src='https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS_CHTML'></script>
      </head>
      <body>
        {{ content }}
      </body>
    </html>
    ```

    Jekyll uses the [Liquid](https://shopify.github.io/liquid/)
    templating engine---where you see `{{ site.baseurl }}`, that will
    be replaced by the
    value from `_config.yml`, `/ccsc-example`; where you see `{{ content }}`,
    that will be replaced by the content of the Markdown file, converted
    to HTML by kramdown.  Everything else from `_layouts/default.html`
    will be copied directly to the generated HTML file.
    {% endraw %}

    So we'll get a link to the CSS file needed to bring color to our
    code blocks (`css/gruvbox.css`).  We'll also get a link to JavaScript
    code, provided by MathJax, that runs on the HTML file when it's
    loaded in the browser, converting Latex math input to nicely
    rendered mathematical notation.

## What about the other files (and folders)?

1.  `_site` is where the full generated site goes.  You shouldn't make
    any changes there, since they would be overwritten the next time
    Jekyll runs on your source files.

2.  `Gemfile`, as you might remember from the earlier "Setup" section,
    is used to direct Bundler to the Ruby packages needed for the
    project; `Gemfile.lock` is created when Bundler runs on `Gemfile`;
    It stores information about which versions of the Ruby packages
    should be used.

    *Note: `Gemfile.lock` is intentionally left out of the Github
    repository.  It will be generated locally when Bundler runs, and
    if Bundler is run on more than one operating system (for example)
    for this project, it's possible that different versions of the
    Ruby packages will be used.  So it wouldn't make sense to try to keep
    one consistent `Gemfile.lock` file as part of the project on
    GitHub.*

3.  `README.md` isn't part of the site; it's just there because it's
    normal to include a readme file in every GitHub repository.  If you
    look up the repository on GitHub, you see the contents of the
    readme file below the list of source files included in the project.
