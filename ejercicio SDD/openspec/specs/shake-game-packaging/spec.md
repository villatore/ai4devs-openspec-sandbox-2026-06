## Purpose

Define the single-file packaging rules for the shake game: no external network requests after load, no build step, direct execution from `file://` or any static host, and a reasonable file-size budget.

## Requirements

### Requirement: Single-File Delivery
The system SHALL be delivered as exactly one HTML file that contains all markup, styles, scripts, and assets required to play the game.

#### Scenario: File opens directly from disk
- **WHEN** the user double-clicks the `.html` file in a file explorer
- **THEN** the browser SHALL open it via the `file://` protocol and the game SHALL be fully playable

#### Scenario: File served by a static host
- **WHEN** the file is served by any static HTTP/HTTPS host with no additional configuration
- **THEN** the game SHALL be fully playable

### Requirement: No Build Step
The system SHALL require no build, transpilation, bundling, or preprocessing step; the source file SHALL be runnable as-is.

#### Scenario: Direct execution
- **WHEN** the file is opened in a modern browser without any preprocessing
- **THEN** the game SHALL run without console errors

### Requirement: No External Network Requests
The system SHALL make zero external network requests at runtime (other than the initial file load itself).

#### Scenario: Offline operation
- **WHEN** the device is disconnected from the network after the file has loaded
- **THEN** the game SHALL continue to function identically

### Requirement: Reasonable Size Budget
The system SHALL be delivered as a single file whose total size does not exceed 200 KB so it remains trivially shareable via email, chat, or USB.

#### Scenario: File size under budget
- **WHEN** the file is saved to disk
- **THEN** its total byte size SHALL be less than or equal to 204,800 bytes (200 KiB)
