# Baseline attack images on 1,000 paired inputs

Comparison baseline images for numeric source/target pairs **0–999**. Previously verified fixed-100 images (IDs 0, 10, …, 990) are reused for the other baselines; FOA is regenerated using the freshly cloned author repository.

**All baseline image collections are complete and verified.** A method archive is published only after all 1,000 images pass validation. See [status.json](status.json) for the recorded snapshot. Existing counts for incomplete methods are not completion claims.

| Method / setting | Required images | Published archive |
| --- | ---: | --- |
| AttackVLM / ViT-B/16 | 1,000 | [AttackVLM_B16.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/AttackVLM_B16.zip) |
| AttackVLM / ViT-B/32 | 1,000 | [AttackVLM_B32.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/AttackVLM_B32.zip) |
| AttackVLM / LAION ViT-G/14 | 1,000 | [AttackVLM_Laion.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/AttackVLM_Laion.zip) |
| AdvDiffVLM / ensemble | 1,000 | [AdvDiffVLM_ensemble.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/AdvDiffVLM_ensemble.zip) |
| SSA-CWA / ensemble | 1,000 | [SSA-CWA_ensemble.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/SSA-CWA_ensemble.zip) |
| AnyAttack / released `coco_cos.pt` | 1,000 | [AnyAttack_official_B32.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/AnyAttack_official_B32.zip) |
| M-Attack / ensemble | 1,000 | [M-Attack.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/M-Attack.zip) |
| M-Attack-V2 / ensemble | 1,000 | [M-Attack-V2.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/M-Attack-V2.zip) |
| Original FOA / cluster 3 | 1,000 | [FOA-Attack_cluster_3.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/FOA-Attack_cluster_3.zip) |
| Original FOA / cluster 5 | 1,000 | [FOA-Attack_cluster_5.zip](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/download/baselines-1000-v1/FOA-Attack_cluster_5.zip) |

## Download and pairing

Full PNG collections are stored as ZIP assets in [GitHub Releases](https://github.com/estreallaxy/Baseline-Attack-Images-1000/releases/tag/baselines-1000-v1). Each archive contains `<setting>/<id>.png`. Match the numeric ID to the corresponding source and target hashes in [input_manifest.json](input_manifest.json). The source and target datasets, model weights, credentials, and private experiment files are not included.

Each completed setting has a JSON manifest containing the SHA-256 of every PNG, the archive checksum, and its protocol. Verify a downloaded archive with `sha256sum <archive>.zip` (PowerShell: `Get-FileHash <archive>.zip -Algorithm SHA256`). Images are RGB, 224 × 224. Bounded attacks use epsilon 16/255; saved PNG validation allows at most one quantization level. AdvDiffVLM follows its natural unrestricted diffusion protocol and is not described as a bounded attack.

## Upstream methods and protocol notes

- [AttackVLM](https://github.com/yunqing-me/AttackVLM): target CLIP projection alignment, 300 sign-gradient steps, alpha 1/255. Epsilon 16/255 is the comparison protocol rather than the upstream example's 8/255. The LAION surrogate is a documented adaptation.
- [AdvDiffVLM](https://github.com/gq-max/AdvDiffVLM): pretrained latent diffusion, RN50/RN101/ViT-B16/ViT-B32 ensemble, 200 DDIM steps × 10 rounds, source masks verified against the paired dataset.
- [SSA-CWA](https://github.com/huanranchen/AdversarialAttacks): upstream spectral/CWA optimizer, with target image cosine alignment replacing its classification criterion. B16/B32/LAION ensemble, 20 spectral samples, 10 CWA outer steps.
- [AnyAttack](https://github.com/jiamingzhang94/AnyAttack): released `coco_cos.pt`, B32 inference encoder. The author reports this checkpoint as ensemble-trained with qualified recollection; its exact training configuration is not embedded. The JSON protocol preserves this evidence boundary.
- [M-Attack](https://github.com/VILA-Lab/M-Attack): upstream attack body, source/target crops, 300 steps, alpha 1 pixel, epsilon 16 pixels.
- [M-Attack-V2](https://github.com/VILA-Lab/M-Attack-V2): upstream attack and loss bodies, original target plus CLIP-B16 top-2 retrieval over all 82,783 COCO train2014 images. Author-published retrieval is reused only when target hashes match.
- [FOA-Attack](https://github.com/jiaxiaojunQAQ/FOA-Attack), freshly cloned commit `6aa9ad463a1e812b7f77ff2e5c4b1712e7c78c4b`: upstream `fgsm_attack`, upstream `EnsembleFeatureLoss_OT_foa_attack`, B16/B32/LAION, 300 steps, alpha 1 pixel, epsilon 16 pixels, crop scale [0.5, 0.9], cluster settings 3 and 5. The original loss and attack are executed directly. Old local FOA images are not reused.

Method-specific adaptations and checkpoint identities are recorded in each completed manifest. These archives are attack images, not new black-box model evaluation scores. Upstream methods and source datasets remain attributable to their original authors and licenses.

