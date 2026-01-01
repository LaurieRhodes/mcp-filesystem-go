# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.0.0] - 2026-01-02

### Breaking Changes

- **`get_file_info`** now returns JSON instead of plain text
  - **Before**: `"size: 1024\nmodified: ..."`
  - **After**: `{"exists": true, "size": 1024, "modified": "...", "lines": 10}`
  - Migration: Parse JSON response instead of text lines
  - Benefit: Easier to use programmatically, includes file existence check

### Added

- **`get_file_info`**: File-not-found is now a status, not an error
  - Returns `{"exists": false}` when file doesn't exist (not an error)
  - Returns `{"exists": true, ...metadata}` when file exists
  - Includes `lines` field with line count for text files
  - Makes it easy to check file existence without error handling
  
- **`insert`**: Support for keyword line numbers
  - `"line_number": "start"` or `"beginning"` - Insert at beginning
  - `"line_number": "end"` or `"append"` - Append to end
  - Integer line numbers still work (backward compatible)
  - More intuitive than counting lines manually
  
- **`insert`**: Auto-create files when they don't exist
  - If file doesn't exist and `line_number` is `0`, `"start"`, `-1`, `"end"`, or `"append"`: Creates file
  - Parent directories are created automatically
  - Eliminates need for `create_directory` + `write_file` + `insert` sequence
  - Single tool call can now create and populate files

### Changed

- **`insert`**: Updated description to document new keyword and auto-create features
- **`get_file_info`**: Updated description to document JSON response format
- Tool schemas updated to reflect new capabilities

### Benefits

These changes make the filesystem MCP tool:
- **More intuitive**: File-not-found is a status, not an error
- **More powerful**: Single tool can create and append to files
- **More user-friendly**: Keywords instead of line counting
- **More reliable**: LLMs don't need complex error handling

### Migration Guide

**For `get_file_info`**:

Old code (v1.x):
```python
result = get_file_info("file.txt")
# Parse: "size: 1024\nmodified: ..."
```

New code (v2.0):
```python
result = json.loads(get_file_info("file.txt"))
if result["exists"]:
    print(f"Size: {result['size']}, Lines: {result['lines']}")
else:
    print("File doesn't exist")
```

**For `insert`** (no breaking changes, new features):

New capabilities:
```python
# Append to end (keyword)
insert(path, "end", "new line")

# Insert at start (keyword)  
insert(path, "start", "header")

# Create new file
insert("new.txt", "end", "first line")  # Auto-creates file
```

## [1.1.0] - 2025-11-07

### Added

- `str_replace`: Surgical string replacement with validation
- `insert`: Insert text at specific line numbers
- `undo_edit`: Rollback file changes with automatic backups

### Changed

-

### Fixed

- Local config file location finding
- When the MCP server runs on Windows, the `list_allowed_directories` tool returns lowercase versions of the allowed directory paths, even when the config file contains mixed-case paths

## [1.0.0] - 2025-05-13

### Added

- Initial release

---

## Release Types

### Major (x.0.0)

- Breaking changes
- Major feature additions
- Architecture changes

### Minor (0.x.0)

- New features
- Non-breaking enhancements
- New provider support

### Patch (0.0.x)

- Bug fixes
- Documentation updates
- Performance improvements

[Unreleased]: https://github.com/LaurieRhodes/mcp-filesystem-go/compare/v2.0.0...HEAD
[2.0.0]: https://github.com/LaurieRhodes/mcp-filesystem-go/releases/tag/v2.0.0
[1.1.0]: https://github.com/LaurieRhodes/mcp-filesystem-go/releases/tag/v1.1.0
[1.0.0]: https://github.com/LaurieRhodes/mcp-filesystem-go/releases/tag/v1.0.0
