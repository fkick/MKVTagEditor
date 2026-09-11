# MKVTagEditor

MKVTagEditor is a macOS SwiftUI application for viewing, editing, creating, and deleting Matroska tags in `.mkv` files. It focuses on Global tags and per-track Simple tags, and it uses the MKVToolNix command-line tools to read and write Matroska tag XML.

The app writes tag changes in place with `mkvpropedit`, so media streams are not remuxed during a save.

> Current target: macOS 12.4 or later. 

## Features

- Open `.mkv` files from the toolbar, the `Open MKV File` button, or drag and drop when no file is open.
- Browse Global tags and each MKV track from the left `Tracks` column.
- View and edit tag name, string value, language, and default-language state in the right inspector column.
- Add top-level tags from an official Matroska tag-name catalog, or create a custom tag name.
- Add nested Simple tags with the `Add Child Tag` control.
- Delete tags without immediately losing the original value from the session.
- Reset individual fields or a whole selected tag back to the value loaded from the source file.
- Copy a selected tag, including nested child tags, and paste it into another track or scope.
- Save changes in place, with an option to create a sibling backup first.
- Use the bundled `MKV Tag List` help view as a quick reference for official tag names and descriptions.
- Configure a custom MKVToolNix folder when the tools are not installed in a standard location.

## Requirements

- macOS 12.4 or later.
- MKVToolNix installed separately.
- The executable tools `mkvmerge`, `mkvextract`, and `mkvpropedit`.

The app currently checks these locations for MKVToolNix:

1. A custom folder or executable path entered in Settings.
2. `/opt/homebrew/bin`
3. `/usr/local/bin`
4. `/Applications/MKVToolNix.app/Contents/MacOS`
5. `~/Applications/MKVToolNix.app/Contents/MacOS`

If the tools are missing, install MKVToolNix with Homebrew:

```sh
brew install mkvtoolnix
```

You can also install MKVToolNix from the official project site: <https://mkvtoolnix.download/>.

## How To Use

1. Launch MKVTagEditor.
2. Open an MKV file with the toolbar `Open` button, the center `Open MKV File` button, or by dropping an `.mkv` file into the empty window.
3. Select `Global` or a specific media track from the `Tracks` column.
4. Select a tag in the tag list to edit its values in the inspector column.
5. Use `Add Tag` to create a new top-level tag in the current scope.
6. Use `Add Child Tag` to create a nested Simple tag under the selected tag.
7. Use the reset controls to restore edited fields or deleted tags to their original source values before saving.
8. Use `Copy` and `Paste` to duplicate a tag and its child tags into another scope.
9. Click `Save`, review the save options, and leave `Create a sibling backup first` enabled unless you intentionally do not want a backup.

Saving updates the selected MKV file directly. Reloading, closing the file, or opening a different file will ask before discarding unsaved edits. Quitting the app discards session-only reset history.

## In-Place Editing

MKVTagEditor does not implement its own Matroska writer. It extracts tags with MKVToolNix, edits an in-memory model, serializes the selected scope back to Matroska tag XML, and asks `mkvpropedit` to write the metadata back into the existing file.

This is the intended workflow for fast tag edits:

- `mkvmerge -J` identifies tracks and track UIDs.
- `mkvextract` extracts the existing tag XML.
- `mkvpropedit --tags` writes updated tag XML for Global or track-specific scopes.

Because the write is in place, keeping a backup is strongly recommended when editing files you cannot easily recreate.

## Current Scope

The editor is built around Matroska Global tags and Simple tags associated with individual tracks. The current UI does not provide editing workflows for every possible Matroska tag target, such as edition, chapter, attachment, or multi-track target combinations.

Binary tag values are detected and preserved in the model, but the inspector is optimized for string tag values.

## Development

Open `MKVTagEditor.xcodeproj` in Xcode and build the `MKVTagEditor` scheme.

The current project configuration targets macOS 12.4. The app sandbox is disabled for the current ad hoc distribution model because the app launches user-installed command-line tools and writes selected MKV files in place. Re-enabling the sandbox would require rechecking file access, security-scoped resources, and subprocess behavior.

## Distribution Notes

This project is not currently configured for Mac App Store distribution. For ad hoc distribution, sign and notarize the app according to Apple's current Developer ID distribution flow, and make sure users understand that MKVToolNix is a separate dependency.

Do not bundle MKVToolNix binaries unless you are prepared to comply with MKVToolNix and third-party license obligations.

## Licensing Concerns

The Author grants users a non-exclusive, non-transferable, revocable license to download, install, and use copies of the MKVTagEditor for personal use. See `LICENSE`

MKVTagEditor currently depends on a user-installed copy of MKVToolNix instead of bundling MKVToolNix. That keeps the app distribution separate from the MKVToolNix binaries, but it does not remove the need to respect MKVToolNix's license if you later decide to redistribute those binaries with the app.

MKVToolNix source packages include the GNU GPL v2 license text and project license notes. If MKVToolNix is bundled or redistributed with this app, review the MKVToolNix source package, license notices, and third-party dependency notices before shipping. Common obligations can include preserving copyright notices, providing license text, and making corresponding source code available under the applicable license terms.

Matroska is an open standard, but the Matroska project also documents trademark and logo restrictions. Avoid using the Matroska name or logo in a way that implies endorsement, and review the Matroska legal pages before using project branding in marketing, packaging, or app artwork.

## References

- MKVToolNix: <https://mkvtoolnix.download/>
- MKVToolNix documentation: <https://mkvtoolnix.download/docs.html>
- `mkvpropedit` documentation: <https://mkvtoolnix.download/doc/mkvpropedit.html>
- Matroska tagging documentation: <https://www.matroska.org/technical/tagging.html>
- Matroska license notes: <https://www.matroska.org/license.html>
- Matroska legal notes: <https://www.matroska.org/legal.html>
- GNU GPL v2: <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html>
