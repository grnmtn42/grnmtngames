# Angry Serfs

A free, single-file browser RTS: storm the same escalating fortification across
five levels, buying units and siege equipment before each battle. No build step,
no dependencies — just open `index.html` (or the hosted URL) and play.

## Play

Open `index.html` in a browser, or serve the repo root locally (see the top-level
[README](../../README.md)) and visit `/games/angry-serfs/`.

You always play the attacker. Break through the outer gate, then the keep's own
gate, and defeat the defenders — and, on level 5, the King himself, waiting in
his tower — to win. See the in-game "How to Play" screen for controls (desktop
and touch use different schemes) and rules.

It's permadeath: units that die don't come back, but survivors carry into the
next level at full health. Beat all five levels on any difficulty to submit your
run to the leaderboard — doing so also permanently unlocks jumping straight to
any level from the start screen, so you can, say, try level 5 on Impossible
without replaying 1-4 at that difficulty first.

## Leaderboard

Score, survivor count, and hero count are always tracked locally (per-browser,
via `localStorage`) — this works out of the box with zero setup. A "survivor" is
any non-siege unit still alive at the end of level 5 (siege equipment like the
ram and catapult never carries between levels anyway, and armies of one or two
big machines instead of soldiers shouldn't be able to game the stat). A "hero"
is a survivor who traces all the way back to a level-1 purchase or carryover.

The global leaderboard reuses the same Supabase project as
[Signal Lost](../signal-lost/README.md), just with its own table — there's no
need to stand up a second project if you've already wired that one up. If you
haven't:

1. Create a free [Supabase](https://supabase.com) project (or use the one
   already backing Signal Lost's leaderboard).
2. Open the SQL Editor and run:

   ```sql
   create table if not exists angry_serfs_scores (
     id uuid primary key default gen_random_uuid(),
     created_at timestamptz not null default now(),
     player_name text not null check (char_length(player_name) between 1 and 16),
     score integer not null check (score >= 0 and score <= 2000000),
     survivors integer not null default 0 check (survivors >= 0 and survivors <= 500),
     heroes integer not null default 0 check (heroes >= 0 and heroes <= 500),
     difficulty text not null default 'normal' check (difficulty in ('normal', 'hard', 'impossible')),
     victory boolean not null default false
   );

   alter table angry_serfs_scores enable row level security;

   create policy "Anyone can read angry serfs scores"
     on angry_serfs_scores for select
     using (true);

   create policy "Anyone can submit an angry serfs score"
     on angry_serfs_scores for insert
     with check (true);
   ```

3. In Project Settings → API, copy the **Project URL** and the **anon public
   key**.
4. In `index.html`, find the `LEADERBOARD_CONFIG` object (search for
   `SUPABASE_URL`) and fill in both values:

   ```js
   const LEADERBOARD_CONFIG = {
       SUPABASE_URL: 'https://xxxxxxxx.supabase.co',
       SUPABASE_ANON_KEY: 'eyJhbGciOi...'
   };
   ```

   (Already filled in with Signal Lost's project if you copied this file as-is
   — step 2 is the only thing you actually need to do in that case.)

5. Redeploy (commit + push). The leaderboard panels will now show global
   All-Time and Today's Top 10, and every submission that beats the full game
   is inserted into `angry_serfs_scores`.

**Note on abuse:** the anon key is public by design (it's meant to be embedded
in client-side code) and the table only restricts submissions by range checks
(score/survivor/hero caps, name length, a fixed difficulty enum) — a determined
user could still script fake submissions within those bounds. That's an
inherent limit of a serverless static-site leaderboard with no auth; it's fine
for a casual free game, but don't treat the board as tamper-proof.

If the Supabase config is ever blanked out, the game works exactly the same,
just with local-only leaderboards.
