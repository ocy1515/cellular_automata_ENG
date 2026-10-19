# Cellular Automata Lab

> An HTML tool for simulating an **ant** that moves according to custom rules based on cell states and the surrounding eight cells.

This is an extension of Langton's ant. Write one rule per line, and the ant will move across the grid and change cell colors according to those rules.

You can also find the syntax guide inside `index.html`.

**Table of Contents**

- [I. Syntax and Usage Guide](#i-syntax-and-usage-guide)
- [II. Copyright and License](#ii-copyright-and-license)

---

## I. Syntax and Usage Guide

### 1. Quick Start

Visit the [Cellular Automata Lab website](https://ocy1515.github.io/cellular_automata_KR/).

### 2. Rule Syntax

#### 2.1 Overall Structure

The rules are text commands, with one command per line. There are four types of lines:

| Type | Format | Purpose |
| --- | --- | --- |
| Color definition | `setcolor (symbol)=(#RRGGBB)` | Assigns a color to a state symbol |
| Background state | `setbg (symbol)` | Sets the initial state of every grid cell |
| Starting cell state | `setstart (symbol)` | Sets the state of the cell where the ant starts |
| Rule | `(condition) -> (command)` | Executes a command when the condition is met |

- Blank lines are ignored.
- Lines beginning with `#` or `//` are comments.
- Commands are case-insensitive (`a` and `A` represent the same state).

#### 2.2 State Symbols and Colors — `setcolor`

```text
setcolor B=#0000FF
```

- **State symbol:** One letter from `A`–`Z` or one digit from `0`–`9`. It identifies a cell's “color state.”
- **Color:** A hexadecimal color code in `#RRGGBB` format.
- You cannot define the same symbol with `setcolor` more than once.
- Symbols without a `setcolor` definition use the default colors below.

| Symbol | A | B | C | D | E | F |
| --- | --- | --- | --- | --- | --- | --- |
| Default color | `#F0F2F5` | `#252B38` | `#F2B84B` | `#8B7CF6` | `#E56C8A` | `#4AA8D8` |

| Symbol | G | H | I | J | K | L |
| --- | --- | --- | --- | --- | --- | --- |
| Default color | `#72C58B` | `#E58B50` | `#E5C7A9` | `#9ED7C7` | `#D7A8EF` | `#A8B8D8` |

Symbols after `M` repeat these 12 colors.

#### 2.3 Cell Position Numbers

Position numbers used in conditions are **fixed relative to the screen**. They do not change based on the direction the ant is facing.

```text
[2][3][4]
[5][1][6]
[8][7][9]
```

`1` is the cell occupied by the ant, `3` is the cell above it (north), and `6` is the cell to its right (east).

#### 2.4 Conditions

```text
ant_cell_(position)=(state)
```

Example: `ant_cell_1=A` means the cell occupied by the ant is in state A.

- Use `&` to require multiple conditions simultaneously.  
  Example: `ant_cell_1=A & ant_cell_3=B` means the current cell is A **and** the cell to the north is B.
- If multiple rules match, **only the first matching rule** is executed. Put specific conditions above general ones.
- You cannot define two rules with exactly the same conditions.
- Conditions are checked against the grid as it was at the beginning of the step. Colors changed by commands on the same line do not affect that step's condition checks.
- With the boundary mode set to “Stop at boundary,” cells outside the grid do not match any state. With “Wrap around,” the ant reads cells on the opposite edge.

#### 2.5 Commands

**Movement commands**

| Command | Action |
| --- | --- |
| `straight` | Move one cell in the current direction |
| `right` | Turn 90° right, then move one cell |
| `left` | Turn 90° left, then move one cell |
| `flip` | Turn 180°, then move one cell |

- Append `_n` to move `n` cells (for example, `straight_2` or `left_3`; `n` must be between 1 and 1000).
- The ant turns only once, then continues moving in that direction for all `n` cells.

**Painting commands**

| Command | Action |
| --- | --- |
| `paint (state)` | Change the cell currently occupied by the ant to the specified state |
| `paint_prev (state)` | Change the cell occupied by the ant **before the most recent movement command began** to the specified state |

If no movement command has occurred yet on the current rule line, `paint_prev` refers to the cell where the ant began moving during the previous step.

**Combining commands**

Use `;` to chain commands. They are executed **from left to right**.

```text
ant_cell_1=A -> right_2; paint_prev C
```

#### 2.6 Rule Format

```text
(condition) -> (command)
```

Example:

```text
ant_cell_1=A & ant_cell_3=B -> left_2; paint_prev C
```

#### 2.7 Multi-Cell Movement and When Rules Are Checked

- Rules are **not checked on cells the ant passes through** during a multi-cell movement.
- The next step's rules are checked at the **final destination** after the movement finishes.
- Therefore, cells passed over by `straight_5` do not trigger rules and their colors are not changed.

#### 2.8 Background State — `setbg`

```text
setbg B
```

- Sets the **initial state of every cell** in the grid.
- Can be used only once. If omitted, the default is `A`.
- Background cells behave just like any other cells. If the background is `A`, rules for `ant_cell_1=A` can run on those cells. If no rule matches the background state, an error occurs during execution.
- The background color is determined by `setcolor`. The legend labels it, for example, as `A (background)`.

#### 2.9 Starting Cell State — `setstart`

```text
setstart C
```

- Sets the state of the **single cell** where the ant starts.
- Can be used only once. If omitted, it defaults to the background state.

> [!NOTE]
> If the background state and starting-cell state differ, **the starting-cell setting takes precedence**.
> Example: `setbg D` + `setstart A` → the ant's cell is A, and all other cells are D.

### 3. Interface Settings

#### Rule Selection

- **Free Input:** Apply rules you write yourself. Click **Help** to open the syntax guide in a pop-up.
- **Examples:** Load a preset rule set. The selected rules are shown below and can be copied for editing with **Copy This Example to Free Input**.
- The active mode (Free Input or Examples) and the selected example button are highlighted.

#### Simulation Settings

| Setting | Description |
| --- | --- |
| Grid width / height | 20–300 cells. The ant starts at the center of the grid. |
| Speed (steps/second) | 1–10,000 |
| Boundary mode | **Stop at boundary** (default): the ant stops if it tries to cross an edge. **Wrap around**: the ant continues from the opposite edge. |
| Starting direction | North / East / South / West |
| Page background color | Changes the page background. The choice is saved in the browser and does not affect cell colors in the grid. |

Changing a setting resets the simulation.

#### Control Buttons

- **Step:** Execute one rule.
- **100 Steps:** Execute 100 rules.
- **Reset:** Restart from the beginning using the current rules and settings.
- **▶ Run / Ⅱ Pause:** Start or pause continuous execution.

#### Displayed Information

The step count, ant position and direction, and number of states used are displayed above the simulation. The legend below shows each state's color and the ant marker (mint).

### 4. Examples

| Example | Description |
| --- | --- |
| Langton's Ant | The best-known ant. A diagonal highway emerges after roughly 10,000 steps. |
| RLR Ant | A two-color ant with reversed turning directions |
| Neighbor Detection | A three-color rule set that also checks the cell to the north |
| Five-Color Example | The example from the guide. It moves straight and stops at the boundary, so try switching the boundary mode to “Wrap around.” |
| Nine-Color Triangle Growth | Spreads chaotically at first, then grows into a large triangle. Automatically sets the grid to 200×200 and speed to 3000. |

#### Try It Yourself: An Ant That Turns Based on Its State

You can easily create a multi-color ant by assigning a `left` or `right` turn to each state and then changing to the next state.

```text
setcolor A=#0B0F1A
setcolor B=#1E3A8A
setcolor C=#2563EB

ant_cell_1=A -> left; paint_prev B
ant_cell_1=B -> right; paint_prev C
ant_cell_1=C -> right; paint_prev A
```

### 5. Common Errors

| Message | Cause and solution |
| --- | --- |
| No matching rule was found | No rule matches the state of the cell occupied by the ant or the combination of conditions. Check that you have a rule for the background state (`setbg`, default `A`). |
| The ant stopped at the grid boundary | Boundary mode is set to “Stop at boundary.” Increase the grid size or switch to “Wrap around.” |
| Add `->` between the condition and the action | The rule does not follow `(condition) -> (command)`. |
| Invalid condition format: '…' | Position numbers must be 1–9 and states must be A–Z or 0–9. Example: `ant_cell_1=A`. |
| Unknown command: '…' | Check the spelling and format (`straight` / `left` / `right` / `flip` / `paint` / `paint_prev`). |
| Movement distance must be 1–1000 | Check the range of `n` in `_n`. |
| Duplicate rule conditions | Two rules have the same conditions. Remove one of them. |
| Duplicate color definition / `setbg` / `setstart` | The same command was used more than once. |
| Invalid `setbg` / `setstart` format | The symbol must be a single character from A–Z or 0–9. Example: `setbg A`. |

### 6. Tips

- Rules are checked from top to bottom, so put rules with more `&` conditions above simpler rules.
- Each state should have at least one rule if you want the ant to keep moving. If you introduce a state with `paint`, add a rule for that state too.
- Rules that only move straight can make the ant reach the boundary quickly. Starting with “Wrap around” can make it easier to observe the behavior.
- If you are unsure how a rule works, use **Step** to execute one step at a time.

---

## II. Copyright and License

### cellular_automata_KR — License Terms

> These terms are also included as a comment at the bottom of the `index.html` source file.

- **Copyright:** Copyright © 2026 ocy1515 (Oh Chanyeong)
- **Project name:** cellular_automata_KR
- **Original source:** https://ocy1515.github.io/cellular_automata_KR/
- **License:** Cellular Automata Lab Non-Commercial License v1.0

### Article 1. Purpose and Scope

These license terms define the conditions for using the source code of `cellular_automata_KR` and the creative components for which the copyright holder has the right to grant permission.

These terms apply only to the extent of the rights held by the copyright holder. Third-party works, external libraries, and materials governed by separate licenses may be subject to their own rights and license terms.

### Article 2. Permitted Use

Users may use the work for non-commercial purposes if they comply with the following conditions:

1. The program's source code may be copied and redistributed.
2. The program's source code may be modified, and modified versions may be redistributed.
3. The program may be used for personal learning, education, research, and non-commercial projects.
4. The original program may be provided free of charge on non-commercial websites.

### Article 3. Attribution

When distributing the original or a modified version, clearly indicate the following:

1. Original program name: `cellular_automata_KR`
2. Original author: ocy1515 (Oh Chanyeong)
3. Original source: https://ocy1515.github.io/cellular_automata_KR/
4. The name of these license terms and a link to them or their full text
5. If modified, the fact that it has been modified and a summary of the major changes

Do not present a modified version as the original program or imply that the original author officially approved or endorsed it.

### Article 4. Distribution of Modified Versions

When distributing a modified version, clearly identify the changes and include these license terms or other terms separately approved by the copyright holder.

If a modified version includes separate independent works, their rights and applicable license terms must be checked separately.

### Article 5. Restriction on Commercial Use

Commercial use is not permitted under this license.

Commercial use may include sales for profit, paid access, integration into commercial services, and other uses whose primary purpose is to obtain commercial benefits or monetary compensation.

Anyone wishing to use the work commercially must obtain separate permission from the copyright holder in advance. If it is unclear whether a use is commercial, contact the copyright holder before using the work.

### Article 6. Rights and Responsibilities

This license does not grant rights beyond those held by the copyright holder.

The program is provided without express warranties. This clause does not exclude warranties or liabilities that cannot be excluded under applicable law.

Users must comply with applicable laws and respect third-party rights when using the program or distributing modified versions.

### Article 7. Violations of the License

If a user violates these terms, the copyright holder may request that the use be stopped or seek remedies for infringement in accordance with applicable law.

### Article 8. Use of AI Tools

AI tools were used to assist with the development and code improvement of this project.

The project may include code and explanations suggested by AI tools. This notice does not claim that the copyright holder exclusively owns every AI-generated part. The rights associated with each part depend on actual creative contributions, applicable law, and third-party licenses.

### Article 9. Interpretation

These terms are interpreted under the laws of the Republic of Korea. Any specific dispute will be resolved in accordance with applicable law.
