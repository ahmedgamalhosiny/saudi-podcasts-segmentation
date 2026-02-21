# Audio Segmentation – Saudi Podcasts ASR Dataset

## Project Overview

This project processes the HuggingPanda/Saudi_Podcasts_ASR dataset (train split, 1009 episodes) and splits each long Arabic podcast episode (26–107 seconds) into shorter, natural-sounding clips of 2–15 seconds.

The pipeline uses WhisperX for forced word-level alignment and custom grouping logic to ensure linguistically appropriate splits.

## Task Requirements (Fully Met)

- Segments are strictly between 2 and 15 seconds.
- No cuts in the middle of a word (word-level alignment enforced).
- No unnatural mid-sentence breaks (pause detection + conjunction avoidance).
- Splits occur at natural points (pauses, sentence boundaries, topic shifts).
- Each segment has precise transcription matching only its audio content.
- Incomplete or non-coherent boundary segments are discarded (trailing short segments removed).
- Output per segment: .wav audio file + .json metadata.
- Final CSV includes: original_idx, segment_id, wav_relative_path, duration_sec, start_sec, end_sec, text.
- Pipeline is fully programmatic and reproducible.
- Quality: segments 2–15 s, sound natural in isolation, transcriptions accurately aligned, original reconstructible (except discarded boundaries).

Bonus (speaker identification): intentionally skipped due to repeated Pyannote.audio version/API conflicts.

## How to Access the Output Files

All generated files are available in this shared Google Drive folder:

https://drive.google.com/drive/folders/189w9yJE6LGyG3XM589aCIO_eI63Tkkff?usp=sharing

### Download Instructions

1. Open the link in a browser.
2. Sign in with a Google account if prompted.
3. Click the folder name at the top, then click "Download" (or select all files/folders → right-click → Download).
   - Contents:
     - segments_all_final.csv (complete metadata summary, ~5660 rows)
     - Multiple segments*batch*\*.zip files (each contains .wav audio segments and .json metadata for 100 episodes)
     - (Optional) unzipped segments/ folder with all .wav and .json files
4. Unzip the batch archives into a local folder named segments (recommended name).

After unzipping, the structure will include:

- All segmented .wav audio clips
- Corresponding .json metadata files
- One consolidated CSV summary

## Project Structure

```
saudi-podcasts-segmentation/
├── README.md                    # This file
├── pyproject.toml               # Python project configuration
├── .python-version              # Python version
├── .gitignore                   # Git ignore rules
├── locally/                     # Local development files
│   ├── audio_segmentatio.ipynb  # Main notebook for local execution
│   ├── requirements.txt         # Python dependencies
│   └── dockerfile               # Docker configuration
└── colab_notebook/              # Google Colab version
    └── saudi_podcasts_segmentation.ipynb
```

## Output Structure (after downloading and unzipping)

saudi-podcasts-segmentation/
├── notebook.ipynb # Main Jupyter notebook
├── requirements.txt
├── README.md # This file
├── segments_all_final.csv # Final metadata CSV
├── segments/ # All .wav and .json files
│ ├── ex_0000_seg_000.wav
│ ├── ex_0000_seg_000.json
│ └── ...
└── zips/ # Original batch zips (optional)
├── segments_batch_0.zip
└── ...

## How to Run Locally 

Prerequisites:

- Python 3.10–3.12 installed
- uv installed (curl -LsSf https://astral.sh/uv/install.sh | sh)

Steps:

1. Clone the repository or download the files.
2. Enter the project directory:
   cd saudi-podcasts-segmentation
3. Create and sync the virtual environment:
   uv venv
   source .venv/bin/activate # Linux/Mac
   uv pip sync requirements.txt
4. (Optional) Install Jupyter kernel for VS Code:
   uv pip install ipykernel
   python -m ipykernel install --user --name saudi-segmentation
5. Open VS Code → open the project folder → open notebook.ipynb
6. Select the saudi-segmentation kernel (top right)
7. Update paths if needed (e.g. OUTPUT_DIR = "./segments")
8. Run cells sequentially.

Note: GPU is optional. On CPU-only systems, processing will be slower.

## How to Run Locally with Docker

### Prerequisites

- Docker installed
- NVIDIA GPU with CUDA support (optional, for GPU acceleration)

### Steps

1. Build the Docker image:

```bash
cd locally
docker build -t saudi-podcasts-segmentation .
```

2. Run the container:

```
bash
docker run --gpus all -p 8888:8888 -v $(pwd):/app saudi-podcasts-segmentation
```

3. Access Jupyter Lab at `http://localhost:8888`

### Docker Features

- Base image: nvidia/cuda:12.1.1-cudnn8-runtime-ubuntu22.04
- Python 3.11
- Includes uv for package management
- Pre-downloads output files from Google Drive
- Jupyter Lab pre-configured

## How to Run in Google Colab (**Recommended**)

1. Open the notebook in Colab (upload or import from GitHub).
2. In Colab, select GPU runtime (Runtime → Change runtime type → GPU)
3. Run cells sequentially.

## Summary Statistics (from final CSV)

- Total segments: ~5660
- Unique episodes processed: 999 / 1009
- Average segment duration: ~8.2 seconds
- All segments within 2–15 seconds
- Transcriptions precisely aligned
- Incomplete boundary segments discarded

## Approach

- WhisperX (medium model + int8 quantization) for transcription and forced alignment.
- Custom grouping: pause detection, duration cap, conjunction avoidance.
- Trailing short/incomplete segments discarded.
- Batch processing (100 episodes per batch) + per-batch zipping.

## Trade-offs

- Medium model + int8 → lower memory and faster speed vs large-v3 (minor quality trade-off).
- Speaker diarization skipped due to dependency issues.

## Known Limitations

- No speaker labels.

## Future Improvements

- Test large-v3 model with more resources.
- Lightweight speaker diarization fallback.
- Semantic speaker role assignment (Host/Guest).

## Conclusion

All mandatory task requirements are met. The pipeline is reproducible, efficient, and produces high-quality segmented output.

For questions or issues, refer to the notebook comments or contact the author.