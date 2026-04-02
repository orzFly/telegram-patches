# Patch: Hide Screen Reader Mode Banner

Patch target: **tdesktop v6.7.1** (commit `666e40f`)

## What it does

Suppresses the "Telegram is working in Screen Reader mode." banner that appears at the top of the window when accessibility software is detected. Screen reader mode itself remains fully functional — only the persistent notification bar is hidden.

### How it works

The banner is implemented as a `SlideWrap<Bar>` widget created by `Ui::CreateScreenReaderBar()`. Upstream, the widget subscribes to `Ui::ScreenReaderModeActiveValue()` and toggles visible whenever the system reports an active screen reader. This patch removes the reactive subscription and forces the initial toggle state to `false`, so the bar is never shown.

## Files modified

| File | Change |
|------|--------|
| `ui/controls/window_screen_reader_bar.cpp` | Remove `ScreenReaderModeActiveValue` subscription; force `wrap->toggle(false, ...)` |

## Porting hints

This is a small, self-contained patch touching only `CreateScreenReaderBar()` in `window_screen_reader_bar.cpp`. When porting to a new tdesktop version:

1. Check if `CreateScreenReaderBar()` still exists and has the same structure (a `SlideWrap` toggled by `ScreenReaderModeActiveValue`).
2. If the function signature or toggle mechanism changes, adapt the patch to ensure `wrap->toggle(false, ...)` is called and the reactive subscription is removed.
3. If upstream removes the banner entirely or adds a setting to hide it, this patch can be dropped.
