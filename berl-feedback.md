# ST456 Project - Provisional feedback and marks

## Section

### Problem formulation + summary

Excellent, motivated formulation and good summary.

### Solution methodology

Ambitious and beyond-syllabus: BioViL with a custom conv-LoRA on the image encoder, PEFT LoRA on the text encoder, and a clean three-condition ablation. But masking plus findings-only text plus ~25% coverage leave the text modality near signal-free, so the central hypothesis can't really be tested.

### Implementation

Good engineering: a custom LoRAConv2d for the image encoder (PEFT handles the text q/v adapters), AMP, early stopping, resume-safe runs, a 720GB pipeline. One monolithic notebook with a separate, duplicated pipeline per modality, not a unified codebase.

### Choice and description of data

Excellent choice and good description of data.

### Numerical evaluation

Rigorous protocol (three seeds, six budgets, confounds, AUROC). But every arm is at chance, so nothing is established, and image-only at 0.514 on ~38k images points to a broken or under-powered image adaptation rather than the "unlearnable regime" concluded; masking can't explain it.

### Conclusion

Honest and thorough, with good limitations and future work. But it reads the null as a property of the regime and never questions the image pathway itself.

### Presentation quality

Clear and well-written, with a good architecture figure and clean tables.
Template was not followed.

**Marking**:

| **Section** | **Max marks** | **Your points** |
|-------------------|-------------------|---------------|
| Problem formulation + summary  | 10 | 7.5 |
| Solution methodology | 20 | 13.0 |
| Implementation | 25 | 15.0 |
| Choice and description of data | 10 | 7.0 |
| Numerical evaluation | 25 | 13.750000000000002 |
| Conclusion | 5 | 3.5 |
| Presentation | 5 | 3.5 |
| Total | 100 | 63 |
