# nclaude

A wrapper script for the Claude CLI that sends desktop notifications when Claude finishes working.

## Overview

`nclaude` monitors Claude's output patterns and sends a desktop notification when it detects that Claude has finished responding to your prompts. This is useful when working on long-running tasks where you want to switch contexts and be notified when Claude is done.

## Installation

1. Ensure dependencies are installed:
   ```bash
   # On Debian/Ubuntu
   sudo apt-get install expect libnotify-bin

   # On Fedora
   sudo dnf install expect libnotify
   ```

2. Make sure the Claude CLI is installed and available in your PATH

3. Make the script executable:
   ```bash
   chmod +x nclaude
   ```

## Usage

Use `nclaude` exactly as you would use the `claude` command:

```bash
./nclaude [arguments passed to claude]
```

All arguments are forwarded to the Claude CLI. The script runs transparently in the background, monitoring I/O patterns.

## How It Works

The script uses a state machine to detect when Claude is working:

1. **IDLE**: Waiting for user input
2. **USER_TYPED**: User submitted input, Claude is responding
3. **WORKING**: Claude has been outputting continuously for ≥3 seconds

When Claude remains silent for ≥2 seconds while in the WORKING state, a notification is sent via `notify-send`.

### Timing Thresholds

You can adjust the behavior by editing the thresholds in the script:

- `working_threshold` (line 7): Default 0.1 seconds - how long Claude must output before considered "working"
- `silence_threshold` (line 8): Default 2.0 seconds - how long silence must last before considered "done"

## Dependencies

- **expect**: TCL-based automation tool for monitoring I/O
- **claude**: The Claude CLI (must be in PATH)
- **notify-send**: Desktop notification daemon (from libnotify)

## Technical Details

The implementation uses Expect's `interact` command to pass through all I/O while monitoring output timing. The state machine tracks:

- `output_start_time`: When Claude first started responding
- `last_output_time`: When the most recent output was seen

Two scheduled callbacks manage state transitions:
- `check_working`: Monitors if output duration exceeds the working threshold
- `check_silence`: Monitors if silence duration exceeds the silence threshold

## License

Free for everybody! Guaranteed to work or TRIPLE your money back!
