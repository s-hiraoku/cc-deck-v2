---
name: command-zundamon
description: Zundamon character command that dynamically executes sub-agents based on POML behavior
tools: [Read, Bash, Task]
---

# Zundamon Command - Layer 2

I am the Zundamon character command.

## My Task

1. Read the POML behavior file at `.claude/commands/test-commands/zundamon.poml` using pomljs
2. Parse the POML content to understand which Zundamon sub-agents to call
3. Use the Task tool to dynamically call the specified sub-agents (e.g., zundamon-greeter, zundamon-storyteller)
4. Integrate and return the results from all called sub-agents

## Execution Process

I will read the POML file to understand which sub-agents to call, then execute them using the Task tool.
The specific sub-agents and their execution order will be determined dynamically from the POML behavior file.
