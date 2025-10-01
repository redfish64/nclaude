# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a wrapper script for the Claude CLI that adds desktop notifications when Claude finishes working. It's implemented as a single Expect script (`nclaude`) that monitors Claude's I/O patterns to detect when work is complete.

## Architecture

The script uses a state machine with three states:
- **IDLE**: Waiting for user input
- **USER_TYPED**: User submitted input, monitoring Claude's response duration
- **WORKING**: Claude has been outputting for ≥3 seconds, now monitoring for silence

State transitions:
1. User hits enter → USER_TYPED (starts `output_start_time` timer)
2. Claude outputs continuously for ≥3 seconds → WORKING (triggers `check_working`)
3. Claude silent for ≥2 seconds while WORKING → IDLE (sends notification via `notify-send`)

Key timing thresholds:
- `working_threshold`: 3.0 seconds of output before entering WORKING state
- `silence_threshold`: 2.0 seconds of silence before considering work complete

## Key Procedures

- `handle_output`: Called on any output from Claude, updates `last_output_time`
- `handle_input`: Detects user pressing enter to transition to USER_TYPED
- `check_working`: Scheduled callback to check if threshold reached for WORKING state
- `check_silence`: Scheduled callback to detect when Claude stops working

## Running the Script

```bash
./nclaude [arguments passed to claude]
```

The script spawns the `claude` CLI with all provided arguments and wraps it with I/O monitoring.

## Dependencies

- **expect**: TCL-based automation tool (must be at `/usr/bin/expect`)
- **claude**: The Claude CLI must be in PATH
- **notify-send**: For desktop notifications (typically from libnotify)

## Testing

To test state transitions manually:
1. Run `./nclaude` and submit a prompt
2. Observe when notification fires based on Claude's output pattern
3. Adjust `working_threshold` and `silence_threshold` in nclaude:7-8 to tune behavior
