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

### Requirement: Inline Assets
The system SHALL encode all images, icons, and fonts as data URIs (or pure CSS/SVG) so that the file makes zero network requests for media.

#### Scenario: No external media requests
- **WHEN** the page is opened with the network panel recording requests
- **THEN** the page SHALL make zero requests to any URL outside the file's own `data:` or `blob:` origins (excluding the initial file load itself)

### Requirement: Self-Explanatory UI
The system SHALL display a short instructions panel that explains how to play (start, shake, score) so the file is understandable when shared standalone.

#### Scenario: Instructions visible on load
- **WHEN** the page loads
- **THEN** a clearly labeled instructions section SHALL be visible without requiring user interaction

#### Scenario: Instructions dismissable
- **WHEN** the user activates the instructions' dismiss control
- **THEN** the instructions panel SHALL hide but remain re-openable from a help control
