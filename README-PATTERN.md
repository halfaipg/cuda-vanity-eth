# Vanity Eth Address with Pattern Matching

This is an enhanced version of the original [vanity-eth-address](https://github.com/MrSpike63/vanity-eth-address) tool with added **pattern matching capabilities**.

## New Features

### Pattern Matching
- **`--pattern <hex_pattern>`** - Search for addresses that start with a specific hex pattern
- Supports up to 8 bytes (16 hex characters) of pattern matching
- Perfect for finding addresses with specific prefixes like "a1decaf", "a1c0ffee", etc.

## Usage

```bash
./vanity-eth-address-pattern [PARAMETERS]
    Scoring methods:
      (-lz) --leading-zeros               Count zero bytes at the start of the address
       (-z) --zeros                       Count zero bytes anywhere in the address
       (-p) --pattern <hex_pattern>       Match specific hex pattern (e.g., a1decaf)
    Modes (normal addresses by default):
       (-c) --contract                    Search for addresses and score the contract address generated using nonce=0
      (-c2) --contract2                   Search for contract addresses using the CREATE2 opcode
      (-c3) --contract3                   Search for contract addresses using a CREATE3 proxy deployer
    Other:
       (-d) --device <device_number>      Use device <device_number> (Add one for each device for multi-gpu)
       (-b) --bytecode <filename>         File containing contract bytecode (only needed when using --contract2 or --contract3)
       (-a) --address <address>           Sender contract address (only needed when using --contract2 or --contract3)
      (-ad) --deployer-address <address>  Deployer contract address (only needed when using --contract3)
       (-w) --work-scale <num>            Defaults to 15. Scales the work done in each kernel.
```

## Examples

### Find addresses starting with "a1decaf"
```bash
./vanity-eth-address-pattern --pattern a1decaf --device 0
```

### Find contract addresses starting with "a1c0ffee"
```bash
./vanity-eth-address-pattern --pattern a1c0ffee --contract --device 0
```

### Find addresses with multiple leading zeros (original functionality)
```bash
./vanity-eth-address-pattern --leading-zeros --device 0 --device 2 --work-scale 17
```

## Pattern Matching Details

- **Pattern Length**: Up to 8 bytes (16 hex characters)
- **Scoring**: Returns the number of matching bytes from the start of the address
- **Performance**: Maintains the same high performance as the original tool
- **GPU Acceleration**: Fully CUDA-accelerated for maximum speed

## Requirements

- NVIDIA CUDA-enabled GPU with compute capability ≥ 5.2
- CUDA Toolkit
- Same requirements as the original tool

## Building

```bash
nvcc -O3 -std=c++17 -o vanity-eth-address-pattern src/*.cu -lcuda -lcurand
```

## Performance

The pattern matching maintains the same high performance as the original tool:
- **RTX 5090**: ~2.8 billion addresses/second
- **RTX 4090**: ~3.8 billion addresses/second (normal addresses)
- **RTX 3090**: ~1.6 billion addresses/second

## Use Cases

Perfect for:
- **AI Projects**: Find addresses with AI-themed patterns like "a1decaf", "a1c0ffee"
- **Branding**: Create addresses that match your project name
- **Memorable Addresses**: Generate addresses that are easy to remember
- **Contract Deployment**: Deploy contracts to addresses with specific patterns

## Original Tool

This is based on the excellent work by [MrSpike63](https://github.com/MrSpike63/vanity-eth-address). All original functionality is preserved, with pattern matching as an additional feature.

## License

Same as original: AGPL-3.0
