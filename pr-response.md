# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:**
Renamed `save_to_watchlist()` to `add_to_watchlist()` to match the project's `verb_to_noun` naming convention (same pattern as `add_to_collection()` in `services/collection_service.py`).

Files changed:
- `services/watchlist_service.py` — function definition renamed
- `routes/watchlist/watchlist.py` — import and call site updated

**How I verified:**
Searched the entire project for `save_to_watchlist` using ripgrep (`rg save_to_watchlist`). The only remaining hits are in `specs.md`, which documents the original review comment — no references remain in application code. Ran `pytest tests/ -v` to confirm existing tests still pass.

## Comment 2 — Deduplication
**What I did:**
Added deduplication to `add_to_watchlist()` in `services/watchlist_service.py`, following the same pattern as `add_to_collection()` in `services/collection_service.py`.

Changes:
- Added `AlreadyInWatchlistError` (mirrors `AlreadyInCollectionError` in collection service)
- Before creating a new `WatchlistEntry`, query for an existing entry with the same `user_id` and `film_id`
- If a match exists, raise `AlreadyInWatchlistError` instead of inserting a duplicate row
- Updated the function docstring `Raises:` section to document the new error

Reference pattern from `add_to_collection()`:
1. Check that the film exists (`FilmNotFoundError`)
2. Query `filter_by(user_id=..., film_id=...).first()`
3. If found → raise domain-specific duplicate error
4. If not found → create and commit the entry

**How I verified:**
Ran `pytest tests/ -v` — all existing collection tests still pass. Manually traced the logic against `add_to_collection()` in `collection_service.py` (lines 47–53) to confirm the check and error behavior match. A duplicate call to `add_to_watchlist()` with the same `user_id` and `film_id` now raises `AlreadyInWatchlistError` at the service layer instead of creating a second entry.

## Comment 3 — Missing test
**What I did:**
**How I verified:**

## Comment 4 — Default visibility
**My position:**
**Reasoning:**
**Tradeoff acknowledged:**

## Comment 5 — Sort order
**My position:**
**Reasoning:**
**Engagement with reviewer's point:**

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->
