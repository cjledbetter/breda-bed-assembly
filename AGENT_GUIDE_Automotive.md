# AI AGENT WORKFLOW & INSTRUCTIONS GUIDE: AUTOMOTIVE REBUILD SERIES

> **Note for AI Assistant (Antigravity Agent):**
> If a new session starts or context is reset, read this guide to understand the exact processing workflow, naming conventions, split detection rules, summary structures, and Kanban/Manual web app updates required for this automotive engine rebuild project.

---

## 📍 WORKSPACE & SYSTEM ENVIRONMENT
* **Workspace Path:** `/Users/chris/Library/Mobile Documents/com~apple~CloudDocs/Personal/Automotive/`
* **OS / Hardware:** macOS (Apple Silicon M1/M2/M3/M4)
* **Transcription Tooling:**
  * `whisper-cli` installed at `/opt/homebrew/bin/whisper-cli`
  * Model path: `/Users/chris/.gemini/antigravity-cli/brain/5768dd65-76ba-4f80-9d59-df3889cf9249/scratch/ggml-base.en.bin` (or `~/whisper-models/ggml-base.en.bin`)
  * `ffmpeg` installed at `/opt/homebrew/bin/ffmpeg`

---

## 🏷️ NAMING CONVENTION SCHEME
All output files MUST strictly follow this numbered CamelCase scheme:

| Video # | Topic / Video Title | Audio Output (`.m4a`) | Transcript (`.txt`) | Summary (`.txt`) |
| :--- | :--- | :--- | :--- | :--- |
| **1** | Dismantling The Engine | `1DismantlingTheEngine.m4a` | `1DismantlingTheEngine-Transcript.txt` | `1DismantlingTheEngine-Summary.txt` |
| **2** | Engine Block | `2EngineBlock.m4a` | `2EngineBlock-Transcript.txt` | `2EngineBlock-Summary.txt` |
| **3** | Crankshaft | `3Crankshaft.m4a` | `3Crankshaft-Transcript.txt` | `3Crankshaft-Summary.txt` |
| **4** | Tools - Using A Tap Set | `4Tools-UsingATapSet.m4a` | `4Tools-UsingATapSet-Transcript.txt` | `4Tools-UsingATapSet-Summary.txt` |
| **5** | Using An Engine Stand | `5UsingAnEngineStand.m4a` | `5UsingAnEngineStand-Transcript.txt` | `5UsingAnEngineStand-Summary.txt` |
| **6** | The Pistons | `6ThePistons.m4a` | `6ThePistons-Transcript.txt` | `6ThePistons-Summary.txt` |
| **7** | Tips - How To Install Piston Rings | `7Tips-HowToInstallPistonRings.m4a` | `7Tips-HowToInstallPistonRings-Transcript.txt` | `7Tips-HowToInstallPistonRings-Summary.txt` |
| **8** | Tips - How To Install Pistons | `8Tips-HowToInstallPiston.m4a` | `8Tips-HowToInstallPiston-Transcript.txt` | `8Tips-HowToInstallPiston-Summary.txt` |
| **9** | Head Gasket | `9HeadGasket.m4a` | `9HeadGasket-Transcript.txt` | `9HeadGasket-Summary.txt` |
| **10**| Cylinder Head | `10CylinderHead.m4a` | `10CylinderHead-Transcript.txt` | `10CylinderHead-Summary.txt` |
| **11**| Engine Valves | `11EngineValves.m4a` | `11EngineValves-Transcript.txt` | `11EngineValves-Summary.txt` |
| **12**| Tips - How To Install & Remove Valves | `12Tips-HowToInstallAndRemoveValves.m4a` | `12Tips-HowToInstallAndRemoveValves-Transcript.txt` | `12Tips-HowToInstallAndRemoveValves-Summary.txt` |
| **13**| Camshafts | `13Camshafts.m4a` | `13Camshafts-Transcript.txt` | `13Camshafts-Summary.txt` |
| **14**| Tips - How To Install Camshafts | `14Tips-HowToInstallCamshafts.m4a` | `14Tips-HowToInstallCamshafts-Transcript.txt` | `14Tips-HowToInstallCamshafts-Summary.txt` |
| **...**| *Future Videos* | `[N][Topic].m4a` | `[N][Topic]-Transcript.txt` | `[N][Topic]-Summary.txt` |

---

## 🛠️ COMPLETE AI EXECUTION PIPELINE

### STEP 1: Process & Transcribe Master Audio
When the user provides a single master recording (e.g. `NewMasterAudio.m4a` or individual `.m4a` files):

1. **Convert to 16kHz Mono WAV:**
   ```bash
   ffmpeg -y -i "MasterAudio.m4a" -ar 16000 -ac 1 -c:a pcm_s16le "scratch/input.wav"
   ```

2. **Run Metal GPU Accelerated Transcription with Timestamps:**
   ```bash
   whisper-cli -m ~/whisper-models/ggml-base.en.bin -f "scratch/input.wav" -otxt -of "scratch/transcription"
   ```

3. **Identify Section Boundaries & Detect Bleed-Over:**
   * Parse the log/output timestamps to find where each video topic starts and ends.
   * **BLEED-OVER HANDLING:** Omit any trailing audio/transcripts that extend into future video topics beyond the requested set.

---

### STEP 2: Split Audio Files (If Merged)
If the input file contains multiple videos combined, split them using lossless stream copying (`-c copy`):

```bash
ffmpeg -y -ss [START_TIME] -to [END_TIME] -i "MasterAudio.m4a" -c copy "11EngineValves.m4a"
# Repeat for each sub-video
```

---

### STEP 3: Generate Individual Transcript Files (`.txt`)
Save the full timestamped spoken text for each video to `[Number][Topic]-Transcript.txt`.

---

### STEP 4: Generate Executive Summaries & Key Points (`.txt`)
Analyze each transcript and generate `[Number][Topic]-Summary.txt` containing the full Executive Summary, Technical Specs, Fasteners/Tooling rules, and Critical Risks/Warnings.

---

### STEP 5: Update the Master Web App (`kanban_board.html`)
The workspace contains a dual-mode interactive web app (`kanban_board.html`).

Whenever new videos are processed, update `kanban_board.html`:
1. **Phase Kanban Mode:** Add new action cards to the appropriate phase column (`#phase1`, `#phase2`, `#phase3`, or `#phase4`) with interactive checkboxes and spec badges.
2. **Master Shop Manual Mode:** Add any new torque values to the **Spec Cheat-Sheet Table** and append a new chapter object to `VIDEO_MANUAL_DATA` with the full executive summary & takeaways.

---

## 🎯 SUMMARY OF DELIVERABLES EXPECTED BY THE USER
1. Split `.m4a` files (if provided as a merged file).
2. `[N][Topic]-Transcript.txt` for each video.
3. `[N][Topic]-Summary.txt` for each video.
4. Updated `kanban_board.html` with new cards and chapter manual entries embedded.
5. Provide clickable `file:///` links to all output files in the final response.
