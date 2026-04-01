# EEG Biomarkers of Mind Wandering vs Focused Meditation

A replication and extension of Brandmeyer & Delorme (2018) examining 
electrophysiological differences between mind wandering and focused 
attention states during meditation using EEG connectivity and spectral analysis.

---

## Project Overview
This short project implements a full EEG analysis pipeline using MNE-Python, 
applied to a publicly available meditation dataset (OpenNeuro ds001787). 
The analysis compares EEG spectral power, frontal alpha asymmetry, and 
functional connectivity between self-reported mind wandering and focused 
meditation states across 24 participants and 2 sessions.

This work directly replicates and extends the findings of:
> Brandmeyer, T., & Delorme, A. (2018). Reduced mind wandering in 
> experienced meditators and associated EEG correlates. 
> *Experimental Brain Research*, 236(9), 2519–2528.

---

## Dataset
- **Source:** OpenNeuro ds001787
- **Participants:** 24 subjects, 2 sessions each
- **EEG System:** BioSemi ActiveTwo, 64 channels, 256 Hz
- **Paradigm:** Continuous meditation with experiential sampling probes
  - Response code 2 = Mind Wandering
  - Response code 4 = Focused Attention
- **Recordings included:** 22 (2 excluded due to insufficient clean epochs)
- **Total clean epochs:** 638 (392 mind wandering, 246 focused)

### Data Access
```
# Install datalad
pip install datalad

# Download dataset
datalad install https://github.com/OpenNeuroDatasets/ds001787.git
cd ds001787
datalad get sub-*/ses-*/eeg/
```

Place downloaded data in `data/` following BIDS structure.

---

## Pipeline

### Preprocessing
- Channel type correction (EXG → EOG, physiological → misc)
- Channel renaming: BioSemi A1-B32 → standard 10-20 nomenclature
- Bandpass filtering: 0.1–40 Hz (FIR, Hamming window)
- Response-locked epochs: −2000ms to 0ms around button press
- Baseline correction: −2000ms to −1500ms
- Artifact rejection: 150µV peak-to-peak threshold

### Epoch Counts per Subject
| Subject | Session | Mind Wandering | Focused | Included |
|---------|---------|---------------|---------|----------|
| sub-001 | ses-01  | 5  | 1  | YES |
| sub-001 | ses-02  | 4 | 3  | YES |
| ...     | ...     | .. | .. | . |
| sub-003 |ses-01   | 0|0   |  NO|
| ...     | ...     | .. | .. | . |
| All 24  | Both    | 329 total | 171 total | 21 recordings |

---

### Band Power Comparison (N=27 recordings, conservative threshold p<0.005)

| Band  | MW Mean (µV²/Hz) | Focused Mean (µV²/Hz) | p-value | Sig |
|-------|-----------------|----------------------|---------|-----|
| Delta | 1.203e-11 | 1.402e-11 | 0.016 | ns† |
| Theta | 1.675e-12 | 2.208e-12 | 0.0005 | ✓* |
| Alpha | 5.542e-12 | 6.043e-12 | 0.386 | ns |
| Beta  | 6.653e-13 | 9.425e-13 | 0.003 | ✓* |
| Gamma | 1.520e-13 | 1.777e-13 | 0.066 | ns |

†Delta reached p<0.05 but not the conservative p<0.005 threshold applied
to control for 5 simultaneous comparisons.

- **Theta power** was strongly elevated during focused meditation (p=0.0005),
  directly replicating Brandmeyer & Delorme (2018)'s frontal midline theta finding
- **Beta power** was also significantly higher during focus (p=0.003),
  consistent with active attentional engagement
- A conservative p<0.005 threshold was applied across 5 bands to reduce
  false positives — Delta (p=0.016) and Gamma (p=0.066) did not meet this threshold

### Frontal Alpha Asymmetry (FAA)
- No significant difference between conditions (p=0.846)
- High inter-subject variability likely masks any true effect at this sample size

### Alpha Band Functional Connectivity (PLV + Permutation Test, n=1000)

**5 significantly increased connections during focused meditation (p<0.05):**
- Fp1–C3, Fp2–FCz, Fz–FC4, F4–FC3, F4–C3
- Forms a tight right prefrontal → bilateral central network consistent
  with top-down attentional control via prefrontal-sensorimotor pathways
- No significant decreases detected

---

## Figures
| Figure | Description |
|--------|-------------|
| `figures/08_band_power_comparison.png` | Band power boxplots with statistics |
| `figures/09_frontal_alpha_asymmetry.png` | FAA comparison between conditions |
| `figures/10_plv_connectivity_matrix.png` | PLV connectivity matrices + difference |
| `figures/11_connectome_difference.png` | Connectome circle plot (increased) |

---

## Notebooks
| Notebook | Description |
|----------|-------------|
| `03_erp_analysis.ipynb` | ERP + PSD pipeline (single & grand average) |
| `04_mindwandering_vs_focused.ipynb` | Connectivity & spectral comparison |

---

## Tools & Libraries
- Python 3.x
- MNE-Python 1.11.0
- mne-connectivity
- NumPy, Pandas, SciPy
- Matplotlib, Seaborn

---

## Background & Motivation
Mind wandering — the tendency of attention to drift from the present task — 
is associated with reduced wellbeing, increased rumination, and depressive 
symptoms. Understanding its neural correlates via EEG offers a path toward 
objective, passive monitoring of attentional states with applications in 
digital mental health, neurofeedback, and clinical assessment.

This project sits at the intersection of contemplative neuroscience and 
applied neurotech — combining rigorous signal processing with clinically 
relevant biomarkers (FAA, theta power, connectivity) that have direct 
translational value.

---

## Limitations & Next Steps
- Conservative p<0.005 threshold applied for multiple comparisons — future
  work should use FDR correction for a more principled approach
- PLV inflation due to short epoch duration (2s) — imaginary coherence is
  a more robust alternative
- FAA null result likely underpowered — all 3 sessions and expert/novice
  separation would improve sensitivity
- Amplitude thresholding for artifact rejection is conservative — ICA would
  improve signal quality and epoch retention

---

## Citation
If you use this analysis, please also cite the original dataset:

Brandmeyer, T., & Delorme, A. (2018). Reduced mind wandering in experienced 
meditators and associated EEG correlates. *Experimental Brain Research*, 
236(9), 2519–2528. https://doi.org/10.1007/s00221-016-4811-5
