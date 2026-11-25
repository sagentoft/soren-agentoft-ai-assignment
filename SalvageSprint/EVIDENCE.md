# Week 1 evidence

Keep this short. Bullet points, a small table and one diagram or code trace are enough. This is your checkpoint record, not an extra report. Replace the prompts as you work.

Name: Søren Agentoft
Exact Unity version: 6000.3.26f1
Checkpoint commit / repository:
Current stage and anything unfinished: Stage 1 complete.
Perception, task commitment and route failure recovery remain
to be implemented. An unexplained navigation failure was
observed and will be revisited during Stage 4 if it persists.

## Initial prediction

Three observations the rival needs, including one it might not have:
- Whther it is currently carrying scrap
- Where available scrap is located. This information might be unavailable if the scrap is obscured by an obstacle.
- Whether its current navigation attempt is succeeding

Seeing a pickup across a gap proves / does not prove reachability because:
Seeing a pickup does not prove reachability. Line of sight and navigability are two separate things. There may be no traversable path between the rival and the pickup, even if its is clearly visible.

One question about NPC behaviour:
How can an NPC make believeable decisions based on incomplete information without appearing unintelligent to the player?

## One complete trace

Use one actual situation. Include the observation data, derived facts, selected task and reason, movement request/result, and how feedback affects a later decision. Identify the responsible code locations. A tiny state diagram can accompany this trace.

- Observation: The rival is not carrying cargo and has available pickup candidates.
- Derived facts: DecisionPolicy.Infer() selects pickup 11 as the nearest available candidate. NeedsDelivery is false and HasUsableTarget is true.
- Decision: DecisionPolicy.Choose() selects Collect with the reason "Nearest available candidate, regardless of visibility".
- Execution: RivalBrain calls Navigator.Request(), which requests a path through NavMeshAgent.SetDestination().
- Feedback: Navigator.Poll() reports Failed. The exact cause of the navigation failure is unknown.
- Subsequent decision: RivalBrain marks pickup 11 as blocked for six seconds. However, the current policy ignores the Blocked flag and continues selecting the same target.

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

11 passed, 6 failed after Stage 1.
All six failures are expected at this stage.

Live walkthrough checked by / recording link if used:

## One correction and one independent check

The original weakness and why it happened:
What I changed and why it addresses the cause:
My counterexample or experiment: prediction, observed result, and one limitation:

- Prediction: Increasing the stopping distance beyond the collection radius could cause the rival to stop without collecting scrap.
- Observation: At 1.6, the rival still collected scrap. At 2.5, the rival stopped without collecting it.
- Limitation: Successful navigation does not guarantee successful task execution. The stopping distance must be compatible with the collection radius.
- Restored the original stopping distance of 0.6 after testing.

## Collaboration and help

People I discussed or playtested with:
Code, sources or substantive tools/AI assistance I used, and how I verified them:
If unfinished: what works, exact reproduction steps, and the help I need:
