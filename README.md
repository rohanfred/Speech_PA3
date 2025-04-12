KALDI SPEAKER VERIFICATION - X-VECTOR PIPELINE
==============================================

To run Kaldi’s x-vector-based speaker verification system
(e.g., using the sre16/v2 recipe), extract x-vectors, compute similarity scores using
PLDA or cosine, and evaluate Equal Error Rate (EER) and other verification metrics.

REQUIREMENTS
------------
- Ubuntu/Linux system
- Kaldi installed and compiled
- Python 3 (optional, for scoring and visualization)

DIRECTORY STRUCTURE
-------------------
Ensure your Kaldi setup includes the following directory structure:

  kaldi/
    └── egs/
        └── sre16/
            └── v2/
                ├── data/
                ├── exp/
                ├── local/
                └── run.sh

STEPS TO RUN X-VECTOR PIPELINE
------------------------------

1. Clone and Build Kaldi:
   -----------------------
   $ git clone https://github.com/kaldi-asr/kaldi.git
   $ cd kaldi/tools && extras/install_portaudio.sh
   $ make -j $(nproc)
   $ cd ../src && ./configure && make -j $(nproc)

2. (Optional) Download Pretrained Models:
   --------------------------------------
   Visit https://kaldi-asr.org/models.html to download pretrained x-vector models if you
   want to skip training from scratch.

3. Prepare the Dataset:
   ---------------------
   Your dataset should be organized into the following Kaldi-style files:

   - wav.scp     : maps utterance IDs to audio file paths
   - utt2spk     : maps utterance IDs to speaker IDs
   - spk2utt     : reverse of utt2spk (can be generated via a Kaldi utility)
   - trials      : list of enrollment-test utterance pairs with 'target' or 'nontarget' labels

   Place these files under: data/<your_eval_set>/

4. Run the Kaldi Recipe:
   ----------------------
   $ cd kaldi/egs/sre16/v2/
   $ ./run.sh

   This script performs:
   - Feature extraction (MFCCs)
   - x-vector extraction using a TDNN model
   - PLDA model training (or using pretrained)
   - Scoring of trial pairs with cosine or PLDA

OUTPUT
------
After successful execution, the scoring results are saved to:

  exp/xvectors_<your_set>/scores

The format of the score file is:

  utt1 utt2 score
  utt3 utt4 score

EVALUATION
----------

Option 1: Kaldi-native scoring tools
------------------------------------
Combine trials and scores:
  $ paste data/<your_eval_set>/trials exp/xvectors_<your_set>/scores > scores_with_labels

Compute EER:
  $ compute-eer < scores_with_labels

Compute minDCF (optional):
  $ compute-min-dcf --p-target=0.01 --c-miss=1 --c-false-alarm=1 scores_with_labels

  ![image](https://github.com/user-attachments/assets/84dd2987-ae9b-412d-8d36-b8fc6064aa20)
