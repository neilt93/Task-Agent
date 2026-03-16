---
status: submitted
---

## Goal

Evaluate robustness of 3D U-Net dose prediction model for head-and-neck radiotherapy (OpenKBP dataset). Two reports: adversarial attacks (Report 1) and clinically plausible CT perturbations (Report 2).

## Tasks
- [x] Train best model (64 filters, 100 epochs, SE blocks, augmentation, PTV weight 4.0, CT_MAX=4095)
- [x] Report 1: Adversarial robustness evaluation (FGSM/PGD attacks)
- [x] Report 2: CT perturbation robustness (5 perturbation families, 2 severity levels)
- [x] Add finer severity levels (L0, L3-L5) to find perturbation take-off thresholds
- [x] Re-run inference on RunPod for new levels (16 new conditions, 40 patients each)
- [x] Update Report 2 with finer-level results and regenerated figures
- [x] Send updated Report 2 to Birjoo
- [x] Blog post: CT perturbation robustness findings 📅 2026-03-03
- [x] Paper submitted to SERA 2026 (submission #37, uploaded by Weixian Liao) 📅 2026-03-14

## Notes

### Key Results (Feb 27, 2026)
- **P4 Resolution**: Only perturbation with immediate impact. Degrades from L0 (+1.5% DVH) to L4 (+18.2% DVH). No safe threshold.
- **P2 Bone Shift**: Late take-off at L4-L5 (+11.2% DVH at L5). Threshold ~500 HU bone shift.
- **P1 Noise, P3 Bias Field, P5 Dental**: Flat through L5. Model is robust even at extreme amplitudes.
- Best model: DVH Score 2.535, Dose Score 3.731 Gy

### Conference Submissions

**SERA 2026** (24th IEEE/ACIS International Conference on Software Engineering Research, Management and Applications)
- Title: "Adversarial Robustness Evaluation of 3D U-Net-Based Radiation Therapy Dose Prediction Using Clinical DVH Metrics"
- Authors: Rahim Chowdhury, Neil Tripathi, Lei Ren, Amit Sawant, Birjoo Vaishnav, Weixian Liao
- Submitted: 2026-03-14, submission #37
- EasyChair: https://easychair.org/conferences/?conf=sera2026

**ASTRO 2026** (68th Annual Meeting of the American Society for Radiation Oncology)
- Title: "Assessing the Generalizability and Robustness of Deep-Learning Dose Prediction in Head-and-Neck Radiotherapy to Clinically Realistic CT Perturbations"
- Authors: N. Tripathi (NYU), R. Chowdhury (UMD St. Joseph), L. Ren, A. Sawant, B. D. Vaishnav (UMD Radiation Oncology)
- Abstract ID: #79011, submitted 2026-02-25 by Birjoo
- Presenting author: Birjoo Vaishnav (backup: Amit Sawant)
- Junior Investigator Travel Award applied for Neil
- Note from Birjoo: "Congratulations to Neil for getting his first abstract ever in!"

### Files
- Report 2 PDF: `reports/report2_ct_perturbation/ct_perturbation_robustness_report.pdf`
- Report 2 LaTeX: `reports/report2_ct_perturbation/ct_perturbation_robustness_report.tex`
- Metrics: `open-kbp-modified/openkbp_hn_robustness/metrics/summary.csv`
- Figures: `open-kbp-modified/openkbp_hn_robustness/figures/`
