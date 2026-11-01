---
title: "atomicfilekit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["AtomicFileException", "AtomicFileTransaction"]
dependencies: []
symbols: ["AtomicFileException", "AtomicFileTransaction"]
related: ["api.md", "getting-started.md", "index.md"]
---

# atomicfilekit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## AtomicFileException

`Public Class AtomicFileException Inherits Exception`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `IsCommitted` | `Boolean` | No | No | `False` | — |

### Constructor

`Public Sub Constructor(message As String, committed As Boolean = False)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `message` | `String` | `ByVal` | Required |
| `committed` | `Boolean` | `ByVal` | `False` |

Returns: no value.

## AtomicFileTransaction

`Public Class AtomicFileTransaction`

Single-threaded staged replacement. Parent directories must be trusted.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Count` | `Integer` | Yes | No | — | — |
| `IsClosed` | `Boolean` | Yes | No | — | — |
| `IsCommitted` | `Boolean` | Yes | No | — | — |
| `CleanupError` | `String` | Yes | No | — | — |
| `RetainedPath` | `String` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(destination As String, maxBytes As Integer = 1073741824)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `destination` | `String` | `ByVal` | Required |
| `maxBytes` | `Integer` | `ByVal` | `1073741824` |

Returns: no value.

### Append

`Public Sub Append(data As Bytes)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `data` | `Bytes` | `ByVal` | Required |

Returns: no value.

### CommitExpectedText

`Public Sub CommitExpectedText(expected As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `expected` | `String` | `ByVal` | Required |

Returns: no value.

### Commit

`Public Sub Commit()`

No parameters.

Returns: no value.

### Abort

`Public Sub Abort()`

No parameters.

Returns: no value.

### Destructor

`Public Sub Destructor()`

No parameters.

Returns: no value.

## AtomicFileKit functions

### AtomicWriteText

`Public Sub AtomicWriteText(destination As String, content As String, expected As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `destination` | `String` | `ByVal` | Required |
| `content` | `String` | `ByVal` | Required |
| `expected` | `String` | `ByVal` | Required |

Returns: no value.
