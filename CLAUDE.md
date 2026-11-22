# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page tic-tac-toe game hosted on GitHub Pages at https://najwazfrh.github.io/tictactoe/ (repo `najwazfrh/tictactoe`, served from `main`, root folder). No build step, no dependencies, no tests: plain HTML, CSS and JavaScript in one file.

## Files and deploying

- `index.html` and `tictactoe.html` are **identical copies**. `index.html` serves the root URL, and `tictactoe.html` keeps the `/tictactoe.html` URL working. After any change, keep them the same: edit one, then `cp index.html tictactoe.html`.
- Deploy by committing and running `git push`. Pages rebuilds automatically. To check the build, run `gh api repos/najwazfrh/tictactoe/pages/builds/latest --jq .status`, which should print `built`.
- To run it locally, open `index.html` in a browser. Fonts load from Google Fonts. Without a connection, the page falls back to system fonts.
- The parent folder (`../tic-tac-toe.html`) holds the original claude.ai artifact version. It has no `<html>`/`<head>` skeleton and isn't part of this site.

## How the page works

- **Styling**: all colors are CSS custom properties on `:root`, redefined for dark mode under `prefers-color-scheme: dark` and `[data-theme="dark"]`. Add new colors as tokens in all three places. The graph-paper background comes from `body` gradients. The board lines and win strike are absolutely positioned SVGs (`viewBox 0 0 300 300`, 100 units per cell) layered over a 3×3 grid of `<button class="cell">`.
- **Ink animation**: marks and the win strike animate `stroke-dashoffset` from `--len`, set inline on each path. If you change a path's shape, update its `--len` to about the path's length.
- **Game logic** (the IIFE in the `<script>`):
  - `board` is an array of 9 cells, each `'X'`, `'O'` or `null`. `winner()` checks `LINES` and returns `{p, line}`, or `{p:'D'}` for a draw.
  - `place()` is the only function that changes the board, the turn or the score. `humanMove()` blocks clicks during the computer's turn. `maybeCpu()` schedules the computer's move.
  - In computer mode, the human is always X and the computer is O. "Unbeatable" uses full minimax with depth-adjusted scores. "Easy" plays a random square 60% of the time.
  - `render()` updates cells on the board without redrawing ones that are already filled (tracked with `data-p`), so their animation doesn't replay.
- **Persistence**: `state` (mode, level, scores, starting player) is saved to `localStorage` under the key `ttt-state`. Every read and write is wrapped in try/catch. Switching mode resets the scores, and starting a new round swaps who goes first.
