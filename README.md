# typst-custom-outline

A table of contents drawn as a tree, with box-drawing characters instead of indentation:

```text
┌┬┐
││└ Avant-propos .............................
│└ Introduction .............................. 1
│  └ Contexte ................................ 1
└ Développement .............................. 2
```

Every line is a link to its heading, the page numbers are the ones the pages actually print, and a title can be shortened for the outline without touching the heading itself.

> [!IMPORTANT]
> This is not a standalone template. It is meant to be **paired with [clean-cnam-template](https://github.com/TomPlanche/clean-cnam-template) > v2**, whose `outline.custom` and `front-matter.pages` decide what this outline replaces and which page it lands on. It does work in a bare Typst document, but the integration below is what it was written for. `front-matter.pages`, `outlined: false` and the page numbering anchors need clean-cnam-template 2.0.0 or later.

## Usage

`outline.typ` is a single file, not a published package: copy it next to your document.

```typst
#import "@preview/clean-cnam-template:2.0.0": *
#import "outline.typ": customTitle, tree-outline

#show: clean-cnam-template.with(config: (
  info: (title: "Mémoire d'ingénieur", author: "Tom Planche", class: "EiCnam"),
  page: (numbering-start: 1),

  // this tree replaces the default table of contents
  outline: (custom: tree-outline(color: "#0B607E", max-depth: 3)),

  // ... and `front-matter.pages` says on which page it goes
  front-matter: (pages: (
    "blank",
    "cover-text",
    (title: "Avant-propos", body: [ ... ]),
    (title: "Note interne", body: [ ... ], outlined: false),
    "outline",
    "figures",
    "tables",
  )),
))

= Introduction
```

### Shorter titles in the outline

Place `customTitle` immediately before a heading to give it a different, usually shorter, label in the tree. The heading itself is untouched.

```typst
#customTitle("Analyse")
== Analyse détaillée des résultats de la campagne de mesures
```

The marker only applies to the heading that directly follows it.

## Parameters

| Parameter          | Default         | Effect                                                                                                   |
| ------------------ | --------------- | -------------------------------------------------------------------------------------------------------- |
| `symbol-font`      | `"Courier New"` | Font for the box-drawing characters. Needs a monospaced font that actually has them.                     |
| `text-font`        | `"Courier New"` | Font for the entry titles                                                                                |
| `number-font`      | `"Courier New"` | Font for the page numbers                                                                                |
| `text-size`        | `1em`           | Size of the whole tree                                                                                   |
| `line-spacing`     | `0.4em`         | Leading. Tuned so the `│` characters join from one line to the next; raise it and the tree breaks apart. |
| `max-depth`        | `none`          | Deepest heading level shown (`none` = every level)                                                       |
| `use-dots`         | `true`          | Dot leaders between title and page number; `false` leaves blank space                                    |
| `color`            | `black`         | Color of the whole tree. Accepts a color, a hex string (`"#0B607E"`) or a color name (`"blue"`).         |
| `exclude-children` | `()`            | Titles (as displayed) whose children are hidden, the entry itself staying visible                        |

## What it takes from the document

The tree is built from `query(heading)`, so it follows whatever the document declares, including three things clean-cnam-template relies on:

- **`outlined: false` is honored.** A front-matter entry that opts out -- `(title: "Note interne", body: [ ... ], outlined: false)` -- is absent from the tree, as are the titles Typst renders for `outline(title: ..)`, which is what the template's `"figures"` and `"tables"` pages use. Only headings that asked to be listed appear.
- **Page numbers are the printed ones.** Not the position of the page in the file: with `page: (numbering-start: 1)` the body restarts at 1 and the tree says 1, not 9. `page.numbering-from` shifts both the same way.
- **An unnumbered page contributes no number.** The cover and the front matter print no page number by default, so their entries show leaders and nothing else -- the same rule the template applies to the built-in outline. In a document that sets no page numbering at all, the tree falls back to the position of the page, as Typst's own outline does.

## Known behavior

- Page numbers are rendered as arabic digits, right-aligned by space padding. A document numbering its pages in roman numerals prints `iv` at the bottom of the page and `4` here.
- `exclude-children` matches the title _as displayed_, so it matches the `customTitle` label when there is one.
- The tree is content, not an `outline` element: the template's `colors.outline` does not reach it. Use the `color` parameter.
- Tested with Typst 0.15.

aled
