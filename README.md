# My Whisper - Speech-to-Text for Emacs - My enhanced fork.

A simple Emacs package that provides speech-to-text functionality using Whisper.cpp.

Record audio directly from Emacs and have it transcribed and inserted in current buffer at point.
The package provides `my-whisper-mode`, a global minor mode that starts audio recording and transcribes text when stopped.
It also provides the `my-whisper-transcribe-file` command to transcribe an already recorded WAV audio file.

I will eventually change this page in my fork.  This is under construction.

More information on how to use this fork of the project is available
in my PEL project inside the [Writing Tools PDF](https://raw.githubusercontent.com/pierre-rouleau/pel/master/doc/pdf/writing-tools.pdf#page=2).
[PEL](https://github.com/pierre-rouleau/pel#readme) can install the package automatically and creates bindings for the global commands.



## Demo Video

[![Perfect Speech to Text in Emacs](https://img.youtube.com/vi/bTU9ctFtyBA/0.jpg)](https://youtu.be/bTU9ctFtyBA)

**[Perfect Speech to Text in Emacs](https://youtu.be/bTU9ctFtyBA)** - See the package in action!

## Features

- Record audio with simple key bindings
- Several transcription modes selected by customization, including:
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

## Usage


This provides the **my-whisper-mode**, a global minor mode that provides the ability to record audio and then process it with whisper.cpp and insert transcribed text in the current buffer at point.

⚠️  🚧 🚧 🚧 Most of the text below is obsolete and needs to be updated.

### Key Bindings

- **`M-x my-whisper-mode`**: Start recording using the selected model.
- **`C-c .`**: Stop recording, transcribe recorded speech into text and insert in current buffer at point.
- **`C-c ,`**: Start recording again.
- **`M-x my-whisper-mode`**: Turn my-whisper-mode off.


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
