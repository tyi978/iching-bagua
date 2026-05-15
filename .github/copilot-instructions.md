# AI Coding Agent Instructions for I Ching Bagua

## Project Overview
Interactive web-based simulation of the **大衍筮法 (Dayanyi divination method)** from the I Ching. Single-file HTML application combining classical Chinese philosophy with modern web interactivity.

**Key Files:**
- [index.html](../index.html) - Complete application (embedded JS, CSS, and structured data)

## Architecture Pattern
This is a **single-file self-contained application** with clear internal structure:

### 1. Data Layer: Embedded JSON (lines 150-1200)
- `guaNames` array: 64 hexagram names (八卦 + combinations)
- `guaData` object: Comprehensive reference data for each hexagram
  - `guaCi`: Judgment/meaning text
  - `xiangZhuan`: Commentary on symbolism
  - `tuanZhuan`: Detailed interpretation
  - `yaoCi`: 6 individual line texts (爻辭)
  - `yaoXiang`: 6 commentary texts for each line

### 2. Application State (lines 110-130)
- `stalks` / `totalStalks`: Current bamboo stick count (starts at 49)
- `leftPile` / `rightPile`: Divided piles from splitting step
- `remainders`: Modulo results after grouping by 4
- `yaos`: Array storing 6 line values [9/8/7/6 for each line from bottom-up]
- `state`: FSM across ['divide', 'hang', 'count']

### 3. Core Algorithm: Dayanyi Process (lines 1395-1510)
**6 iterations to generate hexagram** (each iteration uses 49 stalks):
1. **State: divide** → Click to split stalks → `leftPile`, `rightPile`
2. **State: hang** → Remove 1 from right pile (represents 三 trinity)
3. **State: count** → Group by 4, collect remainders, reduce count
4. **Repeat 3 times** per line (to get one line value)
5. **Calculate line value**: Final `stalks / 4` yields {9, 8, 7, 6}
   - 9 = 老陽 (changing yang, marked with red circle)
   - 6 = 老陰 (changing yin, marked with red X)

### 4. Display & Interaction
- **Canvas rendering** (`drawStalks`): Visual representation of stick distribution
- **Collapsible sections**: I Ching texts toggle via click handlers
- **Line tracking**: `updateCurrentYaos()` displays results bottom-to-top (爻 ordering)

## Critical Developer Workflows

### Adding New Features
- **Modify guaData**: Add/update hexagram reference data
- **Change visualization**: Update `drawStalks()` color/layout logic
- **Adjust interaction**: Modify canvas click handler for splitting logic

### Testing Changes
- **Manual step-through**: Use UI buttons to validate state transitions
- **Verify calculations**: Check `remainders` array and final line values
- **Hexagram lookup**: Cross-reference generated `yaos` against expected gua names

## Project-Specific Conventions

### Line Numbering
- Lines stored in `yaos` array: **index 0 = bottom line (第1爻), index 5 = top line (第6爻)**
- Display iteration reverses: Loop from `yaos.length-1` down to `0`
- Chinese text displays "第N爻" with index inverted

### Line Value Encoding
- No separate "change" tracking; change lines encoded directly: 9 (old yang), 6 (old yin)
- Stable lines use 7 (young yang), 8 (young yin)
- Generate **2 hexagrams**: original (本卦) + transformed (變卦)
  - Transform: 9→8, 6→7 (old becomes young)

### Modulo Remainder Logic
```javascript
let rem = pile % 4 || 4;  // Use 4 if pile % 4 == 0 (never 0)
```
This is mathematically precise to the classical method.

### HTML Structure
- **No external scripts**: XLSX library commented but unused
- **Responsive styling**: CSS Grid for gua display, inline color coding
- **Traditional Chinese**: All UI and content in Traditional Chinese (zh-TW)

## Integration Points
- **No external APIs** — entirely self-contained
- **Browser-only execution** — no backend required
- **PWA manifest**: Configured for offline use and mobile

## Common Pitfalls
1. **Line array indexing**: Forget to reverse when displaying (yaos[0] = bottom)
2. **Remainder calculation**: Forgetting `|| 4` causes zero issues
3. **State transitions**: Canvas event only works in 'divide' state; skip steps causes hangs
4. **Collapsible toggle**: Must re-query DOM after rendering new content

## Debugging Tips
- Use browser console to inspect `yaos` array after each iteration
- Verify `getGuaIndex()` binary string matches expected 64-gua index
- Check `guaData` keys against displayed gua names
