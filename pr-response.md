# PR Response Doc

## Comment 1 — Use project naming conventions for watchlist service functions
- **What I changed:** Renamed the watchlist write function to `add_to_watchlist()` and used the same `verb_to_noun` pattern used in `collection_service.py`.
- **Why:** The project convention is explicit in `CONTRIBUTING.md`, and matching that convention keeps service APIs predictable.

## Comment 2 — Prevent duplicate watchlist entries
- **What I changed:** Added `AlreadyInWatchlistError`, added deduplication checks in `add_to_watchlist()`, and returned HTTP `409` from the watchlist add endpoint when duplicates are attempted.
- **Why:** Watchlist entries should be unique per `(user_id, film_id)` to avoid duplicated state and inconsistent API behavior.

## Comment 3 — Add test coverage for missing watchlist edge cases
- **What I changed:** Added `tests/test_watchlist.py` covering happy path creation, duplicate handling, nonexistent film IDs, newest-first sort order, and remove behavior.
- **Why:** This aligns watchlist test depth with existing collection test patterns and protects expected behavior during refactors.

## Comment 4 — Design decision: default `public` visibility
- **Decision:** Kept default visibility as `public=True`.
- **Reasoning:** CineLog is positioned as a community film app, and public-by-default supports discovery and social interaction without requiring extra opt-in steps for every entry. The tradeoff is privacy: some users may not expect public visibility on first use. To balance this, the endpoint now accepts a `public` request parameter so callers can explicitly set visibility per entry.

## Comment 5 — Design decision: watchlist sort order
- **Decision:** Returned watchlist entries by `date_added DESC` (newest first) instead of alphabetical title order.
- **Reasoning:** A watchlist behaves more like a recency queue of intent than a catalog. Newest-first keeps recently saved films visible and matches the existing `get_collection()` ordering pattern for consistency across list features.

## Comment 6 — Rebase conflict from film ID refactor (int → UUID)
- **What I changed:** Implemented watchlist models/services/routes using UUID-safe `String(36)` IDs and UUID-shaped nonexistent IDs in tests.
- **Why:** Main now uses UUID film IDs, so watchlist code had to follow the same schema to avoid runtime type mismatch and lookup bugs after rebasing.
