# Telegram Patches

Custom patches for Telegram clients.

## Patches

### `desktop/` — Telegram Desktop

**Hide Messages from Blocked Contacts** — Comprehensively hides blocked contacts' presence in group chats: messages, chat list preview, notifications, reactions, typing indicators, unread counters, and quoted messages.

- [`tdesktop-hide-blocked-messages.patch`](desktop/tdesktop-hide-blocked-messages.patch) — Unified diff against tdesktop v6.6.4
- [`tdesktop-hide-blocked-messages.md`](desktop/tdesktop-hide-blocked-messages.md) — Feature documentation and porting hints

## How to use

Prepare the source code or build metadata for Telegram Desktop using one of:

- [telegramdesktop/tdesktop](https://github.com/telegramdesktop/tdesktop) — upstream source
- [flathub/org.telegram.desktop](https://github.com/flathub/org.telegram.desktop) — Flatpak build metadata
- Your distro's package build scripts (e.g., Arch Linux PKGBUILD)

Then, give the following prompt to your coding agent:

> Integrate the patch from https://github.com/orzFly/telegram-patches/tree/master/desktop, and build for me. If version is not matched, try to rebase the patch with the help of the markdown file. If the patch is changed, ask the user to submit the updated patch back to the repo.
