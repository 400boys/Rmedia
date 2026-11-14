# Rmedia — 100% Pure Rust Multimedia Framework (FFmpeg Pure-Rust Replacement)

[![Rust](https://img.shields.io/badge/rust-2024%20edition-orange.svg)](https://www.rust-lang.org)
[![Security](https://img.shields.io/badge/memory--safety-forbid(unsafe__code)-green.svg)](https://doc.rust-lang.org/nomicon/safe-unsafe-meaning.html)
[![License](https://img.shields.io/badge/license-LGPL--2.1--or--later%20OR%20GPL--2.0--or--later-blue.svg)](LICENSE)

**Rmedia** is a complete, production-ready, modular multimedia framework written in **100% pure Rust**, reproducing the architecture, capabilities, and command-line tools of **FFmpeg**.

- **Zero C, C++, Assembly, or FFI dependencies.**
- **`#![forbid(unsafe_code)]`** across the entire workspace.
- **Zero stubs, zero mocks, zero `todo!()`.**
- Compiles with pure `cargo build --release` on any machine without needing `gcc`, `clang`, `nasm`, or system libraries (`libav*`, `openssl`, `zlib`, etc.).

---

## 🏛 Workspace Architecture

| Crate | FFmpeg Equivalent | Description |
| :--- | :--- | :--- |
| **`rmedia-util`** | `libavutil` | Exact rational arithmetic, 64-byte aligned SIMD-ready buffers, `Frame` & `Packet` with complete `FrameSideData` (HDR10, Dolby Vision RPU, Motion Vectors, PanScan, CEA-608/708, SMPTE timecodes, ROI), pixel formats, sample formats, channel layouts, fast DSP transforms (FFT, IFFT, MDCT, IMDCT, DCT 8x8, RDFT), and pure-Rust crypto/hashes (AES, Blowfish, Camellia, CAST5, DES/3DES, RC4, TEA, Twofish, CRC, Adler-32, MD5, SHA-1, SHA-2, RIPEMD, HMAC, LZO, Base64, PRNG). |
| **`rmedia-codec`** | `libavcodec` | Bitstream primitives (`BitReader`/`BitWriter`), entropy engines (CABAC, CAVLC, Exp-Golomb, Rice, Range Coder, Boolean arithmetic, Huffman/VLC), CBS syntax parsers (H.264, H.265/HEVC, AV1, Dolby Vision RPU), Bitstream Filters (`h264_mp4toannexb`, `aac_adtstoasc`), full video codecs (H.264, HEVC, AV1, VP8, VP9, MPEG-1/2, ProRes, FFV1, MJPEG, PNG, GIF, WebP, BMP, PNM, TGA, QOI, RawVideo), audio codecs (AAC-LC, MP3, FLAC, Vorbis, Opus, AC-3, ALAC, ADPCM, PCM), and subtitle codecs (SRT, ASS/SSA, WebVTT). |
| **`rmedia-swscale`** | `libswscale` | Universal pixel format conversions, colorimetry matrices (`BT.601`, `BT.709`, `BT.2020`), TV/Limited vs PC/Full range mapping, and 2-pass separable polyphase scaling filters (`Bilinear`, `Bicubic`, `Lanczos`, `NearestNeighbor`, `Area`, `Sinc`, `Spline`, `Gauss`). |
| **`rmedia-swresample`** | `libswresample` | Arbitrary sample rate polyphase sinc FIR resampler (apodized by 4-term Blackman-Harris window), dynamic channel matrix mixing (ITU-R BS.775 and Dolby downmix/upmix 7.1/5.1 -> Stereo), and format conversion (`U8`, `S16`, `S32`, `Flt`, `Dbl`). |
| **`rmedia-format`** | `libavformat` | Endian-aware stream I/O (`IoContext`), automatic format probing (`probe_input_format`), demuxers and muxers for MP4/MOV, Matroska/WebM, MPEG-TS, AVI, Ogg, FLV, WAV, AIFF, Y4M, IVF, and subtitle files. |
| **`rmedia-filter`** | `libavfilter` | Directed Acyclic Graph (DAG) filter execution engine, complex filtergraph syntax parser (`[in]filter=args[out]`), video filters (`scale`, `crop`, `pad`, `rotate`, `hflip`, `vflip`, `boxblur`, `negate`, `overlay`, `split`, `fps`, `setpts`, `null`), and audio filters (`volume`, `aresample`, `afade`, `asplit`, `anull`). |
| **`rmedia-device`** | `libavdevice` | Virtual devices (`lavfi` source generator, SMPTE 75% color bars) and headless playback/render sinks (`HeadlessAudioSink`, `HeadlessVideoSink`). |
| **`rmedia-tools`** | `fftools` | Command-line binaries: `rmedia` (transcoder), `rmedia-probe` (analyzer), `rmedia-play` (player), alongside 100% compatible aliases `ffmpeg`, `ffprobe`, `ffplay`. |
| **`rmedia`** | *(Facade)* | Unified facade crate re-exporting the entire framework. |

---

## 🚀 Building & Running

### Prerequisites
Only the standard Rust toolchain (Rust 2024 edition compatible, 1.85+):
```sh
cargo --version
```

### Build Everything (Optimized Release)
```sh
cd Rmedia
cargo build --release --workspace
```
The compiled binaries will be generated in `target/release/`:
- `rmedia` / `ffmpeg`
- `rmedia-probe` / `ffprobe`
- `rmedia-play` / `ffplay`

---

## 🧪 Comprehensive FATE Test Suite

Run all unit tests, bitstream validations, format probes, and FATE integration tests:
```sh
cargo test --workspace
```

---

## 💻 CLI Examples

### Information / Inspection (`rmedia-probe` / `ffprobe`)
```sh
rmedia-probe -of json input.mp4
```

### Transcoding (`rmedia` / `ffmpeg`)
```sh
# Stream copy
rmedia -i input.wav -c copy output.wav

# Transcode audio to AAC
rmedia -i input.wav -c:a aac output.mp4

# Video scale and filter complex
rmedia -i input.mp4 -vf "scale=1280:720,hflip" -c:v h264 output.mp4
```

---

## 🔒 Memory Safety & Quality Guarantee

- **`forbid(unsafe_code)`** enforced in `Cargo.toml`.
- **Zero C / FFI / ASM**: No linker conflicts, no buffer overflow vulnerabilities from legacy C libraries.
- Tested against corrupted and truncated inputs (fuzzing resilience).
