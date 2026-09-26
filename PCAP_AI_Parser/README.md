# PCAP AI Parser

> AI-powered bulk PCAP analysis pipeline using `dpkt` and `tshark`.

## Project Structure

```
PCAP_AI_Parser/
├── src/
│   └── pcap_ai_parser/
│       ├── __init__.py
│       ├── cli.py              # Click CLI entry-point
│       ├── parser/
│       │   ├── __init__.py
│       │   ├── dpkt_parser.py  # dpkt-based fast parser
│       │   └── tshark_parser.py # tshark subprocess parser
│       ├── models/
│       │   ├── __init__.py
│       │   └── packet.py       # Packet dataclass
│       └── utils/
│           ├── __init__.py
│           └── logging.py      # Loguru setup
├── data/
│   └── samples/                # Place .pcap/.pcapng files here (git-ignored)
├── tests/
│   ├── __init__.py
│   ├── conftest.py
│   └── test_dpkt_parser.py
├── scripts/
│   └── generate_sample_pcap.py # Helper to generate synthetic test PCAPs
├── output/                     # Parsed output (git-ignored)
├── .gitignore
├── pyproject.toml
└── README.md
```

## Quick Start

### 1. Install dependencies

```bash
# Install uv (if not present)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Create virtual environment and install all deps
uv venv .venv
source .venv/bin/activate
uv pip install -e ".[dev]"
```

### 2. Install tshark (system package)

```bash
# Arch / Manjaro
sudo pacman -S wireshark-cli

# Ubuntu / Debian
sudo apt install tshark

# macOS
brew install wireshark
```

### 3. Parse a PCAP file

```bash
# Quick parse with dpkt (fastest)
pcap-parse parse data/samples/sample.pcap

# Parse with tshark backend
pcap-parse parse --backend tshark data/samples/sample.pcap

# Generate a synthetic sample PCAP for testing
python scripts/generate_sample_pcap.py
```

### 4. Run tests

```bash
pytest
```

## Architecture

```
PCAP file
    │
    ▼
┌─────────────────────────────────┐
│         Parser Layer            │
│  ┌─────────────┐ ┌───────────┐  │
│  │ dpkt Parser │ │  tshark   │  │
│  │  (fast I/O) │ │ (deep DPI)│  │
│  └──────┬──────┘ └─────┬─────┘  │
└─────────┼──────────────┼────────┘
          │              │
          ▼              ▼
      PacketRecord dataclass (normalised)
          │
          ▼
      pandas DataFrame
          │
          ▼
      AI Analysis Layer (Phase 2)
```

## Development

```bash
# Linting
ruff check src/ tests/

# Type checking
mypy src/

# Coverage report
pytest --cov=pcap_ai_parser --cov-report=html
```
