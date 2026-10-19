# House agent models

The learned parts of the house agents and how they were trained. Every file
is listed with its SHA-256; `python -m tac_scm_gallery.house --check-models`
verifies the v4 model directory.

## Where the files are

| Agent | Files | In the repository |
|---|---|---|
| `tactex-inspired-v1` | `tac_scm_gallery/tactex_model_v1.json` (1.6 KB) | yes (package data) |
| `socrates-inspired-v1` | `tac_scm_gallery/socrates_model_v1.json` (21 KB) | yes (package data) |
| `mertacor-inspired-v1` | `tac_scm_gallery/mertacor_model_v1.json` (7 KB) | yes (package data) |
| `iota-inspired-v1` | `tac_scm_gallery/iota_model_v1.json` (0.7 KB) | yes (package data) |
| `tactex-inspired-v4-m3` | `models/tactex-v4/models-3.json` (5.2 MB) | yes |
| `tactex-inspired-v4-m4` | `models/tactex-v4/models-4.json` (5.5 MB) | yes |
| `tactex-inspired-v4-m5` | `models/tactex-v4/models-5.json` (6.5 MB) | yes |
| `deepmaize-inspired-v4-m2` | `models/deepmaize-v4-m2/component-residual.json` (13 KB) | yes |
| `deepmaize-inspired-v4-m2` | `models/deepmaize-v4-m2/pc-database.npz` (210 MB) | **no: release asset** |

`botticelli-inspired-v4` fits its acceptance model during each game and ships
no file; the other v1 agents have no learned parts.

The v4 agents read `agents/models/` (with the agents installed in editable
mode, `pip install -e ./agents`, or run from a checkout) or the directory named
by `TAC_SCM_HOUSE_MODELS`. A v4 agent whose model file is missing exits at
start with a message naming the file; it never plays untrained.

## Downloading the DeepMaize database

`pc-database.npz` is larger than a repository file should be and is published
as a release asset of the kit's tag `v1.0` (unchanged in `v1.1`):

<https://github.com/rekrevs/tacscm-challenge/releases/download/v1.0/pc-database.npz>

From the root of the kit:

```sh
curl -fL -o agents/models/deepmaize-v4-m2/pc-database.npz \
  https://github.com/rekrevs/tacscm-challenge/releases/download/v1.0/pc-database.npz
```

Then check it:

```sh
shasum -a 256 agents/models/deepmaize-v4-m2/pc-database.npz
# d5059386e7fc232700c839bf73be3f212ad266cd0b01559e79b62144935471ca
python -m tac_scm_gallery.house --check-models
```

The path is git-ignored. If the download does not work for you, ask the
organiser for the file.

## SHA-256

| File | SHA-256 |
|---|---|
| `tac_scm_gallery/tactex_model_v1.json` | `d3d6dbef5890ee167e17410f8c5927ae5b456233cdee2e2f834e7fb446c265db` |
| `tac_scm_gallery/socrates_model_v1.json` | `5f455c1c0a046dd781fd2a55c0422ca286ea6827d2e6136d3a28d51996dba00c` |
| `tac_scm_gallery/mertacor_model_v1.json` | `87f861b9f3cb550f9936f1a3a133d1abecd5ad6bd92e153265a2be2351f6b3a1` |
| `tac_scm_gallery/iota_model_v1.json` | `ed6c3eed7b5bf7967d83e630185fc33d6fe503ce1ae2c8bd64b8ff6249416585` |
| `models/tactex-v4/models-3.json` | `2c523979199cd9153327c4841721996bb09ca6905e306b085c078a32818a4eec` |
| `models/tactex-v4/models-4.json` | `243b64397a031b0c5444e212d1594eac288a1212224945dd262da575837ec04d` |
| `models/tactex-v4/models-5.json` | `31b65521a2af430aec921d06730e5da8c75bb1be1dbd051f258abe67c6b31ef2` |
| `models/deepmaize-v4-m2/component-residual.json` | `ba7bf5d825d0f12ab7d53d943524479d182578e2f240a9ef0672dd109e3da572` |
| `models/deepmaize-v4-m2/pc-database.npz` | `d5059386e7fc232700c839bf73be3f212ad266cd0b01559e79b62144935471ca` |

## Training cards

The models were trained on records of complete games played on this engine:
every seat's daily observations and committed actions (the runner's
`TAC_SCM_TRAINING_RECORD`). They are published as trained and are not
retrained for the challenge.

- **v1 models (TacTex, Socrates, Mertacor, Iota).** Fitted on games among the
  gallery agents themselves, before the organiser's agent existed, on an
  earlier engine release with the same game; they ship inside the package.
- **TacTex v4 models-3.** 688 games under `tac-scm-2026-draft2`: fields of
  house agents, of copies of TacTex v4, and fields containing the organiser's
  agent.
- **TacTex v4 models-4.** 832 games under `tac-scm-2026-draft2`, from a
  broader population of the same kinds of fields.
- **TacTex v4 models-5.** 1,462 games under `tac-scm-2026-draft2` and
  `tac-scm-2026-draft3`, adding draft3 fields of the strongest house agents
  and the organiser's agent.
- **DeepMaize v4 models-2.** The nearest-neighbour database of Pr(win | bid)
  curves per PC type and historical market state, and the component-price
  residual trees, fitted on 1,462 games (150 more held out) under
  `tac-scm-2026-draft2` and `tac-scm-2026-draft3`. The curves are built from
  all six seats' bids, including the organiser's agent's.

**Every v4 model was trained on fields that contained the organiser's agent.**
The models therefore predict the market of that population; behaviour unlike
it is off-distribution for them. Models m3 and m4 saw only draft2 games; under
draft3 (no day-0 customer RFQs, offers on the issue day only, delivered
supplier orders retired, the supplier RFQ limit enforced) they are used as
trained.

Each file's `training` field states its game count and profiles. The training
programs and records are not part of this edition of the kit.
