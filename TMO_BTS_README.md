# TETRA TMO BTS Proof of Concept Implementation

## Overview

This implementation extends `hamtera_main2` to support a new mode: TMO BTS PoC (Proof of Concept).

## Usage

```bash
./hamtera_main2 sx 430 2  # TMO BTS mode
```

Where:
- `sx` = SXceiver hardware
- `430` = Frequency in MHz (430.000 MHz) 
- `2` = TMO BTS PoC mode

## Implementation Details

### Mode Support

The implementation adds support for mode `2` which activates TMO (Trunked Mode Operation) BTS functionality:

- **Mode 0**: DMO Monitor
- **Mode 1**: DMO Repeater (default)
- **Mode 2**: TMO BTS PoC (NEW)

### Key Components

1. **Main Program (`hamtera_main2.c`)**
   - Added mode 2 support in help text
   - Added TMO mode initialization
   - Ensures TX is enabled for BTS mode

2. **MAC Layer (`hamtera_mac.c`)**
   - Added `mac_hamtetra_set_mode()` function
   - Added TMO-specific transmission logic
   - Implemented basic SYNC burst generation
   - Added BSCH (Broadcast Synchronization CHannel) support

3. **Infrastructure Mode**
   - Uses `TETRA_INFRA_TMO` for TMO mode
   - Maintains backward compatibility with DMO mode

### TMO BTS Transmission Logic

The PoC implementation transmits:

- **SYNC bursts** on timeslot 1 of frames 1 and 11 (BSCH slots)
- **Silent operation** on traffic slots
- **Basic AACH** (Access Assignment CHannel) content

### ETSI Compliance

This implementation aims for partial ETSI TETRA TMO BTS compliance by:

- Using proper TETRA burst structure
- Implementing RCPC encoding (Rate Compatible Punctured Convolutional)
- Using block interleaving
- Applying scrambling
- Using Reed-Muller coding for AACH

### Based on Existing Code

The TMO implementation reuses components from:
- `osmo-tetra-dmo/src/conv_enc_test.c` - Especially the `build_sb()` function
- Existing DMO MAC functions
- TETRA burst generation functions

## Limitations (PoC)

- Basic SYNC PDU content (placeholder data)
- Limited to BSCH transmission
- No full TMO protocol stack
- No subscriber management
- No call handling

## Testing

This code must be pushed to Git and tested on Raspberry Pi with real SDR hardware as it cannot be compiled in the current environment.

## Future Enhancements

- Full SYNC-PDU implementation
- System Information broadcasting
- Access management
- Call control
- Subscriber database integration
- Full ETSI EN 300 392 compliance

## Notes

- Maintains backward compatibility with existing DMO functionality
- Uses existing TETRA encoding/decoding functions
- Isolated TMO functionality prevents interference with DMO operation
