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
- [archlinux/packaging/packages/telegram-desktop](https://gitlab.archlinux.org/archlinux/packaging/packages/telegram-desktop) — Arch Linux PKGBUILD
- Your distro's package build scripts

Then, give the following prompt to your coding agent:

> Integrate the patches from https://github.com/orzFly/telegram-patches and build Telegram Desktop for me. If the target version doesn't match, rebase the patch accordingly — refer to the accompanying markdown file for context and porting hints. If you had to modify the patch to make it apply, kindly ask the user to submit the updated patch back to the repository.
