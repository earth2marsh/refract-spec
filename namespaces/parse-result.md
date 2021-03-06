# Parse Result Namespace

This document defines elements needed for holding the result of parsing
(or other processing) API description documents.

This document uses [API Namespace][].

## Content

<!-- TOC depth:3 withLinks:1 updateOnSave:0 -->
- [Parse Result Namespace](#parse-result-namespace)
	- [Content](#content)
	- [About this Document](#about-this-document)
	- [Parse Result (Element)](#parse-result-element)
		- [Properties](#properties)
		- [Example](#example)
	- [Annotation (Element)](#annotation-element)
		- [Properties](#properties)
		- [Example](#example)
	- [Source Map (Element)](#source-map-element)
		- [Properties](#properties)
		- [Example](#example)


<!-- /TOC -->

## About this Document

This document conforms to [RFC 2119][], which says:

> The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in RFC 2119.

[MSON][] is used throughout this document.

## Parse Result (Element)

A result of parsing of an API description document.

### Properties

- `element`: parseResult (string, fixed)
- `content` (array)
    - (Category)
    - (Annotation)

### Example

Given following API Blueprint document:

```apib
# GET /1
```

The parse result is (using null in `category` content for simplicity):

```json
["parseResult", {}, {}, [
    ["category", {"classes": ["api"]}, {"sourceMap": [[0,9]]}, null],
    ["annotation", {"classes": ["warning"]}, { "code": 6, "sourceMap": [[0,9]] }, "action is missing a response"]
  ]
]
```

## Annotation (Element)

Annotation for a source file. Usually generated by a parser or adapter.

### Properties

- `element`: annotation (string, fixed)
- `meta`
  - `classes` (array, fixed)
      - (enum[string])
          - error - Annotation represents an error
          - warning - Annotation represents a warning

- `attributes`
    - `code` (number) - Parser-specific code of the annotation.
    Refer to parser documentation for explanation of the codes.

    - `sourceMap` (Source Map) - Location of the annotation in the source file.

- `content` (string) - Textual annotation.

    This is – in most cases – a human-readable message to be displayed to user.

### Example

```json
{
  "element": "annotation",
  "meta": {
    "classes": ["warning"]
  },
  "attributes": {
    "code": 6,
    "sourceMap": [ [ 0, 10 ] ]
  },
  "content": "action is missing a response"
}
```

## Source Map (Element)

Source map of an Element.

The Source Map element MAY be used in any other element attributes in its
normal ("unrefracted") form under the `sourceMap` property.

If used, it represents the location of characters in the source file.
This location SHOULD include the characters used to build the parent element.

A source map is a series of character-blocks. These
blocks may be non-continuous. For example, a block in the series may not start
immediately after the previous block. Each block, however, is a continuous
series of characters.

### Properties

- `element`: sourceMap (string, fixed)
- `content` (array) - Array of character blocks.
    - (array, fixed) - Continuous characters block. A pair of character index and character count.
      - (number) - Zero-based index of a character in the source document.
      - (number) - Count of characters starting from the character index.

### Example

```json
{
	"element": "...",
	"attributes": {
		"sourceMap": [
			[4, 12],
			[20, 12]
		]
	}
}
```

This reads, "The location starts at the 5th character of the source file. It
includes the 12 consequent characters including the starting one. Then it
continues at the 21st character for another 12 characters."

[MSON]: https://github.com/apiaryio/mson
[API Namespace]: api-namespace.md

[RFC 2119]: https://datatracker.ietf.org/doc/rfc2119/
