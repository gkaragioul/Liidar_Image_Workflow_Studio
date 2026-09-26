<h1 align="center">Liidar Image Workflow Studio</h1>

<p align="center">
  <strong>Local AMD-first model studio for dataset prep, character analysis, generation, and LoRA training.</strong><br>
  <em>ComfyUI-oriented workflow management, prompt recipes, local training helpers, runtime checks, and output review.</em>
</p>

<p align="center">
  <a href="#what-this-project-is">What It Is</a> •
  <a href="#runtime-requirements">Runtime</a> •
  <a href="#start-the-app">Start</a> •
  <a href="#verification">Verification</a> •
  <a href="#repository-hygiene">Hygiene</a> •
  <a href="#license">License</a>
</p>

## What This Project Is

- A local workflow manager for AI image generation.
- A React/Vite frontend plus FastAPI backend.
- A ComfyUI-oriented job and workflow helper.
- A place to manage profile schemas, prompt recipes, workflow templates, training metadata, and output review.
- A source-code project, not a bundled model, dataset, or media pack.

## What This Project Is Not

- It is not a model-weight repository.
- It is not a dataset repository.
- It is not a generated-image pack.
- It is not a service for impersonating real people or bypassing consent requirements.

## Current State

Liidar currently includes:

- React/Vite frontend at `http://127.0.0.1:5274`.
- FastAPI backend at `http://127.0.0.1:8000`.
- Runtime and live system monitoring.
- Profile creation and local reference analysis.
- Single-brief local prompt enhancement.
- ComfyUI generation preview, preflight, queueing, and output tracking.
- Dataset preparation and local training workflow helpers.
- Training run launch, polling, cancellation, and log tail support.
- Frontend and backend test coverage for the current workflow.

## Optional Anthropic API Feature

Everything above runs locally. The backend also has one optional cloud feature, off by default: dataset-prep crop scanning with Anthropic's Claude API.

- It is used only when you save an Anthropic API key (`POST /api/settings/anthropic-key`) and run dataset prep with `scan_mode: "claude"` (or the older `use_ai: true`). The frontend uses local scanning.
- When it is used, Liidar uploads each scanned image to Anthropic (`https://api.anthropic.com/v1/messages`), downscaled to at most 384 x 384 pixels as a JPEG, for up to `ai_max_images` images per run (default 100). The key test (`POST /api/settings/anthropic-key/test`) sends a short text-only request. Anthropic's terms and data policies apply to what you upload, so only use it for images you have the right and consent to share.
- The API key is stored in plain text in `config/secrets/anthropic.json` inside the project folder (the folder is git-ignored). Anyone who can read that folder can use the key. Remove it with `DELETE /api/settings/anthropic-key` or by deleting the file.

## Runtime Requirements

This repo does not include ComfyUI, checkpoints, adapters, model files, generated outputs, datasets, logs, or secrets.

For full generation and training tests, the local machine should provide:

- `ComfyUI/` in the project root.
- A working ComfyUI Python environment.
- Compatible checkpoint files under the local ComfyUI model folders.
- Optional trained adapter files under the local ComfyUI adapter folders or registered absolute paths.
- GPU drivers and the correct PyTorch backend for the machine.

On machines without ComfyUI or a compatible GPU runtime, the app can still be developed and UI-tested, but generation preflight will report missing runtime or model readiness.

## Getting Started

Requirements: Windows with PowerShell, Git, Python 3.12, [uv](https://docs.astral.sh/uv/), and Node.js with npm. The backend dependencies are locked in `app/backend/uv.lock`.

```powershell
git clone https://github.com/gkaragioul/Liidar_Image_Workflow_Studio.git
cd Liidar_Image_Workflow_Studio\app\backend
uv sync --extra test
cd ..\frontend
npm install
cd ..\..
```

For generation, run `.\tools\setup\install-comfyui.ps1`, then install a GPU-enabled PyTorch build, the ComfyUI requirements, and your chosen model files as the script's closing notes describe. For LoRA training, `.\tools\setup\install-sd-scripts.ps1` installs kohya-ss sd-scripts with an AMD RDNA4 ROCm PyTorch build. `.\tools\setup\check-system.ps1` reports the OS, CPU, RAM, and GPU it finds. Then start the app as below and open `http://127.0.0.1:5274`.

## Start The App

Use the project launcher:

```powershell
.\Start-Liidar.ps1
```

The launcher starts:

- ComfyUI on `http://127.0.0.1:8188`
- Backend on `http://127.0.0.1:8000`
- Frontend on `http://127.0.0.1:5274`

If ComfyUI is not installed, start the backend and frontend manually for development:

```powershell
cd app\backend
.\.venv\Scripts\python.exe -m uvicorn local_model_studio.main:app --reload --host 127.0.0.1 --port 8000
```

```powershell
cd app\frontend
npm run dev -- --host 127.0.0.1 --port 5274 --strictPort
```

## Verification

Backend:

```powershell
cd app\backend
.\.venv\Scripts\python.exe -m pytest
```

Frontend:

```powershell
cd app\frontend
npm test
npm run build
```

## Repository Hygiene

Public releases should not include:

- Generated images or output media.
- Training datasets or prepared local crops.
- Model weights, checkpoints, adapters, or other binary model artifacts.
- Private prompt sets, personal reference files, logs, credentials, or cloud endpoints.
- Temporary local worktree/cache folders.

The repository is intended to contain source code, tests, configuration templates, and documentation only.

## Disclaimer

Liidar is provided as is, without warranty of any kind, under the [MIT License](LICENSE). Use it at your own risk. It reads the image folders you choose, writes and removes data in its own profile, dataset, and training folders, and runs long GPU workloads; the setup scripts clone and install third-party software. You are responsible for the images you use and generate, including consent from anyone they depict, and for the licenses of the models you install.

## License

MIT. See [LICENSE](LICENSE).
