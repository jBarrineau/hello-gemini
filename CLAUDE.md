# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-file Minesweeper web game with Firebase integration for global leaderboards. The entire application is self-contained in `index.html` with embedded CSS and JavaScript modules.

## Architecture

**Single-Page Application Structure:**
- All code lives in `index.html` (HTML markup, CSS styles, and JavaScript logic)
- No build process or package management required
- Open directly in browser to run

**Firebase Integration:**
- Uses Firebase Firestore for persistent leaderboard storage
- Firebase SDK loaded via CDN (v10.7.1) as ES modules
- Configuration object is embedded in the HTML (lines 308-315)
- Scores are stored in a "scores" collection with fields: name, time, difficulty, createdAt

**Game State Management:**
- Grid-based architecture using 2D array (`grid[rows][cols]`)
- Each cell object contains: element (DOM ref), isMine, isRevealed, isFlagged, neighborMines
- Global state variables: gameActive, flags, time, minesLocations (Set), revealedCount, firstClick
- Timer runs via setInterval, cleared on game over/reset

**Key Game Mechanics:**
- Mines are placed AFTER first click to ensure safe start (placeMines excludes clicked cell)
- Flood-fill algorithm for revealing empty cells (recursive revealCell)
- Win condition: revealed cells = total cells - mines
- Three difficulty levels: Easy (9x9, 10 mines), Medium (16x16, 40 mines), Hard (16x30, 99 mines)

**UI Components:**
- Main grid: dynamically generated cells with CSS Grid layout
- Two modals: game-over modal (win/loss + score submission) and leaderboard modal
- Stats display: mine counter (mines - flags) and timer
- Difficulty selector buttons that reset game with new parameters

## Development Commands

**Running the game:**
```bash
# Simply open the file in a browser
start index.html  # Windows
open index.html   # macOS
xdg-open index.html  # Linux
```

**No build, test, or lint commands** - this is a static HTML file with no tooling.

## Firebase Configuration

The Firebase config object (lines 308-315) contains the project's credentials. When modifying Firebase integration:
- The config is for project "sweeping-mines-44f32"
- Scores collection structure: `{ name: string, time: number, difficulty: string, createdAt: timestamp }`
- Leaderboard queries fetch top 10 scores per difficulty, ordered by time ascending

## Code Modification Guidelines

**When editing the game logic:**
- The grid is generated in startGame() (lines 464-507)
- Cell event handlers are attached during grid creation (click for reveal, contextmenu for flag)
- Mine placement logic is in placeMines() - preserve the excludeR/excludeC parameters for first-click safety
- Number calculation happens after mine placement via calculateNumbers()

**When modifying UI:**
- CSS custom properties (lines 8-20) control theming
- Grid cell size is controlled by --cell-size variable (default 35px)
- Number colors (val-1 through val-8) are defined in lines 135-142
- Modal visibility controlled by "show" class on modal elements

**When changing difficulty levels:**
- Difficulty buttons store data attributes: data-rows, data-cols, data-mines
- getDifficultyName() (lines 458-462) maps grid sizes to difficulty strings
- Must update both button definitions (HTML) and leaderboard logic if adding new difficulties
