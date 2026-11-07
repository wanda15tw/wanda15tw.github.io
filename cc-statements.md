---
layout: page
title: cc-statements
description: A personal tool that archives my own credit card statements
permalink: /cc-statements/
sitemap: false
---

# cc-statements

cc-statements is a personal script that runs on my own Mac. Once a month it reads credit card statement emails from my own Gmail account, downloads the PDF attachments, unlocks them, and saves them to my own Google Drive.

It is not a public service. The only user is the site owner.

## Privacy Policy

*Last updated: 2026-11-08*

**Data accessed.** The app requests read-only access to Gmail (`gmail.readonly`). It only searches for and reads credit card statement emails and their PDF attachments.

**How data is used.** Statement PDFs are unlocked and saved to the owner's own Google Drive. Nothing else is done with Gmail data.

**Storage and sharing.** Downloading and unlocking happen locally on the owner's computer, and the OAuth token is stored locally with owner-only file permissions. The unlocked statements in the owner's Google Drive are then analyzed by an AI assistant (Anthropic's Claude) that the owner uses for personal budgeting. Gmail data is not sold or shared with anyone else.

**Revoking access.** Access can be revoked at any time at [myaccount.google.com/permissions](https://myaccount.google.com/permissions).

**Contact.** Through the owner's GitHub profile: [github.com/wanda15tw](https://github.com/wanda15tw).
