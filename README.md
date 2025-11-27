## Support & Donations

If you find this project helpful, consider supporting it!

[Donate via PayPal](https://www.paypal.com/paypalme/revrari)

# My Whisper - Speech-to-Text for Emacs

A simple Emacs package that provides speech-to-text functionality using Whisper.cpp. Record audio directly from Emacs and have it transcribed and inserted at your cursor position.

## Demo Video

[![Perfect Speech to Text in Emacs](https://img.youtube.com/vi/bTU9ctFtyBA/0.jpg)](https://youtu.be/bTU9ctFtyBA)

**[Perfect Speech to Text in Emacs](https://youtu.be/bTU9ctFtyBA)** - See the package in action!

## Features

- Record audio with simple key bindings
- Two transcription modes:
  - **Fast mode** (`C-c n`): Uses base.en model for quick transcription
  - **Accurate mode** (`C-c v`): Uses medium.en model for more accurate results
- **Vocabulary hints**: Provide a custom vocabulary file to improve recognition of proper nouns and specialized terms (e.g., Greek names like Socrates, Alcibiades, Diotima)
- Automatic transcription using Whisper.cpp
- Text insertion at cursor position
- Non-blocking recording with user-controlled stop

## Prerequisites

Before setting up this package, you need to install the following system dependencies:

### 1. Sox (for audio recording)

**Ubuntu/Debian:**
```bash
sudo apt install sox
```

**macOS:**
```bash
brew install sox
```

**Arch Linux:**
```bash
sudo pacman -S sox
```

### 2. Whisper.cpp

Clone and build Whisper.cpp:

```bash
# Clone the repository
git clone https://github.com/ggerganov/whisper.cpp.git
cd whisper.cpp

# Build the project
make

# Download models
# For fast mode (required)
bash ./models/download-ggml-model.sh base.en

# For accurate mode (required)
bash ./models/download-ggml-model.sh medium.en
```

By default, the package expects Whisper.cpp to be installed in `~/whisper.cpp/`. If you install it elsewhere, you can customize the location (see Configuration section below).

## Installation

### Option 1: Manual Installation

1. Clone or download this repository:
   ```bash
   git clone <your-repo-url> ~/.emacs.d/my-whisper
   ```

2. Add the following to your `init.el` or `.emacs` file:
   ```elisp
   ;; Add the package directory to load-path
   (add-to-list 'load-path "~/.emacs.d/my-whisper")

   ;; Load the package
   (require 'my-whisper)
   ```

### Option 2: Direct File Installation

1. Copy `my-whisper.el` to your Emacs configuration directory:
   ```bash
   cp my-whisper.el ~/.emacs.d/
   ```

2. Add the following to your `init.el`:
   ```elisp
   ;; Load the whisper package
   (load-file "~/.emacs.d/my-whisper.el")
   ```

### Option 3: Using use-package

If you use `use-package`, add this to your `init.el`:

```elisp
(use-package my-whisper
  :load-path "~/.emacs.d/my-whisper"
  :custom
  (my-whisper-homedir "~/whisper.cpp/")  ; Optional: customize if different
  (my-whisper-model "ggml-base.en.bin")  ; Optional: choose default model
  :bind (("C-c v" . my-whisper-transcribe-fast)
         ("C-c n" . my-whisper-transcribe)))
```

## Usage

### Key Bindings

- **`C-c n`**: Fast mode (base.en model) - quicker transcription, suitable for most use cases
- **`C-c v`**: Accurate mode (medium.en model) - slower but more accurate transcription

### Basic Workflow

1. **Start recording**: Press `C-c n` (fast) or `C-c v` (accurate) to begin recording audio
2. **Stop recording**: Press `C-g` to stop recording and start transcription
3. **Get results**: The transcribed text will be automatically inserted at your cursor position

### Example

1. Open any text buffer in Emacs
2. Position your cursor where you want the transcribed text
3. Press `C-c n` for fast transcription or `C-c v` for accurate transcription
4. Speak into your microphone
5. Press `C-g` when finished speaking
6. Wait a moment for transcription to complete
7. The text appears at your cursor position

## Configuration

You can customize my-whisper through Emacs' built-in customization interface or directly in your `init.el`.

### Using Emacs Customize Interface

Run `M-x customize-group RET my-whisper RET` to access all customization options:

- **my-whisper-homedir**: Directory where Whisper.cpp is installed (default: `~/whisper.cpp/`)
- **my-whisper-model**: Which model to use by default
  - `ggml-base.en.bin` - Fast mode (quick, good accuracy)
  - `ggml-medium.en.bin` - Accurate mode (slower, better accuracy)
  - Custom model filename
- **my-whisper-vocabulary-file**: Path to vocabulary hints file (default: `~/.emacs.d/whisper-vocabulary.txt`)

### Custom Configuration in init.el

```elisp
;; Set custom Whisper.cpp installation directory
(setq my-whisper-homedir "/usr/local/whisper.cpp/")

;; Choose default model (base.en or medium.en)
(setq my-whisper-model "ggml-medium.en.bin")

;; Set custom vocabulary file location
(setq my-whisper-vocabulary-file "~/Documents/my-vocabulary.txt")
```

### Custom Key Bindings

To change the key bindings, add to your `init.el`:

```elisp
;; Use different key bindings
(global-set-key (kbd "C-c s") #'my-whisper-transcribe-fast)  ; Fast mode
(global-set-key (kbd "C-c S") #'my-whisper-transcribe)       ; Accurate mode
```

### Custom Vocabulary for Proper Nouns

To improve transcription accuracy for proper nouns, technical terms, or specialized vocabulary, create a vocabulary file at `~/.emacs.d/whisper-vocabulary.txt`.

**Example `~/.emacs.d/whisper-vocabulary.txt`:**
```
This transcription discusses classical Greek philosophy, including scholars and figures such as Thrasymachus, Socrates, Plato, Diotima, Alcibiades, and Phaedrus.
```

**Custom vocabulary location:**
```elisp
(setq my-whisper-vocabulary-file "~/Documents/my-vocabulary.txt")
```

**For detailed guidance** on vocabulary formats, tips, domain-specific examples, and managing multiple vocabularies, see [VOCABULARY-GUIDE.md](VOCABULARY-GUIDE.md).

## Troubleshooting

### Common Issues

1. **"sox: command not found"**
   - Install sox using your system package manager

2. **"whisper-cli: command not found" or path errors**
   - Ensure Whisper.cpp is built and the path is correct
   - Check that `~/whisper.cpp/build/bin/whisper-cli` exists
   - If installed elsewhere, customize `my-whisper-homedir` to match your installation
   - Run `M-x customize-group RET my-whisper RET` to verify paths

3. **No audio recorded**
   - Check your microphone permissions
   - Test sox manually: `sox -d -r 16000 -c 1 -b 16 test.wav`

4. **Transcription not working**
   - The package now validates paths on startup and will show clear error messages
   - Verify the model files exist in `~/whisper.cpp/models/`
   - Run `M-x my-whisper-transcribe-fast` to see validation errors
   - Test whisper-cli manually with a wav file

### Testing the Setup

Test each component individually:

```bash
# Test sox recording (record 5 seconds)
sox -d -r 16000 -c 1 -b 16 test.wav trim 0 5

# Test whisper transcription (fast mode)
~/whisper.cpp/build/bin/whisper-cli -m ~/whisper.cpp/models/ggml-base.en.bin -f test.wav

# Test whisper transcription (accurate mode)
~/whisper.cpp/build/bin/whisper-cli -m ~/whisper.cpp/models/ggml-medium.en.bin -f test.wav
```

## How It Works

1. **Recording**: Uses `sox` to record audio at 16kHz, mono, 16-bit
2. **Processing**: Calls `whisper-cli` with the recorded audio file
3. **Integration**: Captures the output and inserts it into your Emacs buffer
4. **Cleanup**: Automatically cleans up temporary files and buffers

## License

This project is released under the MIT License.

## Contributing

Feel free to submit issues and pull requests to improve this package.
