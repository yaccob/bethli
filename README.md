<p align="center"><img src="bethli.svg" width="128" alt="The BeThLi icon: a photo whose corner dissolves into pixels"></p>

# BeThLi

Hide files in images and reveal them again: fast, purely algorithmic (no AI), as a desktop app and on the command line.

This repository holds the [releases](https://github.com/yaccob/bethli/releases) of BeThLi. The source code is not public at the moment.

## What it does

- **Any cover image, any files and texts.** The files go into the lowest bits of every colour channel of a lossless PNG. Any PNG can be the cover, and since app 0.5.0 and command line 0.7.0 any JPEG photo: it either stays a JPEG (the data go into its compressed values, room for kilobytes, the picture unchanged) or becomes a PNG, scaled to 9/10 of its edges so that the grid of the JPEG does not give the change away. Texts travel next to the files (app 0.5.0, command line 0.7.0).
- **Encryption is a separate layer.**
  - With a passphrase, everything is encrypted (Argon2id with 256 MiB, XChaCha20-Poly1305).
  - For a person, with their public key (since app 0.4.0 and command line 0.6.0): nobody has to agree on a passphrase. Each person makes their own key once and passes on its public part, as a short line or a file. The first image is quantum-safe once the sender has the whole key, the following ones through a pair key from ML-KEM-768 and X25519. Images can be signed provably (Ed25519 and ML-DSA-65).
  - For yourself, with your own key (since app 0.5.0 and command line 0.7.0): only your keyring reads it, quantum-safe.
  - Without one, the data is only whitened: it still looks like noise, but anyone with BeThLi can read it.
- **No readable signature.** Without BeThLi, an image with hidden files cannot be told from noise in either mode. Salt and nonce are random for every image.
- **Bits per channel** from 1 to 8. The cover keeps its size by default; on request it is scaled to exactly the size the files need, or capped anywhere in between (app 0.5.0: a slider; command line 0.7.0: `--size`).
- **Effects:** with 6 or 7 bits per channel, the noise becomes part of a deliberate look (pop art, retro, stencil and more). The reader never needs to know the effect.
- **Checked output:** an image is written only after a separate process has read it back and found the same files.
- **No metadata in the output.**

Send the image as a file, not as a photo through a messenger: any lossy re-encoding destroys the hidden data, a JPEG written by BeThLi included (it must not be saved again by an image program).

Images written by app 0.5.0 or command line 0.7.0 or later need app 0.5.0 or command line 0.7.0 or later to be read (format version 3). Newer versions read every older image.

## Installation

Download from the [releases](https://github.com/yaccob/bethli/releases). Each package contains everything it needs. Next to each file is its SHA-256 (`.sha256`).

The releases page is the only place to get BeThLi: the licence does not allow passing the packages on. Whoever receives an image with hidden files downloads BeThLi here too.

### Desktop app

Releases `gui-vX.Y.Z`. The app is in English and German.

| System | File | Notes |
|---|---|---|
| macOS 11 or later, Apple silicon | `BeThLi_<version>_aarch64.dmg` | Signed and notarized. Open it, accept the licence, and drag BeThLi into Applications. |
| macOS 11 or later, Intel | `BeThLi_<version>_x64.dmg` | The same, for Macs with an Intel processor. |
| Windows 10 or later, 64-bit | `bethli-gui_<version>_x64-setup.exe` | Not signed yet: SmartScreen may say "Windows protected your PC"; choose "More info", then "Run anyway". |
| Linux, x86-64 | `bethli-gui_<version>_amd64.deb` or `bethli-gui_<version>_x86_64.AppImage` | The `.deb` for Debian and Ubuntu; the AppImage for any distribution (make it executable first). |

With [Homebrew](https://brew.sh) on macOS: `brew install --cask yaccob/tap/bethli-gui`.

### Command line

Releases `cli-vX.Y.Z`. The command is `bethli`.

| System | File | Notes |
|---|---|---|
| macOS 11 or later, Apple silicon | `bethli-cli_<version>_aarch64-apple-darwin.tar.gz` | Signed and notarized. |
| macOS 11 or later, Intel | `bethli-cli_<version>_x86_64-apple-darwin.tar.gz` | The same, for Macs with an Intel processor. |
| Windows 10 or later, 64-bit | `bethli-cli_<version>_x86_64-pc-windows-msvc.zip` | Not signed yet: SmartScreen may say "Windows protected your PC"; choose "More info", then "Run anyway". |
| Linux, x86-64 | `bethli_<version>_amd64.deb` or `bethli-cli_<version>_x86_64-unknown-linux-gnu.tar.gz` | The `.deb` (package `bethli`) for Debian and Ubuntu; the archive for any distribution. |

With [Homebrew](https://brew.sh) on macOS: `brew install yaccob/tap/bethli`.

```sh
bethli plan --cover photo.png notes.pdf                      # does it fit?
bethli hide --cover photo.png --out photo-2.png notes.pdf    # hide, encrypted
bethli hide --cover photo.jpg --out photo-2.jpg notes.txt    # a JPEG that stays a JPEG
bethli reveal photo-2.png --out revealed/                    # reveal
bethli key new --name Anna                                   # your own key
bethli hide --cover photo.png --out photo-2.png --to Ben notes.pdf   # hide for a contact
bethli --help                                                # everything else
```

`brew upgrade` brings new versions of both.

## License

BeThLi is licensed under the [PolyForm Strict License 1.0.0](LICENSE):

- **Allowed:** using it for any noncommercial purpose, by individuals and by noncommercial organisations.
- **Not allowed:** commercial use, passing the software on, changing it or building new works on it.
- **Anything else,** such as a translation, a package for a distribution or commercial use, needs the author's permission: please ask.
