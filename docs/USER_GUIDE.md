# R2 Media Sync User Guide

This guide explains R2 Media Sync in plain language: setup, daily usage, mobile behavior, local cleanup, troubleshooting, and what each option does.

## What The Plugin Does

R2 Media Sync uploads local images referenced by Obsidian Markdown notes to Cloudflare R2, then rewrites those note links to public R2 URLs.

In short:

1. A note references a local image.
2. The plugin uploads that image to Cloudflare R2.
3. The plugin rewrites the note to use the public R2 URL.
4. If local cleanup is enabled, the plugin moves the original local image to trash or a review folder.

## Good Use Cases

- Pasting images into notes.
- Dropping images into notes.
- Selecting image files from the command palette.
- PDF-to-Markdown workflows that create local image files.
- Import tools or AI assistants that write Markdown and images directly into the vault.
- Reducing iCloud or vault attachment storage.

## Recommended First Setup

Start conservatively:

1. Set `R2 settings source` to `Manual` and enter your Cloudflare R2 settings.
2. Keep `Delete local image after upload` disabled.
3. Keep `Scan on startup` disabled.
4. Create a test note with one image.
5. Run `R2 Media Sync: Upload local images in current note`.
6. Confirm that the note is rewritten to an R2 URL and the image renders.
7. Enable local cleanup or broader scan scopes only after the manual test works.

## R2 Settings

### R2 Settings Source

Choose:

- `Read from EzImage`: reuse an existing EzImage R2 configuration.
- `Manual`: store R2 settings directly in R2 Media Sync.

EzImage is optional. R2 Media Sync does not require it.

### Cloudflare Account ID

Your Cloudflare account ID. This is not your API token and not your access key ID.

### Access Key ID

The R2 Access Key ID generated for your bucket/API token.

### Secret Access Key

The R2 Secret Access Key. Treat it as sensitive and do not publish it.

### Bucket Name

The target R2 bucket name.

### Public URL

The public URL prefix for your bucket or custom domain, without a trailing slash.

Example:

```text
https://example.com
```

### Path Template

Controls the object key used in R2. The default is:

```text
{yyyy}/{MM}/{timestamp}-{random}.{ext}
```

Supported tokens include:

- `{yyyy}`: year
- `{MM}`: month
- `{dd}`: day
- `{timestamp}`: current timestamp
- `{random}`: random suffix
- `{name}`: original file name
- `{ext}`: file extension

## Daily Workflows

### Upload Pasted Images

When enabled, Obsidian first inserts the pasted image into the current note. R2 Media Sync then uploads it to R2 and rewrites the local link.

Useful for:

- screenshots
- clipboard images
- quick image notes

### Upload Dropped Images

When enabled, images dropped into the editor are uploaded after Obsidian inserts them into the note.

Useful for:

- dragging files from Finder or Explorer
- organizing project images
- desktop image workflows

### Upload Selected Image Files

Run this command:

```text
R2 Media Sync: Upload selected image files
```

Choose one or more images. The plugin uploads them to R2 and inserts links into the active note.

## Selected File Insert Format

This setting only affects the `Upload selected image files` command.

### Markdown Image

Format:

```markdown
![image](https://example.com/image.png)
```

Result: the image renders directly in the note.

Best for:

- visual notes
- travel journals
- design records
- notes where the image should be visible inline

### Markdown Link

Format:

```markdown
[image](https://example.com/image.png)
```

Result: the note shows a clickable text link instead of rendering the image.

Best for:

- attachment lists
- reference lists
- notes where images should not take up visual space

### Plain URL

Format:

```markdown
https://example.com/image.png
```

Result: inserts only the URL.

Best for:

- sharing links
- pasting into other tools
- automation workflows

## Local Cleanup

### Delete Local Image After Upload

When enabled, the plugin handles the local image after it has uploaded successfully and the note link has been rewritten.

Keep this disabled for your first tests.

### Local Cleanup Mode

Choose:

- `Move to Obsidian trash`
- `Move to review folder`

### Move To Review Folder

Recommended for multi-device sync.

Example folder:

```text
_r2_media_review
```

Uploaded local images are moved there first. After confirming other devices render the R2 URLs correctly, you can clear the review folder.

This is safer than deleting immediately, especially with iCloud.

## Scan Scope

### Whole Vault

Scan the entire vault. Use this only after your exclusions are correct.

### Only Included Folders

Process only selected folders, for example:

```text
AI Workspace, Imported
```

This is safer for PDF, AI, and project import workflows.

### Excluded Folders

Recommended exclusions:

```text
.obsidian, .git, .trash, Templates
```

The configured review folder is also protected automatically so `_r2_media_review` does not get processed again.

## Dashboard

Run:

```text
R2 Media Sync: Open sync dashboard
```

The dashboard shows:

- latest status
- Markdown files in scope
- upload history count
- failed upload count
- review folder size and file count

Common actions:

- `Scan now`
- `Repair missing links`
- `View failed uploads`
- `Clear failed log`
- `Clear review folder`

## Repair Missing Local Image Links

If a local image was manually deleted but the plugin has a previous upload record for the same image, this command can rewrite the note to the known R2 URL.

Useful when:

- the local image is gone
- the note still points to the local image
- upload history still has the hash-to-URL record

## Mobile And iCloud Tips

Recommended mobile setup:

1. Keep `Scan on startup` disabled.
2. If local cleanup is enabled, use `Move to review folder`.
3. After inserting an image, wait 30 to 60 seconds.
4. Confirm the desktop renders the R2 URL correctly.
5. Clear `_r2_media_review` later.

iCloud is not always instant. Review-folder cleanup prevents files from being permanently removed before another device has finished syncing.

## Relationship With Geo Capture

R2 Media Sync works independently and does not require Geo Capture.

When uploading JPG/JPEG files, R2 Media Sync attempts to read EXIF GPS coordinates and stores a small local metadata cache. Geo Capture can optionally use that cache after images are rewritten to R2 URLs.

This is optional cooperation, not a dependency.

## Relationship With EzImage

R2 Media Sync works independently and does not require EzImage.

If EzImage is already configured, R2 Media Sync can import or reuse its R2 settings.

## Troubleshooting

### The Image Was Not Rewritten To An R2 URL

Check:

- R2 settings are complete.
- Public URL is correct.
- The image is referenced by the note.
- The note and image are in scan scope.
- The folder is not excluded.
- The dashboard failed upload count.

### Upload Worked But The Local File Is Still There

Possible reasons:

- `Delete local image after upload` is disabled.
- The note still contains local image links, so cleanup was skipped for safety.
- Cleanup mode is `Move to review folder`, so the image is in `_r2_media_review`.

### Why Not Delete Immediately?

Immediate deletion can be risky with multi-device sync. The review folder lets you confirm other devices render the R2 URLs before removing local files.

### Can I Use This Without EzImage?

Yes. Manual R2 settings allow R2 Media Sync to handle paste, drop, selected file uploads, scanning, rewriting, and cleanup by itself.

### Can I Use This Without Geo Capture?

Yes. R2 Media Sync does not depend on Geo Capture.

## Suggested Workflows

### Daily Image Notes

1. Enable pasted image uploads.
2. Enable dropped image uploads.
3. Use `Markdown image` as the selected-file insert format.
4. Use `Move to review folder` for cleanup.

### PDF Or AI Imports

1. Limit scan scope to the project folder.
2. Run `Upload local images in current note` or `Scan configured scope now`.
3. Confirm links are rewritten to R2 URLs.
4. Clear the review folder after checking.

### Mobile Travel Journal

1. Insert an image on mobile.
2. Wait 30 to 60 seconds.
3. Confirm the link is rewritten to an R2 URL.
4. Check the same note on desktop.
5. Clear `_r2_media_review` later.

