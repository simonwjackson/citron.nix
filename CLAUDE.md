# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Nix flake packaging for [Citron](https://citron-emu.org), a Nintendo Switch emulator forked from yuzu. Current version: **0.11.0**

## Commands

```bash
# Build the package
nix build

# Run directly
nix run

# Test build without adding to store
nix build --no-link --print-out-paths

# Update flake inputs
nix flake update
```

## Architecture

**flake.nix** - Exposes the package for x86_64-linux and provides an overlay for integration into other flakes.

**package.nix** - The derivation that:
- Fetches Citron source from git.citron-emu.org with submodules
- Pre-downloads nx-tzdb timezone data (required since sandbox can't fetch during build)
- Patches CMakeLists.txt for Qt 6.10.0 compatibility (adds GuiPrivate component)
- Uses system libraries instead of bundled ones via `CITRON_USE_BUNDLED_*=OFF` flags
- Wraps the Qt application with Vulkan loader in LD_LIBRARY_PATH

## Updating Citron Version

1. Update `version` in package.nix
2. Update `rev` to the new git commit
3. Run `nix build` - it will fail with hash mismatch
4. Update `hash` with the correct value from the error message
5. Check if nx-tzdb needs updating (rarely changes)
