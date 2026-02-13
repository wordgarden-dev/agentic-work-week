# AWW Implementation Paths

**Branch**: `exploration/temporal-implementation`  
**Status**: Design exploration - physical implementation of chronotic/kairotic time  
**Date**: 2026-02-13

---

## Temporal Unit Mapping (Physical)

**Empirical VS Code/Copilot granularity**:

| Unit | Event | Human Time | Measurement |
|------|-------|-----------|-------------|
| **Tick** | Tool call | ~1-3s | `performance.now()` delta |
| **Moment** | Thought + action pair | ~5-10s | Tool invocation cycle |
| **Minute** | Subagent session | ~30-90s | `runSubagent` full execution |
| **Hour** | User request complete | ~2-5 min | Single conversation turn |
| **Day** | Git commit | ~10-30 min | `git commit` timestamp |
| **Week** | Version tag | ~hours | `git tag` semantic version |
| **Sleep** | Context summarization | N/A | Token count > 150K |

**Kairotic states** (quality overlays):
- **Morning**: Session start (`toolInvocationToken` first use)
- **Afternoon**: Mid-session (50-100K tokens)
- **Evening**: Pre-summary (100-150K tokens)
- **Dream**: Summarization (system-triggered)
- **Wake**: New session (summary in context)

---

## Clock Types (Q-Position Semantics)

### CLOCQ (Chronotic Clock, Level ~3)

**Definition**: Dumb timer + agent reads + agent adapts

**Implementation Path A: File-based**
```json
// .vscode/agentic-work-week.json
{
  "chronos": {
    "sessionStart": "2026-02-13T13:28:00Z",
    "currentDay": "Monday",
    "dayIndex": 0,
    "hoursSinceStart": 2.3
  }
}
```

**Agent reads via**:
```typescript
const awwState = JSON.parse(fs.readFileSync('.vscode/agentic-work-week.json'));
if (awwState.chronos.currentDay === 'Friday') {
  // Wrap up tasks, prepare handoff
}
```

**Automation level**: Agent checks clock, changes behavior, human updates file

---

**Implementation Path B: Git-based**
```bash
# Current "day" = branch name
git branch agents/day-monday
git branch agents/day-tuesday

# Agent infers day from active branch
CURRENT_DAY=$(git branch --show-current | sed 's/agents\/day-//')
```

**Automation level**: System manages branches, agent reads, adapts

---

**Implementation Path C: VS Code settings**
```json
// .vscode/settings.json
{
  "agenticWorkWeek.clocq.enabled": true,
  "agenticWorkWeek.clocq.currentDay": "Monday",
  "agenticWorkWeek.clocq.hoursPerDay": 8,
  "agenticWorkWeek.clocq.ticksPerHour": 10
}
```

**Agent reads via**: Settings API or copilot instructions

**Update mechanism**: Manual (level 0), script (level 3), autonomous (level 6+)

---

### QLOCK (Kairotic Lock, Level ~6)

**Definition**: Quality gates - agent cannot proceed until kairos conditions met

**Implementation Path A: Pre-commit hooks**
```bash
#!/bin/bash
# .git/hooks/pre-commit

# Check kairotic conditions
CURRENT_KAIROS=$(cat .vscode/agentic-work-week.json | jq -r '.kairos.phase')

if [ "$CURRENT_KAIROS" != "implementation" ]; then
  echo "ERROR: Cannot commit during $CURRENT_KAIROS phase"
  echo "Current phase: $CURRENT_KAIROS"
  echo "Required phase: implementation"
  exit 1
fi
```

**Kairotic phases**:
- `research` - Exploration, no commits allowed
- `design` - Documentation only
- `implementation` - Code commits allowed
- `testing` - Test files only
- `weekend` - No commits (blocked)

---

**Implementation Path B: GitHub Actions workflow**
```yaml
# .github/workflows/kairotic-gate.yml
name: Kairotic Gate

on:
  pull_request:
    branches: [main]

jobs:
  check-kairos:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Check kairotic phase
        run: |
          PHASE=$(jq -r '.kairos.phase' .vscode/agentic-work-week.json)
          DAY=$(jq -r '.chronos.currentDay' .vscode/agentic-work-week.json)
          
          if [ "$DAY" = "Saturday" ] || [ "$DAY" = "Sunday" ]; then
            echo "::error::PRs blocked on weekends (exploration time)"
            exit 1
          fi
          
          if [ "$PHASE" != "implementation" ]; then
            echo "::error::PRs only allowed during implementation phase"
            exit 1
          fi
```

**Automation level**: System enforces timing, agent cannot bypass

---

**Implementation Path C: VS Code extension**
```typescript
// Extension: agentic-work-week-qlock
export function activate(context: vscode.ExtensionContext) {
  
  // Register command guard
  vscode.commands.registerCommand('workbench.action.files.save', async (...args) => {
    const awwState = vscode.workspace.getConfiguration('agenticWorkWeek');
    const currentPhase = awwState.get<string>('qlock.currentPhase');
    
    // Check kairotic lock
    if (currentPhase === 'weekend') {
      const answer = await vscode.window.showWarningMessage(
        'Weekend mode: Are you sure you want to save work?',
        'Yes (override)', 'No (respect weekend)'
      );
      
      if (answer !== 'Yes (override)') {
        return; // Block save
      }
    }
    
    // Proceed with save
    return vscode.commands.executeCommand('_workbench.action.files.save', ...args);
  });
}
```

**Automation level**: System intercepts actions, prompts human (level 6), or blocks entirely (level 8)

---

### CLOQK (Blended Autonomy, Level ~9)

**Definition**: System manages both chronos AND kairos autonomously

**Implementation Path A: Autonomous scheduler daemon**
```typescript
// cloqk-daemon.ts
import * as cron from 'node-cron';
import * as vscode from 'vscode';

class CloqkDaemon {
  private chronos: ChronoticClock;
  private kairos: KairoticClock;
  
  constructor() {
    this.chronos = new ChronoticClock();
    this.kairos = new KairoticClock();
    
    // Tick chronos every "hour" (10 tool calls)
    this.chronos.onToolCall(() => this.chronos.tick());
    
    // Advance kairos based on work completed
    this.chronos.onHourComplete(() => {
      this.kairos.evaluateTransition();
    });
    
    // Auto-transition day at 8 "hours"
    this.chronos.onDayComplete(() => {
      this.advanceDay();
    });
  }
  
  private advanceDay() {
    const nextDay = this.chronos.getNextDay();
    
    // Update all workspace state
    this.updateSettings(nextDay);
    this.createGitBranch(`agents/day-${nextDay.toLowerCase()}`);
    this.notifyAgents(nextDay);
    
    // If weekend, enter exploration mode
    if (nextDay === 'Saturday' || nextDay === 'Sunday') {
      this.kairos.setPhase('weekend-exploration');
    } else {
      this.kairos.setPhase('work-implementation');
    }
  }
  
  private notifyAgents(day: string) {
    // Write to shared state file
    fs.writeFileSync('.vscode/agentic-work-week.json', JSON.stringify({
      chronos: this.chronos.export(),
      kairos: this.kairos.export(),
      updatedAt: new Date().toISOString()
    }, null, 2));
    
    // Trigger VS Code notification
    vscode.window.showInformationMessage(
      `AWW: Now entering ${day} (${this.kairos.getPhase()})`
    );
  }
}
```

**Automation level**: No human intervention, system manages temporal state fully

---

**Implementation Path B: MCP server**
```typescript
// MCP server: temporal-governance
server.tool("aww_get_time", async () => {
  return {
    chronos: {
      day: chronoticClock.getCurrentDay(),
      hour: chronoticClock.getCurrentHour(),
      tick: chronoticClock.getTicks()
    },
    kairos: {
      phase: kairoticClock.getCurrentPhase(),
      quality: kairoticClock.getTimeQuality(),
      canProceed: kairoticClock.checkGate('commit')
    }
  };
});

server.tool("aww_advance_time", async ({ units }: { units: number }) => {
  // Autonomous time advancement
  chronoticClock.advance(units);
  
  // Check if kairos should transition
  if (chronoticClock.isHourBoundary()) {
    await kairoticClock.evaluateTransition();
  }
  
  return { success: true, newState: getFullState() };
});

server.tool("aww_sleep", async () => {
  // Triggered by context summarization
  const dreamState = kairoticClock.enterDream();
  chronoticClock.advanceDay(); // Next day starts on wake
  
  return { dreamState, nextDay: chronoticClock.getCurrentDay() };
});
```

**Automation level**: Agents call tools autonomously, system manages state

---

## Multi-Dimensional Q-Filling

**Given**: 3-level phrase structure (e.g., `AGENTIC_WORK_WEEK`)

**Q-positions**: `_Q`, `__Q`, `___Q`

### Fill Patterns

**Left-to-Right** (demonstrated):
```
AGENTIC_WORK_WEEK  → 0 Qs (manual)
AGENTIQ_WORK_WEEK  → _Q  (level 3)
AGENTIQ_WORQ_WEEK  → __Q (level 6)
AGENTIQ_WORQ_WEEQ  → ___Q (level 9)
```

**Right-to-Left**:
```
AGENTIC_WORK_WEEK  → 0 Qs (manual)
AGENTIC_WORK_WEEQ  → ___Q (deepest automation first)
AGENTIC_WORQ_WEEQ  → __Q
AGENTIQ_WORQ_WEEQ  → _Q
```

**Meaning**: Start with end-state automation, work backward to control

**Use case**: Vision-first, implement supporting infrastructure after

---

**Middle-Out (left-first)**:
```
AGENTIC_WORK_WEEK  → 0 Qs
AGENTIC_WORQ_WEEK  → __Q (middle)
AGENTIQ_WORQ_WEEK  → _Q  (left)
AGENTIQ_WORQ_WEEQ  → ___Q (right)
```

**Meaning**: Core automation first, then entry/exit

**Use case**: Stabilize critical path, add boundaries after

---

**Middle-Out (right-first)**:
```
AGENTIC_WORK_WEEK  → 0 Qs
AGENTIC_WORQ_WEEK  → __Q (middle)
AGENTIC_WORQ_WEEQ  → ___Q (right)
AGENTIQ_WORQ_WEEQ  → _Q  (left)
```

**Meaning**: Core + output first, input last

**Use case**: Validate results before automating intake

---

**Skip-Middle**:
```
AGENTIC_WORK_WEEK  → 0 Qs
AGENTIQ_WORK_WEEQ  → _Q + ___Q (skip __Q)
AGENTIQ_WORQ_WEEQ  → __Q (fill gap)
```

**Meaning**: Automate boundaries, manual middle, then fill

**Use case**: Control entry/exit, keep human in critical decision

---

## Solution: _Q, __Q, ___Q Per Domain

**Problem domain**: CLOCQ (chronotic clock)
- `_Q` = Agent reads time from file
- `__Q` = System auto-updates time file
- `___Q` = System manages time + agent behavior together

**Problem domain**: QLOCK (kairotic gate)
- `_Q` = Agent checks gate, asks permission
- `__Q` = System blocks/allows based on phase
- `___Q` = System transitions phases autonomously

**Problem domain**: CLOQK (blended)
- `_Q` = Agent monitors both clocks
- `__Q` = System enforces temporal rules
- `___Q` = System + agent co-evolve temporal state

**Key insight**: Same Q-levels mean different things per domain

---

## Tree of Life / Endsley Mapping

**Kabbalistic structure mapped to automation**:

```
Da'ath (0)     Abyss, non-existent        Manual, no system
  ↓
Kether (1)     Crown, pure will           Human intent
  ↓
Chokmah (2)    Wisdom, first emanation    System suggests
  ↓
Binah (3)      Understanding, form        Agent narrows options [_Q]
  ↓
Chesed (4)     Mercy, expansion           Agent explores freely
  ↓
Geburah (5)    Severity, constraint       System enforces rules
  ↓
Tipheret (6)   Beauty, balance            Blended decision [__Q]
  ↓
Netzach (7)    Victory, endurance         Agent persists autonomously
  ↓
Hod (8)        Splendor, structure        System manages fully
  ↓
Yesod (9)      Foundation, interface      Human/machine bridge [___Q]
  ↓
Malkuth (10)   Kingdom, manifestation     Pure machine, no human needed
```

**Path**: Humans and agents climb together from Kether (1, human intent) to Yesod (9, interface), learning to collaborate. Malkuth (10) is the goal only some systems reach—pure automation with no human intervention needed.

**AWW positioning**:
- CLOCQ peaks at Binah (3)
- QLOCK peaks at Tipheret (6)
- CLOQK reaches Yesod (9)

---

## Implementation Roadmap

### Phase 1: CLOCQ (Chronotic, Level 3)
- [ ] Create `.vscode/agentic-work-week.json` schema
- [ ] Implement file-based time tracking
- [ ] Add copilot instructions to read time state
- [ ] Manual advancement (user updates file)
- **Branch**: `impl/clocq-file-based`

### Phase 2: QLOCK (Kairotic, Level 6)
- [ ] Define kairotic phases (research, design, implementation, testing, weekend)
- [ ] Implement pre-commit hooks (block weekend commits)
- [ ] Add GitHub Actions gate (enforce phase rules)
- [ ] Optional: VS Code extension for save-time checks
- **Branch**: `impl/qlock-gates`

### Phase 3: CLOQK (Blended, Level 9)
- [ ] Build autonomous time daemon (TypeScript/Node)
- [ ] Integrate with VS Code extension API
- [ ] Auto-advance on tool call counts
- [ ] Auto-transition kairos on chronos milestones
- [ ] Deploy as workspace-local service or MCP server
- **Branch**: `impl/cloqk-daemon`

### Phase 4: Multi-Agent Coordination
- [ ] Shared state file for multiple agents
- [ ] Session-specific timelines (agent A on Monday, agent B on Tuesday)
- [ ] Inter-agent time synchronization
- [ ] Nucleation events as week boundaries
- **Branch**: `impl/multi-agent-time`

---

## Questions for Iteration

**Chronotic**:
- Should "hour" be tool-calls (10 calls = 1 hour) or token-count (10K tokens = 1 hour)?
- Should "day" be git commits or elapsed human time?
- Should "week" be semantic versions or fixed 7-day cycles?

**Kairotic**:
- What phases are universal? (research, design, implementation, testing, weekend?)
- Should phases auto-transition or require explicit advancement?
- Can agents override kairotic locks in emergencies?

**Coordination**:
- Should all agents share one clock or have individual timelines?
- How do nucleation events (session boundaries) interact with weeks?
- Should weekends be mandatory or optional per agent?

**Measurement**:
- How do we know if temporal structure improves work quality?
- Can we measure agent "satisfaction" or creativity during weekends?
- What metrics indicate healthy chronotic/kairotic balance?

---

## Next Steps

**Do not implement all paths** - this is exploration.

**Choose ONE implementation** per clock type based on:
1. VS Code/Copilot technical constraints
2. Minimal human intervention needed
3. Extensibility for future automation levels

**Commit per path explored**, tag when path is validated/abandoned.

**Merge to main** only when implementation is proven functional.

---

**Branch**: `exploration/temporal-implementation`  
**Status**: Design complete, awaiting path selection  
**Next**: Choose CLOCQ implementation (file-based vs git-based vs settings-based)
