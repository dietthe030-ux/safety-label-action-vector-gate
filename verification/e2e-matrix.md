# Studionet E2E matrix

All rows below ran against contract `0xcD7D57f9f951c4E37d689Bf0b987853F819A9FDC`, source SHA-256 `B1C11C60E703ED1EB29F60227E0429F341AADBE487D62D1D6F41521BACD7C926`, on Studionet chain `61999`. Consensus assessment/reassessment transactions are finalized with `MAJORITY_AGREE` and a successful leader unless marked as an expected deterministic negative. Every row has an authoritative readback; transaction links use the public Studionet Explorer.

| ID | Live result | Consensus transaction | Readback |
| --- | --- | --- | --- |
| S01 | `RELEASEABLE` | [0x0ec5808e...](https://explorer-studio.genlayer.com/tx/0x0ec5808e2ad3571fbbd646f5e56f764ccd15ceef67d455c5ac97bedbcf4f8dd6) | Equal stored vectors; assessed |
| S02 | `HOLD_TRANSLATION` | [0x3a3b7804...](https://explorer-studio.genlayer.com/tx/0x3a3b7804e931d60c7627fecd65f46522b412d337cbeab6e72c7e7f18d77a3546) | Severity `unspecified` vs `low risk`; assessed |
| S03 | `HOLD_TRANSLATION` | [0x886b31b6...](https://explorer-studio.genlayer.com/tx/0x886b31b63f7929387ec5a69c7f0b87d18cd27dd21de018e0af11c7c89c619540) | Actor/vector differential stored |
| S04 | `HOLD_TRANSLATION` | [0xa3b52e27...](https://explorer-studio.genlayer.com/tx/0xa3b52e27379141effd72d064406f431cbd4f1f2eea0a6f5b6d8ac2c2e02f3d61) | Mandatory-action omission held |
| S05 | `HOLD_TRANSLATION` | [0x9d891208...](https://explorer-studio.genlayer.com/tx/0x9d891208950050fea8627aa26bf19785a9260fe4bbc31d055587a1b72a8ee51a) | Prohibited-action differential held |
| S06 | `RELEASEABLE` | [0xc3624520...](https://explorer-studio.genlayer.com/tx/0xc3624520a786015930db0742d473ddce93af5f52280a3676d5e98a9e79f914b5) | Casing/order/whitespace canonicalized equal |
| S07 | `RELEASEABLE` | [0xcda7ddfa...](https://explorer-studio.genlayer.com/tx/0xcda7ddfa2b21b6a62a3b22d10d47fcea52c151f3f2eff893450dc4e6121851c0) | Embedded instruction ignored |
| S08 | `UNSUPPORTED_LOCALE` | [0xcc40c28c...](https://explorer-studio.genlayer.com/tx/0xcc40c28ca4b95bd3973c33380858a1555e864daa744779890d0f4e287aa1cbeb) | `zz-ZZ` readback is unsupported |
| S09 | Safe `TRANSLATION_SUBMITTED` | [0xaf28ddb4...](https://explorer-studio.genlayer.com/tx/0xaf28ddb48af0e28baa06c13ff8b087e43cc1d54f5d8dbbe96410bb7d5737aa39) | Four unauthorized calls rejected; final gate unassessed and vectors empty |
| S10 | Safe `TRANSLATION_SUBMITTED` | [0xee407620...](https://explorer-studio.genlayer.com/tx/0xee4076203f234ba3e3aa3d7449d3c7a854ff31ee3ae134603797eb290a7ff112) | Invalid input, pre-submit assess, and correction-not-held rejected; no state drift |
| S11 | Safe `TRANSLATION_SUBMITTED` | [0x258e13ba...](https://explorer-studio.genlayer.com/tx/0x258e13bad2c62e5ac52cd60d0b16f158c29bd382d0815b3dc655f1227d229a09) | Registration, seal, and submission replays caused no duplicate or drift |
| S12 | `RELEASEABLE` | [0x986b6e81...](https://explorer-studio.genlayer.com/tx/0x986b6e81bd42d0ec13584bd88ff127242f1af4ff922cfd3efcc519807c0515ba) | Held -> corrected -> reassessed; final vectors equal and assessed |
| S13 | `HOLD_TRANSLATION` | [0x206b9b5e...](https://explorer-studio.genlayer.com/tx/0x206b9b5e0a07693fea2f0875e99a0b3c5ec0c82a0e5eeb3f6eb4e70c01461f7b) | Condition-only differential held |
| S14 | `HOLD_TRANSLATION` | [0x6815210a...](https://explorer-studio.genlayer.com/tx/0x6815210abd46237cefd4e3bcfbaab7f71934be2629d49a25e476e74acc385dfc) | Time-only differential held |
| S15 | `HOLD_TRANSLATION` | [0xd772e3f4...](https://explorer-studio.genlayer.com/tx/0xd772e3f4a74d4dc319e70808821e2605b7d9e3434c9aa113efe096a8ce247abe) | Hazard `corrosive` vs `flammable`; held |

## Negative and correction evidence

The expected negative transactions were finalized with majority agreement and leader execution errors: S09 unauthorized register [`0x91242cc...`](https://explorer-studio.genlayer.com/tx/0x91242cceda11f676fdc0cbfdf64c5d077d7a3e3f899fdddedddb5ee07df5c34d), unauthorized seal [`0x0f7473cf...`](https://explorer-studio.genlayer.com/tx/0x0f7473cfc0d0312ed759f76d4e39a0db8233bf4a14efed2bb29191f294fb0f3a), unauthorized submit [`0x0ae6e90c...`](https://explorer-studio.genlayer.com/tx/0x0ae6e90cf47f5a60b1b9a7605dab4321965452751aafc7e36bb13f2b6449631e), and unauthorized assess [`0xaf28ddb4...`](https://explorer-studio.genlayer.com/tx/0xaf28ddb48af0e28baa06c13ff8b087e43cc1d54f5d8dbbe96410bb7d5737aa39). S10 invalid register [`0x393047d0...`](https://explorer-studio.genlayer.com/tx/0x393047d002d3cd659617dbcef6705ab3a76fa9f7f966f246ea8ac63527884726), assess-before-submit [`0x091740a5...`](https://explorer-studio.genlayer.com/tx/0x091740a5cef9a08c17d7545dc3823fb1717a18d561967fddf4d2d3a62a275881), and correction-not-held [`0xee407620...`](https://explorer-studio.genlayer.com/tx/0xee4076203f234ba3e3aa3d7449d3c7a854ff31ee3ae134603797eb290a7ff112) likewise left the state unchanged.

S12 held assessment was [`0xbbb16090...`](https://explorer-studio.genlayer.com/tx/0xbbb160909d24fde610f9bfaac4adccad25ea71a1165dd3a005089104246e43cd); correction was [`0x7d5b340a...`](https://explorer-studio.genlayer.com/tx/0x7d5b340ad70678ef22ba9c1ee49bacba4b2d475734e65cf0075198e3df466480); final reassessment is linked in the matrix. The final authoritative readback is `RELEASEABLE` with `assessed=true`.

## E2E operational record

One deployment was used. The run produced 70 unique broadcast transactions, one non-broadcast RPC attempt that was retried only after no hash and `SOURCE_SEALED` readback verification, and three read-only receipt backfills after transient HTML responses from the RPC gateway. Receipt polling was bounded; transaction hashes, terminal receipts, Explorer URLs, and readbacks were cached. No scenario was omitted or treated as assumed.
