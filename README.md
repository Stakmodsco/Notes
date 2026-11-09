cd ~/Downloads
ipconfig getifaddr en0
python3 -m http.server 8000 --bind 0.0.0.0
