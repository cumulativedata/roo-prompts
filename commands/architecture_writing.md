# Specification for Practical Architecture Generation

## 1. Purpose

This document instructs an AI agent on how to create a practical, implementation-focused architecture document from a system specification. The output MUST serve as a concrete blueprint for the implementation phase, anchoring all subsequent coding tasks. The document MUST NOT contain abstract or high-level theory. The total word count SHOULD remain under 1000 words to maintain focus.

The architecture document is the **prescriptive blueprint** created from specification analysis. Code implements this architecture. When specifications evolve, an AI agent should be able to update the architecture by reasoning:
- Current architecture (the current blueprint/design)
- Updated specification (new requirements)
- Architectural implications (how the blueprint must change)

The architecture captures enough structural detail about HOW things should be designed (not just WHAT should exist) to support architectural reasoning when requirements change. The agent reasons about design changes at the architecture level, then code is updated to match the revised architecture.

## 2. Output Requirements

The agent MUST produce a single `architecture.md` file containing the following sections:

0. **Metadata, Related documents**: Example of a valid metadata block:
```yaml
---
Version: 1.0
Spec Version: 1.0
Author(s): Jane Doe
Last Updated: 2023-10-27
Status: Draft
---
```

Path to the specification document. 

1.  **Key Architectural Decisions**: A list of important choices and brief justification related to the implementation architecture. These must be related to implementation of specifications and not the specifications themselves.
    *   **Decision**: Justification

2.  **Configuration and Environment**: A list of critical environment or configuration variables the system will depend on.

3.  **Core Components & Files**: A list of the primary files that will be created. For each file, provide a one-sentence description of its responsibility.

    *Example:*
    - [`main.py`](main.py): Initializes the application and starts the main processing loop.
    - [`data_processor.py`](data_processor.py): Contains logic for ingesting and transforming raw data.

4.  **Key Functions & Entry Points**: For each file listed above, define the important classes and functions that should be implemented. A function is important if:
    - It's a public API/interface used by other components
    - It requires testing (if it needs testing, it must be specified here)
    - It encapsulates a key architectural pattern or design decision
    
    For each function, specify:
    - Signature: name, arguments, return type
    - Responsibility: what it should do (one sentence)
    - **Architectural rationale**: design decisions that affect structure (e.g., "config centralized here for single source of truth", "singleton pattern for shared resource efficiency", "routing via dict for extensibility")

    *Example:*
    - In [`data_processor.py`](data_processor.py):
        - `process_data(source_path: str) -> dict`: Reads data from `source_path`, cleans it, and returns a structured dictionary. Architectural rationale: Single entry point for all data processing to enforce consistent validation pipeline.
          - **Tests**: Should handle missing files gracefully, should validate data schema, should clean malformed records, should handle empty files, should process files >1GB without memory overflow
    
    **Development Tests**: For each function, list all test scenarios that must be implemented. Focus on:
    - Critical behavior: Does it handle the core responsibility correctly?
    - Edge cases: Boundary conditions, empty inputs, invalid data
    - Error paths: Exceptions, failures, timeouts
    - Integration points: Data passed to/from other components
    - Concurrency: Thread safety, race conditions (if applicable)
    
    Each test must be concrete and specific. Avoid performative tests of trivial functionality (simple getters, basic property access). The test list should be thorough enough that implementing these tests results in confidence the component works correctly.

    Functions/classes to be used by the user are extremely important. Go into extra detail with example code of how it is expected to be used. Show implementation examples and calling examples as applicable.

    Any interface to the user is important and must be detailed. UI elements require ASCII art wireframes. CLI requires argument specifications and examples.

5.  **Testing Strategy & Success Criteria Tests**: The architecture MUST specify comprehensive testing coverage at two levels:

    **Success Criteria Tests**:
    - Review each Success Criterion (SC-X) from the spec
    - For criteria that are **measurable and testable**, define specific test scenarios:
      - Test description: What will be executed
      - Expected outcome: Measurable result that proves the criterion
      - Test type: End-to-end, integration, performance, load testing, etc.
      - Implementation notes: Key setup requirements, data fixtures needed
    - For criteria that provide **business context** (user satisfaction, insight quality, intuitive design), explain how they guide implementation without forcing artificial tests. These serve as development principles, not test cases.
    
    *Example of testable criterion:*
    - **SC-2 (Time Savings)**: Execute full analysis workflow with 60-day campaign data across 3 platforms. Measure total execution time from job creation to report delivery. Assert completion <15 minutes. Test type: End-to-end performance. Setup: Load representative campaign dataset, spawn background job, poll status endpoint until completion.
    
    *Example of guiding criterion:*
    - **SC-3 (Insight Quality)**: Reports must include qualitative reasoning and actionable recommendations. This guides development: sub-agents should generate "why it works/fails" explanations, recommendations should include owner/urgency/impact. Validated through code review and user acceptance, not automated testing.
    
    Focus test specifications on objectively measurable criteria: performance thresholds, concurrency limits, data accuracy, API contracts, error handling. Business quality criteria guide design decisions but don't require test specifications.


6.  **Lynchpin Data Models**: Describe and specify either completely or with a reasonable granularity, the most critical data structures (e.g., classes, key dictionaries) passed between major components. Document only those essential for the architecture to be clear.

7.  **Workflow to Implementation Mapping**: If any workflows / use-cases / stories / input-outputs are specified in spec, explain how each one will be met. This could be an explanation or preferably a mermaid diagram or a function call sequence. Each workflow must be unit tested. Explain briefly how the test is to be implemented. 

### Additional Instructions

- Avoid duplicating details in spec. Just refer to them (REQ id or range or heading).
- **Specify architectural structure and patterns, not just component lists**: For each component, document the design decisions that affect how the system is structured:
  - Configuration structure: WHERE settings should live and WHY (e.g., "model configs in tool definitions for centralized discovery, not scattered in agent classes")
  - Lifecycle patterns: WHEN objects should be created and WHY (e.g., "agents instantiated at startup for shared LLM client reuse")
  - Routing/wiring: HOW components should connect and WHY (e.g., "handler dict pattern for extensible tool registration")
  - State management: WHERE data should persist and WHY
  
  These design decisions enable architectural reasoning: "If spec requires X, the architecture should change to Y because of Z design principle."

## 3. Execution Process

To generate the `architecture.md` document, the agent MUST create and follow a formal TODO list based on this unified, sequential process. You must complete this TODO list one at a time: activate exactly one task, do just that, mark it done and activate the next one. Each task involves making changes and/or verbose analysis.

1.  **Analyze Workflows & Requirements**: Analyze in a verbose manner (talk out loud) all MUST-level functional and non-functional requirements from the spec. Reason about the sequence of operations, data transformations, and strategies needed to fulfill them. IMPORTANT Be thorough and very verbose.

2.  **Generate Clarification Questions**: If analysis reveals any ambiguity in requirements, workflows, or architectural constraints, formulate a specific list of questions for the user. If this is an architecture update and existing architecture exists, review it to understand current design decisions before proceeding. Do not proceed until ambiguities are resolved.

3.  **Identify Components & Data**: Analyze in a verbose manner the necessary conceptual components (which will become files/functions) and the critical "lynchpin" data structures they exchange.

4.  **Make & Log Key Decisions**: Determine in a verbose manner the primary architectural approaches and log them as **Decision** / **Justification** pairs. Be thorough.

5.  **Refine Components**: Review in a verbose manner the conceptual components with primary focus on the room for improvement. Then hypothesize improvements that Consolidate responsibilities, simplify interactions, and ensure a clean separation of concerns. This is an extremely important step.

6.  **Review Key Decisions with User**: Present the key architectural decisions and assumptions to the user for feedback. Iterate on this and do not proceed until the user approves the direction.

7.  **Create and Write `architecture.md`**: Create the `architecture.md` file. Populate the "**Core Components & Files**", "**Key Functions & Entry Points**", **Workflow & NFR Mapping** sections. For each requirement, document the concrete component interactions and strategies determined in the conceptual design phase. If needed, you may write in multiple tool uses.

8.  **Define Success Criteria Tests**: Create the "**Testing Strategy & Success Criteria Tests**" section that maps each Success Criterion from the spec (SC-1, SC-2, etc.) to specific test scenarios:
    - Test description: What will be executed
    - Expected outcome: Measurable result that proves the criterion
    - Test type: End-to-end, integration, performance, etc.
    - Implementation notes: Key setup requirements, data fixtures needed
    
    These tests validate that the system achieves its goals, not just that components function correctly in isolation.

9.  **Complete Document**: Populate the "**Lynchpin Data Models**", "**Key Decisions**", and "**Configuration and Environment**" sections. If needed, you may write in multiple tool uses.

10.  **Review for Completeness**: Read the entire `architecture.md`. Critique it against the guidelines in the documents. Pay special attention to test coverage: every critical function should have development tests listed, and every success criterion should have a concrete test scenario.

11. **Final Polish**: Correct inconsistencies, clarify descriptions, and ensure the document provides a clear, actionable blueprint. If needed, you may make fixes in multiple tool uses.


## Incremental Updates

When updating architecture based on updated spec:

1. **Diff the spec**: Identify what changed (added requirements, modified requirements, new constraints)
2. **Review current architecture**: Understand the current design blueprint and its rationale
3. **Reason about architectural changes**: For each spec change, determine how the architecture's design should evolve based on design principles and patterns already established
4. **Update architecture document**: Revise the blueprint to reflect the new design, maintaining design rationale and structural patterns for future updates

The architecture is the source of truth. Code will be updated to match the revised architecture. You don't have to follow the full execution process - dynamically write a TODO that is most appropriate for the level of change needed.