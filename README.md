# Development of a New Image Compression Format — **.IRM**

> **Project:** Development of a new image compression format “.IRM”

## Table of Contents

- [Overview & Goals](#overview--goals)
- [Key Features](#key-features)
- [.IRM Format Specification](#irm-format-specification)
- [Architecture & Compression Pipeline](#architecture--compression-pipeline)
- [Repository Structure](#repository-structure)
- [Installation](#installation)
---

## Overview & Goals

This project aims to design and implement a new **image compression format** (`.irm`) optimized for:

- **High visual quality** with competitive compression ratios  
- **Low decoding complexity**, suitable for embedded/mobile systems  
- **Extensibility** (lossless/lossy modes, metadata support…)  
- **Interoperability** with ML pipelines and image-processing tools  
- **Reproducible evaluation** (benchmark scripts, metrics, notebooks)

This repository includes: format specification, encoder/decoder implementation, evaluation scripts, experiment notebooks, and documentation.

---

## Key Features

- Python-based encoder and decoder for the `.irm` format  
- Compression modes: *lossy* (with adjustable quantization) and *lossless*  
- Optional **progressive compression** (multi-resolution)  
- Metadata support (EXIF-like) + payload checksum  
- CLI for encoding/decoding and computing PSNR/SSIM  
- Benchmark and conversion scripts for analysis and dataset processing  

---

## .IRM Format Specification

### 1. Header
- Magic bytes: `0x49 0x52 0x4D 0x01`  
- Flags: `lossless`, `progressive`, `color_space` (RGB/YUV)  
- Width, height, channels, bit depth  
- Metadata size + payload offset  

### 2. Metadata (optional)
- Encoded in JSON or TLV format  
- Contains: author, description, timestamp, checksum, source dataset  

### 3. Payload
- **Lossy mode:** transform (DCT/DWT) → quantization → entropy coding  
- **Lossless mode:** adaptive prediction → error coding → entropy coding  
- Tile (block) support for large images  
- Progressive decoding with multiple resolution layers  

### 4. Footer
- SHA256 checksum  
- Total size  
- Version number  

---

## Architecture & Compression Pipeline

1. Preprocessing: RGB → YCbCr, optional chroma subsampling, tiling  
2. Transform: DCT 8×8 (lossy) or DWT 5/3 (lossless)  
3. Quantization (lossy mode)  
4. Prediction & residual generation (lossless mode)  
5. Entropy coding: range coder or Huffman  
6. Packaging: header + metadata + payload + footer  

---

## Repository Structure

<img width="342" height="676" alt="image" src="https://github.com/user-attachments/assets/bd62da2c-7ac4-4637-b6f4-d843146c519e" />

---

## Installation

### Requirements
- Python **3.8+**
- `pip`

### Steps

```bash
git clone https://github.com/<your-username>/IRM-format.git
cd IRM-format

python -m venv .venv
source .venv/bin/activate      # Linux/macOS
.venv\Scripts\activate         # Windows

pip install -r requirements.txt
pip install -e .
# Encode (lossy)
python -m src.cli encode --input examples/lena.png --output out/lena.irm --mode lossy --quality 75

# Decode
python -m src.cli decode --input out/lena.irm --output out/lena_decoded.png

# Metrics
python -m src.cli metrics --original examples/lena.png --decoded out/lena_decoded.png
from irm.encoder import encode_image
from irm.decoder import decode_image

encode_image('examples/lena.png', 'out/lena.irm', mode='lossy', quality=75)
decoded = decode_image('out/lena.irm')

