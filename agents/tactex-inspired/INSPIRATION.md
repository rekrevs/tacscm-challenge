# TacTex-inspired house agents

Ids: `tactex-inspired-v1` (`tac_scm_gallery/tactex.py`) and
`tactex-inspired-v4-m3`, `-m4`, `-m5` (`tactex_v4.py`, `tactex_v4_models.py`,
`tactex_v4_state.py`, `demand_filter.py`).

A historical team name identifies a role model only. These agents are
independently written for the 2026 rules: they are not the TacTex team's agent,
are not endorsed by that team or its institution, and are not claimed to
behave like it.

## Published work followed

- D. Pardoe and P. Stone. *An Autonomous Agent for Supply Chain Management.*
  In Handbooks in Information Systems: Business Computing, 2009 (TacTex-06).
- D. Pardoe. *Adaptive Trading Agent Strategies Using Market Experience.*
  PhD dissertation, University of Texas at Austin, 2011 (chapters 4, 5 and 9).
- D. Pardoe and P. Stone. *TacTex-05: A Champion Supply Chain Management
  Agent.* AAAI 2006.
- D. Pardoe, P. Stone and M. VanMiddlesworth. *TacTex-05: An Adaptive Agent
  for TAC SCM.* TADA/AMEC workshop, 2006.
- D. Pardoe and P. Stone. *Predictive Planning for Supply Chain Management.*
  ICAPS 2006.
- D. Pardoe and P. Stone. *Adapting Price Predictions in TAC SCM.* AMEC
  workshop, 2007.

## Claim level

v1: publication-inspired, trained. v4: publication-inspired, trained; the
fuller reconstruction of the published design (TacTex as it played under the
2007 rules, with its learned models trained on the organiser's fields). No
exact reproduction is claimed. Retained historical TacTex binaries were
neither executed nor decompiled.

## Reconstructed versus new

- **v1** (common 2026 completion layer: `agent`, `history`, `safety`,
  `runtime`, plus the Botticelli-inspired v1 plumbing). From the papers:
  separate supply and demand managers, learned demand, supplier-price and
  offer-acceptance predictions, a ten-day rolling plan. New: the three
  compact models below, a bounded greedy planner, every limit and constant.
- **v4.** From the papers: the demand manager's bidding algorithm
  (Algorithm 1 of the dissertation) over today's and predicted RFQs under the
  factory constraint, late-as-possible greedy production, the supply manager
  with its supplier-price model and lead-time choice, an inventory threshold
  falling to zero at the end, and the learned components C2b-C8 (day factors,
  acceptance particle filter, computer price-change model, supplier model,
  component price-change model, initial-order and endgame adaptation). New:
  the transfer to the 2026 rules, the published Bayesian demand model (C1)
  written as a particle filter of the 2026 demand process
  (`demand_filter.py`, shared with DeepMaize v4), and every value the papers
  leave open (fields of `TacTexV4Parameters`, each with
  its source or reason). The dissertation's research-only methods (transfer
  and online learning across games) are not included.

## Learned components and training

- **v1** (`tactex_model_v1.json`): ridge regressions for a residual of
  five-day demand and for normalised supplier offer prices, and a monotone
  five-point offer-acceptance curve. Fitted on 24 train-only games among the
  gallery agents (a collector varied its customer prices to identify the
  curve), on engine 0.3.1 under profile `tac-scm-2026-draft2`, before the
  organiser's agent existed. Weights never change during a game.
- **v4** (`models/tactex-v4/models-{3,4,5}.json`): C3 prior and transition,
  C4 boosted stumps, C6 model tree, C7 and C8 tables, fitted offline on
  complete game records (all six seats). m3: 688 games, draft2. m4: 832
  games, draft2. m5: 1,462 games, draft2 and draft3. **All three were trained
  on fields that contained the organiser's agent**, alongside house agents
  and copies of TacTex v4 (`agents/MODELS.md`). Models are fixed during
  games and across games.

## Known limitations

- The v4 state's capacity input reads the market report's supplier
  capacity field (`mean_capacity_nano`, whole units) with a nano scaling
  (divided by 10^9), so the input is close to zero and effectively constant
  in play; the models were trained with the same input.
- m3 and m4 saw only draft2 games and are used under draft3 as trained.
- The models predict the market of their training population; behaviour
  unlike it is off-distribution for them.
- v1 was developed under draft2 and an earlier engine and plays draft3
  unchanged; its planner did not dominate its planner-free ablation. Its
  learned supplier-price model (about half the base price, fixed within a
  game) caps its RFQ reservation prices at about 0.53 x base; under the 2026
  supplier pricing that is offered only while a line is nearly uncommitted,
  and the agent orders only when offers for all four components of a
  product meet the cap on one day. It therefore never traded in any
  measured field with the v4 agents and trades in a field of v1 agents only
  while the lines stay uncommitted. Removed from the tournament lists on
  2026-10-19 (`tournament/FORMAT.md`, amendment); kept in the kit unchanged
  for local games.
- Fidelity against the papers has been checked through held-out prediction
  errors, not reviewed component by component.
