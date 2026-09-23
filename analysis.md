# Day 2 Analysis — Direct Prompting vs Chain-of-Thought vs ReAct

## 1. Scenario

I chose a campus technical-event planning scenario. It combines arithmetic and ordering questions with one question requiring external information.

The tool-required question is:

> Our event is planned for room B-204. What is its capacity? We have 4 technical sessions, 2 mentors per session, and each mentor receives Rs. 1,500. We have a budget of Rs. 20,000. Tell me the room capacity and remaining budget.

The external tool stores the room-capacity data. B-204 has 40 seats.

The budget calculation is:
- 4 sessions × 2 mentors = 8 mentor assignments.
- 8 × Rs. 1,500 = Rs. 12,000.
- Rs. 20,000 − Rs. 12,000 = Rs. 8,000 remaining.

Expected answer: B-204 has 40 seats and Rs. 8,000 remains.

## 2. Direct Prompting

Direct prompting asks the model for an answer immediately. It does not expose a reasoning process and it has no tool access in this experiment.

For questions whose required information is already present in the prompt, direct prompting can answer quickly. Its limitation appears when the answer requires information outside the prompt. In this scenario it cannot call `get_room_capacity`, so it cannot reliably obtain the room capacity from the external source.

## 3. Chain-of-Thought Prompting

Chain-of-Thought prompting asks the model to work through a problem step by step before giving the final answer.

For arithmetic and ordering questions, this can make intermediate reasoning explicit and can help with multi-step problems. However, Chain-of-Thought does not itself provide access to the room-capacity data. More reasoning cannot replace a missing information source.

## 4. ReAct Agent

ReAct combines reasoning with actions and observations. The agent determines what information it needs, calls a tool, receives an observation, and continues until it can produce a final answer.

This project provides:
- `get_room_capacity(room_code)`
- `calculate(expression)`

For B-204, the agent should call `get_room_capacity|B-204`, receive `40`, and combine that observation with the supplied budget information.

The advantage is access to external information. The trade-off is additional steps, time, and implementation complexity.

## 5. Comparison Table

| Basis | Direct prompting | Chain-of-Thought | ReAct agent |
|---|---|---|---|
| Reasoning depth | Low/implicit | Higher, step-by-step | Reasoning interleaved with actions |
| Tool usage | No | No in this experiment | Yes |
| Reliability on multi-step questions | Can make mistakes | Often improves multi-step reasoning | Combines reasoning with observations |
| Transparency | Final answer only | Prompted steps are visible | Actions and observations are visible |
| Speed / cost | Fastest | More tokens | Extra tool/model calls |
| Consistency | High at temperature 0 | Can vary at non-zero temperature | Depends on model and settings |

## 6. Self-Consistency Observation

The self-consistency experiment runs the same reasoning question five times at temperature 0.8 and takes the most frequent final answer.

Correct answer: **Rs. 8,000**.

Record the actual terminal output below after running the program:

| Item | Value |
|---|---|
| Run 1 | ______ |
| Run 2 | ______ |
| Run 3 | ______ |
| Run 4 | ______ |
| Run 5 | ______ |
| Majority answer | ______ |
| Majority correct? | Y/N |
| Temperature = 0 result | ______ |

Do not invent these values; they depend on the model run.

## 7. Suitability Analysis

For this scenario, ReAct directly addresses the external-information requirement because it can call `get_room_capacity` and use the returned observation.

Direct prompting is useful when the required information is already in the prompt and the problem is simple.

Chain-of-Thought is useful when information is already available but several reasoning or calculation steps are required.

ReAct is appropriate when reasoning must be combined with external information or tools. Its trade-off is additional steps and tool-call cost.

## 8. Conclusion

Direct prompting is appropriate for straightforward questions where the model already has all required information.

Chain-of-Thought is appropriate for problems requiring several reasoning steps over information already provided.

ReAct is appropriate when a problem requires both reasoning and interaction with tools or external information.

The three approaches therefore address different needs: direct prompting emphasizes immediate answers, Chain-of-Thought emphasizes step-by-step reasoning, and ReAct combines reasoning with tool use.
