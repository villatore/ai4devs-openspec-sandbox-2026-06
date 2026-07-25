# snake-game-runtime Specification

## Purpose
TBD - created by archiving change snake-game-single-html. Update Purpose after archive.
## Requirements
### Requirement: Game Loop
The system SHALL run a fixed-timestep game loop that advances the snake one cell per tick while the game is in the `playing` state, and SHALL render the board on each tick.

#### Scenario: Game starts in idle state
- **WHEN** the page loads
- **THEN** the game SHALL render an idle board with a visible "Start" control and a score of 0

#### Scenario: Game advances to playing
- **WHEN** the user activates the "Start" control
- **THEN** the game SHALL transition to the `playing` state and begin ticking the snake at the configured speed

#### Scenario: Loop pauses when tab hidden
- **WHEN** the document becomes hidden while in `playing`
- **THEN** the game SHALL pause the loop and resume without advancing lost time when the tab is shown again

### Requirement: Movement and Direction
The system SHALL move the snake one grid cell per tick in the current heading and SHALL NOT allow a 180° reversal into the snake's own neck (the immediately opposite direction is ignored).

#### Scenario: Direction changes per tick
- **WHEN** the user issues a valid direction command while in `playing`
- **THEN** the system SHALL queue the change to take effect on the next tick, preventing mid-cell reversal

#### Scenario: Reversal blocked
- **WHEN** the user issues a direction opposite to the current heading (e.g., moving right and pressing left)
- **THEN** the system SHALL ignore that command and keep the current heading

### Requirement: Food and Growth
The system SHALL place exactly one food cell on an unoccupied board cell and SHALL grow the snake by one segment after it eats food, incrementing the score by 1.

#### Scenario: Food spawns on a free cell
- **WHEN** food needs to be placed
- **THEN** the system SHALL choose a cell that is not occupied by any snake segment

#### Scenario: Eating grows the snake
- **WHEN** the snake's head moves onto the food cell in `playing`
- **THEN** the system SHALL increment the score by 1, grow the snake by one segment, and spawn fresh food on a free cell

#### Scenario: Board full on win
- **WHEN** the snake occupies every cell of the board
- **THEN** the system SHALL transition to the `ended` state declaring a win

### Requirement: Collision and Game Over
The system SHALL end the round when the snake's head collides with a wall or any segment of its own body.

#### Scenario: Wall collision
- **WHEN** the snake's next head position lies outside the board bounds
- **THEN** the system SHALL transition to the `ended` state and stop the loop

#### Scenario: Self collision
- **WHEN** the snake's next head position coincides with any existing body segment
- **THEN** the system SHALL transition to the `ended` state and stop the loop

### Requirement: Reset
The system SHALL provide a "Reset" control that returns the game to the `idle` state with a fresh board and a score of 0 without reloading the page.

#### Scenario: User resets the game
- **WHEN** the user activates the "Reset" control from any state
- **THEN** the game SHALL return to the `idle` state with the snake restored to its starting length and position and a score of 0

### Requirement: Best Score Persistence
The system SHALL persist the highest score achieved across sessions when storage is available and SHALL operate without persistence when it is not, never crashing.

#### Scenario: New best saved
- **WHEN** the round ends with a score greater than the stored best
- **THEN** the system SHALL store the new best and reflect it in the UI

#### Scenario: Storage unavailable
- **WHEN** `localStorage` access throws (e.g., private mode)
- **THEN** the system SHALL catch the error, continue playing, and skip persistence rather than crashing

