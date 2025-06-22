# Footer Management System

This system helps you manage consistent footers across all your markdown documentation files.

## Files Created

- `templates/footer.md` - The footer content template
- `templates/markdown-template.md` - Template for new markdown files
- `scripts/manage-footers.sh` - Script to add/update/remove footers
- `scripts/new-markdown.sh` - Script to create new markdown files with footer

## Usage

### Managing Existing Files

#### Add footers to files that don't have them:
```bash
./scripts/manage-footers.sh add guides/mail/mailserver.md
./scripts/manage-footers.sh add guides/
```

#### Update all footers (adds if missing, updates if exists):
```bash
./scripts/manage-footers.sh update .
./scripts/manage-footers.sh update guides/
```

#### Remove footers from files:
```bash
./scripts/manage-footers.sh remove guides/mail/mailserver.md
./scripts/manage-footers.sh remove .
```

### Creating New Files

#### Create a new markdown file with footer template:
```bash
./scripts/new-markdown.sh guides/new-guide.md "My New Guide"
./scripts/new-markdown.sh troubleshooting/fix.md
```

### Updating Footer Content

1. Edit `templates/footer.md` to change the footer content
2. Run `./scripts/manage-footers.sh update .` to update all files

## Examples

### Update your existing files with consistent footers:
```bash
# Update all markdown files in the project
./scripts/manage-footers.sh update .

# Or update just the guides directory
./scripts/manage-footers.sh update guides/
```

### Create a new guide:
```bash
./scripts/new-markdown.sh guides/backup/new-backup-method.md "New Backup Method"
```

### Change your footer and update all files:
```bash
# 1. Edit templates/footer.md
# 2. Update all files
./scripts/manage-footers.sh update .
```

## Footer Format

The system uses HTML comments to mark footer sections:
```markdown
<!-- FOOTER_START -->
By Diego Rodriguez
- ![GitHub](https://img.shields.io/badge/GitHub-dewstouh-181717?style=flat-square&logo=github)
<!-- FOOTER_END -->
```

This allows the scripts to reliably find and update footers without affecting your content.
