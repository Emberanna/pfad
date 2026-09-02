# Errata — 2025 edition

Fixes applied 2026-09-02, after a full review of the 13 lecture decks against this code.
This branch is the frozen 2025 course; these changes correct things that were **wrong or
broken**, not things that were merely dated. Nothing pedagogical was changed.

`git log` on this file shows exactly what moved; the original state is at `7eb4077`.

## Dependencies that were never declared

Every one of these meant the documented `pip install -r requirements.txt` produced a tutorial
that could not run.

| Week | Added | Because |
|---|---|---|
| 02 | `matplotlib`, `drawsvg` | `plot_tides.py` and `draw_svg.py` import them |
| 05 | `streamlit-webrtc`, `av` | `st_video_stream.py` — the only camera example — imports both |
| 06 | `pyo`, `scipy`, `pygame`, `torchaudio`, `chatterbox-tts` | the synthesis slides, `6a_spectrogram_pygame.py` and `wav_voice.py` all needed them |
| 07 | `langchain-openai`, `langchain-community`, `wikipedia` | 2 of the 3 tutorial scripts import these; only `langgraph` and `langchain_ollama` were listed |

## Code that would break

- **`week03/run_examples.py`, `week03_notebook.ipynb`** — `freq='6H'` → `'6h'` and
  `.resample('M')` → `.resample('ME')`. Both aliases were deprecated in pandas 2.2 and
  **removed in pandas 3.0**, so this code fails outright on a current install.
- **`week05/*`** — `runwayml/stable-diffusion-v1-5` → `stable-diffusion-v1-5/stable-diffusion-v1-5`
  in four places. RunwayML deleted their Hugging Face repo; the second namespace is the
  canonical `diffusers`-compatible re-host (created Aug 2024, ~1.5M downloads).
- **`week05/4_controlnet_canny.py`** — removed a trailing `image.show()`. At that point `image`
  is a NumPy array, so it raised `AttributeError`. The working call is `canny_image.show()` on
  the line above.
- **`week06/2_gen_audio.py`** — `pipeline.to("cuda")` was unconditional, so the file crashed on
  any Mac or CPU-only machine. Now picks cuda / mps / cpu, uses float32 on CPU (float16 is not
  usefully supported there), and only calls `enable_model_cpu_offload()` when there is a GPU to
  offload from. Every week-05 equivalent already had this guard.

## Documentation that described things that did not exist

- **`week09/README.md`** — rewritten. It documented `speech_to_text_webrtc.py`,
  `simple_transcription_test.py`, `pygame_websocket.py` and a `week09/compose.yml` with a
  faster-whisper service. **None of those files were ever committed.** An agent asked to
  "follow the README" would fail on the first step. The new README describes the five files
  that are actually here.
- **`week11/IMPLEMENTATION_SUMMARY.txt`** — removed the entry for `QUICKSTART.md` (2,169 bytes),
  which is not in the tree, and renumbered the entries after it.
- **`week11/requirements.txt` / `pyproject.toml`** — they disagreed. `numpy` is imported and was
  missing from `pyproject.toml`; `python-dotenv` was listed in `requirements.txt` and is imported
  nowhere. Both now list the same five packages.

## Known-wrong things left alone deliberately

- **`week10/login_app/app.py` hashes passwords with bare unsalted `hashlib.sha256`.** Left as
  is — it is more useful in 2026 as an artifact to critique than as code to copy. Do not ship it.
- **Hardcoded audio device indices** in `week06` (`output_device_index=10`,
  `input_device_index=1`). These are a lottery on lab machines. `list_devices.py` exists and
  solves it but appears on no slide. Left as is: it is the best available "why does this work on
  my machine and not yours" exercise.
- **`week06/4a_asyncio_loopback.py`** calls `asyncio.Queue.put_nowait` from PyAudio's C callback
  thread, which is not thread-safe. Fixing it properly means restructuring the example around
  `call_soon_threadsafe` or a `queue.Queue`, which changes what the slides teach. Flagged rather
  than changed.
- **`week12/tools.py` + `app.py`** run `asyncio.run()` at import and again inside Streamlit, so
  tools bind to a dead event loop. Same reasoning — this is the classic Streamlit+async failure
  and it is worth showing on purpose.

## Errors in the slides, not fixed here

The decks are PDF/PPTX in `~/dev/sd5913/2025/` and are being rewritten for 2026, so these are
recorded rather than patched. Full detail with slide numbers in `docs/deck-review/`.

- **Week 2 slide 42** (repeated verbatim as **week 3 slide 13**) states that Python function
  bodies "must be indented (two spaces)" — PEP 8 says four — and that Python "automatically
  returns the output of the last statement", which it does not.
- **Week 7 slide 43** says `cd pfad/week7`; **week 8 slide 52** says `cd pfad/week8`; **week 9
  slide 49** says `cd pfad/week9`. The folders are `week07`, `week08`, `week09`.
- **Assignment 3's deadline is given as Oct 20** (wk6 s5) **and Oct 26** (wk5 s4, wk7 s33).
- **Team size is "maximum 4"** (wk7 s36) **and "max 3"** (wk8 s5).
- **Tutorial group D starts at 15:30** (wk5, wk7) **and 16:30** (wk6).
- **Week 3 slide 57's tutorial divider is titled "TUTORIAL – WEEK 2".**
