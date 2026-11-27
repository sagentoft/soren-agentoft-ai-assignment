# Week 1 evidence

Keep this short. Bullet points, a small table and one diagram or code trace are enough. This is your checkpoint record, not an extra report. Replace the prompts as you work.

Name: Søren Agentoft
Exact Unity version: 6000.3.26f1
Checkpoint commit / repository:
Current stage and anything unfinished: Current stage and anything unfinished: Stage 5 complete. All 17 supplied tests pass, all six gameplay scenarios have been verified, and an independent stopping distance experiment has been conducted and documented.

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

This trace was recorded during Stage 1, before implementing perception, task commitment and failure recovery.

- Observation: The rival is not carrying cargo and has available pickup candidates.
- Derived facts: DecisionPolicy.Infer() selects pickup 11 as the nearest available candidate. NeedsDelivery is false and HasUsableTarget is true.
- Decision: DecisionPolicy.Choose() selects Collect with the reason "Nearest available candidate, regardless of visibility".
- Execution: RivalBrain calls Navigator.Request(), which requests a path through NavMeshAgent.SetDestination().
- Feedback: Navigator.Poll() reports Failed. The exact cause of the navigation failure is unknown.
- Subsequent decision: RivalBrain marks pickup 11 as blocked for six seconds. However, the original policy ignores the Blocked flag and continues selecting the same target.

## Stage 2 - Perception and memory

The original policy selects the nearest available candidatewithout considering visibility or memory.

| Available | Visible | Remembered | Eligible |
|---|---|---|---|
| Yes | Yes | No | Yes |
| Yes | No | No | No |
| Yes | No | Yes | Yes |
| No | No | Yes | No |

A pickup must be available and either visible or rememberedto be eligible for selection.

Before implementation (F4): The rival selected the nearer hidden pickup (ID 0) and remained stationary despite another visible pickup being available further away.

After implementation (F4): The rival now selects the visible pickup rather than the nearer hidden pickup.

The policy filters candidates based on availability andwhether they are visible or remembered before comparingtheir distances.

## Stage 3 - Task commitment

A closer candidate does not necessarily justify abandoning the current target. Retaining a usable target prevents unnecessary switching between tasks.

The policy now checks whether an eligible candidate matches CurrentTargetId before comparing distances. If it does, the policy retains it and sets RetainedTarget to true.

Target invalidation is already handled in RivalBrain.Update(), which clears rememberedId when the pickup becomes unavailable.

The Stage 3 tests confirm that the rival retains its current usable target even when a closer candidate exists.

F5 confirms that task commitment does not prevent the rival from abandoning an unavailable target. This behaviour was already supported by the supplied target invalidation logic.

### Task transitions

- Explore -> Collect: A usable pickup is discovered.
- Collect -> Deliver: Scrap is successfully collected.
- Collect -> Collect: The current target is retained or replaced if it becomes unusable and an alternative exists.
- Collect -> Explore: The current target becomes unusable and no alternative exists.
- Deliver -> Collect: Cargo is deposited and a usable pickup is available.
- Deliver -> Explore: Cargo is deposited and no usable pickup is available.

## Stage 4 - Failure recovery

The existing policy ignored the Blocked flag, allowing the rival to repeatedly select a pickup despite failed navigation attempts.

RivalBrain already implements a six-second cooldown for failed pickup targets.

I extended the eligibility guard in DecisionPolicy.Infer() to exclude blocked candidates. Because eligibility is checked before both target retention and distance comparison, 
blocked targets can neither be acquired nor retained.

The cooldown allows the rival to reconsider a target later, but does not guarantee that it becomes reachable.

## Scenario record

| Scenario | Predicted result | Observed result and short evidence / remaining issue |
|---|---|---|
| Normal loop and restart | Scrap can be collected and deposited, the match ends, and R restarts it. | Collection, scoring, match completion and restart work with the final policy. |
| Carrying cargo | 	Prioritize delivery over collecting additional scrap. | F3 confirms that the rival prioritizes delivery when carrying cargo. |
| Hidden / remembered pickup | Ignore unknown hidden pickups while allowing remembered pickups | F4 confirms that the rival selects the visible pickup instead of the nearer hidden pickup. Behaviour remains correct with the final policy. |
| Target taken | Release the unavailable target and select another usable target | The rival successfully switched targets in F5. Existing target invalidation remains functional after implementing task commitment. |
| Unreachable target | Temporarily exclude the failed target and select another usable pickup. | The rival successfully abandoned the unreachable pickup and selected an alternative target after receiving navigation failure feedback. |
| No usable pickup | Fall back to Explore without selecting an invalid target. | The rival correctly fell back to Explore when no usable pickups were available. |

Supplied test result, with any added tests identified:

Stage 1: 11 passed, 6 failed.
Stage 2: 13 passed, 4 failed.
Stage 3: 14 passed, 3 failed.
Stage 4: 17 passed, 0 failed.

Live walkthrough checked by / recording link if used:

I'm not sure what this means. I can provide it in the final submission if I can get it clarified.

## One correction and one independent check

The original weakness and why it happened:

The rival selected the nearest available candidate regardless of whether it knew about the pickup.

The policy also ignored navigation failure feedback. Although RivalBrain temporarily marked failed targets as blocked, DecisionPolicy continued to consider them eligible.

What I changed and why it addresses the cause:

Added a visibility and memory check to the candidate selection condition in DecisionPolicy.Infer(). Unknown hidden pickups are now excluded before distance is considered.

I extended the candidate eligibility guard to exclude blocked candidates. This prevents both the acquisition and retention of temporarily blocked targets, allowing the rival to pursue alternative tasks.

My counterexample or experiment: prediction, observed result, and one limitation:

- Prediction: Increasing the stopping distance beyond the collection radius could cause the rival to stop without collecting scrap.
- Observation: At 1.6, the rival still collected scrap. At 2.5, the rival stopped without collecting it.
- Limitation: Successful navigation does not guarantee successful task execution. The stopping distance must be compatible with the collection radius.
- Restored the original stopping distance of 0.6 after testing.

## Collaboration and help

People I discussed or playtested with:

None. I have been sick all week. 

Code, sources or substantive tools/AI assistance I used, and how I verified them:


If unfinished: what works, exact reproduction steps, and the help I need:

Whatever "Live walkthrough checked by / recording link used" means.
