# Project Setup

## Prerequisites
- Python 3.11+

## Setup

```bash
# Clone repository
git clone <repository-url>
cd <project-directory>

# Buat dan aktivasi virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# Install dependencies
pip install -r requirements.txt
```

## Update Requirements ( jika install package baru )

```bash
# Setelah install package baru
pip freeze > requirements.txt
```


## Deactivate

```bash
deactivate
```