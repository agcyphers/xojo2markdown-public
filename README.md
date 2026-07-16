# Xojo2Markdown

## Description
Xojo2Markdown is a console application that can read your `xojo_project` files and convert them to markdown files with matching file structure. Automatically excludes objects with the `Hidden` and `Deprecated`/`DesprectedWithReplacement` attributes, as well as output of Description fields and user-provided Notes.

## Runtime Arguments
The first argument should always be the path to the `xojo_project` you wish to render. Native paths should be quoted.

| Flag | Value Type | Description | Example |
| --- | --- | --- | --- |
| `--out` | Folder Path | The path to save the rendered file structure to. | `--out=-out="/Volumes/External/projects/myproject/docs"` |
| `--verbose` | Boolean | Controls output messages granularity. The application will only print error messages unless this option is enabled. | `--verbose` |
| `--overwrite` | Boolean | Signals that, should a previous version exist, it should be overwritten but not replaced. | `--overwrite` |
| `--replace` | Boolean | During the rendering process, if the output path exists, it will be removed and the new structure will be built in its place. | `--replace` |
| `--in` | Project path to the Xojo project item(s). | Explicitly includes project items in output. | `--in=Classes/Windows/*;Classes/ProjectReader/ImportThread` |
| `--ex` | Project path to a Xojo project item(s). | Explicitly excludes project items from output. |  `--ex=Classes/Windows/testWindows/*;Classes/ProjectWriter/ExportThread` |
| `--index` | Boolean | Will automatically create empty `index.html` files within the directory structure for access via web server without revealing directory indexes. |

## Inclusion Model
Xojo2Markdown uses an explicit inclusion model. All project items are excluded by default, and only those project items specified by an `--in` flag are included. Project items and members with the `Hidden`, `Deprecated` and `DeprecatedWithReplacement` attributes are *always* ignored.

## Include/Exclude Flag Use
- All paths supplied to `--in` and `--ex` should be relative to the project's root as seen in the Xojo IDE navigator.
- Both flags support wildcards (`*`) as the final path element, allowing the inclusion or exclusion of all project items within, for example, a single folder or module.
- Multiple paths should be separated by a semicolon (`;`).

## Method and Property Headers
Supports Method and Property headers via a JSDoc-like syntax for generating extended documentation within these output sections.
- Supports segment values spanning multiple lines for long descriptions and/or code blocks in examples.
- The documentation header should always be the first non-empty line; beginning with `//**` and ending with `//**`. All lines between the starting and ending documentation header tokens should begin with a single `'` comment character.
- The lines between the documentation header opening token and the first `@` token will be used as the description for the documentation entry.
- Paramters are defined as `@ParameterName Description`.
- Examples are defined as `@Example Name <EOL> Content`.
- Examples can contain raw markdown, such as code wrapped in triple-backticks for markdown code display.
- Return values are defined as `@return Description`

### Example Method
```
Public Sub DoSomething(param1 As String, param2 As String) As Boolean
  //**
  ' Tries to do something and returns a boolean
  '   denoting success.
  '
  '  Doesn't always work, but should...theoretically.
  ' @example How to do something
  ' This example will conditionally try to do something or something else.
  ' ```
  ' If DoSomething() then
  '   MessageBox("We did something!")
  ' Else
  '   MessageBox("We failed to do something!")
  ' End
  ' ```
  ' @param param1 The first parameter.
  ' @param param2 The second parameter.
  ' @return A boolean denoting whether the operation was
  '   successful.
  //**

  if param1.IsEmpty or param2.IsEmpty then Return False

  if param1 = "Test" and param2 = "Something" then
    reeturn doSomethingElse()
  else
    return doAnotherThing()
  end if
End Sub
```

### Properties
Documentation headers can be entered in the center code pane of the IDE when a Property or Computed Property is selected.

## Notes Rendering
### Creation
Xojo project item Notes are rendered to that item's `md` files based on the existence of an exclamation point at the start of the note name. For example:
- `!About` will be rendered
- `About` will not be rendered

### Grouping
Notes may be grouped into a structure for rendering logically associated notes. For example:
- `!Examples`
- `!Examples\Instantiating`
- `!Examples\Adding an Item`

This example will create an `Examples` section header, followed by the content of the `!Examples` note, then sub-headings for `Instantiation` and `Adding an Item`, each followed by their Xojo note content.

## Rendered Sections and Order
Project items are rendered based on their type. Folders and Modules will have a `Members` section with an index of their contained items. All project items' sections are rendered in the following order:
1. `!About` note
1. `!Warnings` note
1. `!License` note
1. Members
1. Compatibility
   1. Framework API Version support
   1. Platform support
      1. Android
      1. Console
      1. Desktop
      1. iOS
      1. Web
1. Attributes
1. Enumerations
1. Constants
1. Delegates
1. Event Definitions
1. Methods
1. Shared Methods
1. Properties (regular or computed)
1. Shared Properties (regular or computed)
1. `!Notes` note
1. `!Examples` note
1. All other rendered notes (IE: `!Changelog`)

## Table Formats
Tables for object members are statically sorted and rendered.

### Framework API Version
| 1.0 | 2.0 |
| --- | --- |
| False | True |

### Platforms
| 32-Bit | 64-Bit |
| --- | --- |
| False | True |

### Attributes
| Name | Value |
| --- | --- |
| DefaultEvent | Pressed |

### Enumerations
| Name | Values | Description |
| --- | --- | --- |
| SomeEnum | One <br> Two <br> Three | Enum values for something. |

### Constants
| Name | Type | Default Value | Description |
| --- | --- | --- | --- |
| kPi | Number | 3.14 | Pi value for calculations. |

### Delegates
| Name | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| SomeDelegate | One As String <br> Two As Double <br> Three As Boolean | Boolean | A delegate method. |

### Event Definitions
| Name | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| Pressed | X As Integer <br> Y As Integer | (None) | Raised when the user presses the left mouse button. |

### Methods / Shared Methods
| Name | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| HasChild | extends arr() As String <br> searchFor As String | Boolean | Returns True when searchFor exists within arr. |

### Properties / Shared Properties
| Name | Type | Default Value | Description |
| --- | --- | --- | --- |
| BackgroundColor | Color | &cff00ff | Color drawn to the background of the control. |

## Rendering Caveats
- HTML contained within notes will be automatically converted to markdown. If you wish to display HTML content in your notes, consider encoding tags to HTML entities.
- Markdown contained within notes will be unaltered, and may not match the overall document structure and require modification.
