🎵 Music Duplicate Finder
A cross-platform desktop application to find duplicate music files by similar title matching, with filters, multiple view options, and a modern dark/light UI.

https://img.shields.io/badge/python-3.9%2B-blue
https://img.shields.io/badge/license-MIT-green
https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey

✨ Features
🔍 Similar-title detection — finds duplicates even when filenames differ slightly (e.g. 01-wham-last christma.flac vs wham - last christmas.mp3)

🎚️ Adjustable similarity threshold with plain-language labels (Quasi identici, Molto simile, Simile, Vago)

⚡ Fast — parallel metadata reading (8 threads) + rapidfuzz (10–100x faster than difflib) + prefix-based blocking to avoid O(n²) comparisons

🎨 Dark / Light theme — switchable at runtime, persisted between sessions

📊 Column selector — show/hide any column (Gruppo, File, Titolo, Ext, Codec, Bitrate, Formato, Dimensione, Durata)

🎛️ Live filters — filter results by free text, format (e.g. 44.1 kHz · 16 bit), codec, or minimum bitrate

📁 Open files directly — double-click a row to open the file with your system player, or right-click for more options (open containing folder, copy path)

⏹️ Stoppable search — cancel long scans at any time

💾 Persistent config — theme, zoom, and column visibility saved to ~/.trova_duplicati.json

📸 How it works
Scan — recursively collects audio files from a folder (.mp3, .flac, .m4a, .aac, .ogg, .opus, .wav, .wma, .aiff, .ape, .alac, .mp4)

Normalize — extracts a clean title from each filename by stripping:

extensions (including double ones)

bracket groups (...), [...], {...} (feat., remix, live, official, etc.)

leading track numbers (01 -, 01., 01_)

common words like feat, ft, official, remastered, remix, live, hd, 320, kbps

punctuation and extra spaces → lowercase

Read metadata — uses mutagen to extract codec, bitrate, sample rate, bit depth, and duration

Group — compares titles pairwise using rapidfuzz.fuzz.ratio, but only within prefix-based "blocks" (first 3–6 chars) to keep it fast. Groups with ≥2 members are shown as duplicates

Display — results appear in a sortable table with color-coded groups; live filters and column selection apply instantly

🚀 Installation
Requirements
Python 3.9+

pip

Install dependencies
bash
pip install customtkinter mutagen rapidfuzz
💡 rapidfuzz is strongly recommended (10–100x faster). Without it, the program falls back to difflib.SequenceMatcher and shows a warning on startup.

💡 mutagen is required to read codec, bitrate, sample rate, and duration. Without it those columns will show ?.

Run
bash
python trova_duplicati.py
🖱️ Usage
1. Select a folder
Click 📁 Sfoglia and choose the music folder to analyze.

2. Adjust the similarity threshold (optional)
Use the slider to control how strictly titles must match:

Value	Label	Example match
0.95+	🎯 Quasi identici	song vs song.
0.85+	🔒 Molto simile	song vs song (official)
0.75+	≈ Simile (recommended)	song vs song remastered
< 0.75	🌊 Vago (many false positives)	song vs song live 2020
Click ❓ Cosa significa? for an in-app explanation.

3. Start the search
Click ▶ AVVIA RICERCA. You can press ⏹ STOP at any time.

4. Explore results
Double-click a row (or press Enter) → opens the file with your default audio player

Right-click a row → context menu with:

▶ Open file

📁 Open containing folder (with the file selected on Windows/macOS)

📋 Copy path

Use the 🔍 Filtra risultati panel to filter by text, format, codec, or minimum bitrate

Click 📊 Colonne to choose which columns to display

5. Customize the UI
Tema dropdown: switch between scuro (dark) and chiaro (light)

Zoom dropdown: scale all widgets from 80% to 125%

All preferences are saved automatically and restored on the next launch.

🧠 Example
Given these two files in the same folder:

text
01-wham-last christma.flac
wham - last christmas.mp3
The program:

Normalizes both filenames to:

wham last christma

wham last christmas

Computes a similarity of ≈ 0.97

Groups them as duplicates with any threshold ≤ 0.95

Displays both rows with their respective format, bitrate, and duration

⚙️ Performance
Library size	Time (without rapidfuzz)	Time (with rapidfuzz)
500 files	~30 s	~2 s
2,000 files	~3 min	~10 s
5,000 files	~20 min	~40 s
Performance is achieved through:

8-thread parallel metadata reading via ThreadPoolExecutor

Prefix-based blocking — only titles sharing the first 3–6 characters are compared

Pre-filters — length difference >35% and prefix similarity <0.6 skip the expensive comparison

rapidfuzz — C++ implementation of string similarity, 10–100x faster than difflib

📂 Project structure
text
trova_duplicati.py   # Complete application (single file)
Everything is contained in one file for easy distribution. The structure:

Section	Purpose
Helper functions	pulisci_titolo, similarita, formatta_*, leggi_metadati
OS helpers	apri_file, apri_cartella, copia_negli_appunti
App class	Main CustomTkinter window
_build_ui	Layout construction
_scansiona	Worker thread (scan + metadata + grouping)
_raggruppa_fast	Fast duplicate detection
_gruppi_correnti	Single source of truth for visible rows (respects filters)
_popola_tabella	Rebuilds the table with the current column order
🛠️ Configuration
Settings are stored at:

Windows: C:\Users\<you>\.trova_duplicati.json

macOS / Linux: ~/.trova_duplicati.json

Example:

json
{
  "tema": "scuro",
  "scala": "100%",
  "colonne_nascoste": ["ext", "durata"]
}
Delete the file to reset to defaults.

🐛 Troubleshooting
Problem	Fix
Codec/bitrate show ?	Install mutagen: pip install mutagen
Search is slow	Install rapidfuzz: pip install rapidfuzz
No duplicates found	Lower the similarity threshold (try 0.75)
Too many false positives	Raise the threshold (try 0.88)
UI is tiny on HiDPI	Use the Zoom dropdown (110%, 125%)
File won't open	Check your OS default audio player association
🤝 Contributing
Contributions are welcome! Feel free to:

Open an issue for bugs or feature requests

Submit a pull request (keep changes focused and documented)

Suggest improvements to the similarity algorithm or UI

📄 License
MIT License — see LICENSE for details.

🙏 Acknowledgements
CustomTkinter — modern UI widgets

mutagen — audio metadata parsing

rapidfuzz — fast string similarity

Built for music lovers tired of manually hunting down song.mp3 vs song (1).flac.

