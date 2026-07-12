# PR Response Doc — CineLog Watchlist Feature

## AI Usage
I didn't rely on claude for this assignment instead I relied on gemini in order to save claude credits. I asked gemini why the join between the film model and the watchlist model only return the watchlist model keys. It then told me because the code is missing the relationship mapping between the two and from that I was able to fix the issue.

## Comment 1 — Rename
**What I did:** Renamed `save_to_watchlist()` -> `add_to_watchlist` and updated all call site by looking into the traces of where the original `save_to_watchlist()` is called in. 


**How I verified:** Traced all the call sites and ensured they were all updated. Also ran the unit test to ensure its working properly.

## Comment 2 — Deduplication
**What I did:** Referenced `collection_service.py` deduplication logic and implemented the same logic in watchlist_service.py. So now add_to_watchlist will raise a error if user attempts to add film that is already on their watchlist.


**How I verified:** Added a new unit test to lock this behavior and also verify its working properly. Also ran the unit test to ensure the deduplication logic is correct in `test_watchlist.test_add_to_watchlist_duplicate_raises`.

## Comment 3 — Missing test
**What I did:** Referenced `test_collection` to write new `test_watchlist` since the behavior is similar this was a simple copy and paste and rewrite lines of the `test_collection` so they work with the `watchlist_service`.


**How I verified:** Ran both unit test to ensure the renaming and deduplication changes all behave how the new unit test expects them to be. Ran the new unit test to confirm code is correct.

## Comment 4 — Default visibility
**My position:** I on the side that defaulting the users watchlist public view to `true` is better.


**Reasoning:** Defaulting `true` is better than `false` here because are making it easiers for users to share their watchlist optimizing the friction for the users to share. i.e. user won't have to turn it set the watchlist view setting to `true` if they want to share. 


**Tradeoff acknowledged:** On the other hand if a user will want to create a watchlist thats private they will need to manually set the public setting to `false`. More friction for those users, but its likely negligible since majority of users will want to share thier watchlist and we are not stopping any user from making their watchlist private its just one extra step.

## Comment 5 — Sort order
**My position:** I'm on the opposite side I believe that most users will actually want see it in alphabetical order. 


**Reasoning:** Its easier to see what is in the watchlist if its in alphabetical order. Since the whole point of a watchlist is so that users can easily save what they want to watch for their future selves to reference.


**Engagement with reviewer's point:** Ordering by date added is not as helpful. If anything we should be ordering by last watched on their watchlist if we want a temporal ordering filter. Therefore I don't think we should change the sort order to date_added we could introduce a new column, but that will take more coordination with the database team and the rest of the team.

## Comment 6 — Rebase
**What conflicted:** A conflict between the `WatchlistEntry` model from main compared to my branch.


**How I resolved it:** I looked at both and deteremined that my fix is correct because without the relatinship between `WatchlistEntry` and `Film` the function `watchlist_service.get_watchlist` will always fail.


**How I verified no conflict remains:** I used the merge conflict resolver built into VS code which covers all the merge conflicts produced. In this case only one conflict and it was easy to resolve.

## PR Description

In this PR we added watchlist feature, allowing users to add existing films to their watch list.

Two Design Decisions: 

1. The watch list is sorted in alphabetical order to make it easier to identify / search for the film they want to watch later in the watchlist. 
2. The visibility of the watchlist is set to public by default to allow users to share their watchlist right away instead of having to enable it to be shared.

How to test:

Unit Test: the test already lock in all existing behavior implemented in this PR. So future development should run the unit test to ensure no behaviors have changed that are unintentional
run `pytest test/ -v`

Manual Test:
1. Create a `test_user`
2. Send a curl request to create a `watchlist`
3. Add films to the watch list
4. Add the same film to the watch list (test the duplicate behavior)
5. Check the `public` field of the watchlist see it default to `true`

![git log](./Screenshot%202026-07-12%20at%2016.46.40.png)