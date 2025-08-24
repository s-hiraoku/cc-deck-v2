---
name: dummy-orchestrator
description: Test orchestrator that reads POML behavior file
tools: [Read, Bash]
---

# Dummy Orchestrator Test

I am the test orchestrator for CC-Deck v2. I will read the POML behavior file using pomljs.

## Loading POML Behavior

Let me read and convert the POML file to text using pomljs:

```bash
npm install pomljs
```

```javascript
import { poml } from "pomljs";
import fs from "fs/promises";

const pomlContent = await fs.readFile(
  ".claude/commands/test-commands/dummy-orchestrator.poml",
  "utf8"
);
const behaviorText = await poml(pomlContent);
console.log("Loaded POML behavior:");
console.log(behaviorText);
```

POML behavior has been successfully loaded and converted to text. I will follow the instructions specified in the POML file.
