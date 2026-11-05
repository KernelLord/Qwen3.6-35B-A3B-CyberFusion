---
license: other
language:
  - en
library_name: transformers
tags:
  - qwen
  - qwen3.6
  - text-generation
  - reasoning
  - code
  - model-merge
  - bf16
  - slerp
pipeline_tag: text-generation
---

# Qwen3.6-35B-A3B-CyberFusion

![CyberFusion emblem](assets/cyberfusion-emblem.png)

**Qwen3.6-35B-A3B-CyberFusion** is an equal-weight SLERP merge of three Qwen3.6-35B-A3B fine-tunes, released in **bfloat16** Hugging Face Transformers layout. The merge combines general, reasoning-distilled, and cybersecurity/offensive-security-oriented behaviors.

> **Status:** merged model release package. Please consult the model files and the release notes for the exact revision and checksums.

## Composition

The merge was produced in two stages:

| Step | A | B | `t` |
|---|---|---|---:|
| 1 | [`Jackrong/Qwopus3.6-35B-A3B-v1`](https://huggingface.co/Jackrong/Qwopus3.6-35B-A3B-v1) | [`lordx64/Qwen3.6-35B-A3B-Kimi-K2.6-Reasoning-Distilled`](https://huggingface.co/lordx64/Qwen3.6-35B-A3B-Kimi-K2.6-Reasoning-Distilled) | `1/2` |
| 2 | step 1 | [`oyildirim/CyberStrike-OffSec-35B`](https://huggingface.co/oyildirim/CyberStrike-OffSec-35B) | `1/3` |

The resulting merge is intended to give each of the three source models an approximately equal contribution under the sequential SLERP construction.

## Important implementation details

- **Precision:** bf16.
- **Format:** Hugging Face Transformers layout; not quantized.
- **MTP head:** taken from the Qwopus/Kimi side because CyberStrike does not provide MTP tensors.
- **Config, tokenizer, and chat template:** taken from Qwopus.
- **Excluded model:** [`jedisct1/Qwen3.6-35B-rust-v2-bf16.mlx`](https://huggingface.co/jedisct1/Qwen3.6-35B-rust-v2-bf16.mlx) was intentionally not included. A later equal four-way merge can fold it in using `merge_models.py --b-format mlx --t 0.25`.
- **Merge tooling:** `merge_models.py` from `C:\Users\lasha\qwen-merge`.

## Intended use

This is an experimental community merge for research, evaluation, local inference, and model-merge experimentation. Benchmark it on your own tasks before relying on it. It is not a security product, and the presence of cybersecurity training data does not imply that every generated answer is correct, safe, or authorized.

## Safety and responsible use

Do not use this model to gain unauthorized access, evade safeguards, deploy malware, or conduct testing without explicit authorization. When using it for defensive security work, operate only against systems and data you own or are explicitly permitted to assess. Add application-level safety controls, logging, human review, and network isolation appropriate to your environment.

## Loading

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

model_id = "<HF_OWNER>/Qwen3.6-35B-A3B-CyberFusion"
tokenizer = AutoTokenizer.from_pretrained(model_id, trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(
    model_id,
    torch_dtype="auto",
    device_map="auto",
    trust_remote_code=True,
)
```

Use the repository's tokenizer and chat template rather than manually reconstructing a prompt format:

```python
messages = [{"role": "user", "content": "Explain the difference between authentication and authorization."}]
text = tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True)
inputs = tokenizer(text, return_tensors="pt").to(model.device)
outputs = model.generate(**inputs, max_new_tokens=512)
print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

The exact runtime requirements depend on the final model configuration, Transformers version, GPU memory, and whether a serving stack supports this architecture and its MTP tensors.

## Repository layout

- `README.md` - model card and usage guide.
- `merge_recipe.json` - machine-readable composition and provenance.
- `assets/cyberfusion-emblem.png` - project branding image.
- `docs/RELEASE_CHECKLIST.md` - checklist for publishing weights and validating the release.
- `docs/EVALUATION.md` - suggested evaluation template.
- `CITATION.cff` - citation metadata.

## License and attribution

The upstream licenses and usage conditions apply. Before distributing weights, verify the license terms of all three source repositories and preserve any required notices. This package deliberately does not assert a single upstream license until those terms have been checked.

## Citation

```bibtex
@misc{cyberfusion_qwen36_35b_a3b,
  title        = {Qwen3.6-35B-A3B-CyberFusion},
  author       = {KernelLord},
  year         = {2026},
  howpublished = {Hugging Face model repository},
  note         = {Equal-weight sequential SLERP merge of three Qwen3.6-35B-A3B fine-tunes}
}
```
