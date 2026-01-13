# Personal Portfolio

Personal website hosted at [rapture244.github.io](https://rapture244.github.io)

## Tech Stack

- **Hugo** v0.154.5 (Static Site Generator)
- **Theme**: [Hugo Coder](https://github.com/luizdepra/hugo-coder)
- **Hosting**: GitHub Pages


## Project Structure 

**What you actually touch**
- **📝 Write Content:** `content/` - Add `.md` files here for new pages
- **🖼️ Add Files:** `static/` - images or files (PDFs, resumes, etc.)
- **⚙️ Configure:** `hugo.toml` - Change site settings, menu, social links
- **🚫 Ignore:** Everything else is auto-generated or managed by Hugo

```
.
├── content/             # YOUR PAGES - Write Markdown files here
│   └── about.md         # About page
│
├── static/              # YOUR FILES - Images, PDFs, downloads
│   └── images/         
│       └── avatar.jpg   # Your profile picture
│
├── themes/              # Theme (don't touch)
│   └── hugo-coder/     
│
├── hugo.toml            # MAIN CONFIG - All your settings
│
├── public/              # AUTO-GENERATED - Don't edit (this gets deployed)
├── resources/           # AUTO-GENERATED - Ignore this
└── README.md            
```


## Installation (WSL2 Ubuntu)

```bash
# ================================================================================================ #
# Hugo Installation (WSL2 Ubuntu)                                                                  #
# ================================================================================================ #

# Download latest Hugo extended release (2026-01-12)
wget https://github.com/gohugoio/hugo/releases/download/v0.154.5/hugo_extended_0.154.5_linux-amd64.deb

# Install Hugo
sudo dpkg -i hugo_extended_0.154.5_linux-amd64.deb

# Clean up installer
rm hugo_extended_0.154.5_linux-amd64.deb

# ------------------------------------------------------------------------------------------------ #
# Verify Installation                                                                              #
# ------------------------------------------------------------------------------------------------ #

# Check Hugo version (should show v0.154.5+extended)
hugo version

# NOTE: If "command not found", restart your shell (close and reopen terminal)

# ------------------------------------------------------------------------------------------------ #
# Update Hugo to Latest Version (when needed)                                                      #
# ------------------------------------------------------------------------------------------------ #

# 1. Check latest release: https://github.com/gohugoio/hugo/releases
# 2. Download new version (replace X with actual version number)
wget https://github.com/gohugoio/hugo/releases/download/v0.XXX.X/hugo_extended_0.XXX.X_linux-amd64.deb

# 3. Install (automatically replaces old version)
sudo dpkg -i hugo_extended_0.XXX.X_linux-amd64.deb

# 4. Clean up
rm hugo_extended_0.XXX.X_linux-amd64.deb

# 5. Restart shell and verify
hugo version
```

## Project Setup 

```shell
# ================================================================================================ #
# Project Setup (From Empty Git Repo)                                                             #
# ================================================================================================ #

# Navigate to your empty repository (contains only .git/ at this point)
cd rapture244.github.io

# ------------------------------------------------------------------------------------------------ #
# Initialize Hugo Site Structure                                                                   #
# ------------------------------------------------------------------------------------------------ #

# Create Hugo site in current directory (--force allows init in non-empty dir)
hugo new site . --force

# This creates:
# - archetypes/   (content templates)
# - content/      (your pages)
# - data/         (data files)
# - layouts/      (custom templates)
# - static/       (images, CSS, JS)
# - themes/       (themes folder)
# - hugo.toml     (configuration file)

# ------------------------------------------------------------------------------------------------ #
# Install Theme (Hugo Coder)                                                                       #
# ------------------------------------------------------------------------------------------------ #

# Add theme as git submodule
git submodule add https://github.com/luizdepra/hugo-coder.git themes/hugo-coder

# Initialize and update submodule
git submodule update --init --recursive

# ------------------------------------------------------------------------------------------------ #
# Run Development Server                                                                           #
# ------------------------------------------------------------------------------------------------ #

# Start Hugo development server
hugo server

# Site now available at: http://localhost:1313
# Press Ctrl+C to stop server
```

## Deployment (Github Actions)


Create `.github/workflows/deploy.yml`:
```yaml
# ================================================================================================ #
# GitHub Actions Workflow - Auto Deploy to GitHub Pages                                           #
# ================================================================================================ #

name: Deploy Hugo site to Pages

on:
  # Runs on pushes to main branch
  push:
    branches: ["main"]
  
  # Allows manual trigger from Actions tab
  workflow_dispatch:

# Sets permissions for GitHub Pages deployment
permissions:
  contents: read
  pages: write
  id-token: write

# Cancel in-progress deployments
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default shell
defaults:
  run:
    shell: bash

jobs:
  # ------------------------------------------------------------------------------------------------ #
  # Build Job                                                                                        #
  # ------------------------------------------------------------------------------------------------ #
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive  # Fetch Hugo theme
          fetch-depth: 0         # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.154.5'
          extended: true

      - name: Build with Hugo
        run: hugo --minify

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # ------------------------------------------------------------------------------------------------ #
  # Deploy Job                                                                                       #
  # ------------------------------------------------------------------------------------------------ #
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

**Setup Steps:**
1. Go to your repo settings on GitHub
2. Navigate to **Settings** → **Pages**
3. Under **Source**, select **GitHub Actions**
4. Commit and push the workflow file
5. Site auto-deploys on every push to `main`


### Verify Deployment

Visit: [https://rapture244.github.io](https://rapture244.github.io)

Changes may take 1-2 minutes to appear.

