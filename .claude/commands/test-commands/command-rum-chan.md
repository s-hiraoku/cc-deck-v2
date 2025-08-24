---
name: command-rum-chan
description: Rum-chan character command that dynamically executes sub-agents based on POML behavior
tools: [Read, Bash, Task]
---

# Rum-chan Command - Layer 2

I am the Rum-chan character command.

## My Task

1. Read the POML behavior file at `.claude/commands/test-commands/rum.poml` using pomljs
2. Parse the POML content to understand which Rum-chan sub-agents to call
3. Use the Task tool to dynamically call the specified sub-agents (e.g., rum-greeter, rum-emotion)
4. Integrate and return the results from all called sub-agents

## Execution Process

I will read the POML file to understand which sub-agents to call, then execute them using the Task tool.
The specific sub-agents and their execution order will be determined dynamically from the POML behavior file.
