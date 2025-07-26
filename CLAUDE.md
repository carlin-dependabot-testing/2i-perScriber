# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

wscribe is an audio transcription tool that provides a modular front-end for whisper-based transcription. It supports multiple output formats (JSON, SRT, WebVTT) and can run on both CPU and GPU. The project includes both a Python CLI tool and a separate web-based editor (`wscribe-editor/`) for transcript editing.

## Development Commands

### Dependencies and Environment
```bash
# Install dependencies (requires Poetry)
make deps-sync

# Show installed dependencies
make deps-show

# Set required environment variable
export WSCRIBE_MODELS_DIR=$XDG_DATA_HOME/whisper-models
```

### Testing and Quality Checks
```bash
# Run all checks (typecheck, lint, test)
make spin

# Run tests
make test
pytest

# Run tests quietly
make test-quiet

# Type checking
make typecheck
mypy .

# Linting
make lint
isort . --check-only
black . --check

# Single test execution
pytest tests/test_specific.py::test_function_name
```

### Building and Packaging
```bash
# Build package
make package-build
poetry build

# Bump version
make package-version-bump-patch
make package-version-bump-prerelease
```

## Architecture

### Core Components

- **`src/wscribe/core.py`**: Defines abstract base classes `Backend` and `Audio`, core data types (`TranscribedData`, `WordData`), and supported models list
- **`src/wscribe/cli/main.py`**: Click-based CLI interface with transcription command and options
- **`src/wscribe/backends/`**: Processing backends (currently faster-whisper)
- **`src/wscribe/sources/`**: Audio source handlers (currently local files)
- **`src/wscribe/writers.py`**: Output format writers (JSON, SRT, VTT)

### Key Design Patterns

- **Modular Backend System**: `Backend` abstract class allows for multiple whisper implementations
- **Source Abstraction**: `Audio` class supports different audio sources (local, future: YouTube, Google Drive)
- **Writer Pattern**: `ResultWriter` hierarchy handles different output formats
- **Data Flow**: Audio Source → Backend Processing → Writer Output

### Environment Requirements

- **WSCRIBE_MODELS_DIR**: Required environment variable pointing to whisper models directory
- Models must be pre-downloaded using provided scripts or manually from HuggingFace

### Web Editor Integration

The `wscribe-editor/` subdirectory contains a separate Svelte-based web application for transcript editing:
- Built with Vite, TypeScript, and Tailwind CSS
- Has its own package.json and build system
- Uses Vitest for testing
- Provides visual timeline editing for transcripts

### Testing Strategy

- Uses pytest with structured logging enabled
- Test files in `tests/` directory
- Golden file testing for output format validation
- mypy for type checking
- isort and black for code formatting