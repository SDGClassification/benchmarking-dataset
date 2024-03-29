# AFD SDG Prospector

The SDG Prospector uses deep learning techniques to analyze the information
contained in any written document. The SDG Prospector builds upon
DistilRoBERTa, a light version of a model first developed by Meta. The
accuracy of the SDG Prospector derives directly from the quality of the
learning base. Exclusively built by selecting relevant texts manually, the
learning base consists of over 9,000 paragraphs related to the SDGs. For each
SDG, more than 500 paragraphs were collected, of maximum 200 words. These were
mostly extracted from specialized UN official documents and completed with
texts from other sources.

The SDG Prospector is the result of a research project initiated in 2021 by
the Agence Française de Développement (AFD).


Learn more: https://sdgprospector.org/

## Evaluation

| sdg   |   n |   Accuracy (%) |   Precision (%) |   Recall (%) |   F1 score |   TP |   FP |   TN |   FN |
|-------|-----|----------------|-----------------|--------------|------------|------|------|------|------|
| All   | 161 |          91.93 |           93.42 |        89.87 |       0.92 |   71 |    5 |   77 |    8 |
| 7     | 100 |          95    |           90.91 |       100    |       0.95 |   50 |    5 |   45 |    0 |
| 10    |  61 |          86.89 |          100    |        72.41 |       0.84 |   21 |    0 |   32 |    8 |

**Benchmarked on**: March 19, 2024

**Detailed benchmark results**: [results.csv](results.csv)