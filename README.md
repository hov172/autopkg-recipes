# hov172 AutoPkg recipes

AutoPkg recipes for apps published by Ayala Solutions. Add the repo once and every app in it becomes available:

```bash
autopkg repo-add hov172/autopkg-recipes
```

## Apps

| App | Recipes | Source |
|-----|---------|--------|
| Stash Video | `StashVideo.download`, `StashVideo.munki`, `StashVideo.jamf`, `StashVideo.intune` | [hov172/StashVideos](https://github.com/hov172/StashVideos) |

Every recipe downloads the vendor's signed and notarized installer package straight from GitHub Releases. No repackaging happens on your machine, so the artifact you deploy is byte-for-byte what the vendor shipped. The download recipe fails if the Developer ID Installer signature does not match Team ID `N859JA9UCJ`.

## Setup

```bash
# Avoid GitHub API rate limits
defaults write com.github.autopkg GITHUB_TOKEN "ghp_..."

# Jamf Pro (for .jamf recipes)
autopkg repo-add grahampugh/jamf-upload
defaults write com.github.autopkg JSS_URL "https://yourorg.jamfcloud.com"
defaults write com.github.autopkg CLIENT_ID "..."
defaults write com.github.autopkg CLIENT_SECRET "..."

# Microsoft Intune (for .intune recipes)
autopkg repo-add almenscorner/intune-uploader
/usr/local/autopkg/python -m pip install -r ~/Library/AutoPkg/RecipeRepos/com.github.almenscorner.intune-uploader/IntuneUploader/requirements.txt
defaults write com.github.autopkg CLIENT_ID "..."
defaults write com.github.autopkg CLIENT_SECRET "..."
defaults write com.github.autopkg TENANT_ID "..."
```

Munki recipes need the Munki tools installed (`makepkginfo`) and `MUNKI_REPO` set.

## Run

```bash
autopkg make-override StashVideo.munki.recipe.yaml   # recommended: pin trust info
autopkg run -v StashVideo.munki
autopkg run -v StashVideo.jamf
autopkg run -v StashVideo.intune
```

## Stash Video notes

- Requires macOS 26 or later.
- The installer also places `yt-dlp` and `ffmpeg` symlinks in `/usr/local/bin`.
- A configuration profile that pre-approves the app's background helper is published with each release as `StashVideo-BackgroundItems.mobileconfig`. Deploy it alongside the package if you do not want users prompted for Background Items.
