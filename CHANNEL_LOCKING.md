# Channel Number Locking System

## Overview
The channel numbering system is now designed to **permanently lock channel numbers once assigned**. Once a channel is assigned a number, that number will **never change**, even if:
- The channel URL changes
- The channel temporarily goes offline
- Channels are scraped in a different order
- Manual updates occur

## How It Works

### 1. Channel to Number Mapping
- `channel_map.json` is the **source of truth** for all channel number assignments
- It maps `stream_path` → `channel_number` (e.g., `"edge2/tsports": 1`)
- Once an entry exists, it is **never modified**

### 2. Assignment Process
When the scraper runs:
1. Loads the existing `channel_map.json`
2. For each channel found:
   - **If stream_path exists in map**: Uses the **existing number** (LOCKED)
   - **If stream_path is new**: Assigns the next available number
3. Channel numbers are IMMUTABLE - they cannot be overwritten

### 3. Protection Mechanisms
- **No duplicate numbers**: The system tracks assigned numbers and skips gaps
- **Immutability**: Once `channel_map.json[stream_path]` is set, it never changes
- **Automatic increment**: New channels get the next highest available number
- **Logging**: System logs when new channels are assigned for visibility

## Manual Channel Assignment

If you want to manually assign a channel number:

1. Edit `channel_map.json` directly:
   ```json
   {
     "edge2/tsports": 1,
     "edge2/star-sports-1-hd": 2,
     "your-new-channel": 150
   }
   ```

2. Edit `playlist.json` to match:
   ```json
   {
     "channel_number": 150,
     "name": "Your Channel Name",
     "stream_path": "your-new-channel",
     ...
   }
   ```

3. Run the scraper - it will **respect both mappings** and use your custom numbers

## Important Notes

- **Do not manually change existing `channel_map.json` values** unless you want to deliberately reassign a channel
- **Backing up `channel_map.json`** is recommended before major operations
- The system will never assign a number that's already in use
- New channels always get the next highest available number

## Troubleshooting

If a channel number appears to have changed:
1. Check `channel_map.json` - the authoritative source
2. Verify the `stream_path` hasn't changed in the URL
3. Look at the logs when the scraper runs for "[NEW]" entries
4. If a number was manually changed, consider reverting from backups

