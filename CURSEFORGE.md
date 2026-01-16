# HyFixes

Essential bug fixes for Hytale Early Access servers. Prevents crashes and player kicks caused by known issues.

## What This Fixes

### Pickup Item Crash (Critical)
When a player disconnects while picking up an item, the world thread crashes and **kicks ALL players** in that world.
- **Error:** `NullPointerException` in `PickupItemSystem.tick()` - null `targetRef`
- **Fix:** Safely marks corrupted pickup items as finished before they crash the server

### RespawnBlock Crash (Critical)
When a player breaks a bed or sleeping bag, they get **kicked from the server**.
- **Error:** `NullPointerException` in `RespawnBlock$OnRemove` - null `respawnPoints` array
- **Fix:** Initializes the respawn points array before Hytale's buggy code runs

### ProcessingBench Window Crash (Critical)
When a player breaks a campfire or crafting table while another player has it open, they get **kicked from the server**.
- **Error:** `NullPointerException` in `BenchWindow.onClose0` - null `ref` during window close
- **Fix:** Clears the windows map before the crash-causing close handlers run

### Instance Exit Missing Return World (Critical)
When a player exits an instance (dungeon, cave, etc.) and the return world data is corrupted, they get **kicked from the server**.
- **Error:** `IllegalArgumentException` in `InstancesPlugin.exitInstance` - Missing return world
- **Fix:** Tracks player position before entering instances and uses it as fallback destination

### Empty Archetype Entities (Monitoring)
Logs entities with corrupted/empty component data for debugging. These don't crash but indicate world data issues.

### Chunk Memory Bloat (High - v1.2.0+)
Hytale doesn't properly unload chunks when players move away, causing **unbounded memory growth** and eventual OOM crashes.
- **Symptoms:** Server memory grows from 4GB to 14GB+ while players fly around; chunks never decrease
- **Example:** Player loads 5,735 chunks, only 317 are in view radius, 5,400+ stay in memory forever
- **Fix:** `ChunkCleanupSystem` runs on the main thread every 30 seconds to trigger Hytale's internal chunk cleanup
- **Results:** Observed 77% reduction in loaded chunks (942 → 211) after fix
- **Commands:** `/chunkstatus` (view chunk counts), `/chunkunload` (force cleanup)

## Installation

1. Download `hyfixes-x.x.x.jar`
2. Place in your server's `mods/` folder
3. Restart the server

## Compatibility

- Hytale Early Access (2025+)
- Java 21+
- Server-side only

## Source Code

[GitHub Repository](https://github.com/John-Willikers/hyfixes)
