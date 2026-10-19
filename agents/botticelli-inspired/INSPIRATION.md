# Botticelli-inspired house agents

Ids: `botticelli-inspired-v1` (`tac_scm_gallery/botticelli.py`),
`botticelli-inspired-v4` (`botticelli_v4.py`, built on `botticelli_v3.py` and
`botticelli.py`) and `botticelli-inspired-v4-1` (`botticelli_v4_1.py`: v4 with
a corrected supplier-offer selection).

A historical team name identifies a role model only. These agents are
independently written for the 2026 rules: they are not the Botticelli team's
agent, are not endorsed by that team or its institution, and are not claimed
to behave like it.

## Published work followed

- M. Benisch, A. Greenwald, I. Grypari, R. Lederman, V. Naroditskiy and
  M. Tschantz. *Botticelli: A Supply Chain Management Agent Designed to
  Optimize under Uncertainty.* ACM SIGecom Exchanges 4(3), 2004
  (doi:10.1145/1120701.1120706).
- S. Bell, M. Benisch, M. Benthall, A. Greenwald and M. C. Tschantz.
  *Multi-period Online Optimization in TAC SCM: The Supplier Offer Acceptance
  Problem.* TADA workshop, 2004.

## Claim level

Publication-inspired (v1, v4 and v4-1). v4 adds the paper's in-game
acceptance model; v4-1 corrects v4's supplier-offer selection. No exact reproduction is claimed; the historical mathematical program,
solver and configuration are not reproduced.

## Reconstructed versus new

- **v1** (common 2026 completion layer). From the papers: nested delivery,
  expected production and bidding; offers scheduled by expected quantity;
  scenario lookahead with a shared first-stage action; triangular reservation
  of future factory capacity; marginal value of complementary supplier offers.
  New: a ten-day tree of four scenarios with fixed probabilities, a bounded
  enumeration with a greedy fallback, every coefficient and limit.
- **v4** = the same design sized to the 2026 factory (v3, not published
  separately) with the paper's section 5.1 model: per product a linear
  price-to-win-probability curve fitted each day by least squares to the
  previous day's minimum price (probability 1), maximum price (0) and the
  agent's own mean offer price and win ratio for each of the last five days;
  prices chosen by expected value over a discretised set; bid cover by
  components expected in time and by the triangle heuristic for cycles.
  New: 20 price points, expected profit over the agent's own unit cost, the
  fallback before a product has a fitted curve.
- **v4-1** = v4 with the supplier-offer acceptance of the second paper taken
  as one choice of a set: it accepts the subset of the candidate offers that
  maximises v4's own scenario plan value within the position and cash
  limits, by enumerating every subset when the planner's operation budget
  covers it (up to 15 offers) and otherwise by backward elimination from the
  full set (drop the offer whose removal raises the plan value most, while
  one does). Everything else is v4. New: the exact/elimination split.

## Learned components and training

v1 has none. v4 and v4-1 fit their acceptance curve during each game from
that game's public price reports and their own offers; they ship no model
file and carry nothing between games, so they were not trained on any field.

## Known limitations

- The published linear model puts the win probability at zero at yesterday's
  maximum price, but in the 2026 market about a third of RFQs get no
  competing bid; v4 therefore underrates high prices and sells cheaper than it
  could. Kept as published.
- v4 chooses supplier offers by each offer's marginal plan value with all
  other offers kept (the v1 rule). With several offers for the same
  component, each is redundant given the others and all are refused; in the
  kit's test games v4 held no CPUs on 25 to 50% of days 20-200 and stopped
  production for up to 60 days. Fixed in `botticelli-inspired-v4-1` (above);
  v4 is kept frozen as it was (it fills the fifth `league` seat since the
  2026-10-19 amendment of `tournament/FORMAT.md`). In the organiser's paired games (draft3, 16
  roots, two seats each, v4-1 and v4 in the same seat against the same
  opponents) v4-1 scored +7.0 M (SE 1.1) more in a field of five v4 copies
  (10.3 to 17.3 M) and +3.9 M (SE 0.6) more beside TacTex v4 m5 x2,
  DeepMaize v4 m2 x2 and Botticelli v4 (2.1 to 6.0 M); its days without
  CPUs fell from 59 to 2 and its longest production gap from 19 to 2 days
  on average in that field.
- v4-1 enumerates exactly only up to 15 offers; above that its elimination
  finds a local optimum of the same objective.
- The same selection rule serves v1 and the kernels built on its plumbing
  (NaRC, Southampton, Socrates, Mertacor, Iota, TacTex v1), where it rarely
  bites at v1's 10-unit RFQs.
- v1 was developed under draft2 on an earlier engine and plays draft3
  unchanged; its limits are small for the 2026 factory.
