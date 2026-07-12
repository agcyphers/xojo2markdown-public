# Xojo2Markdown

## Description
Xojo2Markdown is a console application that can read your `xojo_project` files and convert them to markdown files with matching file structure. Supports exlcuding objects with the `Hidden` and `Deprecated`/`DesprectedWithReplacement` attributes, as well as output of Description fields and user-provided Notes.

## Runtime Arguments
The first argument should always be the path to the `xojo_project` you wish to render. Native paths should be quoted.

| Flag | Value Type | Description | Example |
| --- | --- | --- | --- |
| `--out` | Folder Path | The path to save the rendered file structure to. | `--out=-out="/Volumes/External/projects/myproject/docs"` |
| `--verbose` | Boolean | Controls output messages granularity. The application will only print error messages unless this option is enabled. | `--verbose` |
| `--overwrite` | Boolean | Signals that, should a previous version exist, it should be overwritten but not replaces. | `--overwrite` |
| `--replace` | Boolean | During the rendering process, if the output path exists, it will be removed and the new structure will be built in its place. | `--replace` |
| `--in` | Project path to the Xojo project item. | Explicitly includes project items in output. Multiple values should be separated by a semicolon (`;`) | `--in=Classes/Windows/*;Classes/ProjectReader/ImportThread` |
| `--out` | Project path to a Xojo project item. | Explicitly excludes project items in output. Multiple values should be separated by a semicolon (`;`) |  `--ex=Classes/Windows/testWindows/*;Classes/ProjectWriter/ExportThread` |

## Inclusion Model
Xojo2Markdown uses an explicit inclusion model. All project items are excluded by default, and only those project items specified by an `--in` flag are included. Project items are members with the `Hidden`, `Deprecated` and `DeprecatedWithReplacement` attributes are *always* ignored.

## Include/Exclude Flag Use
- All paths supplied to `--in` and `--ex` should be relative to the project's root directory as seen in the Xojo IDE navigator.
- Both flags support wildcards (`*`) as the final path element, allowing the inclusion or exclusion of all project items within, for example, a single folder or module.

## Notes Rendering
### Creation
Xojo project item Notes are rendered to that item's project based on the existence of an exclamation point at the start of the note name. For example:
- `!About` will be rendered
- `About` will not be rendered

### Grouping
Notes may be grouped into a structure for rendering logically associated notes. For example:
- `!Examples`
- `!Examples\Instantiating`
- `!Examples\Adding an Item`

This example will create an `Examples` section header, followed by the content of `!Examples` note, then sub-headings for `Instantiation` and `Adding an Item`, each followed by their Xojo note contennt.

## Rendered Sections and Order
Xojo2Markdown will render project items based on their type. Folders and Modules will have a `Members` section with an index of their contained items. All other sections are rendered in the following order:
1. `!About` note
2. `!Warnings` note
3. `!License` note
4. Members
5. Compatibility
   1, Framework API Version support
   2. Platform support
      1. Android
      2. Console
      3. Desktop
      4. iOS
      5. Web
6. Attributes
7. Enumerations
8. Constants
9. Delegates
10. Event Definitions
11. Methods
12. Shared Methods
13. Properties (regular or computed)
14. Shared Properties (regular or computed)
15. `!Notes` section
16. `!Examples` section
17. All other rendered notes (IE: `!Changelog`)
