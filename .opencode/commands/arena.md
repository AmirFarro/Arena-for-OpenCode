You are running the Arena tournament.

Load the arena skill.

Interpret arguments:

/arena
→ 16 agents

/arena --quick
→ 8 agents

/arena --hard
→ 32 agents

/arena --extreme
→ 64 agents

/arena --agents N
→ N agents

/arena --seed N
→ deterministic strategy selection

---
description: Run a multi-agent competitive Arena tournament using OpenCode subagents.
---

# OpenCode Arena Command

Run the Arena tournament using the Arena skill and OpenCode's native subagent system.

## Tournament Workflow

Follow these steps in order:

1. Capture the user's task exactly as provided.
2. Load the Arena skill from `.opencode/skills/arena/SKILL.md`.
3. Create a unique Arena run ID.
4. Create the tournament state under `.arena/<run-id>/`.
5. Determine the number of competitors:
   - `/arena` = 16
   - `/arena --quick` = 8
   - `/arena --hard` = 32
   - `/arena --extreme` = 64
   - `/arena --agents N` = N
6. Generate a unique strategy card for every competitor.
7. Spawn the competitors using the `arena-competitor` subagent.
8. Give every competitor the exact same task and relevant project context.
9. Collect and store all competitor solutions.
10. Pair competitors for the current tournament round.
11. Spawn `arena-attacker` subagents to attack each competing solution.
12. Give each competitor the attacks against its solution and request a defense/revision.
13. Spawn `arena-judge` subagents to independently judge each revised pair.
14. Eliminate the losing competitor from every match.
15. Advance the winners to the next tournament round.
16. Repeat the attack → defense → judge → elimination process until only one competitor remains.
17. Run a final independent review of the winning solution.
18. Store the final result under `.arena/<run-id>/final/`.
19. Return the winning solution to the main OpenCode agent.
20. Do NOT modify the user's project automatically unless the user explicitly asks to apply the winning solution.

## Important Rules

- Never use Claude Code's `Agent` tool.
- Use OpenCode's native subagent/Task mechanism.
- Competitors must not modify project files during the tournament.
- Keep each competitor independent.
- Do not expose unnecessary full competitor outputs in the main conversation.
- Persist tournament state so an interrupted tournament can resume.
- Do not claim that the winner is guaranteed correct.
- The winner is simply the surviving candidate after the Arena evaluation process.

## Final Response

When the tournament finishes, return:

# Arena Winner

Competitor: <ID>

Strategy:
<strategy>

Winning Solution:
<solution>

Tournament:
- competitors: <N>
- rounds: <N>
- matches: <N>

Important Attacks Survived:
<list>

Remaining Risks:
<list>

Recommended Action:
<apply / review / additional testing>