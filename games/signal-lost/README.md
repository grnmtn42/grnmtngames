# Signal Lost

A free, single-file browser RPG: fight your way across an open, fog-of-war frontier
to the sound of a synthesized psychedelic-western trip-hop score. No build step,
no dependencies — just open `index.html` (or the hosted URL) and play.

## Play

Open `index.html` in a browser, or serve the repo root locally (see the top-level
[README](../../README.md)) and visit `/games/signal-lost/`.

**Controls**
- `WASD` — move
- Left click / `F` — sword swipe
- Right click / `R` — throw dagger (light ranged)
- `E` — exploding energy burst (30 PWR)
- `Shift` — dash (brief dodge/i-frames)
- `Spacebar` — hold shield (blocks projectiles/arrows from the front)
- `1` / `2` — instant-buy weapon/armor upgrades
- `Esc` — open/close the menu

Fully playable without a mouse: `F`/`R` aim wherever you're facing (your last
movement direction) instead of at the cursor. Also playable on mobile/touch
browsers via an on-screen joystick and action buttons, though — as with any
touchscreen action game — it won't be as precise as keyboard + mouse.

Relics grant a random permanent buff for the run (max HP/PWR, attack, speed, crit
chance, or gold find) on top of score — so it's worth detouring for them. Beyond
the open frontier, a labyrinthine cave and a mountain trail each guard a boss and
a piece of your crashed ship — recover all six ship parts to face a final boss
and beat the game.

It's permadeath: when you die, your run's Score / Kills / Relics are shown and you
can submit them to the leaderboard. The leaderboard marks any run that beat the game
with a 🏆 next to the name.

## Leaderboard

Scores, enemies killed, and relics collected are always tracked locally
(per-browser, via `localStorage`) — this works out of the box with zero setup.

To make the leaderboard **global** (shared across everyone who plays), wire up a
free [Supabase](https://supabase.com) project:

1. Create a free Supabase project.
2. Open the SQL Editor and run:

   ```sql
   create table if not exists scores (
     id uuid primary key default gen_random_uuid(),
     created_at timestamptz not null default now(),
     player_name text not null check (char_length(player_name) between 1 and 16),
     score integer not null check (score >= 0 and score <= 5000000),
     enemies_killed integer not null default 0 check (enemies_killed >= 0 and enemies_killed <= 100000),
     relics_collected integer not null default 0 check (relics_collected >= 0 and relics_collected <= 35),
     victory boolean not null default false
   );

   alter table scores enable row level security;

   create policy "Anyone can read scores"
     on scores for select
     using (true);

   create policy "Anyone can submit a score"
     on scores for insert
     with check (true);
   ```

3. In Project Settings → API, copy the **Project URL** and the **anon public key**.
4. In `index.html`, find the `LEADERBOARD_CONFIG` object (search for `SUPABASE_URL`)
   and fill in both values:

   ```js
   const LEADERBOARD_CONFIG = {
       SUPABASE_URL: 'https://xxxxxxxx.supabase.co',
       SUPABASE_ANON_KEY: 'eyJhbGciOi...'
   };
   ```

5. Redeploy (commit + push). The leaderboard panels will now show global All-Time
   and Today's Top 10, and every submitted run is inserted into your `scores` table.

**Note on abuse:** the anon key is public by design (it's meant to be embedded in
client-side code) and the table only restricts submissions by range checks
(score/kill/relic caps, name length) — a determined user could still script fake
submissions within those bounds. That's an inherent limit of a serverless
static-site leaderboard with no auth; it's fine for a casual free game, but don't
treat the board as tamper-proof.

If you never fill in the Supabase config, the game works exactly the same, just
with local-only leaderboards.
