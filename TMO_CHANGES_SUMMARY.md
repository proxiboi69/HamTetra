# TMO BTS PoC Implementation - Changes Summary

## Files Modified

### 1. `osmo-tetra-dmo/src/hamtetra_main2.c`
**Changes:**
- Added support for mode `2` (TMO BTS PoC)
- Updated help text to include new mode option
- Added TMO mode initialization logic
- Added call to `mac_hamtetra_set_mode()` function
- Ensured TX is enabled for TMO BTS mode

**Key additions:**
```c
// Initialize mode-specific settings
if (mode == 2) {
    // TMO BTS mode - initialize TMO-specific components
    printf("Initializing TMO BTS PoC mode...\n");
    mac_hamtetra_set_mode(TETRA_INFRA_TMO);
} else {
    // DMO mode (repeater or monitor)
    mac_hamtetra_set_mode(TETRA_INFRA_DMO);
}
```

### 2. `osmo-tetra-dmo/src/hamtetra_mac.c`
**Changes:**
- Added necessary includes for TMO functionality
- Added TMO-specific state variables
- Implemented `mac_hamtetra_set_mode()` function
- Added `build_tmo_sync_burst()` function for SYNC burst generation
- Added `mac_request_tx_buffer_content_tmo()` for TMO transmission logic
- Modified main MAC function to route to TMO handler when in TMO mode

**Key functions added:**
- `mac_hamtetra_set_mode()` - Sets infrastructure mode
- `build_tmo_sync_burst()` - Generates TETRA SYNC bursts
- `mac_request_tx_buffer_content_tmo()` - TMO-specific transmission logic

### 3. `osmo-tetra-dmo/src/hamtetra_mac.h`
**Changes:**
- Added function declaration for `mac_hamtetra_set_mode()`

## Technical Implementation Details

### TMO Mode Logic
The implementation follows ETSI TETRA standards:

1. **SYNC Burst Generation:**
   - Uses RCPC encoding (Rate Compatible Punctured Convolutional)
   - Applies block interleaving
   - Uses scrambling for type-5 bits
   - Includes Reed-Muller coding for AACH

2. **Transmission Schedule:**
   - SYNC bursts on timeslot 1 of frames 1 and 11 (BSCH)
   - Silent operation on traffic slots
   - Basic AACH content transmission

3. **Infrastructure Mode:**
   - Uses `TETRA_INFRA_TMO` enum value
   - Maintains separation from DMO functionality
   - Backward compatible with existing DMO modes

### Code Reuse
The implementation heavily reuses existing TETRA functions from:
- `conv_enc_test.c` - Especially the burst building logic
- Lower MAC functions for encoding/decoding
- Physical layer burst generation functions

## Compilation Requirements

The following includes were added:
```c
#include <osmocom/core/bits.h>
#include <arpa/inet.h>
#include "phy/tetra_burst_sync.h"
```

## Testing Requirements

- **Hardware:** Raspberry Pi with SXceiver
- **Software:** Full HamTetra build environment
- **Command:** `./hamtetra_main2 sx 430 2`

## Future Enhancements

1. **Immediate:**
   - Proper SYNC-PDU content (currently placeholder)
   - System Information (SI) broadcasting
   - Enhanced BSCH functionality

2. **Medium-term:**
   - Access grant functionality
   - Registration handling
   - Basic call setup

3. **Long-term:**
   - Full TMO protocol stack
   - Subscriber database
   - Advanced features (encryption, mobility)

## Compliance Notes

This PoC implementation aims for partial ETSI EN 300 392 compliance:
- ✅ Correct burst structure
- ✅ Proper encoding chain
- ✅ BSCH transmission schedule
- ⚠️ Basic SYNC-PDU content (needs enhancement)
- ⚠️ Limited protocol support (PoC only)

## Safety Notes

- Does not interfere with DMO functionality
- Isolated TMO logic prevents cross-mode issues
- Maintains existing command-line compatibility
- Safe for testing alongside DMO operations
