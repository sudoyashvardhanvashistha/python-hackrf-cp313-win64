# python-hackrf-cp313-win64

## Overview

This repository provides a locally built Windows x64 wheel of `python_hackrf`
for CPython 3.13:

    python_hackrf-1.5.0.1-cp313-cp313-win_amd64.whl

`python_hackrf` is a Cython wrapper around HackRF's host software
(`libhackrf` and `hackrf-tools`), maintained upstream at
https://github.com/GvozdevLeonid/python_hackrf. This repository does not
contain a new implementation of that project. It contains a build artifact:
a wheel compiled from the upstream source for a target
(CPython 3.13 + Windows x64) that did not have a publicly available
prebuilt wheel at the time this was built.

## Why This Build Exists

While working with a HackRF One during an internship at DRDO, I needed
`python_hackrf` running on Windows under Python 3.13. I could not find
another publicly available prebuilt wheel for this exact CPython 3.13 /
Windows x64 combination at the time of investigation. That does not mean
one has never existed or will never exist elsewhere — only that my search
did not turn one up, which is what made a local source build worthwhile
for my environment.

## What I Built

What I did:

- Set up a Windows x64 development environment for CPython 3.13.
- Verified that the MSVC compiler toolchain being used was targeting x64,
  matching the CPython 3.13 win_amd64 target.
- Configured the HackRF/libhackrf include and library paths required by
  the `python_hackrf` build process.
- Built the project's Cython sources into native extension modules using
  Cython and MSVC.
- Packaged the result into a `.whl` and verified the output.
- Computed and recorded the SHA-256 checksum of the resulting wheel.

What I did not do:

- I did not write `python_hackrf`. That project, including its Cython
  wrapper code and Python API, is the work of its upstream author(s)
  (see Upstream Attribution).
- I did not create HackRF or libhackrf. Those are the work of Great Scott
  Gadgets and contributors.
- I have not modified the upstream source. This is an unmodified build of
  the upstream project for a target platform/interpreter combination that
  I could not find prebuilt elsewhere.

## Technical Build Details

| Item                  | Value |
|-----------------------|-------|
| Package               | python_hackrf |
| Version               | 1.5.0.1 |
| Wheel file            | python_hackrf-1.5.0.1-cp313-cp313-win_amd64.whl |
| Target interpreter    | CPython 3.13 |
| Target platform       | Windows, x86-64 (AMD64) |
| Build toolchain       | Cython + Microsoft Visual C++ (MSVC), x64 |
| Source                | Upstream `python_hackrf` source (unmodified) |
| SHA-256               | 5efcb5581719a61ade34783b8f3f1f03f406972f1c9be3c262d168bcbfae9a8b |

## Wheel Filename Explained

`python_hackrf-1.5.0.1-cp313-cp313-win_amd64.whl` follows the standard
Python wheel filename convention:

- `python_hackrf` — the distribution name.
- `1.5.0.1` — the package version, matching the upstream release this
  build was compiled from.
- `cp313` (build tag) — the Python implementation and version the
  extension was compiled against: CPython 3.13.
- `cp313` (ABI tag) — the ABI the compiled extension is binary-compatible
  with, again CPython 3.13.
- `win_amd64` — the target platform: 64-bit Windows on the x86-64
  (AMD64) architecture.

This tagging means the wheel is specific to that interpreter/ABI/platform
combination. It is not a universal or pure-Python wheel, and it will not
install on other Python versions, other operating systems, or 32-bit
Windows.

## What a `.whl` Is

A Python wheel (`.whl`) is a pre-built binary distribution format for
Python packages, defined by PEP 427. For a project like `python_hackrf`
that includes compiled Cython/C extension modules, the wheel bundles the
already-compiled native code for a specific interpreter and platform. This
means an end user installing the wheel does not need a working C/C++
toolchain or Cython installed locally — `pip` simply extracts the
prebuilt extension into the target Python environment. The tradeoff is
that a wheel only works for the exact interpreter/ABI/platform it was
built for, which is why per-target wheels (such as this one) exist.

## Architecture / Runtime Stack

    Python application (your script)
              |
              v
        python_hackrf (Python/Cython API)
              |
              v
      Cython-generated native extension   <-- this repository provides
              |                                the compiled artifact
              v                                for this layer
      libhackrf / HackRF host software
              |
              v
          HackRF hardware (USB)

This repository publishes the compiled wheel artifact for `python_hackrf`,
targeting CPython 3.13 on Windows x64. It does not bundle libhackrf,
HackRF host tools, USB drivers, or firmware. Those remain separate,
externally installed dependencies.

## Requirements

- Windows x64 (win_amd64)
- CPython 3.13 (win_amd64 build)
- NumPy (runtime dependency of `python_hackrf`)
- A working HackRF host software installation, providing `libhackrf`, on
  the system so that the compiled extension can dynamically link against
  it at runtime, and the appropriate USB driver for the HackRF device
  (e.g. via Zadig) if you intend to use actual hardware
- A physical HackRF device, only if you intend to use hardware-facing
  functionality (importing the package does not require attached hardware)

This wheel contains the compiled Python/Cython extension code for
`python_hackrf`. It does not bundle `libhackrf`, HackRF firmware, or USB
drivers. The HackRF host software providing `hackrf.dll` must be
installed separately and available to the runtime environment.

## Installation

1. Ensure you are using a CPython 3.13, win_amd64 Python installation.
2. Ensure HackRF host software / libhackrf is installed and discoverable
   on your system (required at runtime for any hardware-facing calls).
3. Install the wheel:

       pip install python_hackrf-1.5.0.1-cp313-cp313-win_amd64.whl

No custom installation paths or environment variables are required by
this repository beyond whatever the upstream project documents for
locating `libhackrf` on Windows. Consult the upstream project's
documentation if `pip` or the package cannot locate your `libhackrf`
installation at runtime.

## Verification

After installation, the following commands can be used to check the
install:

    python --version
    python -c "import python_hackrf; print('import OK')"
    python -c "import python_hackrf; print(python_hackrf.__version__)"

To check whether the package can see attached HackRF hardware (requires a
connected device and a working libhackrf/driver setup):

    python -m python_hackrf info

Only commands that reflect the package's documented usage are listed
here. Hardware-facing verification (e.g. `info`) was not performed as
part of producing this build artifact, so it is listed as something the
end user can run in their own environment, not as a result reported by
this repository.

## Applications / Use Cases

`python_hackrf`, and by extension this build, is applicable to typical
Python-based SDR workflows involving a HackRF device, such as:

- SDR experimentation and prototyping
- RF research and coursework
- I/Q sample capture from HackRF hardware
- Digital signal processing (DSP) pipelines in Python
- Spectrum analysis / sweep-based measurements
- Scripting automated RF experiments
- NumPy/SciPy-based post-processing of captured samples
- Educational and research use around SDR concepts

## Limitations

- This wheel targets CPython 3.13 on 64-bit Windows only. It will not
  work on other Python versions, other operating systems, or 32-bit
  Python.
- This wheel is a locally produced build, not an official upstream
  release published by the `python_hackrf` maintainer(s).
- Hardware validation against a physical HackRF device was not performed
  as part of producing this artifact; the build was verified at the
  packaging level (compilation, import, checksum), not through
  end-to-end RF testing.
- The compiled extension dynamically depends on `hackrf.dll` at runtime.
  This DLL is not bundled inside the wheel; the HackRF host software
  providing it must be installed separately.
- This is not a fork of `python_hackrf`. No source modifications were
  made; only the build/compilation step was performed locally.

## SHA-256

    5efcb5581719a61ade34783b8f3f1f03f406972f1c9be3c262d168bcbfae9a8b

Verify the wheel against this checksum before use, e.g. on Windows:

    certutil -hashfile python_hackrf-1.5.0.1-cp313-cp313-win_amd64.whl SHA256

## Upstream Attribution

- `python_hackrf` (the Cython wrapper compiled into this wheel) is
  developed and maintained upstream at
  https://github.com/GvozdevLeonid/python_hackrf. All credit for the
  design and implementation of the Python/Cython API belongs to that
  project's author(s) and contributors.
- HackRF and its host software (`libhackrf`, `hackrf-tools`) are
  developed by Great Scott Gadgets and contributors, at
  https://github.com/greatscottgadgets/hackrf.
- This repository's contribution is limited to producing and publishing a
  compiled wheel of the unmodified upstream `python_hackrf` source for a
  CPython 3.13 / Windows x64 target.

## Licensing

- `python_hackrf` (upstream) is distributed under the MIT License. This
  wheel is a compiled build of that MIT-licensed source and remains
  subject to the same license and its copyright notices.
- HackRF host software (`libhackrf` / `hackrf-tools`) is licensed by
  Great Scott Gadgets, predominantly under GPL-2.0-or-later, with some
  components under a BSD-style license depending on the file. This
  repository does not include or redistribute HackRF host software
  binaries as part of the wheel build process described here; consult
  the upstream `hackrf` repository for the authoritative license terms
  of each file before redistributing any of that software.
- Any documentation and files I personally authored for this repository
  (such as this README) are provided as-is; unless a separate LICENSE
  file in this repository states otherwise, no additional license claim
  is made over the upstream code itself.
- Publishing this compiled wheel does not change, relicense, or waive any
  rights under the upstream licenses above. Users of this wheel remain
  bound by the applicable upstream license terms.

## Background / Motivation

This build came out of a practical need during HackRF-related work at
DRDO: getting the `python_hackrf` stack running on a Python 3.13 /
Windows x64 machine, without a readily available prebuilt wheel for that
combination. Rather than keep the compiled artifact local, it is
published here, along with the exact target, checksum, and dependency
information, for others who may run into the same environment
constraints.

## Future Work / Related Project

If useful, this repository may later be extended with build notes or a
reproducible build script for this target. No such script is included at
this time; the steps described above reflect the process as it was
actually carried out, not a guaranteed reproducible pipeline.