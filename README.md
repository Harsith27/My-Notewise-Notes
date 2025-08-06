# 📚 My Notewise Notes — Automated GitHub Sync ✨

---

## 📝 Problem Statement

I take handwritten and PDF notes using the **Notewise** app on my tablet.  
Over time, I ran into three major issues:

1. **Duplicate Chaos**  
   - Notewise saves updated versions as new files like `mysql (1).pdf`, `mysql (2).pdf` instead of overwriting the old one.  
   - This clutters my folder and makes it confusing to figure out which file is the latest.

2. **Manual Sync is a Pain**  
   - I had to manually move files, delete older ones, and push updates to GitHub every time I made a change.

3. **Cross-Device Access**  
   - I wanted to instantly access my updated notes on my phone or laptop without manual uploads.

I needed a solution that was **automatic, reliable, and clutter-free**.

---

## 💡 The Solution

This repository is powered by an **automated script running in Termux** on my Android tablet.  
It continuously **monitors my notes folder**, detects changes, removes outdated versions, and pushes the latest files to GitHub automatically.

### ✅ What This Achieves
- Only the **most recent** version of each note exists.
- **No duplicate file names** like `(1)`, `(2)`, etc.
- Instant sync — as soon as I save a note, it’s live on GitHub.
- Works **quietly in the background** via `tmux`.

---

## 🛠 How It Works

The automation uses:
- **Termux** → Runs the script on Android.
- **inotifywait** → Watches the folder for file changes in real time.
- **Git + SSH** → Securely pushes updates to GitHub without password prompts.
- **tmux** → Keeps the script running in the background.
- **Duplicate cleanup logic** → Finds and removes older versions of the same note.

---

## ⚙️ Detailed Setup Guide

Follow these steps to set up your own version.  
**Note:** Replace placeholders like `<your_email@example.com>` and `<your_github_username>` with your actual details.  
I’ll also give my own folder path as an example so you know what it looks like.

---

### 1️⃣ Install Termux and give storage access
- Download **Termux** from [F-Droid](https://f-droid.org/packages/com.termux/) (recommended) or Google Play.  
- Open Termux and run:
  ```bash
  termux-setup-storage
  ```
  This will create a `storage` directory inside Termux that links to your Android storage.  

📌 **Example (my Notewise folder path):**
```
/data/data/com.termux/files/home/storage/shared/My Notes
```
Yours may differ — check where Notewise saves your notes.

---

### 2️⃣ Update Termux and install required tools
In Termux, run:
```bash
pkg update && pkg upgrade
pkg install inotify-tools poppler tmux git
```
- **inotify-tools** → watches folders in real time.  
- **poppler** → checks PDF details if safety checks are used.  
- **tmux** → keeps the script running in the background.  
- **git** → manages commits & pushes to GitHub.

---

### 3️⃣ Set up Git & SSH keys
1. Generate a new SSH key:
   ```bash
   ssh-keygen -t ed25519 -C "<your_email@example.com>"
   ```
   Press **Enter** for all prompts (no passphrase).

2. Show your public key:
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
   Copy everything shown.

3. Add it to GitHub: [GitHub SSH Keys Settings](https://github.com/settings/keys) → **New SSH key** → paste → Save.

4. Test connection:
   ```bash
   ssh -T git@github.com
   ```

---

### 4️⃣ Clone your GitHub repo
Replace `<your_github_username>` with your username:
```bash
git clone git@github.com:<your_github_username>/My-Notewise-Notes.git
```
This creates the `My-Notewise-Notes` folder inside Termux.

---

### 5️⃣ Create the watcher script
```bash
nano ~/watch-notes.sh
```
Paste your watcher script logic here.  
The script should:
1. Watch your Notewise folder.
2. Remove older versions of the same file.
3. Copy the latest to your repo folder.
4. Commit & push to GitHub.

Save & exit (`CTRL+O`, `Enter`, `CTRL+X`).

Make it executable:
```bash
chmod +x ~/watch-notes.sh
```

---

### 6️⃣ Run the script in the background
Start a tmux session:
```bash
tmux new -s notes ~/watch-notes.sh
```
- **Detach**: `CTRL+B`, then `D`  
- **Reattach**:  
  ```bash
  tmux attach -t notes
  ```

---

### 7️⃣ (Optional) Auto-start on Termux launch
```bash
nano ~/.bashrc
```
Add this at the bottom:
```bash
if ! tmux has-session -t notes 2>/dev/null; then
    tmux new-session -d -s notes "$HOME/watch-notes.sh"
fi
```

---

## 🧪 Testing
- Save or update a PDF in your Notewise folder.
- The script should detect it, remove older tracked versions, and push the latest one to GitHub.
- Check your repo to confirm the update.

---

## ⚠️ Notes & Warnings
- **Back up first** — This deletes older versions of notes in the repo.
- The script is tailored for PDFs but can be adapted for other file types.
- Make sure Termux has storage permissions enabled.

---
