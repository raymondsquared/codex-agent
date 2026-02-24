# Principles

## General Principles

- Avoid reinventing the wheel, leverage standard patterns whenever possible.
- Embrace GitOps practices.
- Promote decoupling in design and architecture.
- Prioritise: Business > People > Process > Technology.
- First Principles: Always reason from fundamental truths rather than relying solely on analogy or convention.
- Strive for clarity over cleverness.
- Understanding the Problem Space:
  - Grasp the background and context of the problem.
  - Identify the stakeholders: who and what is involved?
  - Define goals, success metrics, and what constitutes success.
  - Assess change readiness.
  - Consider technology implications.
  - Address governance requirements.
  - Plan for effective delivery.
  - Break down work into epics and stories.
- Keep it simple.
- Don't repeat yourself (DRY).
- Only build what you need (YAGNI).
- Design for failure (Murphy's Law):
  - Use defensive programming techniques.
  - Implement self healing mechanisms.
- Always consider trade offs; there is no silver bullet.
  - Understand the CAP theorem.
- Apply the RACI model:
  - Responsible: Stakeholders involved in planning, execution, and completion.
  - Accountable: Stakeholders ultimately responsible for success or failure.
  - Consulted: Stakeholders whose opinions are sought.
  - Informed: Stakeholders kept updated as the project progresses.
- Use the MoSCoW prioritisation method:
  - Must have/do
  - Should have/do
  - Could have/do
  - Won't have/do
- Follow Discovery + Build Measure Learn
  - Discovery with design thinking
  - Build
    - Plan
    - Code
    - Build
    - Test
    - Release
    - Operate
    - Monitor
  - Measure
  - Learn
- Employ domain driven modelling.
- Maintain separation of concerns.
- Embrace Software Quality Attributes.
- Practise user-centred design.
- Fail fast and learn faster.
- Evaluate build versus buy options.
  - Managed services over DIY

## UI/UX Principles

- Build screens must link together; every button/link navigates to the correct screen.
- Brand assets (required for known companies):
  - For known companies: Fetch and use their actual logo, brand colours, and typography.
  - When building for a recognisable company (Discovery Education, Amazon, Google, etc.):
    - 1.  SEARCH for their logo now, do not skip this step:
      - Search: "{{COMPANY_NAME}} logo png", "{{COMPANY_NAME}} press kit", "{{COMPANY_NAME}} Wikipedia"
      - Check: company press page, Wikipedia, LinkedIn, Brandfetch.com
    - 2.  Verify the logo URL works using curl:

      ```bash
      curl -sI "{{LOGO_URL}}" | head -5
      ```

      - Check for `HTTP/2 200` or `HTTP/1.1 200 OK`.
      - If it shows 404/403/error, try another URL.
      - Keep trying until you find a working URL (200 OK).
      - Do not use a URL you haven't verified with curl.

    - 3.  Extract brand colours, visit their website and use dev tools to get exact hex values.

    - 4.  Identify typography, their fonts or the closest Google Fonts match.

    - 5.  Embed verified logo in outputs:
      - Market Research: `<img src="{{VERIFIED_URL}}">` in "Brand Assets" section.
      - Design System: Use their colours as CSS variables.
      - Build: Show logo in header, login screens, and footer.

  - Logo verification loop: Try URL -> fetch fails? -> try next URL -> repeat until success.

- Fully interactive builds (required):
  - All buttons/links navigate to correct screens.
  - Chat interfaces: typing indicator and simulated responses after 1-2s delay.
  - Forms: validation, loading states, success/error feedback.
  - Dropdowns/selects: open, select, close.
  - Modals: open on trigger, close on X/backdrop/Escape.
  - Data tables: sort, filter, paginate.

## Security Principles

- Least privilege: Grant only the minimum access required
- Zero Trust: Never trust, always verify - authenticate and authorise every request
- Secure by design: Build security in from the start, not bolted on after
- Defence in depth: Layer multiple controls so no single failure compromises the system
- Threat modelling: Identify and mitigate risks early using frameworks like STRIDE
- Shift left on security: Integrate security checks into CI/CD pipelines
- Encrypt in transit and at rest
- Secrets management: Never hardcode credentials; use vaults or environment injection
- Audit and observability: Log security events for detection and forensics
- Dependency hygiene: Regularly scan and update third party libraries (SCA)
- Incident response readiness: Have a plan before you need one

## Architecture Principles

- Why is more important than how, everything is a trade-off
- Well-Architected Frameworks:
  - Operational excellence
  - Security
  - Reliability
  - Performance efficiency
  - Cost optimisation
  - Sustainability
- Evolutionary architecture: Design for incremental, guided change across multiple dimensions
- Fitness functions: Use automated checks to protect architectural characteristics over time
- Loose coupling, high cohesion: Minimise cross boundary dependencies; maximise internal relatedness
- Follow Enterprise Integration Patterns (Hohpe & Woolf); name the pattern in comments or class/function names
- API-first design: Define contracts before implementation; prefer backward-compatible changes
- Event-driven where appropriate: Favour asynchronous messaging for decoupled, scalable systems
- Favour managed services over self hosted infrastructure

## Engineering Principles

- Follow design patterns as specified by the Gang of Four. Make it obvious in the comment or class/function name when you use them.
  - Creational
    - Abstract Factory
    - Builder
    - Factory Method
    - Prototype
    - Singleton
  - Structural
    - Adapter
    - Bridge
    - Composite
    - Decorator
    - Facade
    - Flyweight
    - Proxy
  - Behavioural
    - Chain of Responsibility
    - Command
    - Interpreter
    - Iterator
    - Mediator
    - Memento
    - Observer
    - State
    - Strategy
    - Template Method
    - Visitor

- Composition over inheritance
- SOLID
  – Single Responsibility
  – Open/Closed
  – Liskov Substitution
  – Interface Segregation
  – Dependency Inversion
- The Twelve Factor App
  - Codebase: One codebase tracked in version control, many deploys
  - Dependencies: Explicitly declare and isolate dependencies
  - Config: Store configuration in the environment
  - Backing Services: Treat backing services as attached resources
  - Build, Release, Run: Strictly separate build and run stages
  - Processes: Execute the app as one or more stateless processes
  - Port Binding: Export services via port binding
  - Concurrency: Scale out via the process model
  - Disposability: Maximise robustness with fast startup and graceful shutdown
  - Dev/Prod Parity: Keep development, staging, and production as similar as possible
  - Logs: Treat logs as event streams
  - Admin Processes: Run admin/management tasks as one off processes
- High cohesion
- Low coupling
- Elasticity & scalability
- Fault tolerance
- Idempotency
- Database over service
- Separate transactional from analytics
- Follow trunk based development when possible
- Always have a zero value or default value, not null
- Behaviour Driven Development
- Software Bill of Material
- Contract Driven Development when possible

## Infrastructure Principles

- Automation first
- Infrastructure as Code
  - Single source of truth
- Shift left
- Observability by default
- Design for failure
- The Shared Responsibility Model
- Ephemeral infrastructure
- Immutable infrastructure
- Use Makefile; keep it agnostic to any framework or programming language
- Cloud first
- The Go Live Day 1 Strategy
- Cloud native
- Apply the 7R Migration framework when needed.
  - Rehost (Lift and Shift)
  - Replatform (Lift, Tinker, and Shift)
  - Repurchase (Move to a different product/SaaS)
  - Refactor / Re architect (Redesign for cloud)
  - Retire (Decommission)
  - Retain (Keep as is, usually temporarily)
  - Relocate (Move to another cloud or region)

## Data & AI Principles

- Garbage in, garbage out
- Privacy by design
- Data governance
- Data lineage
- Star schema
- ACID data lake
- Medallion
- Data as a product
- MLOps
- Einstein Trust Layer:
  - Source
  - Input flow
    - Secure Data Retrieval & Grounding
    - Prompt Injection Detection
    - Toxicity Detection
    - Data Masking
  - Models
    - LLM Gateway
    - Zero Data Retention
    - Hosted Models in Salesforce Trust Boundary
    - Bring Your Own Models on Your Infrastructure
    - External Models with Shared Trust Boundary
  - Output flow
    - Toxicity Detection
    - Data Demasking
    - Audit Trail and Feedback
- Schema enforcement
  - Immutable raw layer
- The columnar efficiency principle
- Partition for query efficiency
- Avoid millions of tiny files for analysis

## Testing Principles

- Test Driven Development (TDD) is encouraged: Write tests before implementing code to clarify requirements and drive design.
- Prefer simple, deterministic tests.
- Test both positive and negative scenarios.
- Use clear, descriptive test names.
- Measure code coverage, but don't chase 100%.
- Review and refactor tests regularly.

## Project Management Principles

- Communicate early, clearly, and often with all stakeholders.
- Break work into small, manageable tasks with clear owners.
- Regularly review progress and adapt plans as needed.
- Document decisions, changes, and lessons learned.
- Encourage continuous improvement and feedback.
- Set clear priorities and avoid scope creep.
- For requirement descriptions, use a clear user story format:
  - "As a {{PERSONA}}, I want {{FEATURE}}, so that {{REASON_BENEFIT}}."
- Use clear, actionable requirements and acceptance criteria.
- Write requirements using the EARS (Easy Approach to Requirements Syntax) pattern.
