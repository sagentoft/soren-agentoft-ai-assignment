# Week 1 evidence

Keep this short. Bullet points, a small table and one diagram or code trace are enough. This is your checkpoint record, not an extra report. Replace the prompts as you work.

Name: Søren Agentoft
Exact Unity version: 6000.3.26f1
Checkpoint commit / repository:
Current stage and anything unfinished: Baseline established

## Initial prediction

Three observations the rival needs, including one it might not have:
- Whther it is currently carrying scrap
- Where available scrap is located
- Whether its current navigation attempt is succeeding

Seeing a pickup across a gap proves / does not prove reachability because:
Seeing a pickup does not prove reachability. Line of sight and navigability are two separate things. There may be no traversable path between the rival and the pickup, even if its is clearly visible.

One question about NPC behaviour:
How can an NPC make believeable decisions based on incomplete information without appearing unintelligent to the player?

## One complete trace

Use one actual situation. Include the observation data, derived facts, selected task and reason, movement request/result, and how feedback affects a later decision. Identify the responsible code locations. A tiny state diagram can accompany this trace.

## Scenario record

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Normal loop and restart | Scrap can be collected and deposited, the match ends, and R restarts it. | Confirmed that collection, scoring, match completion and restart work. |
| Carrying cargo | | |
| Hidden / remembered pickup | | |
| Target taken | | |
| Unreachable target | | |
| No usable pickup | | |

Supplied test result, with any added tests identified:
Live walkthrough checked by / recording link if used:

## One correction and one independent check

The original weakness and why it happened:
What I changed and why it addresses the cause:
My counterexample or experiment: prediction, observed result, and one limitation:

## Collaboration and help

People I discussed or playtested with:
Code, sources or substantive tools/AI assistance I used, and how I verified them:
If unfinished: what works, exact reproduction steps, and the help I need:
