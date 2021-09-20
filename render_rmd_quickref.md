Rendering R Markdown
================
Chris Johnson

-   [Introduction](#introduction)
-   [`rmarkdown::render()`](#rmarkdownrender)
-   [The YAML argument `output`](#the-yaml-argument-output)
-   [`_output.yml`](#_outputyml)
-   [Custom format objects](#custom-format-objects)
-   [Everything else](#everything-else)
-   [References](#references)

## Introduction

R Markdown documents contain metadata which control how the document is
rendered to its final format(s). The metadata is stored in the document,
sandwiched between lines of `---` using the YAML format. (Historically,
this has been referred to as the *YAML header*.)

The entirety of the metadata can be parameterized, but via two
mechanisms: the output format vehicles (described in depth below) and
`params` (everything else).

Finally, the R Markdown document can be knitted using R Studio or with
`rmarkdown::render()`. This document is primarily concerned with the
latter as the former is relatively easy. Henceforth, `render()` should
be understood to be from `{rmarkdown}`, i.e. `rmarkdown::render()`.

## `rmarkdown::render()`

The following formal arguments of `render()` are straightforward:

-   `input` is the `Rmd` file to be rendered (what to process)
-   `output_file` is the name to give to the rendered file (what to name
    the output)
-   `output_dir` is where the rendered file should be written (where to
    write the output)

There are three output format vehicles for specifying *how* the `Rmd` is
rendered:

-   via the YAML argument `output` in the YAML header
-   a file named `_output.yml`
-   via a *custom format object*

## The YAML argument `output`

Suppose we have an R Markdown document named `my_document.Rmd` that we
wish to render to HTML document with Bootstrap CSS with a table of
contents (TOC). The metadata for such a document would look like this:

    title: "My awesome analysis"
    output:
      html_document:
        toc: true
        toc_float: false

If we decide we want the TOC to float, we have two options. The first is
to change `toc_float: false` to `toc_float: true` in the metadata. The
second is to override this argument with `output_options`:

    render(
      input = "my_document.Rmd",
      output_options = list(toc_float = TRUE)
    )

## `_output.yml`

If using `_output.yml` to specify output options, this can be passed to
`render()` via `output_yaml = "_output.yml"`.

Let `_output.yml` or `_output.yaml` include

    rmarkdown::html_document: 
      toc: true
      toc_float: true
      

The call to `rmarkdown::render()` would be

    render(
      input = "my_document.Rmd", 
      output_yaml = "_output.yml",
    )

## Custom format objects

`{rmarkdown}` provides functions to customize common formats. The result
of any one of these functions is a *custom format object*. `{rmarkdown}`
provides [many](https://rmarkdown.rstudio.com/lesson-9.html), but two
popular ones are `html_document()` and `github_document()`. Both of
these return a format object which can be passed to `output_format` in
`render()`. The appropriate formal arguments for each can be explored
via help (e.g. `?html_document`).

Here’s a bare-bones example of how to render a hypothetical R Markdown
file `my_document.Rmd` to an HTML document with Bootstrap CSS that has a
floating table of contents (TOC):

    render(
      input = "my_document.Rmd",
      output_format =
        html_document(
          toc = TRUE,
          toc_float = TRUE
        )
    )

## Everything else

The rest of the metadata are configure *everything else*. For example,
other YAML arguments in the metadata are `title`, `author`, `date`, etc.
To specify these in a more flexible manner is a two-step approach:

1.  parameterize the YAML arguments
2.  pass the arguments to the `params` formal argument of `render()`

Let `my_document.Rmd` include the following YAML header (metadata):

    title: "My awesome analysis"
    author: "Human Person"

To avoid having to change `author: "Human Person"` to
`author: "Chris Johnson"` directly in the YAML header, we could instead
replace the string `"Human Person"` with BLAH. There are two ways to
send data to this parameter.

The first is to add `params` as a YAML argument to the metadata:

    title: "My awesome analysis"
    author: "`r params$author`"
    params:
      author: "Chris Johnson"

But if we’re using `render()`, we could instead leave the metadata
as-is…

    title: "My awesome analysis"
    author: "`r params$author`"

and instead pass the data to `params` argument of `render()`:

    render(
      input = "my_document.Rmd",
      params = list(author = "Chris Johnson")
    )

## References

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-metadata" class="csl-entry">

Yihui Xie, Emily Riederer, Christopher Dervieux. n.d.
<https://bookdown.org/yihui/rmarkdown-cookbook/>.

</div>

</div>
