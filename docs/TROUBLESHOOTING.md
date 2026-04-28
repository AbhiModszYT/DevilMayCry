# 🐛 Troubleshooting Guide

This document provides solutions to common issues and problems encountered when building, deploying, and using the Devil May Cry Mod Menu.

---

## Table of Contents

- [Build Issues](#build-issues)
- [Deployment Issues](#deployment-issues)
- [Runtime Issues](#runtime-issues)
- [Feature-Specific Issues](#feature-specific-issues)
- [Performance Issues](#performance-issues)
- [FAQ](#faq)
- [Getting Help](#getting-help)

---

## 🔨 Build Issues

### CMake Configuration Error

**Error Message:**
```
CMake Error: CMAKE_CXX_COMPILER is not set
```

**Solution:**
1. Ensure Android NDK is properly installed
2. Set NDK path explicitly:
```bash
cd build
cmake .. -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK_HOME/build/cmake/android.cmake \
  -DANDROID_ABI=arm64-v8a \
  -DANDROID_PLATFORM=android-21
```
3. Verify NDK version is r21 or higher: `ndk-build --version`

---

### Compilation Errors (C++ Standard)

**Error Message:**
```
error: use of undeclared identifier 'std::optional'
```

**Solution:**
1. Ensure C++17 is enabled in CMakeLists.txt:
```cmake
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
```
2. Clean and rebuild:
```bash
rm -rf build/
mkdir build && cd build
cmake ..
cmake --build . -j$(nproc)
```

---

### Linker Errors

**Error Message:**
```
undefined reference to 'function_name'
```

**Solution:**
1. Verify all source files are listed in CMakeLists.txt
2. Check that all dependencies are properly linked
3. Review the [BUILD.md](BUILD.md) guide for correct linker flags
4. Ensure library order is correct (dependent libraries last)

---

### NDK Version Mismatch

**Error Message:**
```
Arch arm64-v8a not supported by NDK r20
```

**Solution:**
1. Download NDK r21 or higher from [Android Developer Site](https://developer.android.com/ndk/downloads)
2. Set ANDROID_NDK_HOME environment variable:
```bash
export ANDROID_NDK_HOME=/path/to/android-ndk-r25
```
3. Verify installation:
```bash
ls $ANDROID_NDK_HOME/platforms/
```

---

## 📦 Deployment Issues

### Device Not Recognized

**Error Message:**
```
error: device offline
```

**Solution:**
1. Enable USB Debugging on device:
   - Settings → Developer Options → USB Debugging (toggle ON)
   - If Developer Options unavailable: Settings → About Phone → tap "Build Number" 7 times
2. Verify device connection:
```bash
adb devices
```
3. Authorize USB on device when prompt appears
4. Try different USB cable or port
5. Restart ADB daemon:
```bash
adb kill-server
adb start-server
adb devices
```

---

### Permission Denied When Pushing Library

**Error Message:**
```
adb: error: cannot stat 'libs/arm64-v8a/libmods.so': No such file or directory
```

**Solution:**
1. Verify build completed successfully:
```bash
ls -la build/lib/arm64-v8a/
```
2. Check file exists with correct name:
```bash
find . -name "*.so" -type f
```
3. Use correct path when pushing:
```bash
adb push ./build/lib/arm64-v8a/libmods.so /data/local/tmp/
```

---

### "Read-Only File System" Error

**Error Message:**
```
adb: error: cannot create '/data/app/...' Read-only file system
```

**Solution:**
1. Ensure device is rooted
2. Remount system partition:
```bash
adb shell su -c "mount -o rw,remount /system"
```
3. Push to temporary directory first:
```bash
adb push libmods.so /data/local/tmp/
adb shell su -c "cp /data/local/tmp/libmods.so /system/lib64/"
```

---

## ⚙️ Runtime Issues

### Mod Menu Doesn't Load

**Symptoms:**
- No overlay appears in-game
- Features list is empty
- Library loads but nothing happens

**Solution:**
1. Verify device meets requirements:
   - Android 5.0 (API 21) or higher:
   ```bash
   adb shell getprop ro.build.version.sdk
   ```
   - ARM64 architecture:
   ```bash
   adb shell getprop ro.product.cpu.abi
   ```

2. Check if library is loaded:
```bash
adb shell "cat /proc/$(pidof com.gameapp)/maps | grep libmods"
```

3. Enable logging and check output:
```bash
adb logcat | grep "DevilMayCry\|MOD"
```

4. Verify app has proper permissions in AndroidManifest.xml

---

### Game Crashes on Startup

**Symptoms:**
- Game force-closes immediately after loading mod
- No error messages in logcat
- Happens consistently

**Solution:**
1. **Disable all features** - crash may be caused by specific feature:
   - Restart game without any mods enabled
   
2. **Check logcat for crash details:**
```bash
adb logcat -e "FATAL|Exception|ERROR" | head -50
```

3. **Enable one feature at a time** to identify culprit:
   - Enable "Infinite Health" only
   - Test for stability (5-10 minutes gameplay)
   - If stable, enable next feature
   - If crashes, that feature is problematic

4. **Verify memory addresses haven't changed:**
   - Game updates often shift memory addresses
   - May need to re-scan and update offsets
   - Check [docs/ARCHITECTURE.md](ARCHITECTURE.md) for offset scanning guide

---

### "Segmentation Fault" or "SIGSEGV"

**Error Message:**
```
signal 11 (SIGSEGV), code 1 (SEGV_MAPERR)
```

**Solution:**
1. **Check NULL pointer handling:**
   - Review recent code changes
   - Ensure all memory accesses have NULL checks
   
2. **Enable memory protection:**
```cpp
// Use SAFE_READ macro for all memory access
SAFE_READ(pointer);
```

3. **Disable memory-intensive features:**
   - Damage Boost
   - Style Modifier
   - Extended Range
   
4. **Rebuild with debug symbols:**
```bash
cmake .. -DCMAKE_BUILD_TYPE=Debug
cmake --build .
```

---

## 🎮 Feature-Specific Issues

### Infinite Health Not Working

**Symptoms:**
- Feature enabled but health still depletes
- Health display shows correct value but character still dies

**Solution:**
1. **Verify health function address:**
   - Address may have changed in game update
   - Re-scan binary for health modification function
   
2. **Check if health is server-synced:**
   - Some games sync health with server
   - Server may override local changes
   - No workaround for server-synced health

3. **Verify feature is actually enabled:**
```cpp
if (Feature::InfiniteHealth::IsEnabled()) {
    LOG("Feature is enabled");
} else {
    LOG("Feature is disabled!");
}
```

4. **Check combat state:**
   - Some games disable health modification during specific scenarios
   - Try disabling/re-enabling during different game states

---

### Damage Boost Causes Crashes

**Symptoms:**
- Game crashes when Damage Boost is enabled
- Crashes occur during combat
- Works fine outside combat

**Solution:**
1. **Lower boost multiplier:**
   - Set multiplier to 1.5x instead of 2.0x
   - Test if lower values are stable
   
2. **Check for overflow:**
```cpp
// Ensure damage value doesn't overflow
if (damage > INT_MAX) damage = INT_MAX;
```

3. **Verify damage calculation hook:**
   - Ensure parameters are passed correctly
   - Check register preservation
   - Review assembly of hooked function

4. **Disable for specific enemies:**
   - Some boss fights may have special damage logic
   - Can cause unexpected behavior with boost enabled

---

### Style Rank Modifier Not Updating

**Symptoms:**
- Style rank stays at "D"
- Modification command sent but no visible change
- Rank jumps back to normal immediately

**Solution:**
1. **Check style system update frequency:**
   - Style rank updates at specific intervals
   - Try re-modifying after 2-3 seconds
   
2. **Verify write address:**
   - Style rank stored in specific memory location
   - Location may change between game versions
   - Use memory scanner to find current location

3. **Check game's style protection:**
   - Some games recalculate style based on actual combat
   - Your modification may be overwritten by game logic

4. **Ensure correct style enum values:**
```cpp
// Verify these match game's internal values
enum StyleRank {
    RANK_D = 0,
    RANK_C = 1,
    RANK_B = 2,
    // ... etc
};
```

---

### Devil Trigger Control Unresponsive

**Symptoms:**
- Feature doesn't enable DT
- DT enabled but can't be controlled
- DT depletes immediately

**Solution:**
1. **Check DT availability:**
   - Some scenarios don't allow DT usage
   - Verify you're not in cutscene or boss arena restriction
   
2. **Verify DT meter address:**
   - Address changes with game updates
   - Re-scan for DT meter location

3. **Check infinite DT depletion:**
   - Ensure depletion prevention is working
   - Review timer/depletion hook

4. **Test with manual activation:**
   - Try activating DT normally first
   - Then enable mod control
   - Ensures DT is actually available

---

## 📊 Performance Issues

### Game Stuttering/Lag

**Symptoms:**
- FPS drops significantly
- Noticeable frame rate hitches
- Game becomes less responsive

**Possible Causes:**

1. **Too many features enabled simultaneously:**
   - Disable non-essential features
   - Memory modifications can be CPU-intensive
   
2. **Memory access bottleneck:**
   - Multiple features reading same memory
   - Add caching for frequently-accessed values:
   ```cpp
   static uintptr_t cached_health_addr = 0;
   ```

3. **Hook overhead:**
   - Complex hook functions slow down execution
   - Optimize hot-path code:
   ```cpp
   // Inline simple checks
   inline bool should_modify() {
       return enabled && in_combat;
   }
   ```

4. **Thread contention:**
   - Excessive mutex locking
   - Use lock-free data structures where possible

**Solutions:**
```bash
# Profile with CPU profiler
adb shell am trace-ipc start
# Run game for 5-10 seconds
adb shell am trace-ipc stop
adb pull /data/anr/traces.txt

# Analyze trace file for bottlenecks
```

---

### High Battery Drain

**Symptoms:**
- Battery drains 2-3x faster than normal
- Device gets warm/hot
- Background processes using CPU

**Solution:**
1. **Reduce update frequency:**
   - Default is 60Hz, reduce to 30Hz if possible
   - Modify update loop timing

2. **Optimize memory scanning:**
   - Cache addresses instead of re-scanning
   - Only scan when necessary

3. **Disable expensive features when not needed:**
   - Damage boost calculations are CPU-intensive
   - Disable when not actively using

4. **Review code for infinite loops:**
```bash
# Check for busy-wait loops
grep -n "while(true)" src/**/*.cpp
```

---

## ❓ FAQ

### Q: Can I use this with online/multiplayer games?
**A:** No. This mod is designed for single-player only. Using in multiplayer will result in:
- Account ban
- Game detection
- Server-side verification failures

---

### Q: Will the game ban me for using this?
**A:** Possibly. The game developer:
- May detect the mod library
- Can see modified memory
- May flag account for investigation
- Could issue permanent ban

Use at your own risk. Only use in single-player mode on accounts you don't mind losing.

---

### Q: How do I disable the mod?
**A:** Remove the library from the device:
```bash
adb shell su -c "rm /system/lib64/libmods.so"
# Or
adb shell rm /data/local/tmp/libmods.so
```

Restart game - mod won't load.

---

### Q: Game updated and mods don't work
**A:** Game updates often change:
- Memory addresses
- Function locations
- Binary structure

**Solution:**
1. Re-scan game binary for new addresses
2. Update offset values in code
3. Recompile mod with new offsets
4. Test on updated game version

---

### Q: Can features be combined safely?
**A:** Most features can be combined, but some conflict:

⚠️ **Known Conflicts:**
- Infinite Health + Damage Boost (may cause overflow)
- Speed Boost + Extended Range (movement bugs)
- Style Modifier + Devil Trigger (meter conflicts)

**Safe Combinations:**
- Infinite Health + No Knockback
- Damage Boost + Speed Boost
- Devil Trigger + Style Modifier

Test any new combination before relying on it.

---

## 📞 Getting Help

### Check Existing Resources

1. **Review [BUILD.md](BUILD.md)** - Building and compilation issues
2. **Review [ARCHITECTURE.md](ARCHITECTURE.md)** - Technical details and design
3. **Check GitHub Issues** - Others may have reported similar problems
4. **Review logcat output** - Most runtime issues leave traces there

### Debug Logging

Enable detailed logging:
```cpp
#define DEBUG_LOG 1
```

View logs:
```bash
adb logcat -s "DevilMayCry" -v threadtime
```

### Provide Good Bug Reports

When reporting issues, include:

```
## Issue Description
[Describe the problem]

## Steps to Reproduce
1. [First step]
2. [Second step]
3. [Etc.]

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Device Info
- Android Version: [e.g., Android 12]
- Device Model: [e.g., Pixel 5]
- NDK Version: [e.g., r25]
- Game Version: [e.g., v1.2.3]

## Logcat Output
[Paste relevant logcat entries]

## Steps Tried
- [ ] Disabled all features
- [ ] Rebuilt from scratch
- [ ] Restarted device
- [ ] Tried different feature combination
```

---

## 📝 Common Error Messages Reference

| Error | Cause | Solution |
|-------|-------|----------|
| `SEGV_MAPERR` | Invalid memory access | Add NULL checks, see [Runtime Issues](#runtime-issues) |
| `CMAKE not found` | CMake not installed | Install CMake 3.18.1+ |
| `NDK not found` | NDK path not set | Set ANDROID_NDK_HOME environment variable |
| `Device offline` | USB connection issue | Check [Deployment Issues](#deployment-issues) |
| `Read-only file system` | Partition is read-only | Remount with RW permissions |
| `undefined reference` | Missing linker flag | Review CMakeLists.txt dependencies |

---

**Last Updated:** April 2026

For the most up-to-date information, check the [GitHub Repository](https://github.com/AbhiModszYT/DevilMayCry).
