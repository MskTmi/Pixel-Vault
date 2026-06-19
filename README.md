# Pixel Vault 🖼️📦

**English** | [简体中文](README.zh-CN.md)

<img width="818" height="752" alt="Screenshot 1" src="https://github.com/user-attachments/assets/70fa79d0-70c0-4e20-bf88-db67330a5ad2" />

<img width="863" height="748" alt="Screenshot 2" src="https://github.com/user-attachments/assets/875c4920-dfa8-4005-bc0d-b195dfb15b90" />

I built this because I wanted to hide files inside images without messing up how the image looks. Most steganography tools tweak pixels and you can see the noise. Mine doesn't.

**Just drag, drop, and hide.**

---

## What is this exactly?

You take any image (PNG, JPG, doesn't matter) and one or more files (PDF, ZIP, another image, whatever). Pixel Vault stitches them together into one image file that:

- ✅ Opens normally in any image viewer
- ✅ Looks exactly like the original
- ✅ Contains your hidden file(s) intact
- 🔐 Optionally encrypted with AES-256-GCM

When hiding multiple files, they are bundled into a single ZIP archive. The entire ZIP is then hidden (and optionally encrypted) inside the image, so the original carrier photo is preserved as a backup.

Nobody can tell anything is there unless they know where to look. And with encryption enabled, even if someone finds the hidden data, they can't read it without your password.

---

## Wait, how?

Short version: Images have an "end of file" marker. Everything after that is ignored by image software. Pixel Vault just... puts your secret file after that marker. That's it.

**Encrypted mode** adds an extra layer: before appending, the hidden file (filename + contents) is encrypted with AES-256-GCM using a password you provide. The key is derived via PBKDF2 (150,000 iterations, SHA-256). A binary footer lets the decoder locate the payload in O(1) without scanning for text markers.

Long version: I spent way too long reading PNG specifications at 2am so you don't have to.

---

## Why not the usual LSB method?

Because I tried it and:

- The image gets slightly discolored
- You can only hide small files
- It's slow as hell

Tail-end method = unlimited file size, zero quality loss, instant encoding.

---

## How to use it

**To hide file(s):**

1. Pick a cover image
2. Pick one or more secret files
3. Toggle encryption on (recommended) and enter a password
4. Click encode
5. You get a new image. Send it anywhere.

**To uncover the file(s):**

1. Drop the encoded image in
2. If it's encrypted, enter the password
3. Click decode
4. Get your original file(s) back
   - Single file: downloaded directly with its original name
   - Multiple files: downloaded as a ZIP archive

The decoder auto-detects whether the image is encrypted or legacy, so old files still work. No servers. No accounts. No "upload to cloud." Everything happens in your browser tab.

---

## Encryption details

| Aspect         | Spec                               |
| -------------- | ---------------------------------- |
| Algorithm      | AES-256-GCM (authenticated)        |
| Key derivation | PBKDF2-SHA-256, 150,000 iterations |
| Salt           | 16 random bytes (per file)         |
| IV             | 12 random bytes (per file)         |
| Integrity      | GCM auth tag (built-in)            |
| Filename       | Encrypted along with file contents |
| Dependencies   | None — uses native Web Crypto API  |

Toggle encryption off to use the original plaintext marker scheme (backward compatible with files encoded before encryption existed). In multi-file mode the marker header is `"ZIP"` and the payload is the ZIP bytes.

---

## What can I hide?

Anything. I've hidden:

- Whole movies inside vacation photos
- A ZIP bomb inside a meme (don't do this)
- My password manager backup inside my dog picture

---

## Known quirks

- Some image hosts strip extra data. Discord is fine. Twitter is not. Test before you rely on it.
- If your image is 10MB and you hide a 100MB file, the result is 110MB. Physics.
- Gmail sometimes blocks these. Use WeTransfer-style links instead of attachments.
- **Lost password = unrecoverable data.** There is no backdoor. Write it down somewhere safe.

---

## PWA & offline

Pixel Vault is installable as a Progressive Web App — add it to your home screen / desktop and it runs as a standalone app. A service worker caches the app shell so it works fully offline after the first visit. The encryption logic itself never touches the network.

---

## Why did you make this?

Honestly? I thought it was cool that old BBS users did this in the 90s and nobody really does it anymore. Also I wanted to see if I could build something useful in just HTML/JS with zero dependencies.

Turns out I could.

---

## One more thing

If you actually use this for anything illegal, that's on you. I made it because steganography is fascinating, not so you can leak corporate secrets or whatever.

---

**Pixel Vault is free, open source, and always will be.**

---

## Acknowledgements

Based on [Pixel-Vault](https://github.com/Achilles9z/Pixel-Vault) by Anurag, licensed under MIT. This fork adds optional AES-256-GCM encryption, multi-file support, PWA support, and SEO improvements while keeping the original single-file, zero-dependency design.

---

## License

MIT
