# Architecture Guardrails: init-sudoku-20260619-0321

> Generated at 2026-06-19T03:23:39.681Z
> Source: LLM-Generated
> Derived from: tpm-contract.json

## Interfaces

### SudokuEngineAPI (api)

Functions to load puzzles, validate moves, apply moves, and query game state

**Contract:** Methods: loadPuzzle(difficulty:string):Puzzle, isMoveValid(row:int,col:int,value:int):bool, applyMove(row:int,col:int,value:int):void, getGameState():GameState, resetPuzzle():void, toggleDuplicateHighlight(enabled:bool):void

### CellSelectionEvent (event)

Dispatched when a user selects a cell

**Contract:** Payload: {row:int, col:int}

### NumberEntryEvent (event)

Dispatched when a user enters a number into a selected cell

**Contract:** Payload: {row:int, col:int, value:int}

### TimerTickEvent (event)

Dispatched every second while the timer is running

**Contract:** Payload: {elapsedSeconds:int}

### PuzzleDataLibrary (shared-lib)

Static array of curated puzzles embedded in the HTML file

**Contract:** Array of objects {id:string, difficulty:'easy'|'medium'|'hard', board:string[81], solution:string[81]}

### LocalStorageDB (database)

Browser localStorage used to persist last game state and user preferences

**Contract:** Key 'sudokuState' stores JSON serialized GameState; Key 'sudokuPrefs' stores JSON serialized UserPreferences

## Data Contracts

### Puzzle

- **Storage:** Embedded static array in HTML
- **Ownership:** Application (code)
- **Notes:** Curated list per difficulty, used by SudokuEngineAPI

### GameState

- **Storage:** In‑memory during session, optionally persisted to localStorage
- **Ownership:** Client (browser)
- **Notes:** Tracks current board, timer, mistake count, paused flag

### UserPreferences

- **Storage:** localStorage
- **Ownership:** Client (browser)
- **Notes:** Stores duplicate‑highlight toggle and last selected difficulty

## Scalability

**Expected Load:** Single user per browser session; typical interaction <100 moves/minute, timer tick every 1 s

**Bottlenecks:**
- DOM reflow on each cell update
- Timer interval handling
- Size of embedded puzzle data

**Mitigations:**
- Batch DOM updates via requestAnimationFrame
- Use a 1 s setInterval for timer ticks
- Compress puzzle data and load only needed difficulty

## Security Baseline

- **Auth Method:** None (offline, no authentication)
- **Data Classification:** Public (non‑sensitive)

**Threat Vectors:**
- Cross‑site scripting via injected HTML
- Denial‑of‑service through infinite loops
- Data tampering via localStorage

**Mitigations:**
- Content‑Security‑Policy restricts script sources to self
- Strict mode, no eval, input validation
- Sanitize stored data and validate before use

## Architecture Decision Records

### ADR-001: Single‑file packaging

**Status:** accepted

**Context:** Addresses NFR-01 and NFR-02: all code, styles, and assets must be packaged in one HTML file

**Decision:** Embed CSS and JavaScript inline, embed puzzle data as JSON within a script tag, and avoid any external resource references

**Consequences:** Larger HTML file size and harder to update individual assets, but fulfills offline and single‑file constraints

### ADR-002: Use curated puzzle list

**Status:** accepted

**Context:** Addresses FR-02 and NFR-01: new puzzle selection must work offline without a generator

**Decision:** Store a static array of pre‑validated puzzles for each difficulty inside the HTML file and select from it on demand

**Consequences:** Limited variety of puzzles; however ensures deterministic offline operation and simplifies implementation

### ADR-003: Event‑driven UI architecture

**Status:** proposed

**Context:** Addresses FR-04, FR-05, and FR-06: need decoupled handling of cell selection, number entry, and optional duplicate highlighting

**Decision:** Define custom DOM events (cellSelected, numberEntered, duplicateToggle) and corresponding listeners within the single file to separate UI from game logic

**Consequences:** Improves modularity and testability; adds minimal overhead which is acceptable given the low expected load

## Confidence & Gaps

**Confidence:**
- interfaces: *assumed*
- dataContracts: *assumed*
- scalability: *assumed*
- securityBaseline: *assumed*
- adrs: *assumed*

**Gaps:**
- No explicit performance or concurrent‑user load numbers provided in the contract
