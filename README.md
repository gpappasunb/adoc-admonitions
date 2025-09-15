## Introduction

:moon: Pandoc lua filter that processes asciidoc-style admonitions and encloses them in a DIV with
a specified class. Its purpose is to simplify the writing of simple admonitions (callouts) in markdown/quarto.

The filter scans the document for a specially formatted token, and if a match is found in the
configured terms, it changes the paragraph to a Div (block) containing a corresponding class, also
configurable.

### Syntax

The admonitions are words appearing in the **first column** of a paragraph followed by a
colon (:) and a space. For example:

    NOTE: this is a note

These are invalid:

    NOTE:this  (no space after the colon)

       NOTE: this (not in the first column)

**IMPORTANT**: This markup works with a single paragraph. Put all the content in a single line

### Default admonitions

The default token names and classes are coded in the ADMONITIONS table.

    NOTE = "admonitionnote",
    TIP = "admonitiontip",
    ERROR = "admonitionerror",
    CODE = "admonitionterm",
    TERMINAL = "admonitionterm",
    QUOTE = "admonitionquote",
    WARNING = "admonitionwarn",
    WARN = "admonitionwarn",

So, it is possible to use:

    WARN: this is a warning

### What is produces

After scanning the document and finding the correctly coded token, the extension produces

- Original

      NOTE: this is a note

- transformed

      ::: admonitionnote
      this is a note
      :::

### Including a title

There is a special markup that permits to include a title to the div. This is OPTIONAL, and the
the syntax is:

    TOKEN|title comes here|: text comes here

- TOKEN is one of the keys above
- |title| the default marker to indicate a title is "|" and it should be provided right after
  the TOKEN, without spaces!
- ":" ending of the token pattern.

  NOTE|note title|: note text

  is changed to:

  ::: {.admonitionnote title="note title"}
  note text
  :::

Without the title specification:

    NOTE: note text

    is changed to:

    ::: {.admonitionnote}
    note text
    :::

## Configuration

### Altering/adding Token classes

It is possible to alter the class names associated with the tokens by modifying the
document metadata using the name of the filter (adoc-admonition)

    ---
    adoc-admonition:
        NOTE: mynoteclass
        TIP: mytipclass

It is also possible to include new tokens:

    ---
    adoc-admonition:
        xxx: admonitionnote
        TIP: TIPADMONITION

In this case, the default ones are still valid.

With that in place, the line:

    xxx: xxx enclosed in a admonitionnote Div

is transformed to:

::: admonitionnote
xxx enclosed in a admonitionnote Div
:::

### Changing the matching patterns

It is also possible to change the token matching regular expression by using a special key as a child of the
metadata entry for the extension. The default configuration is:

    ---
    adoc-admonition:
    config:
      left: ""
      right: ":"
      title: "|"

You can change the values with:

    ---
    adoc-admonition:
        TIP: TIPADMONITION
    config:
      left: ">"
      right: "<"
      title: "*"

In the case above, the new token syntax should be:

    >IMPORTANT*title*< text of div

## Usage in pandoc

    pandoc -s test.md  -t beamer --lua-filter adoc-admonitions.lua

## Author

    Author: Georgios Pappas Jr
    Institution: University of Brasilia (UnB) - Brazil
    Version: 0.1
