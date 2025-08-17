# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

CC-Deck v2 is a specialized development platform focused on **specification, implementation, and evaluation**. Unlike traditional cc-deck, CC-Deck v2 is designed as an AI-native development platform that provides a foundation for AI-driven development through effective use of custom slash commands and sub-agents.

**Platform Mission**: Provide high-quality documents, code, and test environments for both new project creation and continuous development through AI-powered automation.

**Built for**: Claude Opus 4.1 embedded in terminal environment  
**Core Capability**: Transform "hours-long workflows into a single command"  
**Architecture**: Leverages deep codebase awareness and agentic search

### Core Platform Principles

1. **Specification Excellence**: AI-assisted generation of high-quality specification documents, design documents, and technical documentation
2. **Implementation Quality**: Test-driven development with 95%+ coverage targeting robust code implementation  
3. **Comprehensive Evaluation**: Automated unit, integration, E2E, and performance testing environments
4. **Custom Slash Commands**: Simplify complex workflows into concise, executable commands
5. **Specialized Sub-Agents**: Domain-specific AI agents that automate sophisticated development tasks
6. **Quality-First Approach**: Automated quality gates and approval flows at each development stage

## ⚠️ CRITICAL: Anthropic Specification Compliance

**ALL implementations MUST strictly adhere to Anthropic's official specifications:**

### Required Documentation References
- **Official Claude Code**: https://www.anthropic.com/claude-code
- **Sub-Agents**: https://docs.anthropic.com/en/docs/claude-code/sub-agents
- **Slash Commands**: https://docs.anthropic.com/en/docs/claude-code/slash-commands  
- **CLAUDE.md**: https://docs.anthropic.com/en/docs/claude-code/memory
- **Hooks**: https://docs.anthropic.com/en/docs/claude-code/hooks
- **MCP**: https://docs.anthropic.com/en/docs/claude-code/mcp

### Non-Negotiable Requirements
1. **Official Claude Code Compatibility** - Built for Claude Opus 4.1 embedded in terminal
2. **Deep Codebase Awareness** - Leverage agentic search for entire codebase understanding
3. **No Task() calls within sub-agents** - Sub-agents CANNOT call other sub-agents
4. **Proper YAML frontmatter format** - Follow exact specification for all fields
5. **Tool allocation restrictions** - Only assign necessary tools to each agent
6. **Description format compliance** - Keep concise, specific, and action-oriented
7. **Model specification** - Use approved models only (opus, sonnet, haiku)
8. **Explicit Approval Required** - Never modify files without human approval

### Validation Checklist
- [ ] All sub-agents comply with official template structure
- [ ] No circular dependencies or Task() tool usage in agents
- [ ] Commands follow allowed-tools restrictions
- [ ] YAML frontmatter validates against Anthropic schema
- [ ] Description fields are properly formatted

**Any deviation from Anthropic specifications will cause system failures and must be avoided.**

### Core Design Philosophy

1. **Simplicity First**: Reduce complexity while preserving power
2. **AI-Native Development**: Leverage Claude Code's capabilities for intelligent automation
3. **Quality Without Compromise**: Maintain TDD practices and high code quality
4. **Human-Centric Approval**: All critical decisions require human oversight
5. **Specification-Driven**: Clear requirements drive implementation
6. **Agent Specialization**: Use focused, domain-specific sub-agents for precise tasks
7. **Context-Aware Orchestration**: Dynamic agent selection based on project context
8. **Extensible Architecture**: Modular design supporting plugins and custom workflows
9. **State-Driven Context Persistence**: Ensure seamless context flow between workflow phases

## Architecture Overview

### Three-Layer Architecture

```
┌─────────────────────────────────────────┐
│ User Interface Layer                    │
│ - Single orchestrator command           │
│ - Interactive workflow selection        │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Workflow Engine Layer                   │
│ - Spec creation and management          │
│ - TDD implementation cycles             │
│ - Quality assurance gates              │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Execution Layer                         │
│ - Project generation and management     │
│ - Test automation and validation        │
│ - Documentation and reporting           │
└─────────────────────────────────────────┘
```

### Core Workflows

1. **Specification (SPEC)**: Advanced AI-driven requirements analysis and design documentation
2. **Implementation (IMPL)**: TDD-driven development with quality gates
3. **Validation (VALID)**: Testing, review, and approval processes

### Claude Code Enhanced Specification System (CES)

Building on Kiro's specification-driven development while leveraging Claude Code's unique capabilities:

#### Enhanced Requirements Framework
- **EARS+ Notation**: Extended Easy Approach to Requirements Syntax with AI context awareness
- **Context-Aware Specifications**: Deep codebase understanding for consistent requirements
- **Steering Integration**: Project-specific conventions and patterns automatically applied
- **File Reference Linkage**: Live project file integration `#[[file:path/to/file]]`

#### AI-Native Design Patterns
- **Agentic Search Integration**: Leverage Claude Code's codebase awareness for design decisions
- **Smart Context Propagation**: Maintain design consistency across multiple specifications
- **MCP-Enhanced Research**: Real-time documentation access during specification creation
- **Progressive Specification**: Iterative refinement with AI assistance
- **State-Driven Context Management**: POML-based context persistence across workflow transitions

## Project Structure

```
cc-deck-v2/
├── projects/                # Project workspaces
│   └── {project-name}/      # Individual project directory
│       ├── project.json     # Project metadata and configuration
│       ├── specs/           # Claude Code Enhanced Specifications
│       │   └── {feature-name}/
│       │       ├── spec.json        # State management (phase, approvals, progress)
│       │       ├── requirements.md   # EARS+ requirements with context linkage
│       │       ├── design.md        # AI-assisted technical design
│       │       ├── tasks.md         # Implementation tasks with TDD patterns
│       │       ├── context.md       # Feature-specific context and constraints
│       │       ├── steering.md      # Adaptive steering for this specification
│       │       ├── workflow.poml    # POML-based AI workflow definitions
│       │       └── context-state.json # Workflow context persistence state
│       └── src/             # Implementation files
├── workflows/               # POML + YAML hybrid workflow system
│   ├── agents/              # POML-based agent definitions
│   │   ├── spec-generator.poml      # CES specification agent
│   │   ├── implementation.poml       # Implementation agent
│   │   ├── validation.poml          # Testing and validation agent
│   │   ├── context-bridge.poml      # Workflow context bridging agent
│   │   └── orchestrator.poml        # Main workflow orchestration
│   ├── templates/           # Dynamic POML workflow templates
│   │   ├── spec-workflow.poml       # Specification generation workflow
│   │   ├── impl-workflow.poml       # Implementation workflow
│   │   ├── valid-workflow.poml      # Validation workflow
│   │   └── context-bridge.poml      # Context transition templates
│   └── config/              # YAML-based infrastructure configs
│       ├── project-defaults.yaml    # Default project settings
│       ├── quality-gates.yaml       # Quality assurance requirements
│       └── context-config.yaml      # Context persistence configuration
├── steering/                # Global steering files
│   ├── coding-standards.md  # Project-wide coding conventions
│   ├── architecture.md      # System architecture patterns
│   └── quality-gates.md     # Quality assurance requirements
└── docs/                   # Platform documentation
```

### CES Specification Files

#### Enhanced Requirements (requirements.md)
```markdown
---
specification_type: "requirements"
context_references: ["#codebase", "#folder:components/"]
mcp_tools: ["context7", "deepwiki"]
---

# EARS+ Requirements with Context

## Core Requirements
WHEN a user interacts with #[[file:components/AuthForm.tsx]]
THE SYSTEM SHALL validate credentials using #[[file:lib/auth.ts]]
AND display appropriate feedback via #[[file:components/ui/toast.tsx]]

## Context-Aware Constraints
GIVEN the current architecture in #[[file:docs/architecture.md]]
THE implementation SHALL follow established patterns
```

#### AI-Enhanced Design (design.md)
```markdown
---
design_type: "technical"
referenced_files: ["src/components/**", "lib/auth.ts"]
research_sources: ["#context7:next.js", "#deepwiki:authentication-patterns"]
---

# Context-Aware Technical Design

## Architecture Decision
Based on #codebase analysis and #next.js best practices...

## Component Integration
Leveraging existing patterns from #[[file:components/ui/button.tsx]]...
```

#### Project Configuration (project.json)
```json
{
  "project": "example-project",
  "description": "Example project demonstrating CES workflow",
  "language": "english",
  "tech_stack": ["Framework", "Language", "Database"],
  "created_at": "2025-01-16T09:00:00Z",
  "active_features": ["example-feature-1", "example-feature-2"],
  "steering_files": ["coding-standards.md", "architecture.md"]
}
```

#### Feature State Management (spec.json)
```json
{
  "project": "example-project",
  "feature": "example-feature",
  "phase": "design-generated",
  "language": "english",
  "approvals": {
    "requirements": {
      "generated": true,
      "approved": true
    },
    "design": {
      "generated": true,
      "approved": false
    },
    "tasks": {
      "generated": false,
      "approved": false
    }
  },
  "progress": {
    "requirements": 100,
    "design": 100,
    "tasks": 0
  },
  "context_references": ["#codebase", "#folder:components/"],
  "mcp_tools": ["context7", "deepwiki"],
  "created_at": "2025-01-16T10:00:00Z",
  "updated_at": "2025-01-16T15:30:00Z"
}
```

### Naming Conventions

#### Project Names (project-name)
**Format**: `kebab-case` (lowercase with hyphens)

**Examples**:
- `e-commerce-platform` - E-commerce platform
- `blog-platform` - Blog platform  
- `task-management-app` - Task management application
- `social-media-dashboard` - Social media dashboard
- `inventory-management-system` - Inventory management system

#### Feature Names (feature-name)  
**Format**: `kebab-case` (lowercase with hyphens)

**Examples**:
- `user-authentication` - User authentication feature
- `product-catalog` - Product catalog feature
- `shopping-cart` - Shopping cart feature
- `payment-processing` - Payment processing feature
- `article-management` - Article management feature
- `comment-system` - Comment system
- `user-profiles` - User profiles feature
- `search-functionality` - Search functionality
- `notification-system` - Notification system
- `reporting-dashboard` - Reporting dashboard

#### Command Usage Examples
```bash
# Project setup
/project-init example-project

# Specification workflow (CES)
/spec-init example-project example-feature
/spec-requirements example-project example-feature
/spec-design example-project example-feature
/spec-tasks example-project example-feature

# Approval workflow
/approve-requirements example-project example-feature
/approve-design example-project example-feature

# Implementation workflow
/implement example-project example-feature
/test example-project example-feature
/create-pr example-project example-feature
```

## Development Commands

### CES Slash Commands (Enhanced from gotalab/claude-code-spec)

#### Steering Commands
```bash
# Initialize project steering documents
/steering-init

# Update steering after project changes  
/steering-update

# Create custom steering for specialized contexts
/steering-custom
```

#### Project Management Commands
```bash
# Initialize new project with default structure
/project-init [project-name]

# List all projects and their status
/project-list

# Switch active project context
/project-switch [project-name]
```

#### Specification Commands (CES - Claude Code Enhanced Specification System)
```bash
# Initialize specification with Claude Code context awareness
/spec-init [project-name] [feature-name]

# Generate EARS+ requirements with live file references
/spec-requirements [project-name] [feature-name]

# Create AI-enhanced technical design
/spec-design [project-name] [feature-name]

# Generate implementation tasks
/spec-tasks [project-name] [feature-name]

# Check specification progress
/spec-status [project-name] [feature-name]
```

#### Approval Management Commands
```bash
# Approve specification phase progression
/approve-requirements [project-name] [feature-name]
/approve-design [project-name] [feature-name]
/approve-tasks [project-name] [feature-name]
```

#### Implementation & Workflow Commands
```bash
# Execute implementation workflow
/implement [project-name] [feature-name]

# Run testing workflow
/test [project-name] [feature-name]

# Create pull request
/create-pr [project-name] [feature-name]

# Platform dashboard
/dashboard [project-name]
```

### Quality Assurance
```bash
# Lint all code
npm run lint

# Type checking
npm run type-check

# Full quality gate
npm run quality:gate <project-name>
```

## Workflow Integration

### Specification Workflow (SPEC)
- **Input**: User requirements or project idea
- **Process**: Requirement analysis → Technical design → Task breakdown
- **Output**: Complete specification in `specs/{project-name}/`
- **Approval**: Human review required for requirements and design

### Implementation Workflow (IMPL)
- **Input**: Approved specification
- **Process**: Project setup → TDD cycles → Integration → Documentation
- **Output**: Working implementation in `projects/{project-name}/`
- **Quality Gates**: 95%+ test coverage, lint compliance, type safety

### Validation Workflow (VALID)
- **Input**: Completed implementation
- **Process**: End-to-end testing → Performance validation → User acceptance
- **Output**: Validated, production-ready application
- **Approval**: Stakeholder sign-off required

## MCP Integration Strategy

The platform leverages Model Context Protocol (MCP) servers for enhanced development capabilities:

- **Context7**: Up-to-date library documentation and code examples
- **DeepWiki**: Repository analysis and architectural insights
- **Brave Search**: Latest technology trends and best practices

### Advanced MCP Features
- **Dynamic Configuration**: Project-specific MCP server selection
- **Context Priming**: Comprehensive project understanding for agents
- **Token Monitoring**: Enterprise-grade usage tracking and optimization
- **Multi-Provider Support**: Flexible backend service integration
- **Granular Permissions**: Fine-grained access control for security

## TDD Implementation Standards

### Test Coverage Requirements
- **Unit Tests**: 95%+ line coverage
- **Integration Tests**: All API endpoints and data flows
- **E2E Tests**: Critical user journeys
- **Performance Tests**: Key metrics and thresholds

### Testing Stack
- **Vitest**: Fast unit testing with coverage reporting
- **Playwright**: Cross-browser end-to-end testing
- **Testing Library**: Component testing with accessibility focus

## Quality Assurance Framework

### Automated Quality Gates
1. **Code Quality**: ESLint, Prettier, TypeScript strict mode
2. **Security**: Dependency scanning, secret detection
3. **Performance**: Bundle size limits, Core Web Vitals
4. **Accessibility**: WCAG 2.1 AA compliance

### Human Approval Points
- Specification approval after requirements analysis
- Design review before implementation
- Final acceptance before production deployment

## Key Design Patterns

### Specification-Driven Development
1. Every project starts with a clear specification
2. Implementation follows approved design
3. Tasks are tracked with measurable progress
4. Changes require specification updates

### AI-Human Collaboration
- AI handles research, analysis, and implementation
- Humans make strategic decisions and provide approval
- Clear handoff points between AI and human responsibilities
- Comprehensive documentation for decision tracking

### Sub-Agent Orchestration (Anthropic Compliant)
- **Specialized Expertise**: Domain-specific agents with narrow, well-defined focus
- **Context-Aware Selection**: Dynamic agent assignment based on task requirements
- **Modular Architecture**: Extensible framework supporting custom agent development
- **Collaboration Patterns**: Orchestrator-mediated workflows (NO direct agent-to-agent calls)
- **Strict Compliance**: All agents follow https://docs.anthropic.com/en/docs/claude-code/sub-agents

### Progressive Enhancement
- Start with core functionality
- Add features incrementally
- Maintain backward compatibility
- Focus on user value delivery

## Error Handling and Recovery

### Checkpoint System
- Automatic saves at workflow transitions
- Rollback capability for failed implementations
- State preservation across sessions

### Failure Recovery
- Graceful degradation for tool failures
- Alternative workflow paths
- Manual intervention points

## Platform Extension

### Hybrid Workflow System (POML + YAML)
- **POML for AI-Native Workflows**: Agent definitions, dynamic templates, context-aware orchestration
- **YAML for Infrastructure**: CI/CD configs, dependency management, quality gates
- **Plugin system for additional tools**: Extensible architecture supporting custom agents and tools
- **Template-based project scaffolding**: Language-agnostic project generation

### Integration Points
- Git hooks for automated quality checks
- CI/CD pipeline integration
- Deployment automation

### Agent Development Framework (Anthropic Specification)
- **Sub-Agent Templates**: Must follow official template at https://docs.anthropic.com/en/docs/claude-code/sub-agents
- **YAML Frontmatter**: Required fields: `name`, `description`, `model`, optional: `tools`, `color`
- **Tool Restrictions**: No `Task` tool in sub-agents (orchestrator-only delegation)
- **Model Selection**: Only use `opus`, `sonnet`, or `haiku` models
- **Context Management**: Orchestrator handles all inter-agent communication
- **Agent Registry**: Validate all agents against Anthropic specifications before registration

### Enterprise Features
- **Token Usage Analytics**: Comprehensive monitoring and optimization
- **Multi-Workspace Support**: Concurrent project management capabilities
- **Security Framework**: Role-based access control and audit trails
- **Performance Monitoring**: Real-time system health and agent efficiency tracking

## Success Metrics

### Development Velocity
- Time from specification to working implementation
- Reduction in bug reports and rework
- Developer satisfaction and productivity

### Quality Metrics
- Test coverage percentages
- Performance benchmark compliance
- Security vulnerability counts
- Accessibility compliance scores

## Future Evolution

The platform is designed for continuous improvement:
- Workflow optimization based on usage patterns
- Tool integration based on ecosystem changes
- Quality standard evolution with industry best practices
- User feedback integration for UX improvements

## Reference Implementations

### s-hiraoku/cc-deck
Key patterns and insights from the original CC-Deck implementation:

- **Intelligent State Detection**: Orchestrator automatically analyzes project state and resumes from last successful phase
- **Kiro Status Tracking**: Uses `kiro_status.json` for persistent workflow state management
- **Four-Layer Quality Assurance**: AI checks → Automated testing → Intelligent approval → Continuous monitoring
- **MCP as "AI USB-C"**: Modular integration architecture for external tools and services
- **Issue Auto-Management**: Automatic conversion of `tasks.md` to GitHub issues for team coordination
- **Risk-Based Approval Routing**: AI-driven assessment determines automatic vs. human approval requirements

### gotalab/claude-code-spec Integration
Core command patterns and state management from the original implementation:

- **Slash Command Architecture**: `/action-target` kebab-case format for systematic workflow
- **Three-Phase Approval Workflow**: Requirements → Design → Tasks with explicit human approval gates
- **spec.json State Management**: JSON-based phase tracking, approval status, and progress metrics
- **File-Based Persistence**: Structured `.kiro/` directory with feature-specific subdirectories
- **Approval Gate Architecture**: `generated` and `approved` status tracking for each phase
- **Task Progress Automation**: Checkbox parsing in `tasks.md` for completion percentage
- **Steering Drift Detection**: Automated compliance checking against project steering documents

### Kiro.dev Integration
Enhanced specification patterns from Kiro's proven methodology:

- **EARS Requirements**: Extended Easy Approach to Requirements Syntax for clarity and testability
- **Steering Files**: Project-specific behavior modification with `inclusion: always` and `fileMatch` patterns
- **File Reference System**: Live project file linkage using `#[[file:path]]` syntax
- **Context Providers**: Rich context integration with `#codebase`, `#file`, `#folder`, `#current` commands
- **MCP Tool Integration**: Seamless external documentation access via Context7, AWS Docs, Frontend MCP
- **Progressive Specification**: Iterative refinement with AI-assisted requirement evolution
- **Agent Hooks**: Automated task execution triggered by file changes or manual activation

# important-instruction-reminders
Do what has been asked; nothing more, nothing less.
NEVER create files unless they're absolutely necessary for achieving your goal.
ALWAYS prefer editing an existing file to creating a new one.
NEVER proactively create documentation files (*.md) or README files. Only create documentation files if explicitly requested by the User.

## Session Initialization Requirements
- **ALWAYS get current date/time first**: Use date-utility agent or Task tool to get current date and time at session start
- **Context awareness**: Understanding current date is essential for AI/tech landscape research and time-sensitive recommendations

## Critical Response Requirements
- **ALWAYS be honest**: Provide genuine assessments, admit uncertainties, and avoid forced recommendations when evidence doesn't support them
- **ALWAYS provide concrete implementations**: When suggesting alternatives or improvements, provide specific, actionable solutions with code examples or detailed mechanisms
- **NEVER suggest removing systems without replacements**: If proposing to remove YAML, databases, or any system, MUST provide exact alternative with implementation details
- **ALWAYS research before answering**: Use WebSearch AND MCP tools (Context7, DeepWiki, Brave Search) for comprehensive, current information
- **AVOID vague proposals**: Terms like "dynamic", "intelligent", "adaptive" without concrete implementation are meaningless
- **Use multiple information sources**: Combine WebSearch + Context7 (library docs) + DeepWiki (repo analysis) + Brave Search + GitHub MCP for comprehensive answers
- **Keep it simple and quality-focused**: Avoid temporary workarounds, example lists, or documentation bloat. Focus on essential, high-quality content only

## System Naming Accuracy
- **CC-Deck v2**: Complete development platform (NOT just specification system)
- Handles: Specification → Implementation → Testing → Deployment
- **CES**: Claude Code Enhanced Specification System - ONE component for specification creation only
- **Incorrect naming**: Do NOT use "CES" for commands that handle implementation, testing, or deployment

## Multi-Language Platform Support
- **CC-Deck v2 is language-agnostic**: Supports projects in any programming language
- **Common languages**: TypeScript, JavaScript, Python, Go, Rust, Java, C#, PHP, Ruby, etc.
- **When researching solutions**: Always consider multi-language compatibility, not just TypeScript
- **Workflow solutions must be universal**: Should work regardless of the project's implementation language

## POML Integration Strategy

### Why POML Over YAML for AI Workflows
- **AI-Native Design**: Built specifically for LLM prompt orchestration and agent management
- **Semantic Structure**: HTML-like syntax provides clear intent and context for AI operations
- **Dynamic Templating**: Native support for variables, loops, conditionals without external templating engines
- **Rich Context Integration**: Better support for embedding project data, file references, and external sources
- **Microsoft Backing**: Official Microsoft specification with active development and tooling support

### POML Implementation Plan
1. **Agent Definitions**: Use POML for defining sub-agent behaviors, prompts, and capabilities
2. **Dynamic Workflows**: Template-based workflow generation that adapts to project context
3. **Context Management**: Rich integration with MCP tools and project file references
4. **Orchestration Logic**: Complex conditional flows and data transformations in AI-readable format

### Hybrid Architecture Benefits
- **Best of Both Worlds**: POML for AI complexity, YAML for infrastructure simplicity
- **Tool Compatibility**: Maintain CI/CD and external tool integration through YAML
- **Migration Path**: Gradual adoption without breaking existing workflows
- **Performance Optimization**: AI-optimized configuration where it matters most

### POML Implementation Compliance

**ALL POML implementations MUST strictly adhere to Microsoft's official POML specification:**

#### Required POML Documentation References
- **Official POML Repository**: https://github.com/microsoft/poml
- **POML Documentation**: https://microsoft.github.io/poml/latest/
- **VS Code Extension**: https://marketplace.visualstudio.com/items?itemName=poml-team.poml

#### POML Specification Requirements
1. **HTML-like Syntax** - Use proper semantic components with correct tag structure
2. **Semantic Components** - Use official tags: `<role>`, `<task>`, `<example>`, `<output-format>`
3. **Data Components** - Use official data tags: `<document>`, `<table>`, `<img>`
4. **Root Element** - All POML files must start with `<poml>` root element
5. **Templating Engine** - Use POML's built-in templating for variables and conditionals
6. **No Custom Tags** - Only use officially defined POML semantic and data components

#### POML Structure Template
```xml
<poml>
  <!-- Variable definitions -->
  <let name="project_name" value="{{PROJECT_NAME}}" />
  <let name="feature_name" value="{{FEATURE_NAME}}" />
  
  <!-- Role definition -->
  <role>Define the AI agent's persona and context</role>
  
  <!-- Task specification -->
  <task>Specify the primary objective and requirements</task>
  
  <!-- Conditional content -->
  <p if="iteration > 1">Previous iteration context available</p>
  
  <!-- Data components for external resources -->
  <document src="path/to/file.md" />
  <img src="diagram.png" alt="Architecture diagram" />
  
  <!-- Loop for multiple examples -->
  <example for="ex in examples">
    Input: {{ex.input}}
    Output: {{ex.output}}
  </example>
  
  <!-- Output format specification -->
  <output-format>
    Specify exact output format and constraints
  </output-format>
</poml>
```

#### Validation Requirements
- [ ] All POML files validate against official specification
- [ ] Use only officially supported semantic and data components
- [ ] Proper HTML-like syntax with correct tag nesting
- [ ] Templating follows POML engine capabilities
- [ ] No deviation from Microsoft's POML standard

**Any deviation from official POML specification will cause incompatibility issues and must be avoided.**

### State-Driven Context Persistence Architecture

#### Core Context Management System
- **Context State Files**: Each feature maintains `context-state.json` for workflow persistence
- **POML Context Bridging**: Automated context transfer between workflow phases
- **Enhanced CLAUDE.md Integration**: Dynamic context injection into session memory
- **Progressive Context Building**: Cumulative context enhancement across iterations

#### Context Storage Mechanism
```
projects/{project-name}/specs/{feature-name}/
├── context-state.json          # Current workflow context state
├── context-history.jsonl       # Historical context evolution
└── context-cache/              # Cached context artifacts
    ├── previous-decisions.json  # Key decisions from previous phases
    ├── technical-constraints.json # Technical limitations and requirements
    └── quality-metrics.json    # Quality measurements and benchmarks
```

#### Context Bridging Process
1. **Context Extraction**: Mine completed workflow for key information
2. **Context Storage**: Persist extracted context in structured format
3. **Context Injection**: Load relevant context at workflow start
4. **Context Enhancement**: Enrich context throughout workflow execution
5. **Context Validation**: Ensure context quality and consistency

### Workflow Control Requirements
When discussing workflow control:
1. **Define storage mechanism**: POML files in `workflows/agents/` and `workflows/templates/`
2. **Specify execution engine**: Claude Code with POML SDK integration for parsing and execution
3. **Show state management**: JSON-based progress tracking with POML workflow state
4. **Provide transition rules**: POML conditional blocks and loop structures for flow control
5. **Include concrete examples**: Actual POML agent definitions and workflow templates
6. **Context persistence strategy**: State-driven context bridging across workflow phases

