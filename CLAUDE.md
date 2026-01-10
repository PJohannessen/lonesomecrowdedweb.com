# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Hugo static site for lonesomecrowdedweb.com, a personal blog using the PaperMod theme.

## Commands

```bash
# Run local development server
hugo server

# Build the site (output to /public/)
hugo

# Build with drafts enabled
hugo -D
```

## Structure

- `content/` - Markdown content files
  - `blog/` - Blog posts
  - `about.md`, `projects.md`, `reading.md` - Static pages
- `layouts/shortcodes/` - Custom shortcodes for reading list entries (audiobook, ebook, paperback)
- `static/img/` - Image assets
- `themes/PaperMod/` - Theme (do not modify directly unless instructed to update the theme)
- `config.yml` - Site configuration

## Custom Shortcodes

Used in the reading list for book format icons:
- `{{</* audiobook title="Title" authors="Author" */>}}` - Shows 🎧
- `{{</* ebook title="Title" authors="Author" */>}}` - Shows 📓
- `{{</* paperback title="Title" authors="Author" */>}}` - Shows 📘
