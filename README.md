<h1 align="center">
  💀 <span style="background: linear-gradient(90deg, #ff0000, #ff7300, #fffb00, #48ff00, #00ffd5, #002bff, #7a00ff, #ff00ab);
  -webkit-background-clip: text;
  color: transparent;">
  Devil May Cry Mod Menu
  </span>
</h1>

<p align="center">
  <img src="https://readme-typing-svg.herokuapp.com?color=FF0000&center=true&vCenter=true&width=550&lines=Advanced+Android+Mod+Menu;NDK+%7C+ARM64+%7C+Unity+IL2CPP;Smooth+%26+Stable+Performance;Clean+%26+Optimized+Code">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Android-green?style=for-the-badge&logo=android">
  <img src="https://img.shields.io/badge/Arch-ARM64-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/Engine-Unity%20IL2CPP-red?style=for-the-badge">
  <img src="https://img.shields.io/badge/Language-C%2B%2B-orange?style=for-the-badge&logo=cplusplus">
  <img src="https://img.shields.io/badge/Status-Active-success?style=for-the-badge">
</p>

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Requirements](#-requirements)
- [Project Structure](#-project-structure)
- [Architecture](#-architecture)
- [Installation & Build](#-installation--build)
- [Usage](#-usage)
- [Core System](#-core-system)
- [Technical Details](#-technical-details)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [License](#-license)
- [Disclaimer](#-disclaimer)

---

## 🎮 Overview

**Devil May Cry Mod Menu** is an advanced Android mod menu designed for the Devil May Cry game series. Built with native C++ using the Android NDK, it provides smooth, stable, and crash-resistant gameplay modifications.

This project demonstrates advanced reverse engineering techniques, including:
- Unity IL2CPP binary patching
- ARM64 assembly manipulation
- In-memory game state modification
- Clean, maintainable mod architecture

---

## ⚡ Features

<div align="center">

| 🧩 Combat Mods | 💪 Power Ups | 🛡️ Protection |
|------------------|--------------|-----------------|
| ♾️ Infinite Health | 💥 Damage Boost | 🚫 Report Block |
| 🚫 No Knockback | ⚡ Speed Boost | 🔒 Anti-Detection |
| ⏱️ Infinite Devil Trigger | 🎯 Perfect Dodge | 🔐 Secure Hooks |
| 💥 Enhanced Combos | 🏆 Style Rank Modifier | 🛡️ Crash Protection |
| 📏 Extended Range | 💎 Infinite Currency | ⚙️ Stable Runtime |

</div>

---

## 📋 Requirements

### Build Requirements
- **Android NDK**: r21 or higher (r25+ recommended)
- **CMake**: 3.18.1 or higher
- **Android SDK**: API level 21+ (target API 31+)
- **C++ Standard**: C++17
- **Compiler**: Clang (LLVM)

### Runtime Requirements
- **Android Version**: 5.0 (API 21) or higher
- **Architecture**: ARM64 (arm64-v8a)
- **Target Game**: Devil May Cry with IL2CPP runtime

---

## 📁 Project Structure

```
DevilMayCry/
├── src/
│   ├── main.cpp                 # Entry point & initialization
│   ├── hooks/
│   │   ├── game_hooks.cpp       # Core game function hooks
│   │   └── game_hooks.h
│   ├── features/
│   │   ├── infinite_health.cpp  # Health modification module
│   │   ├── damage_boost.cpp     # Damage enhancement
│   │   ├── style_modifier.cpp   # Style ranking system
│   │   ├── devil_trigger.cpp    # Devil Trigger control
│   │   └── protection.cpp       # Anti-detection features
│   ├── utils/
│   │   ├── memory.cpp           # Memory read/write utilities
│   │   ├── logging.cpp          # Debug logging
│   │   └── safety.cpp           # Crash prevention & NULL checks
│   └── mod_menu/
│       ├── ui_overlay.cpp       # In-game UI rendering
│       └── toggle_handler.cpp   # Feature toggle management
├── CMakeLists.txt               # Build configuration
├── build/                       # Build output directory
├── docs/
│   ├── ARCHITECTURE.md          # Detailed architecture docs
│   ├── BUILD.md                 # Build instructions
│   └── TROUBLESHOOTING.md       # Common issues & solutions
└── README.md
```

---

## 🏗️ Architecture

### High-Level Design

```
┌─────────────────────────────────────────┐
│     Mod Menu UI Overlay                 │
│  (Feature Toggle & Configuration)       │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│   Feature Manager & Hook Controller     │
│  (Enable/Disable individual features)   │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│   Game Function Hooks (IL2CPP)          │
│  • Health System                        │
│  • Combat System                        │
│  • Style Ranking System                 │
│  • Detection/Anti-Cheat System          │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│   Memory Access Layer                   │
│  (Safe read/write with NULL checks)     │
└─────────────────────────────────────────┘
```

### Key Technologies

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Hooking** | Direct IL2CPP | Function interception |
| **Memory Access** | /proc/self/mem | Game state modification |
| **Rendering** | OpenGL/Vulkan | Overlay UI |
| **Thread Safety** | Mutexes/Atomics | Crash prevention |
| **Logging** | Custom system | Debug information |

---

## 🚀 Installation & Build

### Step 1: Clone the Repository

```bash
git clone https://github.com/AbhiModszYT/DevilMayCry.git
cd DevilMayCry
```

### Step 2: Install Dependencies

```bash
# On Ubuntu/Debian
sudo apt-get install cmake ndk-build

# On macOS (with Homebrew)
brew install cmake android-ndk
```

### Step 3: Configure Build

```bash
mkdir build && cd build
cmake ..
```

### Step 4: Compile

```bash
cmake --build . -j$(nproc)
```

### Step 5: Deploy to Device

```bash
adb push libs/arm64-v8a/libmods.so /data/local/tmp/
```

**For detailed build instructions**, see [BUILD.md](docs/BUILD.md)

---

## 💻 Usage

### Loading the Mod Menu

```cpp
// The mod menu loads automatically on app startup
// Features are disabled by default for safety
```

### Enabling Features

1. **Open In-Game Menu**: Swipe from the edge of the screen
2. **Select Feature**: Tap the feature toggle
3. **Confirm**: Feature activates immediately

### Feature Configuration

```cpp
// Example: Enable infinite health
Feature::InfiniteHealth::SetEnabled(true);
Feature::InfiniteHealth::SetMaxHealth(999);

// Example: Configure damage boost
Feature::DamageBoost::SetMultiplier(2.0f);
```

---

## 🧠 Core System

### Real-Time Combat Monitoring
- Live health tracking
- Combat state detection
- Style ranking synchronization

### Clean Toggle Handling
- Per-feature enable/disable
- Atomic state transitions
- Zero-lag activation/deactivation

### Crash-Safe NULL Checks
```cpp
// Every memory access is validated
if (SAFE_READ(address) == nullptr) {
    LOG("Invalid pointer - preventing crash");
    return;
}
```

### Optimized Hook Execution
- Minimal function overhead
- Inline hook optimization
- Efficient parameter passing

### Stable Update Loop
- 60 FPS update frequency
- Thread-safe state management
- Graceful error recovery

---

## 🔧 Technical Details

### IL2CPP Hooking Strategy

The mod uses direct IL2CPP binary patching to intercept game functions:

1. **Function Address Resolution** - Locate target functions via IL2CPP metadata
2. **Trampoline Creation** - Create jump stubs for original code
3. **Patch Injection** - Replace function prologue with hook jump
4. **Safe Restoration** - Restore original code when feature disabled

### Memory Safety

```cpp
// All memory operations use these patterns
template<typename T>
T safe_read(uintptr_t address) {
    if (!is_valid_address(address)) return T();
    return *(T*)address;
}
```

### Thread Safety

- Mutex-protected feature state
- Atomic flag updates
- Lock-free read operations where possible

---

## 🐛 Troubleshooting

### Issue: Mod doesn't load
**Solution**: Check Android version (API 21+) and ensure app has proper permissions

### Issue: Game crashes after enabling features
**Solution**: Enable one feature at a time to identify conflicts. Check [TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md)

### Issue: Performance degradation
**Solution**: Disable heavy features to reduce CPU usage

### Issue: Features disabled on game restart
**Solution**: This is by design - features must be re-enabled each session for safety

For more help, see [TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md)

---

## 🤝 Contributing

### Code Standards
- Use C++17 features
- Follow [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html)
- Add NULL checks for all memory operations
- Document complex functions with comments
- Test on ARM64 devices

---

## 📜 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

## ⚠️ Disclaimer

**IMPORTANT - READ CAREFULLY:**

This project is provided **for educational and research purposes only**. By using this software, you agree to:

1. **Legal Compliance**: You are solely responsible for ensuring your use complies with all applicable laws, terms of service, and game policies in your jurisdiction.

2. **Terms of Service**: Using this mod may violate the Devil May Cry Terms of Service. Your account may be suspended or permanently banned.

3. **Fair Play**: This tool is designed for single-player testing only. Do NOT use in multiplayer/competitive environments.

4. **No Warranty**: This software is provided "as-is" without any warranty. The author assumes no responsibility for:
   - Account bans or penalties
   - Device damage
   - Data loss
   - Any other damages

5. **Responsible Use**: Users must not use this project to:
   - Gain unfair competitive advantage
   - Violate game terms of service
   - Circumvent anti-cheat systems maliciously
   - Distribute modified game files

**The author is NOT responsible for any consequences of improper use of this software.**

---

<p align="center">
  <strong>⭐ If you find this project useful, please consider giving it a star!</strong>
</p>

<p align="center">
  Made with 💀 by <a href="https://github.com/AbhiModszYT">AbhiModszYT</a>
</p>
