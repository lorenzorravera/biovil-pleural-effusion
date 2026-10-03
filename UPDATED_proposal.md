## Project proposal form

Please provide the information requested in the following form. Try provide concise and informative answers.

1.Project Title: Fine-Tuning Medical Vision-Language Models for Pleural Effusion Classification in Female Patients

Deep learning in clinical settings faces two compounding challenges: label scarcity and systematic underperformance on underrepresented demographic subgroups (Seyyed-Kalantari et al., 2021). Subgroup-specific fine-tuning directly amplifies the label scarcity problem, since demographically constrained datasets are inherently smaller, making parameter-efficient methods not just useful but necessary in this context.

This paper investigates both challenges jointly. We fine-tune BioViL,  a medical vision-language model pretrained on MIMIC-CXR via contrastive image-text objectives, on a demographically constrained population of female patients diagnosed with pleural effusion. Using LoRA adapters applied selectively to the image encoder, text encoder, and both, we construct an ablation study across three modality conditions (image-only, text-only, multimodal). We evaluate each condition across label budgets N ∈ {25, 50, 100, 200, 500, full}. To assess the sensitivity of our findings to pretraining data, we additionally compare models pretrained on MIMIC-CXR against those pretrained on CheXpert Plus, which differs in dataset scale, label schema, and patient demographics.

Hypothesis: Multimodal fusion provides disproportionately large gains at low label budgets, where clinical report text compensates for weak visual signal. This effect is more pronounced in a female-only subpopulation, where the base model's pretraining representations may not capture demographic-specific physiological variation. To assess whether subgroup-specific fine-tuning is meaningful, we compare female-only fine-tuning against a general baseline trained on the full cohort and evaluated on female patients.


2.The project considers 4 questions:

- Does multimodal fusion (image + radiology text) outperform unimodal baselines for pleural effusion classification in female patients, and by how much does this advantage shrink as labelled data increases?

- Does anchoring the low-label regime with additional data points provide sufficient resolution to identify a genuine crossover between image-only and multimodal performance?

- Does LoRA fine-tuning behave differently when applied to the image encoder vs. the text encoder at very low N, and does one modality's adapter overfit faster?

- Does input-space adaptation (prompt tuning) match or exceed weight-space adaptation (LoRA) on the text encoder at very low N, and does combining both yield further gains?

We will focus on female patients in MIMIC-CXR with pleural effusion (excess fluid accumulation in lungs). We will also look at the CheXpert dataset that also has image and text pairs that we can use for pretraining. By looking specifically at a subpopulation (female only) in the dataset we have identified an underexplored multimodal problem in clinical deep learning literature.

3.Deep Learning Methodologies: 

- Model. We use BioViL (Bannur et al., 2023), a dual-encoder model with a ResNet-50 image tower and a ClinicalBERT text tower, pretrained on MIMIC-CXR via local and global contrastive losses between image patches and radiology report sentences. We add a lightweight classification head (linear layer) on top of the fused [CLS] representation.

- LoRA. LoRA (Low-Rank Adaptation) is used as a parameter-efficient fine-tuning strategy. Rather than updating all model weights, LoRA introduces a small number of trainable low-rank matrices, reducing the risk of overfitting under very small label budgets. We apply LoRA adapters in three configurations: image encoder only, text encoder only, and both encoders jointly.

- Prompt tuning. As an alternative adaptation strategy, we apply prompt tuning to the text encoder, prepending a small set of learnable token embeddings to the input. We compare prompt tuning against LoRA on the text encoder at low N, and evaluate whether combining both yields further gains.

- Modality conditions. Because class labels are derived from radiology reports, we carefully define the text input to avoid direct label leakage. Three conditions: (1) image only, image encoder active, text encoder disabled; (2) text only, text encoder active, image encoder disabled; (3) multimodal, both encoders active, representations concatenated before the classification head.

- Label budget. For each condition we train at N ∈ {25, 50, 100, 200, 500, full}, sampling labelled examples stratified by the binary pleural effusion label. Unlabelled data is not used (no semi-supervised methods).

Evaluation:
- Primary metric: AUROC (appropriate for binary classification with potential class imbalance). 
- Secondary metric: F1, precision/recall curve. We report mean ± std over 3 random seeds per condition.
- The full results table is a 3 (modality) × 5 (label budget) grid of AUROC scores — this is the core empirical contribution.

4.Dataset: 

Primary Dataset:
This analysis will use the MIMIC-Ext-MIMIC-CXR-VQA dataset, which is built on 377,000 entries, most of which are chest radiographic studies. It consists of various datasets in Physionet, recorded at the Beth Israel Deaconess Medical Center in Boston, MA, between 2011 and 2016 (Bae et al., n.d.; Johnson et al., 2019). Each X-ray image is in DIDCOM format and accompanied by a free-text radiology report, which allows for our multimodal design. 

The dataset provides 14 structured binary labels per study, including pleural effusion. The labels were extracted from free-text radiology reports using NLP labelling tools. Labels are included for all studies, but we will deliberately restrict the number of labels visible during training to simulate clinically realistic label scarcity. 

Secondary dataset:
The CheXpert Plus dataset is a comprehensive collection that brings together text and images in the medical field, featuring a total of 223,462 unique pairs of radiology reports and chest X-rays across 187,711 studies from 64,725 patients (53% female).
ttps://aimi.stanford.edu/datasets/chexpert-plus

Ethics and access: 
The dataset is de-identified to remove protected health information. Access is free to researchers following the signature of a data use agreement, which specifies non-redistribution of the data, no re-identification attempts, and public release of relevant code alongside any publication (Bae et al., n.d.). 


5.Key References:

Bae, S., Kyung, D., Ryu, J., Cho, E., Lee, G., Kweon, S., Oh, J., JI, L., Chang, E., Kim, T. & Choi, E. (n.d.) MIMIC-Ext-MIMIC-CXR-VQA: A Complex, Diverse, And Large-Scale Visual Question Answering Dataset for Chest X-ray Images. doi:10.13026/MY7R-V463.

Hu, E.J., Shen, Y., Wallis, P., Allen-Zhu, Z., Li, Y., Wang, S., Wang, L. & Chen, W. (2021) LoRA: Low-Rank Adaptation of Large Language Models. doi:10.48550/ARXIV.2106.09685.

Johnson, A.E.W., Pollard, T.J., Berkowitz, S.J., Greenbaum, N.R., Lungren, M.P., Deng, C., Mark, R.G. & Horng, S. (2019) MIMIC-CXR, a de-identified publicly available database of chest radiographs with free-text reports. Scientific Data. 6 (1), 317. doi:10.1038/s41597-019-0322-0.

Dosovitskiy, A., Beyer, L., Kolesnikov, A., Weissenborn, D., Zhai, X., Unterthiner, T., Dehghani, M., Minderer, M., Heigold, G., Gelly, S., Uszkoreit, J. & Houlsby, N. (2021) An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale. doi: https://doi.org/10.48550/ARXIV.2010.11929

Devlin, J., Chang, M., Lee, K. & Toutanova, K. (2018) BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding. doi:https://doi.org/10.48550/arXiv.1810.04805 

Li, C., Wong, C., Zhang, S., Usuyama, N., Liu, H., Yang, J., Naumann, T., Poon, H. & Gao, J. (2023) LLaVA-Med: Training a Large Language-and-Vision Assistant for Biomedicine in One Day. doi:https://doi.org/10.48550/ARXIV.2306.00890

https://openaccess.thecvf.com/content/CVPR2025/papers/Nath_VILA-M3_Enhancing_Vision-Language_Models_with_Medical_Expert_Knowledge_CVPR_2025_paper.pdf

Seyyed-Kalantari, L., Zhang, H., McDermott, M.B.A., Chen, I.Y., & Ghassemi, M. (2021). Underdiagnosis bias of artificial intelligence algorithms applied to chest radiographs in under-served patient populations. Nature Medicine, 27, 2176–2182. https://doi.org/10.1038/s41591-021-01595-0

https://arxiv.org/html/2311.18681v1#:~:text=Implementation%20Details,-Report%20issue%20for&text=We%20use%20vicuna%2D7b%20%5B8,5%20%5B13%5D%20.

**Please indicate whether your project proposal is ready for review (Yes/No):**

YES

## Feedback (to be provided by the course lecturer)
