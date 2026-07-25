## ADDED Requirements

### Requirement: Inline Styling
The system SHALL render all visual styling from CSS embedded in a single `<style>` element inside the HTML document and SHALL NOT reference any external stylesheets.

#### Scenario: No external stylesheet references
- **WHEN** the file is opened
- **THEN** the document SHALL contain zero `<link rel="stylesheet">` elements

#### Scenario: All rules are inline
- **WHEN** the file is opened
- **THEN** the document SHALL contain exactly one `<style>` element whose content holds every style rule

### Requirement: Inline Scripts
The system SHALL execute all game logic from JavaScript embedded in a single `<script>` element inside the HTML document and SHALL NOT reference any external script files.

#### Scenario: No external script references
- **WHEN** the file is opened
- **THEN** the document SHALL contain zero `<script src="...">` elements

#### Scenario: All logic is inline
- **WHEN** the file is opened
- **THEN** the document SHALL contain exactly one `<script>` element whose content holds every game-logic function

### Requirement: Canvas Board Rendering
The system SHALL render the board, snake, and food on a single `<canvas>` element and SHALL keep the canvas responsive across viewports without external assets.

#### Scenario: Responsive canvas
- **WHEN** the viewport changes size
- **THEN** the canvas SHALL keep a square aspect ratio and scale its drawing buffer to remain crisp

#### Scenario: No external media requests
- **WHEN** the page is opened with the network panel recording requests
- **THEN** the page SHALL make zero requests to any URL outside the file's own `data:` or `blob:` origins (excluding the initial file load itself)

### Requirement: Self-Explanatory UI
The system SHALL display a short instructions panel explaining controls and goal so the file is understandable when shared standalone.

#### Scenario: Instructions visible on load
- **WHEN** the page loads
- **THEN** a clearly labeled instructions section SHALL be visible without requiring user interaction

#### Scenario: Instructions dismissable
- **WHEN** the user activates the instructions' dismiss control
- **THEN** the instructions panel SHALL hide but remain re-openable from a help control

### Requirement: Accessible Controls
The system SHALL provide both keyboard control (arrow keys / WASD) and an on-screen directional pad that work equivalently, and SHALL respect `prefers-reduced-motion` for non-essential animation.

#### Scenario: Keyboard control on desktop
- **WHEN** the user presses arrow keys or WASD while in `playing`
- **THEN** the system SHALL queue the corresponding direction for the next tick

#### Scenario: On-screen D-pad on touch devices
- **WHEN** the user taps a direction button on the on-screen D-pad
- **THEN** the system SHALL queue the corresponding direction for the next tick, identically to keyboard input
