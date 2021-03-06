---
title: "Node Markdown Parser"
description: "Transform Markdown to JSON object with metadata"
position: 1
category: "Getting started"
menuTitle: "Installation"
items:
  - Markdown to JSON structure
  - Markdown to HTML string
  - Rehype plugins
  - Remark plugins
  - Document metadata
---

<list :items="items"></list>

<alert type="info">

Only works in nodejs environment. Not browser.

</alert>

## Install

<code-group>
  <code-block label="Yarn" active>

```bash
yarn add @cenguidanos/node-markdown-parser
```

  </code-block>
  <code-block label="NPM">

```bash
npm install @cenguidanos/node-markdown-parser
```

  </code-block>
</code-group>

## Import

```ts
import { Markdown } from "@cenguidanos/node-markdown-parser";
```

### Create `MarkdownParserOptions`

```ts
import { Markdown, MarkdownParserOptions } from "@cenguidanos/node-markdown-parser";

let markdownOptions: MarkdownParserOptions;

let markdown: Markdown = new Markdown(markdownOptions);
```

### Create `Markdown` file

```md
---
title: With Love
subtitle: To World
---

# Simple markdown doc
```

### Parse to `JSON`

```ts
import { readFileSync } from  "fs";
import { Markdown, MarkdownParserOptions } from "@cenguidanos/node-markdown-parser";

let markdownOptions: MarkdownParserOptions;

let markdown: Markdown = new Markdown(markdownOptions);

let file: string = readFileSync("./markdown/example.md", "utf-8");

let data = markdown.toJSON(file);

console.log(data);
```

### Returns

```json
{
  "title": "With Love",
  "subtitle": "To World",
  "extension": ".md",
  "updatedAt": 1596071484169,
  "toc": [
    {
      "id": "simple-markdown-doc",
      "depth": 1,
      "text": "Simple markdown doc"
    }
  ],
  "body": "<h1 id=\"simple-markdown-doc\">Simple markdown doc</h1>"
}
```

<alert type="info">

remark-slug & rehype-raw are included in the lib

</alert>

## `Remark` plugins

### Install plugins

```bash
npm install remark-attr

yarn add remark-attr
```

### Add plugin to options

```ts
let markdownOptions: MarkdownParserOptions = {
  remarkPlugins: ["remark-attr"],
};
```

## `Rehype` plugins

### Install plugins

```bash
npm install rehype-katex

yarn add rehype-katex
```

### Add plugin to options

```ts
let markdownOptions: MarkdownParserOptions = {
  remarkPlugins: ["remark-attr", "remark-math"],
  rehypePlugins: ["rehype-katex"],
};
```

### Update markdown file

```md
---
title: With Love
subtitle: To World
---

# Simple markdown doc{style="color:yellow;"}
```

### Output

```json
{
  "title": "With Love",
  "subtitle": "To World",
  "extension": ".md",
  "updatedAt": 1596071999302,
  "toc": [
    {
      "id": "simple-markdown-doc",
      "depth": 1,
      "text": "Simple markdown doc"
    }
  ],
  "body": "<h1 style=\"color:yellow;\" id=\"simple-markdown-doc\">Simple markdown doc</h1>"
}
```

## Configuration

### Add absolute path

The libs needs a `absolutePath` for search plugins in the root `node_modules`. If you need change this value you can add to **MarkdownParserOptions**:

```ts
let markdownOptions: MarkdownParserOptions = {
  rehypePlugins: ["rehype-katex"],
  remarkPlugins: ["remark-math", "remark-attr"],
  absolutePath: "/home/angular/docs/projects/project",
};
```

### Extra properties

You can add extra properties in format

```ts
import { readFileSync } from  "fs";
import { Markdown, MarkdownParserOptions } from "node-markddbown-parser";

let markdownOptions: MarkdownParserOptions;

let markdown: Markdown = new Markdown(markdownOptions);

let file: string = readFileSync("./markdown/example.md", "utf-8");

let data = markdown.toJSON({ file, date: Date.now(), age: 27 });

console.log(data);
```

#### Output

```json
{
  "title": "With Love",
  "subtitle": "To World",
  "extension": ".md",
  "updatedAt": 1596414314975,
  "toc": [
    {
      "id": "simple-markdown-docstylecoloryellow",
      "depth": 1,
      "text": "Simple markdown doc{style=\"color:yellow;\"}"
    }
  ],
  "body": "<h1 id=\"simple-markdown-docstylecoloryellow\">Simple markdown doc{style=\"color:yellow;\"}</h1>",
  "date": 1596414314960,
  "age": 27
}
```

### Array as input

```ts
import { readFileSync } from  "fs";
import { Markdown, MarkdownParserOptions } from "@cenguidanos/node-markdown-parser";

let markdownOptions: MarkdownParserOptions;

let markdown: Markdown = new Markdown(markdownOptions);

let file: string = readFileSync("./markdown/example.md", "utf-8");

let data = markdown.toJSON([
  { file, date: Date.now(), age: 27 },
  "# Other stuff in string format",
]);

console.log(data);
```

#### Output

```ts
[
  {
    title: "With Love",
    subtitle: "To World",
    extension: ".md",
    updatedAt: 1596414423314,
    toc: [[Object]],
    body:
      '<h1 id="simple-markdown-docstylecoloryellow">Simple markdown doc{style="color:yellow;"}</h1>',
    date: 1596414423299,
    age: 27,
  },
  {
    extension: ".md",
    updatedAt: 1596414423315,
    toc: [[Object]],
    body:
      '<h1 id="other-stuff-in-string-format">Other stuff in string format</h1>',
  },
];
```

### Multiple files

#### Pass an array of data to `toJSON()` method:

```ts
import {
  Markdown,
  MarkdownParserOptions,
} from "../node-markdown-parser/lib/index";

import { readFileSync } from  "fs";

// create parser
let markdownOptions: MarkdownParserOptions;
let markdown: Markdown = new Markdown(markdownOptions);

// read files from system
let file1: string = readFileSync("./markdown/example.md", "utf-8");
let file2 = "## Hello, from file two";

// parse array of files
let data = markdown.toJSON([file1, file2]);

console.log(data);
```

#### Output array:

```json
[
  {
    "title": "With Love",
    "subtitle": "To World",
    "extension": ".md",
    "updatedAt": 1596301576871,
    "toc": [[Object]],
    "body": "<h1 id=\"simple-markdown-docstylecoloryellow\">Simple markdown doc{style=\"color:yellow;\"}</h1>"
  },
  {
    "extension": ".md",
    "updatedAt": 1596301576872,
    "toc": [[Object]],
    "body": "<h2 id=\"hello-from-file-two\">Hello, from file two</h2>"
  }
]
```

### Only HTML

#### Retrieve only the body of the markdown with `toHTML()`:

```ts
import { readFileSync } from  "fs";

let markdownOptions: MarkdownParserOptions;

let markdown: Markdown = new Markdown(markdownOptions);

let file: string = readFileSync("./markdown/example.md", "utf-8");

let data = markdown.toHTML([file, "# Other stuff in string format"]);

console.log(data);
```

#### Output:

```bash
[
  # first element
  '<hr>\n' +
    '<p>title: With Love</p>\n' +
    '<h2 id="subtitle-to-world">subtitle: To World</h2>\n' +
    '<h1 id="simple-markdown-docstylecoloryellow">Simple markdown doc{style="color:yellow;"}</h1>',

  # second element
  '<h1 id="other-stuff-in-string-format">Other stuff in string format</h1>'

  # ...
]
```

## Special thanks

- [@nuxt/content](https://github.com/nuxt/content)
- [unifiedjs/unified](https://github.com/unifiedjs/unified)
- [remarkjs/remark](https://github.com/remarkjs/remark)
- [rehypejs/rehype](https://github.com/rehypejs/rehype)
- [Render.com](https://render.com/)

## Enjoy !
