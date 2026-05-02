# 🛠️ Go Tools for VS Code — Windows Developer Toolkit

> **5 Windows tools that automate Go development in VS Code**  
> Check, build, watch, sync, and clean — all from a single keyboard shortcut

[![Platform](https://img.shields.io/badge/platform-Windows-blue?logo=windows)](https://github.com)
[![Go](https://img.shields.io/badge/Go-1.21+-00ADD8?logo=go)](https://go.dev/dl/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![VS Code](https://img.shields.io/badge/VS%20Code-ready-007ACC?logo=visualstudiocode)](https://code.visualstudio.com)

---

## 📋 Table of Contents

- [The Tools](#-the-tools)
- [System Requirements](#-system-requirements)
- [Install Go](#-install-go)
- [Install VS Code](#-install-vs-code)
- [Setup](#-setup)
- [GoCheckAll](#-gocheckall)
- [GoBuildRelease](#-gobuildrelease)
- [GoWatcher](#-gowatcher)
- [GoModSync](#-gomodsync)
- [GoClean](#-goclean)
- [VS Code Tasks](#-vs-code-tasks)
- [FAQ](#-faq)
- [Author](#-author)

---
![Ảnh minh họa 1](https://raw.githubusercontent.com/KieuManh366377/Go-Tools-for-VS-Code-Windows-Developer-Toolkit/main/Go%20Tools%20for%20VS%20Code_1.png)

![Ảnh minh họa 2](https://raw.githubusercontent.com/KieuManh366377/Go-Tools-for-VS-Code-Windows-Developer-Toolkit/main/Go%20Tools%20for%20VS%20Code_2.png)

## 🎯 The Tools

| Tool | Description | Shortcut |
|------|-------------|----------|
| 🔍 **GoCheckAll** | Static analysis + go vet + staticcheck + build all projects in parallel | `Ctrl+Shift+B` |
| ⚙️ **GoBuildRelease** | Check → embed VersionInfo → build DLL amd64 + x86 with garble | Task menu |
| 👀 **GoWatcher** | Watch `*.go` files — auto-run checker on every save (debounce 800ms) | Task menu |
| 📦 **GoModSync** | Run `go mod tidy` in parallel for all projects | Task menu |
| 🧹 **GoClean** | Remove build artifacts + `go clean -cache` for all projects | Task menu |

### How they work together

```
Open VS Code
    │
    ├─ Task menu → GoWatcher        ← runs in background, dedicated terminal
    │       │
    │       └─ Edit *.go → Ctrl+S   ← GoWatcher auto-checks after 800ms
    │                                  OK / FAIL appears immediately
    │
    ├─ Ctrl+Shift+B → GoCheckAll    ← full check before every commit
    │
    ├─ Task menu → GoBuildRelease   ← build release DLL when ready
    │
    ├─ Task menu → GoModSync        ← tidy all go.mod files at once
    │
    └─ Task menu → GoClean          ← clean up all build artifacts
```

---

## 💻 System Requirements

| Component | Requirement |
|-----------|-------------|
| 🖥️ OS | Windows 10 / 11 (64-bit) |
| 🐹 Go | **1.21** or later |
| 📝 VS Code | Recommended |
| 🔨 GCC / MSYS2 | Only for CGo / DLL projects |

---

## 🐹 Install Go

### Step 1 — Download

**https://go.dev/dl/**

| Version | File |
|---------|------|
| Windows 64-bit *(recommended)* | `go1.xx.x.windows-amd64.msi` |
| Windows 32-bit | `go1.xx.x.windows-386.msi` |

### Step 2 — Install

Run the `.msi` → Next → Next → Finish.  
Go automatically adds itself to your system `PATH`.

### Step 3 — Verify

Open **Command Prompt** (`Win+R` → `cmd` → Enter):

```bash
go version
# Expected: go version go1.xx.x windows/amd64
```

### Step 4 — Install optional tools

```bash
# staticcheck — advanced linter (used by GoCheckAll)
go install honnef.co/go/tools/cmd/staticcheck@latest

# goversioninfo — embed VersionInfo into DLL (used by GoBuildRelease)
go install github.com/josephspurrier/goversioninfo/cmd/goversioninfo@latest

# garble — obfuscate + reduce DLL size (used by GoBuildRelease)
go install mvdan.cc/garble@latest
```

### Step 5 — Install GCC *(CGo / DLL only)*

Download **MSYS2**: **https://www.msys2.org/**

Open **MSYS2 UCRT64** terminal:

```bash
# GCC 64-bit
pacman -S mingw-w64-ucrt-x86_64-gcc

# GCC 32-bit
pacman -S mingw-w64-i686-gcc
```

Default GCC paths the tools look for:
- 64-bit: `C:\msys64\ucrt64\bin`
- 32-bit: `C:\msys64\mingw32\bin`

> 💡 Pure Go projects do **not** need GCC.

---

## 📝 Install VS Code

### Step 1 — Download

**https://code.visualstudio.com/**

### Step 2 — Install the Go extension

`Ctrl+Shift+X` → search **"Go"** → install by **Go Team at Google**

### Step 3 — Install Go tools

`Ctrl+Shift+P` → `Go: Install/Update Tools` → select all → OK

### Step 4 — Recommended settings

Create `.vscode\settings.json`:

```json
{
    "go.useLanguageServer": true,
    "go.lintTool": "staticcheck",
    "go.formatTool": "goimports",
    "editor.formatOnSave": true,
    "[go]": {
        "editor.defaultFormatter": "golang.go"
    }
}
```

---

## 📦 Setup

### Step 1 — Download the EXEs

Go to **[Releases](../../releases)** and download:

- `GoCheckAll.exe`
- `GoBuildRelease.exe`
- `GoWatcher.exe`
- `GoModSync.exe`
- `GoClean.exe`

### Step 2 — Place in `.vscode\`

```
MyWorkspace\
├── 📂 .vscode\
│   ├── ⚙️  GoCheckAll.exe
│   ├── ⚙️  GoBuildRelease.exe
│   ├── ⚙️  GoWatcher.exe
│   ├── ⚙️  GoModSync.exe
│   ├── ⚙️  GoClean.exe
│   ├── 📄 tasks.json         ← create this (see VS Code Tasks section)
│   └── 📄 settings.json      ← optional
│
├── 📂 project-one\
│   └── 📄 go.mod
├── 📂 project-two\
│   └── 📄 go.mod
└── 📂 project-three\
    └── 📄 go.mod
```

### Step 3 — Open the workspace root

Open the **parent folder** in VS Code — not a project sub-folder:

```
✅ Open: D:\MyWorkspace\          (contains project-one\, project-two\)
❌ Not:  D:\MyWorkspace\project-one\
```

---

## 🔍 GoCheckAll

Checks **all Go projects in parallel** — 8 steps per project.

### What it checks

| Step | Check | Description |
|------|-------|-------------|
| 1 | ✅ go.mod | Verifies `go.mod` exists |
| 2 | ✅ Packages | Lists `package` declarations per file |
| 3 | ✅ Duplicate funcs | Detects functions with the same name across files |
| 4 | ✅ Duplicate exports | Detects duplicate `//export` (CGo / DLL) |
| 5 | 🔧 go vet | Official Go static analysis |
| 6 | 🔍 staticcheck | Advanced linter (auto-detected) |
| 7 | 🏗️ build amd64 | Test build for Windows 64-bit |
| 8 | 🏗️ build 386 | Test build for Windows 32-bit |

### Usage

```bash
# Via VS Code — Ctrl+Shift+B (easiest)

# Via Command Prompt
GoCheckAll.exe                    # auto-detect projects
GoCheckAll.exe D:\GoProjects      # specific directory
GoCheckAll.exe D:\GoProjects 2    # limit to 2 threads
```

### Sample output — all OK

```
=== GO MULTI PROJECT CHECKER ===
Root    : D:\GoProjects
Threads : 4

Tim thay: 3 project(s)
  [1] myapi    (D:\GoProjects\myapi)
  [2] mylib    (D:\GoProjects\mylib)
  [3] mytool   (D:\GoProjects\mytool)

=====================================
         BAO CAO TONG HOP
=====================================

[OK]   myapi    (1.2s)
[OK]   mylib    (890ms)
[OK]   mytool   (1.1s)

=====================================
OK  : 3  |  FAIL: 0  |  Tong: 3
KET QUA: TAT CA OK
=====================================
```

### Sample output — errors found

```
[FAIL] mylib  (450ms)
       [FAIL] Trung ham "Init" trong config.go (lan dau thay o: main.go)
       [FAIL] Export trung: "MyFunc" xuat hien 2 lan
       [FAIL] Exit code 1

Log chi tiet: C:\Users\...\AppData\Local\Temp\GoMultiProject.log
```

> 💡 Detailed log opens automatically when errors are found.

### Log prefix legend

| Prefix | Meaning |
|--------|---------|
| `[INFO]` | General information |
| `[OK]  ` | ✅ Check passed |
| `[WARN]` | ⚠️ Warning — continues |
| `[FAIL]` | ❌ Error — needs fixing |

---

## ⚙️ GoBuildRelease

Builds **DLL 32-bit + 64-bit** from a Go project in 4 steps.

### Build pipeline

```
Step 1 — GoCheckAll
         Run full check on the project
         → If any FAIL: stop immediately, do not build
         → If all OK: continue

Step 2 — VersionInfo (optional)
         If versioninfo.json exists in project folder:
         → Run goversioninfo -platform-specific
         → Generates resource_windows_*.syso
         → Go compiler embeds it into the DLL automatically

Step 3 — Build DLL amd64 + x86
         Uses garble -tiny -seed=random if garble is installed
         → Smaller file (~350KB less than go build)
         → Fewer AV false positives
         Falls back to go build if garble not found

Step 4 — Cleanup
         Remove *.syso, *.h temp files automatically
         Open output folder in Explorer
```

### Usage

```bash
# Via VS Code Task menu
# → ⚙️ Go • Build — GoBuildRelease.exe

# Via Command Prompt
GoBuildRelease.exe <ProjectPath> <OutputDir>
GoBuildRelease.exe D:\GoProjects\mylib D:\Release
```

### Output structure

```
D:\Release\
├── 📂 amd64\
│   └── 🔧 mylib.dll    ← Windows 64-bit
└── 📂 x86\
    └── 🔧 mylib.dll    ← Windows 32-bit
```

### Add VersionInfo to your DLL

Create `versioninfo.json` in your Go project folder:

```json
{
    "FixedFileInfo": {
        "FileVersion":    { "Major": 1, "Minor": 0, "Patch": 0, "Build": 0 },
        "ProductVersion": { "Major": 1, "Minor": 0, "Patch": 0, "Build": 0 }
    },
    "StringFileInfo": {
        "CompanyName":      "Your Name",
        "FileDescription":  "My Go DLL Library",
        "FileVersion":      "1.0.0.0",
        "InternalName":     "mylib",
        "LegalCopyright":   "Copyright 2025 Your Name",
        "OriginalFilename": "mylib.dll",
        "ProductName":      "mylib",
        "ProductVersion":   "1.0.0.0"
    },
    "VarFileInfo": {
        "Translation": { "LangID": "0409", "CharsetID": "04B0" }
    }
}
```

### DLL size comparison

| Build method | Size | AV alerts |
|---|---|---|
| `go build ./...` | ~13,800 KB | high |
| `go build .` + `-s -w` | ~7,408 KB | baseline |
| `garble -tiny` + `-s -w` | **~7,050 KB** | fewer |

---

## 👀 GoWatcher

Watches `*.go` files and **automatically runs GoCheckAll** after every save.

### How debounce works

```
Without debounce:
  Save main.go   → check runs (1)
  Save config.go → check runs (2)   ← 3 runs in 1 second
  Save utils.go  → check runs (3)

With debounce 800ms:
  Save main.go   → T=0ms,   reset timer
  Save config.go → T=300ms, reset timer
  Save utils.go  → T=500ms, reset timer
  T=1300ms       → check runs once ✅
```

### Usage

```bash
# Via VS Code Task menu
# → 👀 Go • Watch — GoWatcher.exe

# Via Command Prompt
GoWatcher.exe                       # auto-detect, 800ms debounce
GoWatcher.exe D:\GoProjects         # specific directory
GoWatcher.exe D:\GoProjects 1500    # 1500ms debounce (slow machine)

# Stop: press Ctrl+C in the GoWatcher terminal
```

### Recommended debounce

| Debounce | Use when |
|----------|---------|
| `500ms` | Fast machine, small project |
| `800ms` | **Default** — suits most cases |
| `1500ms` | Slow machine or large project |
| `3000ms` | Very large project, long build |

### Sample output

```
===================================
  GoWatcher v1.0.0
===================================
[INFO] Watching: D:\GoProjects\mylib
[INFO] Save any *.go file to trigger check

── You save main.go ──

┌─ [mylib] File changed — checking...
│  [OK]   go.mod found
│  [OK]   No duplicate functions
│  [OK]   Exit code 0
└─ [mylib] OK  (1.1s)

── You introduce a bug and save ──

┌─ [mylib] File changed — checking...
│  [FAIL] Duplicate func "Init" in config.go
│  [FAIL] Exit code 1
└─ [mylib] FAIL (890ms)
```

### GoWatcher vs GoCheckAll

| | GoCheckAll | GoWatcher |
|--|------------|-----------|
| Runs | Once then exits | Continuously until Ctrl+C |
| Triggered by | `Ctrl+Shift+B` | Saving any `*.go` file |
| Terminal | Shared panel | Dedicated panel |
| Best for | Before committing | While writing code |

---

## 📦 GoModSync

Runs `go mod tidy` **in parallel** for all projects at once.

### What it does

```
go mod tidy:
  → Removes unused dependencies from go.mod
  → Adds missing dependencies to go.mod
  → Updates go.sum to match
  → Downloads any missing modules
```

### Usage

```bash
# Via VS Code Task menu
# → 📦 Go • Sync — GoModSync.exe

# Via Command Prompt
GoModSync.exe                    # auto-detect projects
GoModSync.exe D:\GoProjects      # specific directory
GoModSync.exe D:\GoProjects 8    # 8 parallel threads
```

### Sample output

```
===================================
  GoModSync v1.0.0
===================================
[INFO] Tim thay: 3 project(s)

=====================================
         BAO CAO TONG HOP
=====================================

[OK]   myapi   (1.1s)
[OK]   mylib   (890ms)
[OK]   mytool  (1.2s)

=====================================
OK  : 3  |  FAIL: 0  |  Tong: 3
KET QUA: TAT CA OK
=====================================

[INFO] Log day du: C:\Users\...\AppData\Local\Temp\GoModSync.log
```

> 💡 Detailed log is always saved — opens automatically only when errors occur.

---

## 🧹 GoClean

Removes build artifacts from all projects and optionally clears Go's build cache.

### What gets deleted

| Pattern | Description |
|---------|-------------|
| `<module>.exe` | Temp EXE from `go build ./...` |
| `*.dll` | Temp DLL from build tests |
| `*.h` | C header from `-buildmode=c-shared` |
| `resource_windows_*.syso` | Resource file from goversioninfo |
| `*.o` | CGo object files |
| `go clean` | Standard Go build artifact cleanup |
| `go clean -cache` | Full build cache *(only with `/cache` flag)* |

### Usage

```bash
# Via VS Code Task menu
# → 🧹 Go • Clean — GoClean.exe (/cache)

# Via Command Prompt
GoClean.exe                      # clean artifacts only
GoClean.exe D:\GoProjects        # specific directory
GoClean.exe /cache               # also clear go build cache
GoClean.exe D:\GoProjects /cache # specific directory + cache
```

> ⚠️ Using `/cache` makes the next build slower — Go has to recompile everything.

### Sample output

```
===================================
  GoClean v1.0.0
===================================
[INFO] Tim thay: 3 project(s)
[INFO] Clean cache: NO

------------------------------------------
[INFO] Cleaning: mylib
[OK]   Del: mylib.exe        (1.2 MB)
[OK]   Del: mylib.dll        (6.9 MB)
[OK]   Del: mylib.h          (2.1 KB)
[OK]   Giai phong: 8.1 MB

------------------------------------------
[INFO] Cleaning: myapi
[INFO] Khong co file tam can xoa

=====================================
  KET QUA DON DEP
=====================================
Projects  : 3
Giai phong: 8.1 MB
Thoi gian : 1.2s
=====================================
```

---

## ⚙️ VS Code Tasks

Create `.vscode\tasks.json` with all 5 tools configured:

```json
{
  "version": "2.0.0",
  "options": {
    "shell": {
      "executable": "cmd.exe",
      "args": ["/d", "/c", "chcp 65001 > nul &&"]
    }
  },
  "tasks": [
    {
      "label": "🔍 Go • Check — GoCheckAll.exe (Static analysis + go vet + staticcheck + build amd64/386)",
      "type": "shell",
      "command": "${workspaceFolder}\\.vscode\\GoCheckAll.exe",
      "group": "build",
      "presentation": { "reveal": "always", "panel": "shared", "clear": true }
    },
    {
      "label": "⚙️ Go • Build — GoBuildRelease.exe (Check → VersionInfo → DLL amd64 + x86 + garble)",
      "type": "shell",
      "command": "${workspaceFolder}\\.vscode\\GoBuildRelease.exe",
      "args": ["${workspaceFolder}", "${workspaceFolder}\\release"],
      "group": "build",
      "presentation": { "reveal": "always", "panel": "shared", "clear": true }
    },
    {
      "label": "👀 Go • Watch — GoWatcher.exe (Auto-check on *.go save, debounce 800ms)",
      "type": "shell",
      "command": "${workspaceFolder}\\.vscode\\GoWatcher.exe",
      "isBackground": true,
      "group": "build",
      "presentation": { "reveal": "always", "panel": "dedicated", "clear": true }
    },
    {
      "label": "📦 Go • Sync — GoModSync.exe (go mod tidy in parallel for all projects)",
      "type": "shell",
      "command": "${workspaceFolder}\\.vscode\\GoModSync.exe",
      "group": "build",
      "presentation": { "reveal": "always", "panel": "shared", "clear": true }
    },
    {
      "label": "🧹 Go • Clean — GoClean.exe (Remove *.exe *.dll *.h *.syso + go clean -cache)",
      "type": "shell",
      "command": "${workspaceFolder}\\.vscode\\GoClean.exe",
      "args": ["/cache"],
      "group": "build",
      "presentation": { "reveal": "always", "panel": "shared", "clear": true }
    }
  ]
}
```

### Running tasks

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+B` | GoCheckAll (set as default) |
| `Ctrl+Shift+P` → `Tasks: Run Task` | Pick any tool from the list |

### Task picker in VS Code

```
🔍 Go • Check — GoCheckAll.exe (Static analysis + go vet + staticcheck + build amd64/386)
⚙️ Go • Build — GoBuildRelease.exe (Check → VersionInfo → DLL amd64 + x86 + garble)
👀 Go • Watch — GoWatcher.exe (Auto-check on *.go save, debounce 800ms)
📦 Go • Sync — GoModSync.exe (go mod tidy in parallel for all projects)
🧹 Go • Clean — GoClean.exe (Remove *.exe *.dll *.h *.syso + go clean -cache)
```

> 💡 `cmd.exe` is used instead of PowerShell to support `&&` and UTF-8 output correctly.

---

## ❓ FAQ

**Q: Which folder should I open in VS Code?**

> Open the **workspace root** — the folder that contains your Go projects as sub-folders:
> ```
> ✅ Open: D:\MyWorkspace\          (has project-one\, project-two\)
> ❌ Not:  D:\MyWorkspace\project-one\
> ```

**Q: Tool shows "Tim thay 0 project"?**

> Make sure the folder you opened has sub-folders containing `go.mod`.  
> Each Go project must have its own `go.mod`.

**Q: Terminal shows `&&` is not valid?**

> VS Code is using PowerShell. The `tasks.json` above uses `cmd.exe` explicitly — this fixes the issue. Make sure you copy the full `tasks.json` as provided.

**Q: Do I need GCC?**

> Only if your Go project uses **CGo** or builds a **DLL**.  
> Pure Go projects do not need GCC at all.

**Q: Can GoWatcher handle multiple projects at once?**

> Yes — GoWatcher creates one watcher thread per project, all running in parallel.  
> Each project has its own independent debounce timer.

**Q: How do I stop GoWatcher?**

> Click the GoWatcher terminal panel → press `Ctrl+C`.  
> All threads stop cleanly within 3 seconds.

**Q: Does GoClean with `/cache` affect all projects?**

> `go clean -cache` clears the **global** Go build cache — it affects all Go projects on your machine, not just the ones in your workspace. The next build of any Go project will be slower.

**Q: Can I use these tools in CI/CD?**

> Yes. All tools return standard exit codes:
> - `exit 0` → success ✅
> - `exit 1` → failure ❌

---

## 🔗 Resources

| Resource | Link |
|----------|------|
| 🐹 Download Go | https://go.dev/dl/ |
| 🎓 Tour of Go | https://go.dev/tour/ |
| 📝 VS Code | https://code.visualstudio.com/ |
| 🔌 Go Extension | https://marketplace.visualstudio.com/items?itemName=golang.go |
| 🔍 staticcheck | https://staticcheck.dev |
| 🔒 garble | https://github.com/burrowers/garble |
| 📦 goversioninfo | https://github.com/josephspurrier/goversioninfo |
| 🔨 MSYS2 (GCC) | https://www.msys2.org/ |
| 📖 Go Modules | https://go.dev/ref/mod |
| 🔍 Find packages | https://pkg.go.dev |

---

## 📜 License

MIT License — free to use, modify, and distribute.

---

## 👨‍💻 Author

**Kieu Manh**

- 📧 Email: kieumanh366377@gmail.com
- 🐙 GitHub: *(this repo)*

> Built while learning Go and VS Code on Windows.  
> If these tools save you time, please give a ⭐ **Star**!

---

<div align="center">

**⭐ If Go Tools helped you, please Star this repo! ⭐**

Made with ❤️ by Kieu Manh — Vietnam 🇻🇳

</div>
