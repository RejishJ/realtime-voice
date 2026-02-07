# Setting up PipeWire
## First check: PipeWire is active
```bash
pactl info | grep "Server Name"
```
### Expected output
```bash
Server Name: PulseAudio (on PipeWire x.x.x)
```
## If not installed install PipeWire packages
```bash
sudo apt update
sudo apt install -y pipewire pipewire-audio-client-libraries pipewire-pulse wireplumber
```
## Disable PulseAudio (user-level)
```bash
systemctl --user disable pulseaudio.service pulseaudio.socket
systemctl --user stop pulseaudio.service pulseaudio.socket
```
## Enable PipeWire services
```bash
systemctl --user enable pipewire pipewire-pulse wireplumber
systemctl --user start pipewire pipewire-pulse wireplumber
```
## Log out & log back in
**This is important:**
- Click top-right
- Log out
- Log back in
## Verify
```bash
pactl info | grep "Server Name"
```
### Expected output
```bash
Server Name: PulseAudio (on PipeWire x.x.x)
```

---

# List your audio input devices
```bash
arecord -l
```

---

# Project Setup
## Install venv module
```bash
sudo apt install -y python3-venv
```
## Create project structure
```bash
mkdir -p ~/realtime_voice/core/audio
cd ~/realtime_voice
```
## Create a virtual environment
```bash
python3 -m venv venv
source venv/bin/activate
```
### Output
```bash
(venv)
```
> if venv shows up it's working
## Install required Python packages
```bash
pip install sounddevice numpy
sudo apt install -y portaudio19-dev
```
## Create the realtime input stream file
```bash
nano core/audio/input_stream.py
```
> input_stream.py-----------------------------------
## Run it
```bash
python core/audio/input_stream.py
```
## Expected Output
```bash
Chunk received: (320, 1)
Chunk received: (320, 1)
...
```
> Speak into mic → chunks keep coming
Silence → chunks still coming,
Audio never stops, 
That’s realtime streaming.

---

# Streaming STT
## FORCE CPU-ONLY TORCH
```bash
pip install torch --index-url https://download.pytorch.org/whl/cpu
```
> Forces CPU-only Torch, 
Prevents CUDA/NVIDIA installs
## Verify Torch
```bash
pip list | grep torch
```
## Install Whisper
```bash
pip install openai-whisper
```
## Sanity check
```bash
python3 - << 'EOF'
import torch
print("CUDA available:", torch.cuda.is_available())
import whisper
print("Whisper OK")
EOF
```
### Expected output
```bash
CUDA available: False
Whisper OK
```
## Create Whisper STT module
```bash
# Create folder
mkdir -p core/stt
# Create file
nano core/stt/whisper_streaming.py
```
> whisper_streaming.py----------------------------------
## Connect Whisper to your audio stream
```bash
# Edit
nano core/audio/input_stream.py
```
> input_stream.py---------------------------------------
## Make core a Python package
### Create __init__.py files
>To avoid the error below
```bash
ModuleNotFoundError: No module named 'core'
```
>Otherwise file runs directly
