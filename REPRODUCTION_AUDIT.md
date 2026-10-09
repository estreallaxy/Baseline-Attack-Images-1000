# Reproduction and integrity audit

Audit date: 2026-10-09 UTC. Machine-readable evidence: [integrity_audit.json](integrity_audit.json).

All 10 collections pass the independent file checks described below. These are baseline implementations on the recorded common paired dataset, with the adaptations listed here. A claim of unchanged, end-to-end reproduction of all original papers would exceed the evidence.

## Checks completed

- Recomputed hashes for all 1,000 source files and 1,000 target files against the generation manifest. Numeric IDs are exactly 0–999.
- Opened and verified all 10,000 PNGs: RGB, 224 × 224, no missing IDs, no duplicate hashes within a collection, no constant images, and no output identical to its preprocessed source.
- Independently rebuilt source preprocessing with PIL bicubic short-edge resize and center crop. All 9 bounded collections satisfy saved-image L-infinity distance ≤16 pixel levels; AdvDiffVLM is unrestricted and excluded from that bound.
- Checked every archive SHA-256, ZIP member list, full ZIP CRC, and every archived image's byte equality with its generated PNG and public manifest. Published release asset checksums were also checked during publication.
- Verified all 100 reused fixed-sample image hashes for each of the 8 non-FOA collections against the original reuse inventory. Both FOA collections were generated anew, with no reused images.
- Matched all 1,000 M-Attack and all 1,000 M-Attack-V2 PNG hashes to generation records. M-Attack has 980 explicit 300-step records; the other 20 records require the accompanying 300-step run configuration. All M-Attack-V2 records explicitly specify 300 steps.
- Matched each FOA cluster collection to generation records covering all 1,000 IDs; the extracted upstream attack-function AST matches the recorded run fingerprint.
- Checked M-Attack-V2 retrieval target hashes for all 1,000 IDs and validated all 2,000 auxiliary JPEGs. Metadata identifies 100 author-published retrieval sets and 900 sets retrieved from the complete 82,783-image COCO train2014 pool. This audit did not independently rerank the full pool again.
- Recorded pinned commits for all 7 author repositories; all have no tracked local changes. External runners and compatibility adaptations still matter, even when author checkouts are clean.

## Method-specific evidence and adaptations

| Method | Implementation used | Boundaries of the reproduction claim |
| --- | --- | --- |
| AttackVLM, three settings | Target CLIP projection cosine alignment, 300 sign-gradient steps, alpha 1/255 | Common epsilon 16/255 replaces the upstream example's 8/255. Hugging Face CLIP encoders substitute the original encoder interface. LAION ViT-G/14 and its BF16 execution are adaptations. |
| AdvDiffVLM | Author DDIM sampler, 200 steps × 10 rounds, four CLIP encoders, pretrained diffusion checkpoint | Paired input selection, compatible Lightning import, and existing source class labels/masks are adapted to the dataset. Source hashes match the mask dataset; masks were not independently regenerated in this audit. This is unrestricted diffusion. Checkpoint loading reports no missing keys; the two unused unexpected entries are EMA bookkeeping fields. |
| SSA-CWA | Author spectral/CommonWeakness optimizer, 20 spectral samples and 10 outer steps | Target-image cosine alignment replaces the original classification criterion. The CLIP ensemble and LAION BF16 execution are adaptations. Spectral samples are evaluated in microbatches. |
| AnyAttack | Author decoder and released `coco_cos.pt`, B32 inference encoder | Released-checkpoint inference, without retraining. The exact ensemble used to train that checkpoint is not independently established. Hugging Face encoder interface and aspect-preserving resize/center crop differ from the demo's direct square resize. |
| M-Attack | Author attack/loss bodies, 300 steps, source/target crops | Common input preprocessing, Transformers return-container compatibility, and omission of deterministic computations used only for logging. Batch scheduling can change random crop draws. |
| M-Attack-V2 | Author attack/loss bodies, four encoders, three targets, 10 passes, 300 steps | Singleton batch-dimension compatibility fix; parallel batch scheduling; some workers store frozen Linear/Conv weights in FP16 under the official AMP context. Bitwise numerical validation covered B16 forward/loss/input gradients only, not every encoder. |
| FOA-Attack, clusters 3 and 5 | Fresh author clone, direct upstream attack and optimal-transport loss, three encoders, 300 steps | Explicit paired IDs, cached model resolution, logging/runtime adaptation. Both cluster settings are retained separately; the original API-based early-success cluster selection is not reproduced by these image archives. |

M-Attack-V2's fixed-100 metadata records crop transitions [150,275], whereas the full-run configuration records [40,260]. All three source crop transforms have identical scale [0.5,1.0], so the choice of transform instance does not change the crop distribution. The original fixed-100 configuration and all full-run worker configurations are preserved in its public manifest, rather than representing the entire collection with one configuration. Different batch sizes and parallel random-number ordering prevent a claim of bitwise rerun identity.

The public M-Attack and M-Attack-V2 manifests initially omitted their protocol because packaging looked for `metadata.json`/`protocol.json`, while these runners wrote `config.json`. The audit restored those fields from the existing run files and corrected the packaging fallback. Image and archive bytes were not changed.

## What these checks do not establish

File validity and bounded perturbations do not prove that every attack succeeds. This audit did not rerun black-box evaluations, reproduce every paper's training stage, establish equality with published success rates, or prove numerical equivalence of all compatibility adaptations. Historical setup failures were repaired before the corresponding completed outputs; the existence of those failures is not a claim that every log is error-free.

Use these collections with the method-specific comparison protocol described above. Report adaptations and unrestricted versus bounded attacks explicitly when using them in an experiment.
