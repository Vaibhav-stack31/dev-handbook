# Graphify Setup

## Windows (PowerShell)

PowerShell doesn't chain with `&&` reliably on older versions — use `;` instead:

```powershell
pip install graphifyy
```

If `graphify` isn't recognized after install, add this to PATH, then reopen terminal:
```cmd
C:\Users\<username>\AppData\Roaming\Python\Python3xx\Scripts
```

**First-time setup (run from project root):**
```powershell
graphify extract . --code-only ; graphify claude install ; graphify cluster-only . --graph graphify-out/graph.json ; graphify tree . --graph graphify-out/graph.json ; start graphify-out\GRAPH_TREE.html
```

**Refresh after pulling changes:**
```powershell
graphify update . ; graphify cluster-only . --graph graphify-out/graph.json ; graphify tree . --graph graphify-out/graph.json ; start graphify-out\GRAPH_TREE.html
```

---

## Windows (Command Prompt)

CMD does support `&&`, so this works as originally written:

```cmd
pip install graphifyy
```

**First-time setup:**
```cmd
graphify extract . --code-only && graphify claude install && graphify cluster-only . --graph graphify-out/graph.json && graphify tree . --graph graphify-out/graph.json && start graphify-out\GRAPH_TREE.html
```

**Refresh after pulling changes:**
```cmd
graphify update . && graphify cluster-only . --graph graphify-out/graph.json && graphify tree . --graph graphify-out/graph.json && start graphify-out\GRAPH_TREE.html
```

---

## Linux

```bash
sudo apt install pipx
pipx ensurepath
# reopen terminal
pipx install graphifyy
```

**First-time setup (run from project root):**
```bash
graphify extract . --code-only && graphify claude install && graphify cluster-only . --graph graphify-out/graph.json && graphify tree . --graph graphify-out/graph.json && xdg-open graphify-out/GRAPH_TREE.html
```

**Refresh after pulling changes:**
```bash
graphify update . && graphify cluster-only . --graph graphify-out/graph.json && graphify tree . --graph graphify-out/graph.json && xdg-open graphify-out/GRAPH_TREE.html
```

---

## Notes
- `--code-only` skips docs/images/PDFs — no LLM API key required.
- `extract` = full rebuild (first time). `update` = lighter re-extract for refreshing after pulls, no LLM needed.
- Run from the **project root** — if root has no `.git` (multi-repo folder), skip `graphify hook install`; refresh manually after pulls instead.
- Output: `graphify-out/GRAPH_REPORT.md` (summary), `graphify-out/GRAPH_TREE.html` (visual), `graphify-out/graph.json` (raw graph, used by Claude Code automatically once `claude install` has run).
