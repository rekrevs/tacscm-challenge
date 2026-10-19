# DeepMaize-inspired house agents

Ids: `deepmaize-inspired-v1` (`tac_scm_gallery/deepmaize.py`) and
`deepmaize-inspired-v4-m2` (`deepmaize_v4.py`, `deepmaize_v4_forecast.py`,
built on `deepmaize_v3.py`, `deepmaize.py` and `demand_filter.py`).

A historical team name identifies a role model only. These agents are
independently written for the 2026 rules: they are not the DeepMaize team's
agent, are not endorsed by that team or its institution, and are not claimed
to behave like it.

## Published work followed

- C. Kiekintveld, M. P. Wellman, S. Singh and V. Soni. *Value-Driven
  Procurement in the TAC Supply Chain Game.* ACM SIGecom Exchanges 4(3),
  2004.
- J. Estelle, Y. Vorobeychik, M. P. Wellman, S. Singh, C. Kiekintveld and
  V. Soni. *Strategic Procurement in TAC/SCM: An Empirical Game-Theoretic
  Analysis.* TADA workshop, 2004.
- C. Kiekintveld, J. Miller, P. R. Jordan, L. F. Callender and M. P. Wellman.
  *Forecasting Market Prices in a Supply Chain Game.* Electronic Commerce
  Research and Applications, 2009 (doi:10.1016/j.elerap.2008.11.005); v4
  only.

## Claim level

v1: publication-inspired, no learned parts. v4: publication-inspired, with the
learned price forecasts of the 2009 paper. No exact reproduction is claimed;
no DeepMaize source, parameters or data were available or used.

## Reconstructed versus new

- **v1** (common 2026 completion layer). From the papers: a dated reference
  inventory trajectory (outstanding orders, expected use, a buffer),
  procurement that closes dated deficits, downstream value propagated from
  product revenue and avoided penalties to components, feedback between
  otherwise separate decisions. New: the fourteen-day horizon, the
  proportional-plus-trend controllers, all gains and limits; customer prices
  from recent public prices (the learned forecasts were deferred).
- **v4** = the same design sized to the 2026 factory (v3, not published
  separately) plus the 2009 paper's forecasts where v3 used heuristics:
  Pr(win | bid) per PC type from relative historical nearest neighbours
  (k = 18) with online affine calibration on the agent's own recent bids;
  component prices by interpolation of its own recent quotes plus a learned
  residual tree. New: the bid optimiser (expected profit over forecast cost;
  the paper gives none), the feature bound, the calibration grid, the demand
  estimate from a particle filter of the 2026 demand process, a database
  thinned to every third day, and the other choices listed in the module
  docstrings.

## Learned components and training

v1 has none. v4 m2 (`models/deepmaize-v4-m2/`): the nearest-neighbour database
of Pr(win | bid) curves per PC type and market state (`pc-database.npz`,
210 MB, a release asset) and the component-price residual trees
(`component-residual.json`), fitted on 1,462 complete game records (150 more
held out) under `tac-scm-2026-draft2` and `draft3`. **They were trained on
fields that contained the organiser's agent**, and the curves are built from
all six seats' bids, the organiser's agent's included. The calibration adapts
within a game; nothing is carried between games.

## Known limitations

- The forecast's capacity input reads the market report's supplier
  capacity field (`mean_capacity_nano`, whole units) with a nano scaling
  (divided by 10^9), so the input is close to zero and effectively constant
  in play; the models were trained with the same input.
- The database describes the market of its training population; other
  behaviour is off-distribution. The online calibration often sits at the
  bounds of its grid.
- v4 keeps a component buffer of about 15,000 to 18,000 units through the
  second half and does not run it down: it ends every game with that stock
  unused (several million credits at base prices).
- In the organiser's league-field games of 2026-10-19 (practice roots), on
  one root in 5 of 14 games v4 traded on only 11-15 % of days and ended near
  -5 M, while in the other 9 games on the same root it traded on 93-95 % of
  days; the cause is being investigated. An entrant who sees this in a local
  game has not found a fault of their own.
- v1 was developed under draft2 on an earlier engine and plays draft3
  unchanged; its limits are small for the 2026 factory.
