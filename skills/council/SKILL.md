---
name: council
description: Asks Claude Fable 5.2 and GPT Astra 6.2 in parallel for a review or a second opinion, and repeats until both members score the work at or above a pass mark, for at most three rounds. Use when the user says "council". Takes an optional pass mark out of 10, as in "/council 9".
---

# Council

The council has two members. Run both at high reasoning effort:

| Member | Provider | Model ID |
| --- | --- | --- |
| Fable 5.2 | Claude | `claude-fable-5-2` |
| Astra 6.2 | Codex | `gpt-6.2-astra` |

In T3 Code, confirm both models with `orchestrator_capabilities`, then start one `delegate_task` per member in parallel. In other harnesses, use the harness's subagent tools. If a member is unavailable, tell the user. Don't substitute another model.

## Prepare before round 1

For a code review, first fix the issues you already know about, address open PR comments, and run the repo's checks. The council reviews the code as it will ship, not a list of fixes you plan to make. If the user wants to replace the implementation, skip fixes to the code being replaced.

## Brief each member

Write one brief and send it to both members:

- The user's request and the relevant files, diff, or PR.
- The constraints the answer must meet.
- For a review, the instruction to stay read-only and give a score out of 10.

Leave out your own conclusion. A member who reads your answer tends to agree with it.

## Run rounds until the council passes

A round is one answer from each member.

The pass mark is the number after `/council`, such as `/council 9` or `/council 7.5`. If the user gives no number, the pass mark is 7.

1. Run round 1.
2. Check each finding against the code as it arrives, while the other member still runs. Don't edit files while a member still reviews the same checkout.
3. Fix the findings you can prove. Dismiss the rest with a reason.
4. For a review, stop when every member scores the work at or above the pass mark. A member with no score has not passed. For a question, stop when the members agree.
5. Otherwise, run the next round. For a review, send the updated diff. For a question, send each member the other member's answer and ask where it still disagrees.
6. Stop after round 3, even if the council has not passed. The report says so.

Keep the scope to the user's request. If a member asks for work outside it, list the request in the report and don't do it.

## Report

Reply with:

- Whether the council passed, the pass mark, and the number of rounds.
- The findings both members agree on, and what you did about each.
- Each disagreement, with both members quoted, for the user to decide.
- Any risk only one member raised.

For a code review that ends in a PR, add this section to the end of the PR description. Edit the description of an open PR. Don't post a comment.

```markdown
## Council review

Pass mark: 9/10. Passed in round 2.

| Member | Round 1 | Round 2 |
| --- | --- | --- |
| Fable 5.2 | 7/10 | 9/10 |
| Astra 6.2 | 8/10 | 9/10 |
```

Add a column for each round you ran. If the council did not pass, write `Not passed after round 3.` If a member gives no score, write `no score`. If there is no PR, put the table in the reply.
