# Sudoku - Single-File Browser Game

A fully offline, single-file Sudoku game built with vanilla HTML, CSS, and JavaScript. No external dependencies, no network requests, and works on any modern browser.

## Features

### Core Gameplay
- **9x9 Sudoku Grid** with clear 3x3 sub-box borders
- **Three Difficulty Levels**: Easy, Medium, and Hard
- **30+ Curated Puzzles** - all pre-validated with unique solutions
- **Cell Selection** - click or tap to select cells
- **Number Entry** - via on-screen keypad or keyboard (1-9)
- **Clear Cells** - remove entered numbers with the X button or 0/Backspace/Delete

### Visual Feedback
- **Row/Column/Box Highlighting** - see related cells when selecting
- **Fixed vs User Numbers** - original puzzle numbers are visually distinct (cyan) and protected from editing
- **Same-Value Highlighting** - when enabled, highlights all cells with the same number as the selected cell
- **Conflict Detection** - highlights duplicate numbers in rows, columns, or boxes
- **Shake Animation** - visual feedback when making a conflicting move

### Game Controls
- **Pause/Resume** - stops the timer and dims the screen; click anywhere or press any key to resume
- **Reset Puzzle** - restore the puzzle to its initial state
- **New Puzzle** - generate a new puzzle of the same difficulty
- **Difficulty Selector** - switch between Easy, Medium, and Hard

### Game Tracking
- **Timer** - starts on first move, pauses when the game is paused
- **Mistake Counter** - increments when you create a conflict
- **Completion Screen** - shows final time, difficulty, and mistake count

### Accessibility
- **Keyboard Navigation** - full arrow key and tab navigation
- **ARIA Labels** - screen reader support for all interactive elements
- **Focus Indicators** - clear visual focus states

### Technical Features
- **Fully Offline** - no network requests, works without internet
- **Responsive Design** - adapts to desktop and mobile screens
- **Local Storage** - saves your preferences (highlight toggle state)
- **Auto-Pause** - automatically pauses when you switch browser tabs

## How to Play

1. **Open `index.html`** in any modern web browser
2. **Select a difficulty** (Easy, Medium, or Hard) from the start screen
3. **Click/tap a cell** to select it
4. **Enter a number** using:
   - The on-screen number pad (1-9), or
   - Keyboard keys (1-9)
5. **Clear a cell** using:
   - The X button on the number pad, or
   - Keyboard keys (0, Backspace, or Delete)
6. **Complete the puzzle** by filling all cells with valid numbers (1-9) - no duplicates in any row, column, or 3x3 box

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| 1-9 | Enter number in selected cell |
| 0, Backspace, Delete | Clear selected cell |
| Arrow Keys | Navigate between cells |
| Tab | Move to next cell |
| Shift+Tab | Move to previous cell |
| Any Key | Resume from pause |

## Technical Implementation

### Architecture

The game follows a clean separation of concerns:

```
┌─────────────────────────────────────────┐
│              SudokuUI                   │
│  (Event handling, DOM manipulation)     │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────▼───────────────────────┐
│            SudokuEngine                 │
│  (Game logic, state management)         │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────▼───────────────────────┐
│         PuzzleLibrary                   │
│  (Static puzzle data)                   │
└─────────────────────────────────────────┘
```

### Components

#### SudokuEngine
The core game engine implementing the `SudokuEngineAPI` interface:
- `loadPuzzle(difficulty)` - loads a random puzzle of the specified difficulty
- `isFixed(row, col)` - checks if a cell is a fixed (original) number
- `applyMove(row, col, value)` - attempts to place a number, returns `MoveResult`
- `getConflicts()` - returns array of `CellConflict` objects
- `toggleDuplicateHighlight(enabled)` - enables/disables conflict highlighting
- `resetPuzzle()` - restores the puzzle to initial state
- `newPuzzle(difficulty)` - loads a new puzzle
- `getGameState()` - returns current game state
- `incrementMistake()` - increments mistake counter
- `getMistakeCount()` - returns current mistake count
- `isComplete()` - checks if the puzzle is solved

#### Event System
The engine emits events for UI updates:
- `CellSelectionEvent` - when a cell is selected
- `NumberEntryEvent` - when a number is entered
- `ConflictDetectedEvent` - when conflicts are detected
- `MistakeIncrementedEvent` - when a mistake is made
- `TimerTickEvent` - every second while timer runs
- `PuzzleCompletedEvent` - when puzzle is solved
- `PuzzleLoadedEvent` - when a new puzzle loads
- `PuzzleResetEvent` - when puzzle is reset

#### LocalStorageDB
Persists user preferences and game state:
- `sudokuState` - serialized `GameState` object
- `sudokuPrefs` - serialized `UserPreferences` object

### Data Structures

#### Puzzle
```javascript
{
  id: string,           // Unique identifier (e.g., "e1", "m5", "h3")
  difficulty: string,   // "easy" | "medium" | "hard"
  board: string,        // 81-character string (0 = empty)
  solution: string      // 81-character solution string
}
```

#### Cell
```javascript
{
  value: number,        // 0-9 (0 = empty)
  isFixed: boolean,     // true for original given numbers
  row: number,          // 0-8
  col: number           // 0-8
}
```

#### MoveResult
```javascript
{
  success: boolean,
  reason: string        // "FIXED_CELL" | "INVALID_RANGE" | "ALREADY_SET" | "CONFLICT" | "OK"
}
```

#### CellConflict
```javascript
{
  row: number,
  col: number,
  duplicateValue: number,
  scope: string         // "row" | "col" | "box"
}
```

#### GameState
```javascript
{
  board: number[],      // 81-element array of cell values
  difficulty: string,
  elapsedSeconds: number,
  mistakeCount: number,
  isPaused: boolean,
  isCompleted: boolean,
  highlightEnabled: boolean
}
```

### Puzzle Library

The game includes 36 pre-validated puzzles:
- **12 Easy puzzles** - more given numbers, simpler solving
- **12 Medium puzzles** - moderate difficulty
- **12 Hard puzzles** - challenging but solvable

All puzzles are:
- Pre-validated to have exactly one solution
- Encoded as compact 81-character strings
- Randomly selected on "New Puzzle" for variety

### Performance

The game is optimized for:
- **Fast load time** - single HTML file under 60KB
- **Instant rendering** - no external resources to load
- **Smooth interactions** - CSS transitions and animations
- **Memory efficiency** - in-memory game state, no heavy computations

### Browser Support

Tested and works on:
- Chrome 80+
- Firefox 75+
- Safari 13+
- Edge 80+
- Mobile browsers (iOS Safari, Chrome for Android)

### File Structure

```
index.html          - Complete game (HTML + CSS + JavaScript)
README.md           - This documentation file
```

## License

This project is provided as-is for educational and personal use.