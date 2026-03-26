# `createXcrunch`

A fork of [HrikB/createXcrunch](https://github.com/HrikB/createXcrunch) with the [heswithme/createXcrunch](https://github.com/heswithme/createXcrunch) Metal kernel implementation merged in, plus additional fixes and features.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/license/mit)

`createXcrunch` is a [Rust](https://www.rust-lang.org)-based program designed to efficiently find _zero-leading_, _zero-containing_, or _pattern-matching_ addresses for the [CreateX](https://github.com/pcaversaccio/createx) contract factory. Uses [OpenCL](https://www.khronos.org/opencl/) or [Metal](https://developer.apple.com/metal/) (on macOS) to leverage a GPU's mining capabilities.

## Changes from upstream

- **Metal GPU support** — native Apple Silicon GPU acceleration via the Metal API (merged from [heswithme/createXcrunch](https://github.com/heswithme/createXcrunch)), selectable with `--metal`
- **Case-sensitive pattern matching** — `--case-sensitive` flag enables matching against the [EIP-55](https://eips.ethereum.org/EIPS/eip-55) checksummed address, so patterns like `Ba5eDXXX...XXXBa5eD` enforce specific uppercase/lowercase positions
- **Stop on find** — `--one` (`-1`) flag stops mining after the first matching address is found, prints the result, and exits
- **EIP-55 checksum module** — standalone checksum implementation for address validation

## Installation

1. **Clone the Repository**

```console
git clone https://github.com/lsheva/createXcrunch.git
cd createXcrunch
```

2. **Build the Project**

```console
cargo build --release
```

> [!NOTE]
> Building on Windows works as long as you have installed the [CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/) or the [AMD Radeon Software](https://www.amd.com/en/resources/support-articles/faqs/RS-INSTALL.html). However, the [WSL 2](https://learn.microsoft.com/en-us/windows/wsl/install) installation on Windows `x64` systems with NVIDIA hardware fails, as the current NVIDIA driver does not yet support passing [OpenCL](https://en.wikipedia.org/wiki/OpenCL) to Windows Subsystem for Linux (WSL) (see [here](https://github.com/microsoft/WSL/issues/6951)).

> [!NOTE]
> On macOS with Apple Silicon, use the `--metal` flag for significantly better performance compared to OpenCL.

## Usage

### Basic pattern matching (case-insensitive)

```console
./target/release/createxcrunch create3 --caller 0x88c6C46EBf353A52Bdbab708c23D0c81dAA8134A \
  --crosschain 1 \
  --matching ba5edXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXba5ed
```

### Case-sensitive pattern matching (EIP-55)

Use `--case-sensitive` to enforce EIP-55 checksum casing. Uppercase letters (A-F) in the pattern must match the checksummed address exactly:

```console
./target/release/createxcrunch create3 --caller 0x88c6C46EBf353A52Bdbab708c23D0c81dAA8134A \
  --crosschain 1 \
  --matching Ba5eDXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXBa5eD \
  --case-sensitive
```

### Stop after first match

```console
./target/release/createxcrunch create3 --caller 0x88c6C46EBf353A52Bdbab708c23D0c81dAA8134A \
  --crosschain 1 \
  --leading 4 \
  --one
```

### Metal GPU (macOS)

```console
./target/release/createxcrunch create3 --metal \
  --caller 0x88c6C46EBf353A52Bdbab708c23D0c81dAA8134A \
  --crosschain 1 \
  --leading 4
```

### Full help

```console
./target/release/createxcrunch create2 --help
./target/release/createxcrunch create3 --help
```

## Example Setup on [Vast.ai](https://vast.ai)

#### Update Linux

```console
sudo apt update && sudo apt upgrade
```

#### Install `build-essential` Packages

> We need the GNU Compiler Collection (GCC) later.

```console
sudo apt install build-essential
```

#### Install CUDA Toolkit

> `createXcrunch` uses [OpenCL](https://en.wikipedia.org/wiki/OpenCL) which is natively supported via the NVIDIA OpenCL extensions.

```console
sudo apt install nvidia-cuda-toolkit
```

#### Install Rust

> Enter `1` to select the default option and press the `Enter` key to continue the installation. Restart the current shell after completing the installation.

```console
curl https://sh.rustup.rs -sSf | sh
```

#### Build `createXcrunch`

```console
git clone https://github.com/lsheva/createXcrunch.git
cd createXcrunch
cargo build --release
```

## Local Development

We recommend using [`cargo-nextest`](https://nexte.st) as test runner for this repository. To install it on a Linux `x86_64` machine, invoke:

```console
curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C ${CARGO_HOME:-~/.cargo}/bin
```

Afterwards you can run the tests via:

```console
cargo nextest run
```

## Contributions

PRs welcome!

## Acknowledgements

- [HrikB/createXcrunch](https://github.com/HrikB/createXcrunch) — original implementation
- [heswithme/createXcrunch](https://github.com/heswithme/createXcrunch) — Metal kernel implementation
- [`create2crunch`](https://github.com/0age/create2crunch)
- [Function Selection Miner](https://github.com/Vectorized/function-selector-miner)
- [`CreateX` – A Trustless, Universal Contract Deployer](https://github.com/pcaversaccio/createx)
