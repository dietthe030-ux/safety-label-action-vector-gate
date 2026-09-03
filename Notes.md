# Safety Label Action Vector Gate

A conservative GenLayer release gate for translated public safety labels. Release occurs only when the locale is assessable and normalized hazard/action vectors match; otherwise the translation is held or the locale marked unsupported.

The publisher seals the source hash, the translator submits the translation hash, and the distributor requests assessment. The contract extracts hazard, severity, actor, mandatory/prohibited actions, condition, and time. Validators compare stable decision data, and the contract recomputes vector equality before storing status. Read views support release and correction workflows.

Studionet deployment: [`0xcD7D57f9f951c4E37d689Bf0b987853F819A9FDC`](https://explorer-studio.genlayer.com/address/0xcD7D57f9f951c4E37d689Bf0b987853F819A9FDC). Evidence: [`e2e-matrix.md`](verification/e2e-matrix.md). This is not product-safety, legal-compliance, or translation-quality certification; it is a transparent vector-consistency gate.
