# State-Driven Context Persistence Architecture

## Overview

CC-Deck v2 implements a sophisticated yet practical context persistence system that ensures seamless information flow between workflow phases. As a specialized development platform focused on **specification, implementation, and evaluation**, CC-Deck v2 leverages POML's AI-native capabilities combined with enhanced CLAUDE.md integration to maintain high-quality context across all development workflows.

### Platform Context Foundation

This context persistence architecture supports CC-Deck v2's core mission of providing high-quality documents, code, and test environments through:

- **Specification Context**: Maintaining requirements, design decisions, and constraints across workflow phases
- **Implementation Context**: Preserving code patterns, architectural decisions, and technical debt information
- **Evaluation Context**: Tracking test results, performance metrics, and quality assessments
- **Custom Slash Command Integration**: Context-aware command execution with historical awareness
- **Sub-Agent Coordination**: Seamless context handoff between specialized AI agents

## Core Architecture Components

### 1. Context State Management

#### Context State Files
Each feature maintains structured context state in JSON format:

```json
{
  "project": "example-project",
  "feature": "user-authentication",
  "current_workflow": "implementation",
  "previous_workflow": "specification",
  "context_version": "1.2.0",
  "last_updated": "2025-01-16T15:30:00Z",
  "context_quality_score": 0.87,
  "state": {
    "key_decisions": [
      {
        "id": "auth-strategy",
        "title": "Authentication Strategy Selection",
        "decision": "JWT with refresh tokens",
        "rationale": "Scalable, stateless, secure for microservices",
        "impact": "High - affects all user-related features",
        "timestamp": "2025-01-16T14:15:00Z"
      }
    ],
    "technical_constraints": [
      {
        "type": "security",
        "constraint": "GDPR compliance required",
        "implementation": "Data encryption and user consent flows"
      }
    ],
    "quality_metrics": {
      "test_coverage": 0.95,
      "performance_targets": {
        "login_time": "< 200ms",
        "token_refresh": "< 100ms"
      }
    },
    "dependencies": [
      {
        "name": "user-profile-service",
        "type": "external_api",
        "status": "pending"
      }
    ]
  }
}
```

#### Context History
Maintains evolution of context over time in JSONL format:

```jsonl
{"timestamp": "2025-01-16T10:00:00Z", "workflow": "specification", "event": "requirements_approved", "context_delta": {"decisions": [{"id": "auth-strategy", "status": "approved"}]}}
{"timestamp": "2025-01-16T11:30:00Z", "workflow": "specification", "event": "design_completed", "context_delta": {"artifacts": [{"name": "auth-flow-diagram", "type": "design"}]}}
{"timestamp": "2025-01-16T15:30:00Z", "workflow": "implementation", "event": "context_bridged", "context_delta": {"inherited": ["auth-strategy", "security-requirements"]}}
```

### 2. POML Context Bridging System

#### Context Bridge Template
```poml
<poml>
  <role>Workflow context transition coordinator responsible for extracting, validating, and transferring context between workflow phases</role>
  
  <task>
    Extract critical information from completed workflow phase and prepare it for injection into the next workflow phase.
    Ensure all key decisions, constraints, and artifacts are preserved and properly formatted.
  </task>
  
  <document src="projects/{{project}}/specs/{{feature}}/spec.json" />
  <document src="projects/{{project}}/specs/{{feature}}/requirements.md" />
  <document src="projects/{{project}}/specs/{{feature}}/design.md" />
  
  <example>
    Input from specification phase:
    - Architecture decision: Microservices with REST APIs
    - Security requirement: JWT authentication + HTTPS
    - Performance target: < 200ms response time
    
    Output for implementation phase:
    - Project structure: services/auth, services/api
    - Dependencies: express@4, jsonwebtoken@9, helmet@7
    - Test scenarios: auth flow, API endpoints, performance
  </example>
  
  <output-format>
    Generate structured context summary for CLAUDE.md injection:
    
    ## Context from Previous Workflow ({{previous_phase}})
    **Key Decisions Made:**
    - [List critical architectural and technical decisions with rationale]
    
    **Technical Constraints:**
    - [List performance, security, compliance requirements]
    
    **Implementation Requirements:**
    - [List specific implementation tasks and dependencies]
    
    **File References:**
    - [List relevant specification files and documentation]
  </output-format>
</poml>
```

#### Spec-to-Implementation Context Bridge
```poml
<poml>
  <role>Context bridge for specification-to-implementation workflow transition</role>
  
  <task>
    Transform approved specification context into actionable implementation context.
    Extract key decisions, constraints, and requirements from specification phase.
    Format information for implementation team consumption.
  </task>
  
  <document src="projects/{{project}}/specs/{{feature}}/spec.json" />
  <document src="projects/{{project}}/specs/{{feature}}/requirements.md" />
  <document src="projects/{{project}}/specs/{{feature}}/design.md" />
  <document src="projects/{{project}}/specs/{{feature}}/tasks.md" />
  
  <example>
    Specification context extraction:
    {
      "architecture_decisions": ["microservices", "event-driven"],
      "technology_stack": ["Node.js", "PostgreSQL", "Redis"],
      "security_requirements": ["OAuth2", "data encryption"],
      "performance_targets": {"response_time": "200ms", "throughput": "1000 req/s"}
    }
    
    Implementation context injection:
    {
      "project_structure": ["services/auth", "services/api", "shared/models"],
      "dependencies": ["express", "passport", "bcrypt", "pg"],
      "test_scenarios": ["auth flow", "API endpoints", "load testing"],
      "deployment_config": ["docker", "kubernetes", "helm"]
    }
  </example>
  
  <output-format>
    Provide structured implementation context:
    - Map architectural decisions to project structure
    - Convert technology choices to specific dependencies and versions
    - Transform requirements into test scenarios and acceptance criteria
    - Include deployment and infrastructure considerations
  </output-format>
</poml>
```

### 3. Enhanced CLAUDE.md Integration

#### Dynamic Context Section
CLAUDE.md is enhanced with a dynamic context section that gets updated automatically:

```markdown
## Current Workflow Context
**Last Updated**: 2025-01-16T15:30:00Z
**Project**: example-project
**Feature**: user-authentication  
**Current Phase**: implementation
**Previous Phase**: specification (completed successfully)

### Context from Previous Workflow
**Key Decisions Made:**
- Authentication Strategy: JWT with refresh tokens
  - Rationale: Scalable, stateless, secure for microservices
  - Impact: High - affects all user-related features
  
- Database Choice: PostgreSQL with Redis caching
  - Rationale: ACID compliance + performance optimization
  - Impact: Medium - affects data layer architecture

**Technical Constraints:**
- GDPR compliance required (data encryption + consent flows)
- Performance targets: login < 200ms, token refresh < 100ms
- Security: Multi-factor authentication support required

**Implementation Requirements:**
- API endpoints: /auth/login, /auth/refresh, /auth/logout
- Database tables: users, sessions, permissions
- Testing: 95%+ coverage, security penetration testing
- Integration: User profile service (pending dependency)

**File References:**
- Design document: specs/user-authentication/design.md
- API specification: specs/user-authentication/api-spec.yaml
- Security requirements: specs/user-authentication/security.md

### Next Steps for Current Workflow
1. Implement JWT authentication service
2. Create user registration/login endpoints  
3. Set up database migrations and models
4. Implement comprehensive test suite
5. Security audit and penetration testing
```

#### Context Quality Indicators
```markdown
### Context Quality Assessment
- **Completeness**: 87% (missing: performance benchmarks)
- **Consistency**: 95% (minor: error handling strategy)
- **Relevance**: 92% (high relevance to current implementation)
- **Freshness**: 2 hours ago (recent and current)

**Recommended Actions:**
- [ ] Define specific performance benchmarks
- [ ] Clarify error handling strategy
- [ ] Validate integration requirements
```

### 4. Implementation Strategy

#### Phase 1: Foundation (Week 1-2)
1. **Basic Context State Files**
   - Implement `context-state.json` structure
   - Create context history logging
   - Set up context cache directories

2. **Simple Context Bridging**
   - Manual context extraction scripts
   - Basic CLAUDE.md context injection
   - Workflow transition helpers

#### Phase 2: POML Integration (Week 3-4)
1. **Context Bridge Agent**
   - Implement POML context bridge agent
   - Create context extraction templates
   - Develop context validation logic

2. **Enhanced CLAUDE.md**
   - Dynamic context section updates
   - Quality assessment integration
   - Automated context summaries

#### Phase 3: Advanced Features (Week 5-6)
1. **Quality Assessment**
   - Context quality scoring
   - Automatic context enhancement
   - Quality-based recommendations

2. **Optimization**
   - Context caching strategies
   - Performance optimization
   - Error recovery mechanisms

## Quality Assurance

### Context Quality Metrics
- **Completeness**: Percentage of required information captured
- **Consistency**: Alignment with project standards and previous decisions
- **Relevance**: Applicability to current workflow phase
- **Freshness**: Recency and currency of information

### Validation Rules
1. All key decisions must include rationale and impact assessment
2. Technical constraints must be specific and actionable
3. Dependencies must include status and resolution timeline
4. Quality metrics must be measurable and tracked

### Error Recovery
- Graceful degradation when context is incomplete
- Manual context injection capabilities
- Context reconstruction from available artifacts
- Fallback to previous known good state

## Benefits

### For Development Teams
- **Continuity**: Seamless workflow transitions without information loss
- **Quality**: Consistent decision-making based on complete context
- **Efficiency**: Reduced rework and context reconstruction time
- **Collaboration**: Shared understanding across team members

### For AI Workflows
- **Accuracy**: Better informed AI decisions with rich context
- **Consistency**: Maintained design coherence across phases
- **Learning**: Pattern recognition and optimization over time
- **Reliability**: Predictable and repeatable workflow outcomes

## Future Enhancements

### Advanced Features (Phase 4+)
- **Semantic Search**: Vector-based context similarity matching
- **Pattern Learning**: AI-driven workflow optimization
- **Multi-Project Context**: Cross-project knowledge sharing
- **Integration APIs**: External tool context synchronization

### Enterprise Features
- **Audit Trails**: Complete context change history
- **Access Control**: Role-based context visibility
- **Backup/Recovery**: Context state backup and restoration
- **Analytics**: Context quality and usage analytics
- **Approval Integration**: Human approval status tracking and workflow control

## Approval Context Management

### Approval-Aware Context Bridging
The context persistence system integrates approval status tracking to ensure proper workflow control:

```json
{
  "context_state": {
    "approval_dependencies": [
      {
        "phase": "specification",
        "deliverable": "requirements.md",
        "approval_status": "pending",
        "required_for": "implementation_start"
      },
      {
        "phase": "specification", 
        "deliverable": "design.md",
        "approval_status": "approved",
        "approved_by": "tech_lead",
        "approved_at": "2025-01-16T14:30:00Z"
      }
    ],
    "blocked_transitions": [
      {
        "from_phase": "specification",
        "to_phase": "implementation", 
        "reason": "requirements approval pending"
      }
    ]
  }
}
```

### Context Bridge with Approval Gates
```poml
<poml>
  <role>Context bridge with approval gate validation</role>
  
  <task>
    Transfer context between workflow phases while enforcing approval gates.
    Ensure no unapproved deliverables proceed to next phase.
  </task>
  
  <document src="projects/{{project}}/specs/{{feature}}/spec.json" />
  
  <example>
    If requirements.md is not approved:
    - Block transition to implementation phase
    - Include approval reminder in context
    - Suggest approval command: "/orchestrator approve requirements for {{project}} {{feature}}"
  </example>
  
  <output-format>
    Include approval status in all context transfers:
    - List approved deliverables and their approval timestamps
    - Identify pending approvals blocking workflow progression
    - Provide specific approval commands for blocked items
  </output-format>
</poml>
```