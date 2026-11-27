# Interface conventions shared by the games

Every game page follows these rules, so that a player who knows one game finds the same things at the same places
in the others. When a game needs something not covered here, keep to the spirit and add the rule here.

## Page layout (top to bottom, left to right)

1. **Header**: the site links (`nav.crumbs`: "&larr; Martin Cousineau &middot; All games"), the title (`h1`), and one
   short paragraph saying what the player does and what the score is. No citation block in the header.
2. **Left panel (the game)**:
   1. Toolbar row: instance choice first (level / seed / driver / day selectors, "New seed" or "Next day"), then
      "Restart", then "Undo" if the game has one. Nothing else in this row.
   2. The board (canvas).
   3. The status line (`#msg`, `role="status"`): one sentence on what to do now, or the result at the end.
   4. The **replay bar** (hidden until replays are opened; see below). It is always right under the status line.
   5. Game-specific extras that belong to the board (closed sheets, minimap legend, price chart...).
3. **Right panel (scores and actions)**:
   1. `label` "Your game", then a `stats` grid (label left, value right in bold accent, tabular numbers). The
      objective comes first, with "(higher is better)" or "(lower is better)" in its label.
   2. `label` "References on this instance", then a `stats` grid with one row per reference (optimum, heuristics,
      bounds), in the same unit as the objective. Each row starts with a small colour swatch (`.sw`), the colour
      the reference has in overlays and replays.
   3. **Actions row**, always these two buttons in this order:
      - **"Watch replays"** (key `W`), `id="watch"`: opens the replay bar.
      - **"Show references"** (key `O`), `id="refs"`: draws the references over the board (routes, paths, load
        profiles, the optimal packing next to yours); the label switches to "Hide references".
   4. The touch pad (on-screen buttons for phones).
   5. `help`: "Keys" first (one paragraph, game keys then replay keys), then how the board reads, then what the
      references are (one sentence each).
   6. For games from a paper: a collapsed `<details class="paper">` "Paper's reported averages (paper instances,
      not this one)".
4. **Footer**: the full citation of the paper the game comes from, the credit line when the page is an
   independent version, and where the assumptions are listed (README.md).

## Names

- "Watch replays" for the button that opens the replay bar (not "Replay": in games, "replay" also means "play
  again").
- "Show references" / "Hide references" for the overlay of reference solutions.
- "Restart", "Undo", "New seed", "Next day": as is.
- Inside the replay bar, the selector reads "Watch: <name>", with "Your game" first, then the references in the
  order of the stats grid.

## Replay bar

One row of controls, then the scrubber, then one line of running values:

    [Watch: Your game v]  [⇤ Start] [← Back] [▶ Play] [Forward →] [End ⇥]  [Speed v]  [✕ Close]
    [=========o------------------------------------------------]  (scrubber, range input)
    <swatch> Step 31 of 200 · <objective so far> · <other running values>

- Button labels are exactly: "⇤ Start", "← Back", "▶ Play" (switches to "❚❚ Pause" while playing), "Forward →",
  "End ⇥", "✕ Close". Use these characters: U+21E4, U+2190, U+25B6, U+275A U+275A, U+2192, U+21E5, U+2715. The step
  buttons never use a triangle, so they cannot be mistaken for Play. `title` and `aria-label` give the key.
- What a step is depends on the game (a block, a move, an EV, a 15-minute step); the running line names it.
- The final running values equal the values in the stats grids. The replay is driven by re-stepping the recorded
  actions through the engine.
- While the replay bar is open, the board shows the replay; closing it restores the game as it was.

## Keys

Game keys stay as they are, except that `W` and `O` are reserved everywhere:

| Key | Everywhere |
| --- | --- |
| `W` | Watch replays: open or close the replay bar |
| `O` | Show or hide references |

While the replay bar is open, keys act on the replay only:

| Key | In the replay |
| --- | --- |
| `Space` or `K` | Play / pause |
| `←` `→` | One step back / forward (with `Shift`: 10 steps) |
| `Home` `End` | Start / end |
| `↑` `↓` | Previous / next item in "Watch:" |
| `-` `+` | Slower / faster |
| `Esc` or `W` | Close |

A focused link followed by `Enter` is never captured by the game.

## Spoilers

None: "Watch replays" and "Show references" work at any time, without confirmation, also during a game (decided
by the owner for class use). They do not end the game; closing the replay bar or hiding the references returns to
the game as it was.

## Colours

- Background white (dark theme supported), text `#111827`, muted `#5f6b7a`, accent (HEC navy) `#003DA5`.
- The player's own solution: accent navy. References, in this order of use: optimum or perfect-information
  bound `#0072B2`, first heuristic `#E69F00`, second `#CC79A7`, third `#009E73`, fourth `#56B4E9`, fifth `#D55E00` (Okabe-Ito,
  colour-blind safe). The same reference has the same colour in the stats swatch, the overlay and the replay.
- What the player already placed is drawn in one neutral colour; only the last placed item is highlighted (accent),
  except where colour carries information: in the packing game, colour encodes the block type (one colour per
  width x height, identical blocks share it), and the last placed block is outlined instead.

## Checks

Each game has a stub-DOM test in node (`test/ui_*.js`) that checks the ids above exist (`watch`, `refs`, `msg`,
the replay bar's buttons), the key bindings `W` and `O`, the button labels, and that each replay ends on the values
of the stats grids.
