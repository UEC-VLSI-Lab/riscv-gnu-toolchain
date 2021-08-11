# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/)
and this project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).


## [Unreleased]

### Added
- gcc: Add `xfhalf`, `xfalthalf`, `xfhalfinx` and `xfhalthalfinx` support. This
  means code generation in the backend, libgcc changes to make softfloat
  routines set the fcsr, itanium style name mangling rules for and some helper
  routines when truncating doubles.
- gcc: Add `xfhalf` and `xfalthalf` compile test
- binutils: Add `xfhalfinx` and `xfalthalfinx` support
- gdb/sim: Add `xfhalf` and `xfhalfinx` support
- gdb/sim: Add float and half float tests generated by patched TestFloat-3e
- newlib: Add support for `zfinx` (POSIX fenv)

### Changed
- gcc, binutils: Conversion instructions between half float and other float
  modes are automatically inferred. Internally, they are stil handled separately
  to make changes such as selectively disabling them easier.

### Fixed
- binutils: Add fcsr alias instructions for zfinx
- gdb/sim: NaN boxing for floats in double float registers
- gdb/sim: dynamic rounding mode didn't work at all
- gdb/sim: Add RISC-V NaN behavior (canonical NaN instead of complicated rules)
- gcc: Various typos in tests
- gcc: Hardware loops for the `xcorev` target are now disabled (due to wrong
  code generation)

## [2.4.0] - 2021-07-27

### Added
- gcc, binutils, sim: Add support for zfinx and zdinx (rv32 and rv64). Use
  `zfinx` (replaces `f`) and `zdinx` (replaces `d`).
- gcc, binutils: Add basic zfinx/zdinx tests
- Added zfinx multilibs

### Changed
- sim: Now uses arch attribute from elf do determine supported extension

### Fixed
- gcc: Fix `__builtin_pulp_ClusterId()` and add test
- gdb: backported fix for NULL pointer deref when using focus or winheight
  command (only happens when using a bleeding edge gcc)
- sim: Fix postmod load/store when using the same register as destination and
  source.

## [2.3.0] - 2021-07-19

### Added
- gcc: Add simd complex insn
- gcc: Add pulp specific tests

### Changed
- gcc: Generalize simd md patterns
- gcc: builtins are now available depending on enabled subextensions instead of
  pulp core version hackery.

### Fixed
- gcc: xpulpvect was not working as argument, but xpulpvectall was
- gcc: Fix single standing xpulphwloop arch string

## [2.2.0] - 2021-07-09

### Added
- gdb/sim: Add pulp vector insn
- gdb/sim: Add bitrev insn
- gdb/sim: Add testsuite

### Fixed
- gas: Add p.bitrev and simd complex to pulpv3 and corev
- gdb/sim: Fix clip boundaries
- gdb/sim: Improve error handling
- gcc: V2QI moves
- gcc: Use pv.pack instead of pv.pack.h

### Changed
- gdb/sim: Set default simulation model to corev
- gcc: Enable SIMD support. Was functional, but disabled.

## [2.1.3] - 2021-07-05

### Added
- Allow setting pkversion string (`--with-pkgversion=ABC`)

### Changed
- gdb, sim: Rebased binutils patches

## [2.1.2] - 2021-07-03

### Added
- gcc: Tweaks for hardware loops (minimum size and alignment)

## [2.1.1] - 2021-07-03

### Fixed
- gcc: PULP builtins fail during reload/lra pass

## [2.1.0] - 2021-07-03

### Added
- `pulp.md` about new subextensions and extension groupings
- `core-v.rst` about using cv32e40p with PULP extensions
- gcc: Enable/Disable PULP extension with `-m` flags (see `--help` and `pulp.md`)
- gcc: Updated multilib settings
- gcc,binutils: Preliminary support for cv32e40p (`xcorev`)
- binutils: smach custom float extension and extension strings
- bintuils: Imported and patched corev assembly tests

### Changed
- gcc: *Split PULP extensions into various subextensions*. Check `pulp.md`.
- gcc: Split PULP specific machine descriptions into separate files
- binutils/gas: More warnings for hardware loops when used in a wrong way
- binutils: Single location for PULP extension meta information making tedious
  copy-paste-sync superfluous

### Fixed
- gcc,binutils: Version parsing of arch string (upstream bug)
- gcc: Code generation for `__builtin_clrsb`
- binutils: Disassembly of ji immeadite of hardware loops

### Removed
- gcc: Various dead `-m` options
- binutils: Dead code from custom dynamic linking

## [2.0.1] - 2020-11-19

### Changed
- Using `a` extension with `xpulpv2` is not an error anymore
- Remove redundant code that forces abi to ilp32 when using xpulp

## [2.0.0] - 2020-11-4

### Added
- gcc: port pulp patches to 9.2.0
- binutils: port pulp patches to 9.2.0
- gdb: port pulp patches to 9.2.0
- gcc: Add support xpulpv2 except vector insn (not verified) and ind-ind-reg
  insn (can't get patterns to work)
- gcc: Use new register allocator (lra) instead of the deprecated reload.
- gcc: Properly gate pulp insns behind pulp arch flags. This makes sure when
  using standard RISC-V arch flags that we clearly don't use patched code paths.
- gcc: Reintroduce default RISC-V builtins
- gdb: Add xpulpv2 insn to gdb-simulator. Used as canonical simulator to run regression tests.
- ld: Add full support for pulp relocations
- as: Remap operand shorthands of insn to resolve collisions
- as: Adapt table and glue logic to new insn class logic
- objdump: Add support for parsing of riscv-attributes to determine arch. Useful
  when trying to disassemble binaries that use insn with overlapping encodings.
- Add gitlabci flow

### Changed
- Most tools only accept lower case `-march` and `-mabi` arguments like upstream
  gcc does.
- gcc: `xpulpv2` will fail when combined with `a` extension. Previously the `a`
  extension was just silently stripped.
- objdump: insn that were previously rendered as pulp insn (prefix `p.`) but
  actually weren't are now shown in their original form (e.g. `mul` instead of
  `p.mul`)

### Fixed
- gcc: Fix `__attribute__ ((visibility ("hidden")))` logic
- gcc: Dismantle mnosext. Broken semantics and fixes regression with wrong insn
  being emitted.
- gcc: Disable `Pulp_DP_Format_type` support
- gcc: Disable mdf (internal) flag
- gcc: Remove the automatic (broken) linking to a file called `riscv.ld` in `riscv/elf.h`
- gcc: Fix conflicts in constraint naming
- gcc: Disable high part insn code path in gcc expand pass. Looks like a gcc
  bug.

### Removed
- ld: Remove lightweight dynamic linking (buggy and interfering with default
  linker code)
- gcc: Remove PULP specific interrupt handler attributes. The code was contending
  with upstream interrupt handler support.