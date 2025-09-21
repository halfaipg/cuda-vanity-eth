# Vanity Eth Address (CUDA) – with Pattern & Dictionary Modes
Generates Ethereum addresses (EOA and contract) that match specific criteria, accelerated with NVIDIA CUDA-enabled GPUs. This fork adds fast hex pattern matching, multi-pattern search, and dictionary mode.

## Features

- Pattern matching for custom hex prefixes (up to 16 hex chars / 8 bytes)
- Multi‑pattern search (supply `--pattern` multiple times)
- Dictionary mode that loads many valid hex “pseudo‑words” at once
- Supports EOA, contract (nonce=0), CREATE2, and CREATE3 modes
- High throughput on RTX‑class GPUs

## Usage
```
./vanity-eth-addresss [PARAMETERS]
    Scoring methods
      (-lz) --leading-zeros               Count zero bytes at the start of the address
       (-z) --zeros                       Count zero bytes anywhere in the address
    Modes (normal addresses by default)
       (-c) --contract                    Search for addresses and score the contract address generated using nonce=0
      (-c2) --contract2                   Search for contract addresses using the CREATE2 opcode
      (-c3) --contract3                   Search for contract addresses using a CREATE3 proxy deployer
    Other:
       (-d) --device <device_number>      Use device <device_number> (Add one for each device for multi-gpu)
       (-b) --bytecode <filename>         File containing contract bytecode (only needed when using --contract2 or --contract3)
       (-a) --address <address>           Sender contract address (only needed when using --contract2 or --contract3)
      (-ad) --deployer-address <address>  Deployer contract address (only needed when using --contract3)
       (-w) --work-scale <num>            Defaults to 15. Scales the work done in each kernel. If your GPU finishes kernels within a few seconds, you may benefit from increasing this number.

### Examples

Pattern (single):
```
./vanity-eth-address-pattern --pattern a1c0de --device 0 --work-scale 15
```

Patterns (multi):
```
./vanity-eth-address-multi --pattern a1c0de --pattern c0ffee --pattern deadbeef --device 0 --work-scale 15
```

Dictionary (EOA):
```
./vanity-eth-address-dict --dictionary --device 0 --work-scale 15
```

Dictionary (contract nonce=0):
```
./vanity-eth-address-dict --contract --dictionary --device 0 --work-scale 15
```

Original zeros scoring:
```
./vanity-eth-address --zeros --device 0 --work-scale 17
```
```

## Benchmarks (indicative)
| GPU  | Normal addresses | Contract addresses | CREATE2 addresses |
| ---- | ---------------- | ------------------ | ----------------- |
| 5090 | 5600M/s          | 2800M/s            | -                 |
| 4090 | 3800M/s          | 2050M/s            | 4800M/s           |
| 3090 | 1600M/s          | 850M/s             | 2300M/s           |
| 3070 | 1000M/s          | 550M/s             | 1300M/s           |

Note that configuration and environment can affect performance.

## Requirements
* A NVIDIA CUDA-enabled GPU with a compute capability of at least 5.2 (Roughly anything above a GeForce GTX 950. For a full list [see here](https://developer.nvidia.com/cuda-gpus)).

## Pattern & Dictionary Details

- Patterns must be valid Ethereum hex: `[0-9a-f]`, case‑insensitive
- Recommended even length (2–16 hex) for byte‑aligned GPU matching
- Dictionary file default: `hex-words-dictionary.txt`
- You can edit this file to add your own hex words (e.g., `a1c0de`, `c0ffee`, `deadbeef`)

## Troubleshooting

- “Loaded X dictionary patterns” and then no speed: avoid piping output (e.g., to `head`), lower `--work-scale` to 12–15 for quicker updates
- If speed seems low, ensure no other GPU jobs are running (`nvidia-smi`), and try reducing `--work-scale`
- If you see NVML or driver mismatch errors, restart NVIDIA modules or system and ensure driver/CUDA versions align

## Repository

Fork: https://github.com/halfaipg/cuda-vanity-eth
Original: https://github.com/MrSpike63/vanity-eth-address