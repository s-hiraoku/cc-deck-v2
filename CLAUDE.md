# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

CC-Deck v2 is a next-generation AI-driven development platform that simplifies and streamlines the software development process. Built as a refined evolution of the original cc-deck concept, it focuses on essential workflows while maintaining enterprise-grade quality standards.

**Built for**: Claude Opus 4.1 embedded in terminal environment  
**Core Capability**: Transform "hours-long workflows into a single command"  
**Architecture**: Leverages deep codebase awareness and agentic search

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

## Project Structure

```
cc-deck-v2/
├── specs/                    # Claude Code Enhanced Specifications
│   └── {project-name}/       # Project-specific specifications
│       ├── project.json     # Project metadata and configuration
│       └── features/        # Feature specifications within project
│           └── {feature-name}/
│               ├── spec.json        # State management (phase, approvals, progress)
│               ├── requirements.md   # EARS+ requirements with context linkage
│               ├── design.md        # AI-assisted technical design
│               ├── tasks.md         # Implementation tasks with TDD patterns
│               ├── context.md       # Feature-specific context and constraints
│               └── steering.md      # Adaptive steering for this specification
├── projects/                # Generated implementations
│   └── {project-name}/      # Individual project workspaces
├── workflows/               # Claude Code workflow automation
│   ├── spec.yaml           # CES-enhanced specification workflow
│   ├── impl.yaml           # Implementation workflow with agentic search
│   └── valid.yaml          # Validation workflow with deep analysis
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
# Example commands
/ces-project-init example-project
/ces-spec-init example-project example-feature
/ces-spec-requirements example-project example-feature
/ces-approve-requirements example-project example-feature

# Multiple feature example
/ces-spec-init example-project another-feature
/ces-spec-design example-project another-feature
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
/ces-project-init [project-name]

# List all projects and their status
/ces-project-list

# Switch active project context
/ces-project-switch [project-name]
```

#### Enhanced Specification Commands
```bash
# Initialize specification with Claude Code context awareness
/ces-spec-init [project-name] [feature-name]

# Generate EARS+ requirements with live file references
/ces-spec-requirements [project-name] [feature-name]

# Create AI-enhanced technical design
/ces-spec-design [project-name] [feature-name]

# Generate TDD-focused implementation tasks
/ces-spec-tasks [project-name] [feature-name]

# Check progress with deep codebase analysis
/ces-spec-status [project-name] [feature-name]

# Execute specification with agentic search
/ces-spec-execute [project-name] [feature-name]
```

#### State Management Commands
```bash
# Approve specification phase progression
/ces-approve-requirements [project-name] [feature-name]
/ces-approve-design [project-name] [feature-name]
/ces-approve-tasks [project-name] [feature-name]

# Track progress across all specifications
/ces-dashboard [project-name]

# Validate specification compliance
/ces-validate [project-name] [feature-name]
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

### Custom Workflows
- YAML-based workflow definitions
- Plugin system for additional tools
- Template-based project scaffolding

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

## Critical Response Requirements
- **ALWAYS provide concrete implementations**: When suggesting alternatives or improvements, provide specific, actionable solutions with code examples or detailed mechanisms
- **NEVER suggest removing systems without replacements**: If proposing to remove YAML, databases, or any system, MUST provide exact alternative with implementation details
- **ALWAYS research before answering**: Use WebSearch AND MCP tools (Context7, DeepWiki, Brave Search) for comprehensive, current information
- **AVOID vague proposals**: Terms like "dynamic", "intelligent", "adaptive" without concrete implementation are meaningless
- **Use multiple information sources**: Combine WebSearch + Context7 (library docs) + DeepWiki (repo analysis) + Brave Search + GitHub MCP for comprehensive answers

## System Naming Accuracy
- **CC-Deck v2**: Complete development platform (NOT just specification system)
- Handles: Specification → Implementation → Testing → Deployment
- CES is one component, not the entire system

## Workflow Control Requirements
When discussing workflow control:
1. **Define storage mechanism**: Where are workflow definitions stored?
2. **Specify execution engine**: What reads and executes the workflow?
3. **Show state management**: How is progress tracked?
4. **Provide transition rules**: How are conditions and branches handled?
5. **Include concrete examples**: Show actual code/configuration, not concepts