# CI/CD Workflows

This directory contains GitHub Actions workflows for building and releasing Solaar.

## Build and Release Workflow

The `build-release.yml` workflow builds Solaar packages and creates GitHub releases.

### What it builds

1. **DEB Package** - Debian/Ubuntu package (`.deb`)
2. **AppImage** - Universal Linux application bundle (`.AppImage`)

### When it runs

The workflow automatically runs when:

- A version tag is pushed (e.g., `1.1.19` or `1.1.19rc1`)
- Manually triggered via GitHub Actions UI

### Triggering a release

#### Automatic (via tag)

1. Update version in `lib/solaar/version`
2. Update `CHANGELOG.md` with release notes
3. Create and push a git tag:
   ```bash
   git tag -a 1.1.19 -m "Release 1.1.19"
   git push origin 1.1.19
   ```

The workflow will:
- Build DEB and AppImage packages
- Create a GitHub Release
- Attach the packages to the release
- Mark as pre-release if version contains "rc"

#### Manual trigger

1. Go to Actions → Build and Release
2. Click "Run workflow"
3. Choose whether to create a release
4. The workflow will build packages and optionally create a release

### Artifacts

Build artifacts are available for 90 days:
- DEB package: `solaar-deb-<version>`
- AppImage: `solaar-appimage-<version>`

### Installation

**DEB Package (Ubuntu/Debian):**
```bash
sudo dpkg -i solaar_<version>_all.deb
sudo apt-get install -f  # Install dependencies
```

**AppImage (Any Linux):**
```bash
chmod +x Solaar-<version>-x86_64.AppImage
./Solaar-<version>-x86_64.AppImage
```

### Customization

To modify the build process:

1. **DEB package**: Edit the debian package configuration in the `build-deb` job
2. **AppImage**: Edit the AppDir structure and dependencies in the `build-appimage` job
3. **Release notes**: The workflow extracts notes from `CHANGELOG.md` automatically

### Requirements

The workflow requires:
- Git tags for version numbers
- Valid `CHANGELOG.md` entries for release notes
- No special secrets (uses default `GITHUB_TOKEN`)

### Troubleshooting

**DEB build fails:**
- Check debian dependencies are correct
- Verify Python dependencies are available in Ubuntu repos

**AppImage build fails:**
- Check all Python dependencies are pip-installable
- Verify desktop file and icons exist

**Release creation fails:**
- Ensure the tag exists
- Check `GITHUB_TOKEN` permissions (should have `contents: write`)

## Other Workflows

- `tests.yml` - Runs unit tests on Ubuntu and macOS
- `checks.yml` - Runs pre-commit checks (linting, formatting)
- `gh-pages.yml` - Builds and deploys documentation
