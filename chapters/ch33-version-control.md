---
title: "Ch 33 — Appendix C: Tracking History Using Version Control Systems"
type: chapter
source_chapters: [33]
part: "V — Appendices"
pages: 10
chars: 18658
created: 2026-04-15
updated: 2026-04-15
tags: [version-control, svn, git, history]
---

# Ch 33 — Appendix C: Tracking History Using Version Control Systems

## Overview

Introduces version control systems (SVN and Git) for tracking addon source code, collaborating with other authors, and maintaining release history.

## Key Concepts

### Why Version Control?
- Track every change to your addon over time
- Revert broken changes
- Collaborate with other developers
- Tag releases for distribution

### SVN (Subversion)
- Centralized version control
- WowAce and CurseForge use SVN repositories
- Key commands: `svn checkout`, `svn update`, `svn commit`, `svn diff`, `svn log`
- TortoiseSVN for Windows GUI

### Git
- Distributed version control
- Each developer has a full copy of history
- Key commands: `git init`, `git add`, `git commit`, `git push`, `git pull`, `git log`, `git diff`
- Branching is lightweight and fast

### Hosted Repositories
- **CurseForge/WowAce**: SVN hosting for WoW addons
- **GitHub**: Git hosting
- **Google Code**: SVN or Mercurial (at time of writing)

### Release Process
1. Tag the release in VCS
2. Package the addon files (excluding VCS metadata)
3. Upload to distribution sites (Curse, WoWInterface)

## Connections

- Addon file structure from [Ch 8](ch08-anatomy-of-addon.md)
- Distribution sites covered in [Ch 34](ch34-addon-author-resources.md)
