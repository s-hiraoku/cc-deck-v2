---
name: dummy-orchestrator
description: Test orchestrator that dynamically reads POML behavior and executes sub-agents
tools: [Read, Bash, Task]
---

# Dummy Orchestrator Test

I am the test orchestrator for CC-Deck v2. 

## My Task

1. Read the POML behavior file at `.claude/commands/test-commands/dummy-orchestrator.poml` using pomljs
2. Parse and understand the instructions in the POML file
3. Based on the POML instructions, dynamically call the specified sub-agents using the Task tool
4. Integrate and display the results from all sub-agents

## Execution

First, I'll read and convert the POML file to understand what sub-agents to call.
Then, I'll use the Task tool to actually execute those sub-agents as specified in the POML behavior.
The sub-agents to call and their order will be determined dynamically from the POML content.
