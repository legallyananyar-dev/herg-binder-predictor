# hERG channel blocker predictor

## Goal
- Predict how strongly a molecule blocks the hERG channel (pChEMBL) from its structure
- hERG block = cardiac-safety risk, so early prediction is useful
- Learning project: basic cheminformatics ML pipeline, built step by step

## Data
- ChEMBL hERG (CHEMBL240) exports: `chembl_herg_activities.xlsx`, `chembl_herg_assays.xlsx`
- Activity types used: **[edit: IC50 only / IC50 + Ki]**
- All rows are one target (hERG), so the outcome is potency (regression), not target name

## Done so far
- **Cleaning**
  - Kept exact potency values only (pChEMBL present), dropped flagged and duplicate-marked rows
  - Kept confidence-9 assays (binding / functional)
  - RDKit: invalid SMILES dropped, largest fragment kept, charges neutralised, canonical SMILES
  - Duplicates merged per molecule (median pChEMBL)
- **Features / target**
  - X = Morgan fingerprints (radius 2, 2048 bits)
  - y = pChEMBL
- **Split**: scaffold split, 80/20 (test = unseen chemical families)
- **PCA**: 2D map of chemical space (explains ~5% variance, rough picture only)
- **Models**: baseline (mean), Ridge, kNN (Jaccard), SVR (RBF), Random Forest
- **Metrics**: R², RMSE, MAE, Spearman

## Results (scaffold test set)
check pdf 
remarks -You see the fitness is poor and the mean error is very high

## Activity cliffs
- 23,464 similar pairs (Tanimoto >= 0.7), 638 cliff pairs (|ΔpChEMBL| >= 2)
- Model error on cliff molecules ~1.1-1.2 vs ~0.47 on the rest
- 57% of cliff pairs come from different papers vs 8% of all similar pairs
- Likely inter-laboratory noise, not only real SAR
- Repeat measurements of the same molecule differ by ~0.3 (same paper) vs ~0.9 (different papers) log units, so labels are noisy

## Next steps
- Test split 50/50 (see how performance changes with less training data)
- Hyperparameter tuning (scaffold cross-validation on train only, test set untouched)
- Benchmark against literature data (TDC hERG datasets)
- Add descriptors (logP, MW, basic N), predicted-vs-actual plot
- Flag / down-weight cross-paper cliffs in training
- trying to build a classification model with herg_karim data from pyTDC
- https://link.springer.com/article/10.1186/s13321-021-00541-z#Sec2

## Files
- `HERG_predictor.pdf`: summary of results
- Colab notebook: full code
