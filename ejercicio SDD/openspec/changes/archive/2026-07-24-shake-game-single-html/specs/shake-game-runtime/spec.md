## ADDED Requirements

### Requirement: Game Loop
The system SHALL run a continuous game loop using `requestAnimationFrame` that updates the on-screen state (score, timer, visual feedback) at the display refresh rate while the game is in the `playing` state.

#### Scenario: Game starts in idle state
- **WHEN** the page loads
- **THEN** the game SHALL render in the `idle` state with a visible "Start" control and a score of 0

#### Scenario: Game advances to playing
- **WHEN** the user activates the "Start" control
- **THEN** the game SHALL transition to the `playing` state and begin the game loop

#### Scenario: Game ends on time-out
- **WHEN** the configured duration elapses while in `playing`
- **THEN** the game SHALL transition to the `ended` state, stop the loop, and display the final score

### Requirement: Shake Detection
The system SHALL detect a "shake" gesture from the device's motion sensors and SHALL also provide an on-screen button that produces the same effect for devices without motion access or when permission is denied.

#### Scenario: Motion sensors detect a shake
- **WHEN** the linear acceleration magnitude across X/Y/Z exceeds the configured threshold for at least two consecutive samples within a 200 ms window
- **THEN** the system SHALL register a shake event

#### Scenario: Motion permission denied
- **WHEN** `DeviceMotionEvent.requestPermission` is unavailable or returns "denied"
- **THEN** the system SHALL hide the motion-permission prompt and keep the on-screen "Shake" button visible and functional

#### Scenario: On-screen Shake button
- **WHEN** the user taps or clicks the on-screen "Shake" button
- **THEN** the system SHALL register exactly one shake event

### Requirement: Scoring
The system SHALL maintain a numeric score that increments on each registered shake and SHALL display the current score in the UI in real time.

#### Scenario: Score increments on shake
- **WHEN** a shake event is registered while in the `playing` state
- **THEN** the system SHALL increment the score by 1 and update the on-screen display within the next animation frame

#### Scenario: Shakes outside playing state are ignored
- **WHEN** a shake event is registered while in `idle` or `ended`
- **THEN** the system SHALL NOT modify the score

### Requirement: Reset
The system SHALL provide a "Reset" control that returns the game to the `idle` state with a score of 0 without reloading the page.

#### Scenario: User resets the game
- **WHEN** the user activates the "Reset" control
- **THEN** the game SHALL return to the `idle` state with score 0 and a fresh "Start" control visible
