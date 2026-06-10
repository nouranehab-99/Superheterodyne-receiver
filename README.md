# Superheterodyne Receiver

A complete communication system implemented in Python, simulating a real-world superheterodyne radio receiver. The system processes real audio signals through AM/FM modulation, FDM multiplexing, and a full multi-stage receiver chain.

---

## Technologies Used
- Python 3
- NumPy
- SciPy
- Matplotlib
- IPython (audio playback)
- SoundFile (`soundfile` / `sf`)

---

## What I Built

### Full Communication Chain

```
Audio Signals → AM / FM Modulation → FDM Combining → Superheterodyne Receiver → Recovered Audio
```

### 1. Signal Preparation
- Loaded 2 real audio signals (WAV format)
- Converted stereo → mono, normalized amplitude, zero-padded to equal length
- Upsampled ×10 to satisfy Nyquist criterion for RF carriers

### 2. Bandwidth Estimation
- Applied FFT to estimate signal bandwidth using 5% peak threshold
- Signal 1 BW: **7.25 kHz** | Signal 2 BW: **7.38 kHz**
- Used results to derive filter parameters dynamically

### 3. Modulation
| Type | Signal | Carrier | Method |
|------|--------|---------|--------|
| DSB-LC (AM) | Signal 1 | FC1 = 100 kHz | φ_AM(t) = A·cos(ωc·t) + m(t)·cos(ωc·t) |
| NBFM | Signal 2 | FC2 = 150 kHz | Phase modulation via cumulative sum |

### 4. FDM Combining
```
FDM(t) = AM(t) + FM(t)
```

### 5. Superheterodyne Receiver Chain
```
FDM Input → RF BPF → Mixer (×LO) → IF BPF → Hilbert Transform → Demodulator → Audio Output
```

| Stage | Function |
|-------|----------|
| **RF BPF** | Isolates desired channel, rejects image frequency (f_image = fc + 2·f_IF) |
| **Mixer** | Frequency translation: f_LO = fc + f_IF |
| **IF BPF** | Fixed-frequency filtering at f_IF = 25 kHz, BW = ±15 kHz |
| **Hilbert Transform** | Converts real IF signal to analytic signal |
| **AM Demodulator** | Envelope detection: magnitude → LPF → audio |
| **FM Demodulator** | Phase discriminator: unwrap phase → differentiate → LPF → audio |

---

## Experiments & Analysis

### Effect of Removing RF Stage
| Signal | With RF | Without RF |
|--------|---------|-----------|
| AM | Clean audio ✅ | Severe distortion ❌ (envelope mixing) |
| FM | Clean audio ✅ | Slight degradation ⚠️ (phase-based, more robust) |

### Effect of LO Frequency Offset
| Offset | AM Effect | FM Effect |
|--------|-----------|-----------|
| 0 Hz | Perfect recovery | Perfect recovery |
| 0.1 kHz | Slight distortion | Minor pitch shift |
| 1 kHz | Severe distortion | Clear pitch shift |

---

## Results
- Successfully recovered AM and FM audio signals from FDM composite channel
- AM more sensitive to LO offset; FM more robust due to phase-based demodulation
- RF stage confirmed critical for image rejection and channel isolation

---

## How to Run

### Install dependencies
```bash
pip install numpy scipy matplotlib soundfile ipython
```

### Prepare audio files
Place two WAV audio files and update paths in the script:
```python
SIGNAL1_PATH = "path/to/signal1.wav"
SIGNAL2_PATH = "path/to/signal2.wav"
```

### Run the script
```bash
python superheterodyne_receiver.py
```

---

## Project Structure
```
superheterodyne-receiver/
│
├── superheterodyne_receiver.py    # Main script
├── README.md
└── outputs/                       # Generated plots (auto-created)
```

---

*Comm. Theory (1) Project — Faculty of Engineering, Suez Canal University | Supervised by Dr. Doaa Gamal*
