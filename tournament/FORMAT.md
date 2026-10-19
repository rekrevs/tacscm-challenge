# Tournament format

How the games of an edition are composed, seeded, seated and scored. This
document is frozen with the kit when the invitation is sent; a change is a
change for the next edition (amended once in this edition, section 1). How to submit and the limits a seat plays
under are in `SUBMISSION.md`.

Every game is the game of `rules/` under profile `tac-scm-2026-draft3`, six
seats, 220 days, with the seat limits, daily deadline and startup grace of
`SUBMISSION.md` section 3.

## 1. Entrants and seats

- One agent per entrant. Each agent plays to maximise its own final cash.
- An entrant is identified by the image ID of its frozen submission
  (`SUBMISSION.md` section 1). The organiser's agent is an entrant; its
  image ID is recorded in `tournament/` before any submission is opened.
- Every seat not held by an entrant is held by a house agent of the kit
  (`agents/README.md`), by its id. No other agent plays.
- Seats are anonymous: an agent learns only its own seat (`agent-a` to
  `agent-f`), never who holds the others.

### The house agents used

| List | Agents, in order |
|---|---|
| Filler (primary table) | `tactex-inspired-v4-m5`, `deepmaize-inspired-v4-m2`, `botticelli-inspired-v4-1`, `tactex-inspired-v4-m4`, `tactex-inspired-v4-m3` |
| `strong-homogeneous` | `tactex-inspired-v4-m5` x 5 |
| `strong-mixed` | `tactex-inspired-v4-m5`, `deepmaize-inspired-v4-m2`, `botticelli-inspired-v4-1`, `tactex-inspired-v4-m5`, `deepmaize-inspired-v4-m2` |
| `league` | `tactex-inspired-v4-m4`, `tactex-inspired-v4-m3`, `deepmaize-inspired-v4-m2`, `botticelli-inspired-v4-1`, `botticelli-inspired-v4` |
| `weak` | `crocodile-inspired-v1`, `deepmaize-inspired-v1`, `iota-inspired-v1`, `socrates-inspired-v1`, `narc-inspired-v1` |

Every Botticelli seat outside the `league` list is
`botticelli-inspired-v4-1`, the kit's current Botticelli. The `league` list
holds the older TacTex models and the older Botticelli,
`botticelli-inspired-v4`, kept frozen with its known supplier-selection
limitation (`agents/botticelli-inspired/INSPIRATION.md`).

### Amendment 2026-10-19 (kit tag `v1.1`)

`tactex-inspired-v1` left the `league` and `weak` lists. Its frozen
supplier-price model limits its supplier RFQs to about half the base price,
which the 2026 suppliers offer only while a line is nearly uncommitted; in
the `league` field it never traded (no order in any measured game) and in
the `weak` field it traded only beside an entrant that leaves the supplier
lines uncommitted. A seat that never trades is not a house field. The
`league` seat goes to `botticelli-inspired-v4`, the only kit agent outside
the list that trades in that field (in the organiser's measurement, two
games per candidate on practice roots, every v1 agent won at most 17 orders
there); the `weak` seat goes to `crocodile-inspired-v1`, the most active v1
agent beside a strong or a weak entrant. The first four agents of the
`league` list, which the two-entrant challenge uses, are unchanged; in
`weak` the replacement takes the same position. The agent stays in the kit
for local games.

This amends a frozen edition: the owner decided that an inert seat is a
defect to fix in this informal edition rather than carry to the next.
Nothing else changes between `v1.0` and `v1.1`: the server, rules, house
agents, models and seat limits are as tagged in `v1.0`, so games played and
models trained against `v1.0` stand; a `v1.0` clone needs only
`git fetch && git checkout v1.1`.

## 2. Roots

A root is the 64-hex-digit seed of one game's random draws (demand,
supplier capacities, rates). Two games on the same root draw the same
world; only the agents differ.

- **Secret.** After the deadline the organiser draws a secret `S`: 32
  bytes from the operating system's random source, written as 64
  lowercase hexadecimal digits.
- **Entrant list.** At the deadline the organiser records `J`: the
  entrants' image IDs, each written `sha256:<64 hex digits>`, sorted
  ascending and joined by `,` (no spaces).
- **Derivation.** Root `i` (a non-negative integer) is
  `SHA-256("tacscm-challenge/edition-1/" + S + "/" + J + "/" + i)`, the
  digest of the ASCII string with `i` in decimal, as 64 lowercase
  hexadecimal digits. It is passed to the game runner as the root seed
  (`runner/play_game.py --seed`). Nobody knows a root before the
  deadline; `S` and `J` are published with the results, so anyone can
  recompute the roots and replay every game.
- **Indices.** Primary table: roots 0 to 15. House-field tables: roots 100
  to 111. Two-entrant challenge: roots 200 to 211.

```sh
python3 -c 'import hashlib,sys; print(hashlib.sha256(f"tacscm-challenge/edition-1/{sys.argv[1]}/{sys.argv[2]}/{sys.argv[3]}".encode()).hexdigest())' S J i
```

The local practice roots (`tacscm-challenge/local/<n>`) never equal these.

## 3. Listed order and seat rotation

A game's six agents are listed in an order `L0 .. L5`, drawn per root from
the table's base order `B0 .. B5` (sections 4 to 6):

- **Listed order.** For root `R` (its 64 hex digits), position `p` (0 to
  5, in decimal) of the base order gets the key
  `SHA-256(R + "/listed/" + p)`, as ASCII, in lowercase hexadecimal; `L`
  is the base order sorted by ascending key. In the house-field tables
  (section 5) `L0` stays the entrant and only positions 1 to 5 are sorted.
  The key depends on the position, not on the agent, so every entrant of
  a paired table meets the same order on a root, and an agent listed
  twice is two positions. Anyone can recompute it from `S` and `J`.
- **Rotation.** Rotation `r` (0 to 5) seats `L[(j + r) mod 6]` in seat `j`
  (seat 0 is `agent-a`), as `runner/paired_set.py` does.

Rotation balances seats; the draw balances the order. A rotation keeps the
agents' cyclic order (who sits next to whom), so with a fixed list the
same agents would be neighbours on every root, and any effect of who sits
next to whom would enter every root alike instead of averaging over the
roots. (In the organiser's dry run, five copies of one image listed in a
fixed order after one house agent differed by up to 3 M by listed position
on two roots; a replay did not confirm it, but a fixed order could not
have averaged it out.)

```sh
python3 -c 'import hashlib,sys; r,f=sys.argv[1],int(sys.argv[2]); print(list(range(f)) + sorted(range(f, 6), key=lambda p: hashlib.sha256(f"{r}/listed/{p}".encode()).hexdigest()))' R 0
```

prints the base positions in listed order, `L0` first (`1` instead of `0`
for a house field).

- **Root block** (primary table, two-entrant challenge): one root played in
  all six rotations, six games. Every agent of the game holds every seat
  once on that root.
- **Seat cycle** (house-field tables): six consecutive roots of the
  field's list, the `k`-th of them (`k` = 0 to 5) played once in rotation
  `k`. Every entrant holds every seat once per cycle. All entrants play the
  same (root, rotation) games against the same house agents in the same
  seats, so their comparison is paired game by game; one game per root
  leaves twelve roots for the standard error where root blocks would
  leave two.

## 4. Primary table: all entrants together

The ranking comes from games that seat the entrants together, as in the
historical finals; most of the tournament's games are spent here.

- **Up to six entrants.** One group, base order: the entrants in
  ascending order of image ID, then the first `6 - n` agents of the filler
  list; the listed order is drawn from it per root (section 3). Each of
  the 16 primary roots is a root block: **96 games**.
- **More than six entrants.** On each root, the entrants are ordered by
  `SHA-256(root + image ID)` (the root's 64 hex digits followed by the
  image ID written `sha256:<64 hex digits>`, as ASCII) and split
  into `g = ceil(n / 6)` consecutive groups whose sizes differ by at most
  one (the first `n mod g` groups are the larger); each group, in that
  order and filled from the filler list, is the base order of its listed
  order (section 3) and plays the root block. Every entrant plays every
  primary root once (six games); partners change from root to root.
  `96 g` games.
- The ranking is by the primary mean (section 7). The house agents'
  means are shown for reference and are not ranked.

## 5. Secondary tables: house fields

Each entrant alone among the five house agents of each list in section 1
(`strong-homogeneous`, `strong-mixed`, `league`, `weak`): `L0` is the
entrant, `L1 .. L5` the list in the order drawn per root (section 3), the
same for every entrant. Each field type uses the twelve
roots 100 to 111 as two seat cycles: **12 games per entrant per field
type**, 48 per entrant. Reported per field type, never mixed into the
ranking.

## 6. Two-entrant challenge

When two entrants want a direct comparison (or an edition has exactly two
entrants), they play together with four house agents: the base order is
the two entrants in ascending order of image ID followed by the first four
agents of a field list, and the listed order is drawn from it per root
(section 3). Roots 200 to 211 are root blocks, three per field type in the
order `strong-homogeneous`, `strong-mixed`, `league`, `weak`: **72
games**. Result: the paired difference A - B (section 7) over the twelve
roots, with its standard error and per-root wins, and the difference per
field type (three roots each, no standard error).

## 7. Scoring

- **Score.** An agent's final cash in a game, in M credits (millions of
  credits; the runner's microcredits divided by 10^12).
- **Root mean.** An agent's mean score over its games on one root (six in
  a root block, one in a seat cycle).
- **Mean.** The mean of the agent's root means over the table's roots.
- **Standard error, clustered by root.** The sample standard deviation of
  the root means divided by the square root of the number of roots. The
  root is the unit: games on one root share their world and are not
  independent.
- **Paired difference** of entrants A and B: per root on which both
  played (in the same games in the primary table and the challenge; in the
  same seat against the same house agents in a house field), A's root mean
  minus B's; its mean and its standard error as above. Published for every pair of entrants in every
  table.
- **Per-root wins.** On each root, the entrant with the highest root mean
  among the entrants who played that root together (in the primary table,
  its group) wins it; entrants tied exactly share it.
- **Ranking.** By primary mean, highest first. There is no rule that turns
  close means into ties: the means, standard errors, paired differences
  and wins are published, and readers judge how close is close.
- Also published per entrant and table: games played, mean default days,
  and the slowest decision.

## 8. Game counts and power

A game's final cash spreads by several M credits from root to root and
seat to seat (in strong fields a single agent's standard deviation over
games is about 5 M). Rotation removes the seat effect within a root and
the paired difference removes the root's common draw. In the organiser's
paired games the standard deviation of a per-root difference between two
agents was about 1.7 to 3.4 M with two games per root; take 2 to 2.5 M
for a root block of six games.

| Table | Games | Roots | SE of a paired difference | Difference resolved (80 %, two-sided 5 %) |
|---|---|---|---|---|
| Primary | 96 per group | 16 | about 0.5 to 0.6 M | about 1.4 to 1.8 M |
| House field, per type | 12 per entrant | 12 | about 1 to 1.5 M | about 3 to 4 M |
| Two-entrant challenge | 72 | 12 | about 0.6 to 0.7 M | about 1.6 to 2 M |

With more than six entrants (section 4), two entrants share a group
only on some roots (about three in seven at eight entrants); their paired
difference rests on those roots and its standard error is larger
accordingly, while each entrant's mean still uses all 16 roots.

Distinct designs differ by tens of M in house fields, so the secondary
tables separate them easily; the primary table carries the interaction
of entrants with each other and gets the game budget.

## 9. Faults and replays

- **Infrastructure faults are replayed** (`SUBMISSION.md` section 6): the
  game is played again on the same root, rotation and seats with the same
  images, and only the replay counts. A game is an infrastructure fault
  when it ends without a result for a reason that is not an agent's
  action: a node failure, an evicted pod, an image that cannot be pulled,
  a runner crash, a game that exceeds its wall-time limit.
- **An agent's own failures stand**: a late decision (that day defaults),
  a crash, a memory kill, a malformed message.
- A game is played at most three times (two replays); a game that still
  has no result is reported as missing, and the table says which.

## 10. Sockets

An agent must not open any network socket (`SUBMISSION.md` section 7). The
tournament runner samples the open sockets of the game's pod every second
and each seat's own sockets every five seconds and at game end. An entrant
whose seat holds a socket in any game is excluded from every table of the
edition, after the organiser has shown the entrant the record; the games
it played stand for the other agents and are not replayed.

## 11. Results

Published in `tournament/results/` after the tournament, as a Markdown
table per table type with:

- every entrant's image ID and repository digests, GPU seats marked;
- the secret `S`, the entrant list `J`, the root indices and seeds;
- the kit tag and the digests of the kit image the runner and the house
  agents ran from;
- one row per game: table, root, rotation, the agent in each seat, scores,
  default days, slowest decision, and the CPU model of the machine.

Images and seeds are kept, so any game can be played again on request.
Game logs (the runner's action transcripts) and an entrant's own stderr
are sent on request after the results.

## 12. The organiser's agent

The organiser's agent is an entrant like any other. It is not in the kit,
and its image ID is recorded before any submission is opened.
