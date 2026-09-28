# Wakeword Training

This repository contains a Google Colab notebook for training a custom wake word model using `openWakeWord`.

The notebook walks through:
- installing the required Python packages and system dependencies,
- cloning and preparing the `openWakeWord` project,
- creating a dataset directory structure,
- uploading and processing your own wake-word recordings,
- generating augmentation clips,
- creating a YAML training config,
- downloading feature data and background audio for training,
- preparing the model to train on a custom wake phrase.

## What this project does

The notebook is designed to help you train a custom wake word such as `Sonic`, `Hey Jarvis`, or any phrase you choose.

Typical workflow:
1. Record or collect WAV samples of your wake word.
2. Upload them to the notebook.
3. Create the folder structure and duplicate/augment audio clips.
4. Prepare positive and negative training examples.
5. Generate the model config.
6. Train the wake word model using the `openWakeWord` pipeline.

## Files in this repo

- `Wake_Word_Training__dubbaka_navaneeth (1).ipynb` — the main Colab notebook used for training.

## Recommended setup

This notebook is intended to run in Google Colab with a GPU.

Recommended requirements:
- Internet access
- Google Colab account
- GPU runtime enabled for faster training
- A microphone or a set of recorded WAV files of your wake phrase

## How to use it

### 1) Open the notebook in Colab

- Open the notebook file in Google Colab.
- Make sure the runtime type is set to GPU.
- In Colab, go to:
  - Runtime > Change runtime type
  - Select `GPU`

### 2) Install dependencies

The notebook starts by installing the required system packages and Python libraries, including:
- `sox`
- `libsndfile1`
- `git-lfs`
- `librosa`
- `soundfile`
- `scipy`
- `torch`
- `speechbrain`
- `audiomentations`
- `onnxruntime`
- `openwakeword`

This step is required before training begins.

### 3) Clone and prepare openWakeWord

The notebook clones the official `openwakeword` repository and installs it in editable mode:

```python
!git clone https://github.com/dscripka/openwakeword
!pip install -e ./openwakeword --no-deps
```

It also downloads the model resources needed by the project:

```python
!wget -q https://github.com/dscripka/openWakeWord/releases/download/v0.5.1/embedding_model.onnx \
    -O ./openwakeword/openwakeword/resources/models/embedding_model.onnx

!wget -q https://github.com/dscripka/openWakeWord/releases/download/v0.5.1/melspectrogram.onnx \
    -O ./openwakeword/openwakeword/resources/models/melspectrogram.onnx
```

### 4) Create your project folder structure

The notebook defines a wake phrase and creates directories like:

```python
WAKEWORD = "Sonic"
```

It creates these folders:

```text
my_recordings_raw/
generated_clips/Sonic/
my_custom_model/Sonic/positive_train/
my_custom_model/Sonic/positive_test/
my_custom_model/Sonic/negative_train/
my_custom_model/Sonic/negative_test/
```

### 5) Upload your wake-word recordings

Use the notebook uploader to add your audio files into `my_recordings_raw/`.

The notebook expects `.wav` files. For best results:
- keep recordings short and clean,
- use the same wake phrase consistently,
- include variations in pitch, distance, and background noise,
- collect multiple samples from different sessions.

### 6) Generate duplicated audio clips

The notebook duplicates your recordings until a target number of clips is reached.

Example from the notebook:

```python
TARGET_TOTAL_CLIPS = 600
```

This helps create a larger positive dataset for training.

### 7) Copy the generated clips into the positive train/test folders

The notebook copies the generated `generated_clips/<WAKEWORD>/` files into the train and test positive folders. It then creates a synthetic negative dataset using silence or background audio samples.

### 8) Download feature and background data

The notebook downloads:
- pretrained feature files
- validation feature data
- `piper-sample-generator`
- `piper-tts` and phonemization utilities
- MIT room impulse response data for augmentation

This is used to improve training quality and reduce false positives.

### 9) Create the model config

The notebook writes a YAML file named `my_model.yaml`.

Example structure from the notebook:

```python
config = {
    "target_phrase": [WAKEWORD],
    "model_name": WAKEWORD,
    "output_dir": "./my_custom_model",
    "use_existing_clips": True,
    "generated_clips_dir": "./generated_clips",
    "steps": 15000,
    "false_positive_validation_data_path": "validation_set_features.npy",
    "feature_data_files": {
        "ACAV100M_sample": "openwakeword_features_ACAV100M_2000_hrs_16bit.npy"
    },
    "piper_sample_generator_path": "./piper-sample-generator"
}
```

You can edit the `WAKEWORD` variable and training values before running the training step.

### 10) Train the model

After the dataset and config are ready, the notebook runs the training process for the custom wake word.

To customize training, edit:

```python
WAKEWORD = "Sonic"
```

and the training settings in the config:

```python
"steps": 15000,
```

The model output is placed under the folder structure created earlier, such as:

```text
my_custom_model/Sonic/
```

## Recommended workflow

A good workflow is:

1. Record 10-30+ clear examples of your wake word.
2. Keep audio around 1-2 seconds per clip when possible.
3. Make sure all audio files are `.wav` and sampled at 16 kHz.
4. Use consistent recordings and avoid noisy or clipped voices.
5. Increase dataset size if validation performance is poor.
6. Train on GPU to reduce runtime.

## Notes and troubleshooting

- The notebook includes a torchaudio compatibility patch for some Colab environments.
- Some dependency versions may conflict depending on your runtime; the notebook includes the required installs and a compatibility workaround.
- If the notebook warns about missing folders or duplicate directory issues, rerun the setup cells in order.
- If you see a `fatal: destination path ... already exists` message, it usually means a previous setup cell was already executed; simply continue with the next cells or restart the runtime.

## Example file structure

```text
wakeword-training/
├── Wake_Word_Training__dubbaka_navaneeth (1).ipynb
├── my_recordings_raw/
├── generated_clips/
├── my_custom_model/
├── my_model.yaml
├── validation_set_features.npy
├── openwakeword_features_ACAV100M_2000_hrs_16bit.npy
├── piper-sample-generator/
├── MIT_environmental_impulse_responses/
└── mit_rirs/
```

## License

This project is provided as a training notebook for experimentation and custom wake word development. The notebook depends on third-party libraries such as `openWakeWord`, `piper-tts`, and the Hugging Face datasets used during augmentation and feature generation.

## Summary

This repository is a practical Colab-based training template for creating your own custom wake word model using `openWakeWord` and a structured audio dataset. The main goal is to let you record your wake phrase, augment it, and train a model tailored to your own voice or trigger phrase.

If you want to use this notebook in your own setup, update the `WAKEWORD` variable and the dataset folders to match your target phrase, then run the notebook cells in order.

