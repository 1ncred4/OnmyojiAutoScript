# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

OnmyojiAutoScript (OAS) is an automation tool for the mobile game Onmyoji, built on Python 3.10+. It shares architecture with AzurLaneAutoScript (Alas). The system consists of a Python backend that handles game automation logic via ADB/UiAutomator2 and exposes a local server for a separate GUI frontend (OASX).

## Development Commands

### Environment Setup
- **Install dependencies**: `pip install -r requirements.txt`
- **Generate requirements**: `pip-compile --annotation-style=line --output-file=requirements.txt requirements-in.txt`

### Running the Application
- **Start Backend Server**: `python server.py` (Starts FastAPI/Uvicorn server)
- **Run Automation Loop**: `python script.py` (Runs the main scheduler loop directly)
- **Start GUI Service**: `python gui.py`

### Helper Tools
- **Asset Extraction**: `python dev_tools/assets_extract.py`
- **Asset Testing**: `python dev_tools/assets_test.py`

## Code Architecture

### Directory Structure
- `tasks/`: Contains specific game task implementations. Each task (e.g., `Exploration`, `Hunt`) is a Python package with a `script_task.py`.
- `module/`: Core framework components.
  - `module/config/`: Configuration management using Pydantic.
  - `module/device/`: Android device control (ADB, minitouch, uiautomator2).
  - `module/ocr/`: OCR implementations (ppocr-onnx).
  - `module/ui/`: UI element definitions and interactions.
- `config/`: User configuration files (JSON).
- `assets/`: Game assets (images, template matching targets).

### Key Concepts
- **Scheduler (`script.py`)**: Manages the execution loop, task scheduling, and error handling.
- **Config**: Uses `pydantic` models defined in `module/config/`. Configurations are reloaded dynamically.
- **Device**: Wraps ADB/UiAutomator2 operations. Handles screenshots and click execution.
- **Tasks**: Inherit from `ScriptTask`. Logic typically involves checking current state (screenshot) -> decision -> action (click/swipe).

### Common Patterns
- **Task Implementation**: To add a new game feature, create a new directory in `tasks/`, inherit from `ScriptTask`, and implement the `run` method.
- **Image Matching**: Uses template matching or OCR. Assets are referenced in code and stored in `assets/`.
- **Logging**: Use `module.logger`.
- **RPC**: `zerorpc` is used for communication between the script process and the GUI/Server.

## Guidelines
- **Paths**: Use absolute paths or `pathlib` for file operations.
- **Type Hinting**: Extensive use of Python type hints is encouraged.
- **Error Handling**: Custom exceptions are defined in `module/exception.py`. Use them to control flow (e.g., `TaskEnd`, `GameStuckError`).
