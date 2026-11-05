# Release checklist

Before publishing the model weights:

- [ ] Confirm the exact upstream license and attribution requirements for all three source models.
- [ ] Add the final Hugging Face owner/repository ID to the README examples.
- [ ] Upload all required `config.json`, tokenizer, generation/configuration files, and model shard files.
- [ ] Verify that the MTP tensors are present and match the documented source.
- [ ] Record the merge date, source revisions/commits, Transformers version, and merge script version.
- [ ] Generate and publish SHA-256 checksums for every weight shard.
- [ ] Run smoke tests for tokenizer loading, chat-template rendering, generation, and save/reload.
- [ ] Run task evaluations and publish results in `docs/EVALUATION.md`.
- [ ] Check that no local paths, credentials, private prompts, or unrelated files are included.
- [ ] Use Git LFS for large weight files; do not place multi-gigabyte weights in ordinary Git blobs.
