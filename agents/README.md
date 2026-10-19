# House agents

Independently written 2026 agents, each inspired by published work on a
historical TAC SCM agent, with their trained models. They are opponents to
train against and fill the tournament's seats not held by entrants.

A historical team name identifies a role model only. These agents are
reconstructions from publications under the 2026 rules: they are not the
historical teams' agents, are not endorsed by those teams or their
institutions, and are not claimed to behave like them. Each family's
`<family>-inspired/INSPIRATION.md` (for example
`tactex-inspired/INSPIRATION.md`, covering all its versions) states the
published work it follows, its claim level, what is reconstructed and what
is new, how its models were trained, and its known limitations.

## The agents

Each id is an executable after installation (`pip install -r
agents/requirements.txt ./sdk -e ./agents`); `python -m tac_scm_gallery.house
--list` lists them.

| Agent id | Role model | Claim | Models |
|---|---|---|---|
| `redagent-inspired-v1` | RedAgent | publication-inspired | none |
| `deepmaize-inspired-v1` | DeepMaize | publication-inspired | none |
| `packatac-inspired-v1` | PackaTAC | publication-inspired | none |
| `crocodile-inspired-v1` | CrocodileAgent | publication-inspired | none |
| `phantagent-inspired-v1` | PhantAgent | publication-inspired, explicitly approximate | none |
| `minnetac-inspired-v1` | MinneTAC | behaviour-informed and publication-inspired (see below) | none |
| `botticelli-inspired-v1` | Botticelli | publication-inspired | none |
| `narc-inspired-v1` | NaRC | publication-inspired | none |
| `southampton-inspired-v1` | SouthamptonSCM | publication-inspired | none |
| `tactex-inspired-v1` | TacTex | publication-inspired, trained; never trades beside the v4 agents (INSPIRATION.md); in no tournament list since 2026-10-19 | v1 (in package) |
| `socrates-inspired-v1` | Socrates | publication-inspired, trained | v1 (in package) |
| `mertacor-inspired-v1` | Mertacor | publication-inspired, trained | v1 (in package) |
| `iota-inspired-v1` | Iota | publication-inspired, train-calibrated | v1 (in package) |
| `tactex-inspired-v4-m5` | TacTex | publication-inspired, trained | models-5 |
| `tactex-inspired-v4-m4` | TacTex | publication-inspired, trained (as m5, earlier models) | models-4 (draft2 games) |
| `tactex-inspired-v4-m3` | TacTex | publication-inspired, trained (as m5, earlier models) | models-3 (draft2 games) |
| `deepmaize-inspired-v4-m2` | DeepMaize | publication-inspired, trained | models-2 (release asset) |
| `botticelli-inspired-v4-1` | Botticelli | publication-inspired (as v4, supplier offers chosen as a jointly optimal set) | fitted in game |
| `botticelli-inspired-v4` | Botticelli | publication-inspired (acceptance model fitted in game; frozen, see INSPIRATION.md; the fifth `league` seat since 2026-10-19) | fitted in game |

The v1 agents are the gallery's first published versions: a kernel per
family on a common 2026 completion layer (`agent`, `history`, `safety`,
`runtime`). The v4 agents are the strong ones: fuller reconstructions of
TacTex, DeepMaize and Botticelli at the scale of the 2026 factory, with the
learned components their papers describe. Their models were trained on
fields that contained the organiser's agent (`MODELS.md`).

TacTex v4 and DeepMaize v4 share `tac_scm_gallery/demand_filter.py`, an exact
Bayesian filter of the game's customer-demand process as the rules define it.

### MinneTAC-inspired

`minnetac-inspired-v1` was written from the published MinneTAC papers and five
neutral behavioural findings, which were cross-checked against a retained
2004 MinneTAC source release under that release's licence. It contains no
code, constants, configuration or structure from that release, and it is not
associated with or endorsed by the University of Minnesota.

## Running them

```sh
python runner/play_game.py --root 0 --out runs/h0 \
  tactex-inspired-v4-m5 deepmaize-inspired-v4-m2 botticelli-inspired-v4-1 \
  narc-inspired-v1 iota-inspired-v1 socrates-inspired-v1
```

Every id is a frozen configuration: the kernels' research overrides
(`TAC_SCM_GALLERY_*` environment variables) are cleared at start, and the v4
agents load only the model files listed in `MODELS.md`. The v4 agents need
numpy and scipy (`requirements.txt` pins the verified versions); the v1 agents
need only the SDK. In the kit's test games on a laptop (Apple M4 Max, three or
four games at once), a game of six house agents took 3 to 6 minutes. Per agent
process:

| Agent | Slowest daily decision | Start (imports, models) | Peak resident memory |
|---|---|---|---|
| TacTex v4 (m3, m4, m5) | about 1 s | about 3 s | 210 to 310 MiB |
| DeepMaize v4 m2 | about 0.6 s | about 1.5 s | 490 to 550 MiB |
| Botticelli v4 | under 0.1 s | under 1 s | 70 to 90 MiB |
| Botticelli v4-1 | about 1.3 s (one test game) | under 1 s | not measured separately (v4's state) |
| v1 agents | under 0.1 s | under 1 s | 40 to 50 MiB |

The start is the time to the first action with all six agents starting at
once; with four games starting together it reached 7 s for some agents. Both
are well inside the 15 s deadline and the startup grace (`rules/TIMING.md`).
Slower or shared CPUs take longer.

## Tests

```sh
python -m pytest agents/tests
```

## Licence

MIT, as the rest of the kit. numpy and scipy are BSD-3-Clause; their wheels
bundle OpenBLAS (BSD) and libgfortran (GPL-3 with the GCC Runtime Library
Exception).
