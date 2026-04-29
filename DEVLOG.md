# Developer Log (DEVLOG.md)

## Maze Escape Using DFS (Spring 2026)

---

### Entry 1
**Date:** 2026-04-15  
**Entry Type:** Engineering Decision  
**Task worked on:** Planning DFS rules (visited + parent tracking + stopping conditions)  
**Issue or decision:** Before coding, I needed a clear set of rules for how DFS should behave so I wouldn’t accidentally break path reconstruction or cause infinite recursion. The main decisions were: when to mark a cell visited, when to set parent pointers, and what “success” means.  
**Error message / symptom (if applicable):** N/A  
**What I tried:** I read the assignment notes and traced a small example maze on paper to see how recursion should expand from S. I also reviewed how `printPath()` works (it walks backwards from E using parent arrays), which helped me understand why parents must be assigned before recursing into a neighbor.  
**Fix / resolution (or final decision):** I decided to treat a move as valid only if the cell is in bounds, open (`0`), and not visited. I planned to mark `visited[r][c] = true` as soon as DFS enters a cell to avoid revisiting it. I also planned to set `parent_r` / `parent_c` for a neighbor right before recursing into it so that once the exit is found the path can be reconstructed correctly.  
**Commit(s):** `fork repo and add DEVLOG template`

---

### Entry 2
**Date:** 2026-04-17  
**Entry Type:** Bug Fix Entry  
**Task worked on:** DFS function stub + base checks  
**Issue or decision:** DFS needs to avoid invalid moves like going out of bounds, stepping into walls, or revisiting the same cell forever.  
**Error message / symptom (if applicable):** Before adding base checks a recursive DFS can easily blow up (infinite recursion) or crash due to invalid indices.  
**What I tried:** I wrote the DFS signature to match the starter variables (`maze`, `visited`, `parent_r`, `parent_c`, exit coords). Then I added the early checks one by one.  
**Fix / resolution (or final decision):** I added three base checks in DFS:
- out of bounds → return false
- wall (`maze[r][c] == 1`) → return false
- already visited → return false


  This made the DFS safe to expand in later commits.  
  **Commit(s):** `add dfs base checks (bounds/walls/visited)`

---

### Entry 3
**Date:** 2026-04-18  
**Entry Type:** Edge Case / Testing Entry  
**Task worked on:** Marking visited + exit base case  
**Issue or decision:** DFS needs to mark visited correctly and stop as soon as it reaches the exit. The tricky part was *when* to mark visited — if it happens too late, DFS can bounce between the same open cells and feel like it’s “stuck.” Also, without an exit base case, DFS will keep exploring even after it reaches `E`.  
**Error message / symptom (if applicable):** When I tested a small maze (like `5x5`), the program would sometimes take a very long time or appear to hang. In a couple runs, it eventually caused a crash/stack overflow behavior because DFS kept re-entering the same open cells through different directions. I also noticed cases where the exit existed but DFS still explored extra branches because it didn’t stop immediately at `E`.  
**What I tried:** I added temporary debug prints inside DFS like `cout << "Visiting (r,c)" << endl;` and saw the same coordinates repeating. I also tested multiple random mazes with small dimensions (`4 4`, `5 5`) to reproduce the issue faster. Then I experimented with marking `visited` at different points (before vs after exploring neighbors) and added a direct exit check.  
**Fix / resolution (or final decision):** I fixed it by marking `visited[r][c] = true` immediately after passing bounds/wall/visited checks (as soon as DFS “enters” the cell). That prevents DFS from revisiting the same cell from another direction. I also added the exit base case `if (r == exit_r && c == exit_c) return true;` so recursion stops as soon as the exit is reached and returns true all the way back to the original call.  
**Commit(s):** `mark visited and add exit base case`

---

### Entry 4
**Date:** 2026-04-20  
**Entry Type:** Bug Fix / Edge Case / Testing Entry  
**Task worked on:** Recursive neighbor exploration using `dr` / `dc`  
**Issue or decision:** DFS needed to explore the maze recursively in exactly four directions (no diagonals) and return `true` as soon as the exit is found. The main issue I ran into was that my DFS sometimes returned `false` even when a path existed, because I wasn’t handling the recursive return values correctly.  
**Error message / symptom (if applicable):** On some random mazes, the program would print a maze that clearly had an open route between S and E, but the DFS result still came back “No path exists.” Also, in a few tests, DFS explored many cells but never “kept” the successful path because it continued searching even after finding E.  
**What I tried:** I added temporary debug prints like `cout << "Trying neighbor..."` and confirmed DFS was visiting cells near the exit but still ending with `false`. I realized I was recursing into neighbors but not immediately returning `true` when a recursive call succeeded. I tested repeatedly with small mazes (`4 4`, `5 5`) so it was easier to spot the mistake and run multiple times quickly.  
**Fix / resolution (or final decision):** I fixed the recursive logic by looping through the 4 directions using the provided `dr/dc` arrays, and adding the “bubble up” rule: if any recursive call returns `true`, then the current call must immediately return `true` as well. If none of the neighbors work, then return `false`. After this change, DFS correctly identifies a path whenever one exists.  
**Commit(s):** `dfs explore 4 directions using dr/dc`

---

### Entry 5
**Date:** 2026-04-21  
**Entry Type:** Bug Fix / Edge Case / Testing Entry  
**Task worked on:** Parent tracking for path reconstruction  
**Issue or decision:** The provided `printPath()` reconstructs the path using `parent_r` and `parent_c`, so DFS must fill those arrays correctly before recursing.  
**Error message / symptom (if applicable):** DFS could find the exit but `printPath()` would not be able to trace backward properly if parent values were missing or wrong.  
**What I tried:** Before recursing into a neighbor, I set:
- `parent_r[nr][nc] = r`
- `parent_c[nr][nc] = c`  


  and made sure I only did it when the neighbor was open and unvisited.  
  **Fix / resolution (or final decision):** Setting parent coordinates before recursion allowed `printPath()` to print a valid path from S to E when DFS returned true.  
  **Commit(s):** `record parent coordinates before recursion`

---

### Entry 6
**Date:** 2026-04-23  
**Entry Type:** Edge Case / Testing Entry  
**Task worked on:** Calling DFS in main + printing path/no-path  
**Issue or decision:** The assignment requires the DFS call to be made from `main()`, and the program must print the path if found or a “No path exists.” message otherwise.  
**Error message / symptom (if applicable):** Before wiring it into main, the program only printed the maze and ended.  
**What I tried:** I uncommented the DFS call in main and stored the result in `found`. Then I used an if/else:
- if found → call `printPath()`
- else → print `No path exists.`


  **Fix / resolution (or final decision):** The program now fully demonstrates DFS traversal and path reconstruction when a path exists, and prints the required message when it doesn’t.  
  **Commit(s):** `call dfs from main and print path or no-path`