# Memory Management Implementation

A C implementation of virtual-to-physical address translation and page eviction policies (FIFO and CLOCK) for a simulated 64-bit system.

## Overview

This project simulates the memory management system of a 64-bit architecture with 4 KiB pages and 4 levels of page tables. It includes implementations for:

- Bitwise operations for page table entry manipulation
- Virtual address breakdown into page table indices and offset
- Virtual-to-physical address translation
- Page eviction policies (FIFO and CLOCK)

## System Architecture

The implementation is based on a 64-bit system with the following specifications:

- Page size: 4 KiB (2^12 bytes)
- Virtual address: 64 bits
- 4 levels of page tables
- Page Table Entry (PTE) format: 12 bits of flags, 40 bits of page frame, 12 reserved bits

### Virtual Address Format

```
63    48 47   39 38   30 29   21 20   12 11    0
+-------+-------+-------+-------+-------+-------+
|Reserved|  PL4  |  PL3  |  PL2  |  PL1  |Offset |
+-------+-------+-------+-------+-------+-------+
   16b      9b      9b      9b      9b     12b
```

- 12 bits for page offset (bits 0-11)
- 9 bits for each level of page table (bits 12-20, 21-29, 30-38, 39-47)
- 16 bits reserved (bits 48-63)

### Page Table Entry Format

```
63    52 51    12 11     0
+-------+--------+-------+
|Reserved| Frame  | Flags |
+-------+--------+-------+
   12b      40b     12b
```

Important PTE flags:
- PRESENT (bit 0): 1 if page frame is present in memory
- READWRITE (bit 1): 1 if read/write, 0 if readonly
- USERSUPERVISOR (bit 2): 1 if accessible in user mode, 0 if only in supervisor mode

## Implementation Details

### Bitwise Operations

The project implements three essential bitwise operations:
- `set_bit()`: Sets a specific bit to 1
- `test_bit()`: Tests if a specific bit is set
- `clear_bit()`: Sets a specific bit to 0

### Virtual Address Processing

Functions to extract relevant components from a virtual address:
- `index_page_level_1()`: Returns index for the 1st level page table
- `index_page_level_2()`: Returns index for the 2nd level page table
- `index_page_level_3()`: Returns index for the 3rd level page table
- `index_page_level_4()`: Returns index for the 4th level page table
- `offset()`: Returns the page offset

### Address Translation

- `pte_to_physical()`: Converts a Page Table Entry and Virtual Address into a Physical Address

### Page Eviction Policies

The project implements two eviction policies:

#### FIFO (First-In-First-Out)
- Evicts the oldest page when memory is full
- Implementation files: `mmem_fifo.c` and `mmem_fifo.h`

#### CLOCK
- A second-chance algorithm that approximates LRU
- Uses a reference bit to give pages a second chance before eviction
- Implementation files: `mmem_clock.c` and `mmem_clock.h`

Each policy implements three main functions:
- `init_list()`: Initializes memory structure
- `map_page()`: Maps a virtual address to a page frame
- `access_page()`: Handles access to a virtual address

## Building and Usage

### Prerequisites
- GCC compiler
- Make (optional, for build automation)

### Compilation
```bash
gcc -o memory_manager *.c -Wall -Werror
```

### Running Tests
```bash
./memory_manager
```

## Project Structure

```
.
├── mmem.h           # Common definitions and structures
├── mmem_bitwise.c   # Bitwise operations implementation
├── mmem_address.c   # Virtual address manipulation
├── mmem_clock.c     # CLOCK eviction policy
├── mmem_clock.h     # CLOCK policy header
├── mmem_fifo.c      # FIFO eviction policy
├── mmem_fifo.h      # FIFO policy header
└── main.c           # Test harness
```

## Notes

- This implementation is for educational purposes and simulates the memory management system rather than interfacing with actual hardware.
- The implementation assumes a 64-bit architecture with specific page table structures as described in the assignment.
