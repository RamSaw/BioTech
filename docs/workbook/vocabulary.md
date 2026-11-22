# Vocabulary

Lookup reference. **This is the one file where the electronic copy is primary** — see the note at
the bottom for why, and for what to handwrite instead.

Terms are added when first met, not in advance. A term I can define fluently without looking gets
marked ✅ and drops out of the handwriting rotation.

## Peptides and sequence

| Term             | Meaning                                                                                   |
| ---------------- | ----------------------------------------------------------------------------------------- |
| **amino acid**   | the 20 building blocks of proteins; each has a one-letter and three-letter code           |
| **peptide**      | a short chain of amino acids. In this project: length 8–12. One dataset row = one peptide |
| **residue**      | one amino acid unit inside a chain — "the residue at position 2"                          |
| **N-terminus**   | the start of the chain; position numbering begins here                                    |
| **C-terminus**   | the end of the chain                                                                      |
| **P1, P2, … P9** | position 1, 2, … 9, counted from the N-terminus                                           |
| **9-mer**        | a peptide of length 9                                                                     |
| **Arg / R**      | arginine, one of the 20 amino acids                                                       |
| **motif**        | the recurring pattern of which amino acids appear at which positions among binders        |
| **anchor**       | a position that matters disproportionately for whether the peptide binds                  |

## Immunology

| Term               | Meaning                                                                                         |
| ------------------ | ----------------------------------------------------------------------------------------------- |
| **HLA**            | human leukocyte antigen — the human MHC genes                                                   |
| **MHC**            | major histocompatibility complex; the protein that displays peptides on the cell surface        |
| **HLA-B\*27:05**   | one specific HLA allele; the one this project models. Strongly linked to ankylosing spondylitis |
| **allele**         | one specific variant of a gene                                                                  |
| **epitope**        | the peptide fragment that the immune system actually recognises                                 |
| **binding groove** | the cleft in the MHC protein that the peptide sits in                                           |

## Measurement

| Term                         | Meaning                                                                               |
| ---------------------------- | ------------------------------------------------------------------------------------- |
| **binding affinity**         | how tightly peptide and MHC associate                                                 |
| **nM (nanomolar)**           | the unit of affinity in this dataset. **Lower = binds tighter.** Counterintuitive     |
| **IC50**                     | concentration giving 50% inhibition; the quantity the nM values report                |
| **assay**                    | a lab procedure producing a measurement; different assays are not directly comparable |
| **qualitative measurement**  | the assay author's own Positive/Negative call                                         |
| **quantitative measurement** | the numeric nM value                                                                  |

## ML terms specific to this domain

| Term                     | Meaning                                                                                                          |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| **ESM-2**                | a protein language model, pre-trained on protein sequences. Fine-tuned here                                      |
| **PWM**                  | position weight matrix — scores a peptide by its amino acid at each position independently. The Phase 1 baseline |
| **attribution map**      | one number per input position: how much it pushed the prediction. "Lit up" = large                               |
| **Integrated Gradients** | the method producing an attribution map                                                                          |
| **decoy**                | a synthetic negative example                                                                                     |
| **hard negative**        | a negative that is deliberately difficult — e.g. a shuffled positive, same composition, motif destroyed          |

## Why this file is electronic-first

Vocabulary is **lookup, not understanding**. The value is in grepping it mid-session when a term
appears and the thread would otherwise break, which paper cannot do. Handwriting the whole table
would be transcription — the low-value kind of copying.

What to handwrite instead: **the subset that keeps tripping me up**, from memory, repeatedly, until
it doesn't. Roughly five terms at a time. Writing a term I already know fluently is wasted motion;
writing one I keep losing is the whole point.

The asymmetry runs the other way for the rest of `docs/workbook/` — there, handwriting is primary
and the repo holds the distilled survivor.
