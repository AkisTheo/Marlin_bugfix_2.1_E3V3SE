# Repository agent instructions

Read [`MEMORY.md`](./MEMORY.md) for the repository's branch map, custom-feature history, and cross-component relationships. Read [`.github/copilot-instructions.md`](./.github/copilot-instructions.md) for target, build, test, architecture, and memory-limit guidance. These files complement one another.

## Before changing code

- Run `git status --short --branch` and establish the active branch, `HEAD`, and existing user changes. Do not assume the current branch from the branch map or switch branches without an explicit request.
- Check the active branch's configuration and source layout before making machine-specific changes. C13/C14 and SD/serial-host branches have different configurations, display paths, and histories; do not copy a change between them without verifying compatibility.
- Trace a custom feature end-to-end: configuration macro and sanity checks, build/source guards, implementation, G-code entry point, DWIN/UI call sites, and persistence or host interactions as applicable.

## Repository-specific rules

- Keep configuration changes in `Marlin/Configuration.h` and `Marlin/Configuration_adv.h`; confirm whether any generated/exported configuration artifacts must be regenerated instead of editing them by hand.
- Use Marlin feature guards (`ENABLED`, `DISABLED`, `TERN`, etc.) consistent with surrounding code. Preserve portable Marlin behavior unless a change is intentionally target-specific and guarded.
- Treat C13/C14 pin mappings, EEPROM setup, serial settings, bootloader offsets, probe/load-cell wiring, and DWIN display paths as hardware-specific. Verify pin definitions and the matching PlatformIO environment together.
- This firmware has tight memory budgets. Check the resulting target's flash and RAM after feature or renderer changes; static buffers and display/protocol changes can materially affect memory and responsiveness.
- Preserve the distinction between SD-only and serial-host firmware. Host actions, MeatPack, host G-code commands, and FT_Motion settings are not automatically shared across all branches.
- Keep experimental work such as `Bed_Scale_test` isolated unless the task explicitly requests integration into a release branch.
- Do not edit prebuilt `.bin` files or `.pio/` build output as a substitute for changing source/configuration.
- Never run Make targets with `GIT_RESET_HARD=true` unless the user explicitly requests it and has approved discarding tracked work. The test runner can invoke `git reset --hard HEAD` in that mode.

## Verification

- Prefer a focused PlatformIO build for the active target plus the relevant native/build test. The exact commands and single-test selectors are in [`.github/copilot-instructions.md`](./.github/copilot-instructions.md).
- Pin edits should be checked with `make validate-pins PINSPATH=<pin-family>`; broader board or formatting changes may also need `make validate-boards` or `make validate-lines`.
- Report which board environment and branch/configuration were verified. A passing build for one variant does not validate the other MCU or the host-support variant.
