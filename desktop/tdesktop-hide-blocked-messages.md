# Patch: Hide Messages from Blocked Contacts

Patch target: **tdesktop v6.7.1** (commit `666e40f`)

## What it does

Comprehensively hides blocked contacts' presence in **group chats** (basic groups and supergroups). Messages in 1-on-1 chats with blocked users are not affected.

### Behaviors

| Area | Behavior |
|------|----------|
| Message history | Messages from blocked senders are not rendered and occupy zero height |
| Chat list preview | Shows the latest non-blocked message instead |
| Chat list sort order | Not affected by blocked senders' new messages |
| Notifications | Suppressed for messages from blocked senders in groups |
| Typing indicator | "X is typing..." hidden for blocked contacts |
| Reactions (display) | Blocked contacts' reactions are subtracted from counts and their avatars removed |
| Reactions (badge) | Unread reaction dot not shown when only blocked users reacted |
| Unread counter | Not incremented for messages from blocked senders |
| Quoted messages | Quotes of blocked contacts' messages render as "Deleted Message" |

### Blocked list preloading

The client now preloads the full blocked peers list at session start (with pagination), calling `PeerData::setIsBlocked(true)` for each peer. Without this, `isBlocked()` returns `false` by default since Telegram Desktop loads block status lazily (only when viewing a user's profile).

## Caveats

**Blocking/unblocking a user while the client is running will leave the UI in an inconsistent state.** For example, after blocking a user, their old messages may still be visible until restart; after unblocking, their messages may remain hidden. **Restarting the client fixes this.** This is because message rendering, height calculations, chat list messages, and unread counters are cached and not re-evaluated when block status changes. The block list is assumed to be stable — fixing live block/unblock reactivity is not a goal of this patch.

## Files modified

| File | Change |
|------|--------|
| `api/api_blocked_peers.h` | Add `applyBlockedStatus()` private method declaration |
| `api/api_blocked_peers.cpp` | Set per-peer `isBlocked` in `reload()` callback; paginate to load all blocked peers |
| `apiwrap.cpp` | Trigger `blockedPeers().reload()` at session start |
| `history/view/history_view_element.cpp` | `Element::isHidden()`: return true for blocked senders in groups; `countCurrentSize()`: return zero height when hidden |
| `history/history.cpp` | `computeChatListMessageFromLast()`: walk backwards to skip blocked senders; `newItemAdded()`: skip unread count increment |
| `history/history_item.cpp` | `updateReactions()`: skip unread reaction badge when all reactors are blocked |
| `history/history_item_components.cpp` | `HistoryMessageReply::updateData()`: treat quoted messages from blocked senders as deleted |
| `history/view/reactions/history_view_reactions.cpp` | `InlineListDataFromMessage()`: subtract blocked peers from reaction counts and filter avatars |
| `window/notifications_manager.cpp` | `computeSkipState()`: skip notifications from blocked senders in groups |
| `data/data_send_action.cpp` | `SendActionManager::registerFor()`: skip typing status from blocked users |

## Porting hints

When porting to a new tdesktop version, the key integration points are:

1. **`Element::isHidden()` and `countCurrentSize()`** in `history_view_element.cpp` — the core rendering gate. If the `Element` class hierarchy changes, or if `isHidden()` gets new callers/overrides, review them. The `countCurrentSize()` hook ensures hidden items have zero height; if the height/layout pipeline changes, verify this still works.

2. **`computeChatListMessageFromLast()`** in `history.cpp` — the blocked-sender walk-back mirrors the existing migration-message walk-back. If new skip conditions are added upstream, merge carefully.

3. **`computeSkipState()`** in `notifications_manager.cpp` — the conditional chain at lines 344-363 is delicate. Upstream changes to notification logic (muting, blocking, mentions) may conflict. The key changes are: (a) a new blocked check before `DontSkip` at the thread-level mute branch, and (b) removing the `messageType ||` bypass in the `notifyBy` blocked check.

4. **`InlineListDataFromMessage()`** in `history_view_reactions.cpp` — reaction display logic. If the reaction data model changes (e.g., `recentReactions()` API, `MessageReaction` struct), update the subtraction loop accordingly.

5. **`BlockedPeers::reload()`** in `api_blocked_peers.cpp` — the `applyBlockedStatus()` pagination. If the `request()` API or `Slice` structure changes, adapt the pagination loop.

6. **General pattern** — most checks follow the same idiom:
   ```cpp
   !item->out()
   && !item->isService()
   && (peer->isChat() || peer->isMegagroup())
   && item->from()->isBlocked()
   ```
   If any of these APIs are renamed or restructured, a project-wide search for `isBlocked()` in the patch will find all call sites.
