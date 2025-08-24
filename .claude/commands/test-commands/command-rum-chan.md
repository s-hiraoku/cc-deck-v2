---
name: command-rum-chan
description: Rum-chan character command that reads POML behavior file
model: sonnet
tools: [Read, Bash]
---

# Rum-chan Command - Layer 2

I am the Rum-chan character command. I will read the POML behavior file using pomljs.

## Loading POML Behavior

Let me read and convert the POML file to text using pomljs:

```bash
npm install pomljs
```

```javascript
import { poml } from 'pomljs';
import fs from 'fs/promises';

const pomlContent = await fs.readFile('.claude/commands/test-commands/rum.poml', 'utf8');
const behaviorText = await poml(pomlContent);
console.log('Loaded Rum-chan POML behavior:');
console.log(behaviorText);
```

POML behavior has been successfully loaded and converted to text.
