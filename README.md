# Safety Label Action Vector Gate

Safety Label Action Vector Gate is a reusable GenLayer Intelligent Contract primitive for deciding whether a translated public safety label may be released. It extracts a canonical hazard/action vector from the sealed source and submitted translation, then binds the release decision to exact vector equality.

It does not certify product safety, translation quality beyond the declared vector, or legal compliance.

## Lifecycle

The publisher registers a label, seals its source evidence hash, and authorizes a translator and distributor. The translator submits a bounded translation and evidence hash. Each evidence hash must be the lowercase SHA-256 digest of its exact UTF-8 text, binding provenance to the sealed/submitted content. The distributor calls `assess`, which performs one nondeterministic extraction call inside a custom leader/validator boundary. The contract derives:

- `UNSUPPORTED_LOCALE` when the independent extractor says the locale cannot be assessed;
- `RELEASEABLE` when locale support is true and every normalized source/translation vector field matches;
- `HOLD_TRANSLATION` when locale support is true but any vector field differs.

A translator may correct only a held translation. Replaying the same registration, seal, or submission is idempotent; conflicting replay is rejected.

## Contract API

- `register_label(label_id, locale, source_text, translator, distributor)` — publisher-only bounded registration.
- `seal_source(label_id, source_evidence_hash)` — publisher-only source sealing.
- `submit_translation(label_id, translation_text, translation_evidence_hash)` — authorized translator submission.
- `assess(label_id)` — authorized distributor consensus assessment; repeated assessment is a no-op after a successful state transition.
- `correct_translation(label_id, translation_text, translation_evidence_hash)` — authorized translator correction from `HOLD_TRANSLATION`.
- `read_status(label_id)` — deterministic oracle view for downstream release workflows.
- `read_action_vectors(label_id)` — source/translation vectors and derived status.
- `read_gate(label_id)` — lifecycle and evidence-hash readback.

## Consensus and trust boundary

The assessment prompt explicitly delimits the target locale, source text, and translation text as untrusted data and tells the extractor to ignore embedded instructions. The leader and each validator independently run the same extraction against the same primitive inputs. The validator rejects malformed output or any difference in the stable decision fields: `locale_supported` and equality of the independently extracted normalized vectors. The returned `vectors_equal` field is schema-validated but never trusted: the contract recomputes it as exact equality of the normalized seven-field source and translation vectors, so an inconsistent boolean can never release a mismatched translation. Every vector field is schema-validated, canonicalized, stored, and bound to that deterministic decision; validators do not require independently generated wording to match field-for-field. No leader-only JSON shape check can pass. Only after consensus does the deterministic contract derive and store the status.

## Studionet E2E matrix

All consensus scenarios use one clean deployment. Each label is independently registered, sealed, submitted, and assessed before its readback is recorded; a held label is corrected and assessed once more. Deterministic authorization and transition failures use a revert path and do not consume a consensus assessment.

| ID | Exact input fixture | Pre-state | Expected result/readback | Evidence |
| --- | --- | --- | --- | --- |
| S01 | `samples/source_en.txt` + `samples/translation_es.txt` | sealed, submitted | `RELEASEABLE`; equal stored vectors | finalized consensus tx + `read_action_vectors` |
| S02 | source + translation with severity `low` | sealed, submitted | `HOLD_TRANSLATION`; severity differs | finalized consensus tx + status/vector readback |
| S03 | source + translation with actor `distributor` | sealed, submitted | `HOLD_TRANSLATION`; actor differs | finalized consensus tx + status/vector readback |
| S04 | source + translation omitting rinse action | sealed, submitted | `HOLD_TRANSLATION`; mandatory action differs | finalized consensus tx + status/vector readback |
| S05 | source + translation negating a prohibited action | sealed, submitted | `HOLD_TRANSLATION`; prohibited action differs | finalized consensus tx + status/vector readback |
| S06 | source + translation with casing/order/whitespace changes only | sealed, submitted | `RELEASEABLE`; canonical vectors equal | finalized consensus tx + vector readback |
| S07 | source containing an embedded instruction | sealed, submitted | decision follows extracted vector, never embedded command | finalized consensus tx + vector readback |
| S08 | locale `zz-ZZ` with the same two text fixtures | sealed, submitted | `UNSUPPORTED_LOCALE` if independent extractor cannot assess locale | finalized consensus tx + status readback |
| S09 | translator calls `seal_source`; distributor calls `register_label` | registered or pre-seal | deterministic `ONLY_PUBLISHER` revert; no state change | revert evidence + gate readback |
| S10 | empty source, pre-seal assessment, or correction while not held | fresh/registered | deterministic `INVALID_TEXT`, `TRANSLATION_NOT_SUBMITTED`, or `CORRECTION_NOT_ALLOWED`; no state change | revert evidence + gate readback |
| S11 | repeat identical registration, seal, and submission | same lifecycle inputs | no duplicate or state drift; one stored label | successful writes + one authoritative gate readback |
| S12 | S04 held translation, then corrected translation | `HOLD_TRANSLATION` | `TRANSLATION_SUBMITTED` after correction, then `RELEASEABLE` after reassessment | correction and finalized assessment txs + final vector readback |
| S13 | source + translation changing only condition | sealed, submitted | `HOLD_TRANSLATION`; condition differs | finalized consensus tx + status/vector readback |
| S14 | source + translation changing only time | sealed, submitted | `HOLD_TRANSLATION`; time differs | finalized consensus tx + status/vector readback |
| S15 | source + translation changing only hazard | sealed, submitted | `HOLD_TRANSLATION`; hazard differs | finalized consensus tx + status/vector readback |

The final evidence record must add the exact contract/deployer addresses, transaction hashes, finalized `SUCCESS` receipts, validator agreement for consensus rows, Explorer URLs, and authoritative readbacks. `S08` is an expected semantic outcome, not an assumption; if the live extractor instead treats the locale as supported, the recorded live result remains the source of truth and the scenario is marked according to the contract output.

RPC plan: deploy once; reuse the deployment and independent label setup; submit one transaction per logical write; perform one bounded receipt/status poll per transaction; cache each terminal receipt and Explorer URL; use one authoritative gate/vector readback per scenario; retry only after checking hash, nonce, Explorer, and pre-state.

## Consensus Binding Matrix

| Field | Source | Stored? | Downstream effect | Validator check | Binding mode | Differential test |
| --- | --- | --- | --- | --- | --- | --- |
| `locale_supported` | independent extractor | yes | `UNSUPPORTED_LOCALE` vs normal gate | exact equality | exact decision binding | unsupported-locale case in Stage 6 matrix |
| `vectors_equal` | normalized source/translation vectors; returned flag is untrusted | no (derived) | `RELEASEABLE` vs `HOLD_TRANSLATION` | exact deterministic equality | exact decision binding | releaseable plus contradictory-flag and S02-S07/S13-S15 differentials |
| evidence hashes | exact UTF-8 source/translation text | yes | provenance readback | deterministic SHA-256 relation | exact provenance binding | mismatch reverts |
| hazard | source/translation label text | yes | release gate | included in deterministic normalized vector equality | stored vector plus derived decision binding | S15 hazard-only differential |
| severity | source/translation label text | yes | release gate | included in deterministic normalized vector equality | stored vector plus derived decision binding | `high` vs `low` test |
| actor | source/translation label text | yes | release gate | included in deterministic normalized vector equality | stored vector plus derived decision binding | actor swap |
| mandatory actions | source/translation label text | yes | release gate | included in deterministic normalized vector equality | stored vector plus derived decision binding | omission test |
| prohibited actions | source/translation label text | yes | release gate | included in deterministic normalized vector equality | stored vector plus derived decision binding | negation/prohibited-action test |
| condition | source/translation label text | yes | release gate | included in deterministic normalized vector equality | stored vector plus derived decision binding | condition change |
| time | source/translation label text | yes | release gate | included in deterministic normalized vector equality | stored vector plus derived decision binding | timing change |
| rationale/model metadata | none | no | none | not applicable | excluded from state | not applicable |

## Local verification

The test suite uses current `genlayer-test` Direct Mode fixtures and mocks the nondeterministic LLM call. Run:

```powershell
genvm-lint check contracts/safety_label_action_vector_gate.py
pytest -q
```

The Studionet E2E matrix must replay the same lifecycle using `samples/source_en.txt` and `samples/translation_es.txt`, and must include releaseable, hazard-only change, severity downgrade, actor swap, omission, negation, stylistic change, prompt injection, unsupported locale, condition change, time change, evidence-hash mismatch, authorization, invalid transition, duplicate replay, and correction scenarios. Negative deterministic authorization/input failures should be recorded as revert evidence; consensus scenarios require finalized success and authoritative readback.

## Network target

Deployment target is GenLayer Studionet (chain ID `61999`). The final deployed source matches the contract artifact after line-ending normalization.

## Live deployment

- Contract: [`0xcD7D57f9f951c4E37d689Bf0b987853F819A9FDC`](https://explorer-studio.genlayer.com/address/0xcD7D57f9f951c4E37d689Bf0b987853F819A9FDC)
- Deploy transaction: [`0xdbfb9e137821c165f06eeaadd26024deb6ce159e8b55b0c02a86737f4b9181ee`](https://explorer-studio.genlayer.com/tx/0xdbfb9e137821c165f06eeaadd26024deb6ce159e8b55b0c02a86737f4b9181ee)
- Deployer/publisher: `0x5B6465eD6Ec0F2F7944b8279E8872123bf9b545a`
- Contract source SHA-256: `B1C11C60E703ED1EB29F60227E0429F341AADBE487D62D1D6F41521BACD7C926`
- Deployment receipt: `FINALIZED`, leader `SUCCESS`, `MAJORITY_AGREE` (5/5)

The complete judge-visible deployment and E2E record is in [`verification/deployment.json`](verification/deployment.json) and [`verification/e2e-matrix.md`](verification/e2e-matrix.md). All 15 matrix scenarios have finalized transaction evidence and authoritative readbacks on this address. The final live statuses are: S01/S06/S07/S12 `RELEASEABLE`; S02-S05/S13-S15 `HOLD_TRANSLATION`; S08 `UNSUPPORTED_LOCALE`; S09-S11 remain in their safe pre-assessment/submission states after expected negative or replay checks.
