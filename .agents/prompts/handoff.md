# Inter-Agent Handoff Schema

This document defines the structured data contracts for communication between specialized agents in the product development workflow. Each agent receives a condensed handoff payload and produces a structured output for the next phase.

## Design Principles

1. Minimal Context Transfer: Only essential information passes between agents
2. Structured Data: JSON schemas enable reliable parsing
3. Artifact References: Full documents stored as files, handoffs contain summaries + paths
4. Idempotency: Any agent can be re-run with the same handoff input
5. Traceability: Each handoff includes metadata for debugging
6. Summary Freshness: Summaries in `payload` MUST be generated immediately from the source artefact at write time. The `artifactSummaryGeneratedAt` timestamp records when this was done. If the source artefact is later patched, the handoff must be regenerated before the next phase begins.

---

## Master Handoff Envelope

All inter-agent communications use this envelope structure:

```json
{
  "handoffVersion": "1.0",
  "timestamp": "ISO-8601 datetime",
  "sessionId": "uuid",
  "productName": "string",
  "productNameSlug": "string (hyphens, no spaces)",

  "sourceAgent": {
    "agentType": "orchestrator | market-research | prfaq | prd | prototype | architect | security | reviewer | project-manager | data-engineer | software-engineer | devops-engineer | quality-assurance",
    "phaseCompleted": "string",
    "executionTimeMs": "number",
    "artifactSummaryGeneratedAt": "ISO-8601 datetime — timestamp when payload summaries were extracted from source artefacts"
  },

  "targetAgent": {
    "agentType": "string",
    "phaseToExecute": "string"
  },

  "artifacts": {
    "created": [
      {
        "type": "markdown | html | json",
        "path": "relative path from project root",
        "description": "string"
      }
    ],
    "referenced": ["paths to prior artifacts this agent may need"]
  },

  "payload": {
    // Agent-specific structured data (see below)
  },

  "workflowState": {
    "executionMode": "manual | automatic",
    "phasesCompleted": ["string"],
    "phasesRemaining": ["string"],
    "progressPercentage": "number"
  }
}
```

---

## Shared Types

The following reusable types are referenced by build-phase payloads using `"...TypeName"` notation. Agents MUST expand these inline when constructing payloads.

### FeatureContext

Used by all Orchestrator -> Build Agent payloads (12, 14, 16, 18):

```json
{
  "featureId": "feature-{{FOURDIGITSNUMBER}}",
  "requirementsPath": "docs/plan/feature-{{FOURDIGITSNUMBER}}-{{FEATURENAME}}/requirements.md",
  "sourcePath": "src/"
}
```

### PhaseResult

Used by all Build Agent -> Orchestrator payloads (13, 15, 17, 19):

```json
{
  "featureId": "feature-{{FOURDIGITSNUMBER}}",
  "phase": "data | software | cicd | qa",
  "status": "passed | failed"
}
```

---

## Phase-Specific Payloads

### 1. User -> Orchestrator (Initial Input)

```json
{
  "payload": {
    "productConcept": {
      "name": "string",
      "problemStatement": "string (1-3 sentences)",
      "proposedSolution": "string (1-3 sentences)",
      "targetAudience": "string",
      "keyFeatures": ["string"],
      "uniqueValueProposition": "string"
    },
    "customerCompany": {
      "name": "string | null",
      "website": "string | null",
      "industry": "string | null"
    },
    "contextFiles": ["string"],
    "preferences": {
      "executionMode": "manual | automatic",
      "researchDepth": "quick | standard | comprehensive",
      "brandGuidelines": "string (optional)"
    }
  }
}
```

### 2. Orchestrator -> Market Research Agent

```json
{
  "payload": {
    "researchRequest": {
      "productName": "string",
      "problemStatement": "string",
      "proposedSolution": "string",
      "targetAudience": "string",
      "industryVertical": "string",
      "geographicFocus": "string | null",
      "researchDepth": "quick | standard | comprehensive"
    },
    "specificQuestions": ["string (optional specific research questions)"]
  }
}
```

### 3. Market Research Agent -> Orchestrator (for routing to PRFAQ)

```json
{
  "payload": {
    "marketResearchSummary": {
      "marketOpportunity": "string (2-3 sentences)",
      "tam": "string",
      "growthRate": "string",
      "competitivePosition": "string (2-3 sentences)",
      "topCompetitors": [
        {
          "name": "string",
          "positioning": "string",
          "pricingRange": "string"
        }
      ],
      "keyCustomerPainPoints": [
        {
          "painPoint": "string",
          "severity": "critical | high | medium"
        }
      ],
      "differentiationOpportunities": ["string"],
      "recommendedPricingPosition": "premium | mid-market | value | freemium",
      "keyRisks": ["string"]
    },
    "fullResearchPath": "docs/discovery/marketresearch-{{PRODUCT}}-{{YYYYMMDD}}.json"
  }
}
```

### 4. Orchestrator -> PRFAQ Agent

```json
{
  "payload": {
    "productContext": {
      "name": "string",
      "problemStatement": "string",
      "proposedSolution": "string",
      "targetAudience": "string",
      "keyFeatures": ["string"],
      "uniqueValueProposition": "string"
    },
    "marketContext": {
      "marketOpportunity": "string",
      "competitiveGaps": ["string"],
      "customerPainPoints": ["string"],
      "differentiationStrategy": "string"
    }
  }
}
```

### 5. PRFAQ Agent -> Orchestrator

```json
{
  "payload": {
    "prfaqSummary": {
      "headline": "string",
      "customerDefinition": "string (2-3 sentences)",
      "problemStatement": "string (2-3 sentences)",
      "solutionDescription": "string (2-3 sentences)",
      "keyCustomerBenefit": "string",
      "successMetrics": ["string"],
      "launchApproach": "string",
      "topFaqThemes": ["string"]
    },
    "workingBackwardsAnswers": {
      "whoIsCustomer": "string",
      "whatIsProblem": "string",
      "whatIsSolution": "string",
      "customerExperience": "string",
      "successDefinition": "string"
    },
    "fullPrfaqPath": "docs/discovery/prfaq-{{PRODUCT}}-{{YYYYMMDD}}.md"
  }
}
```

### 6. Orchestrator -> PRD Agent

```json
{
  "payload": {
    "prfaqContext": {
      "customerDefinition": "string",
      "problemStatement": "string",
      "solutionDescription": "string",
      "keyBenefits": ["string"],
      "successMetrics": ["string"]
    },
    "marketContext": {
      "competitors": [{ "name": "string", "positioning": "string" }],
      "pricingGuidance": "string",
      "marketSize": "string"
    },
    "userProvidedContext": {
      "teamMembers": [{ "name": "string", "role": "string" }],
      "companyInfo": "string",
      "technicalConstraints": ["string"]
    }
  }
}
```

### 7. PRD Agent -> Orchestrator

```json
{
  "payload": {
    "prdSummary": {
      "productOverview": "string (2-3 sentences)",
      "personas": [
        {
          "name": "string",
          "role": "string",
          "primaryNeed": "string",
          "goals": ["string"],
          "painPoints": ["string"],
          "dashboardWidgets": [
            "string"
          ]
        }
      ],
      "coreRequirements": [
        {
          "requirement": "string",
          "priority": "0 | 1 | 2 | 3 | 4 | 5",
          "persona": "string"
        }
      ],
      "mvpScope": ["string"],
      "successKpis": [
        {
          "metric": "string",
          "target": "string"
        }
      ],
      "businessModel": {
        "pricingTiers": ["string"],
        "revenueModel": "string"
      },
      "screensIdentified": ["string"]
    },
    "fullPrdPath": "docs/design/prd-{{PRODUCT}}-{{YYYYMMDD}}.md"
  }
}
```

### 8. Orchestrator -> Prototype Agent

```json
{
  "payload": {
    "prdContext": {
      "productOverview": "string",
      "personas": [
        {
          "name": "string",
          "role": "string",
          "primaryWorkflow": "string",
          "goals": ["string"],
          "painPoints": ["string"],
          "dashboardWidgets": ["string"]
        }
      ],
      "coreRequirements": ["string"],
      "screensToBuild": ["string"],
      "userFlows": [
        {
          "flowName": "string",
          "steps": ["string"]
        }
      ]
    },
    "designContext": {
      "brandGuidelines": "string | null",
      "existingDesignSystemPath": "string | null",
      "platformTargets": ["web", "mobile", "tablet"],
      "customerCompany": {
        "name": "string | null",
        "website": "string | null",
        "industry": "string | null"
      },
      "aestheticPreferences": {
        "direction": "string | null",
        "mood": "string | null",
        "avoid": ["string | null"]
      }
    },
    "dataContext": {
      "sampleDataFiles": ["paths"],
      "realisticDataRequirements": ["string"]
    }
  }
}
```

### 9. Prototype Agent -> Orchestrator (Final)

```json
{
  "payload": {
    "prototypeSummary": {
      "sharedCssPath": "string",
      "screenManifest": [
        {
          "id": "string",
          "filename": "string (exact filename)",
          "title": "string",
          "navLabel": "string",
          "isEntryPoint": "boolean"
        }
      ],
      "screensCreated": [
        {
          "screenName": "string",
          "path": "string",
          "primaryPersona": "string"
        }
      ],
      "userFlowsImplemented": ["string"],
      "interactiveFeatures": ["string"],
      "designSystemReferencePath": "string",
      "themeMode": "LIGHT | DARK",
      "designTokenContract": {
        "cssVariables": [
          {
            "name": "string",
            "value": "string",
            "category": "surfaces | text | brand | borders | semantic"
          }
        ],
        "componentClasses": [
          "string"
        ]
      },
      "brandAssetsVerified": {
        "logoUrl": "string | null",
        "logoGatePassed": "boolean",
        "brandColors": {
          "primary": "string (#hex)",
          "secondary": "string (#hex)",
          "accent": "string (#hex)"
        },
        "brandFonts": { "display": "string", "body": "string" }
      },
      "clickablePrototypePath": "string"
    },
    "testingReadiness": {
      "readyForUserTesting": "boolean",
      "testScenarios": ["string"],
      "knownLimitations": ["string"]
    }
  }
}
```

### 10. Orchestrator -> Project Manager (Plan)

```json
{
  "payload": {
    "prdPath": "docs/design/prd-{{PRODUCT}}-{{YYYYMMDD}}.md",
    "designDocumentPath": "docs/design/designdocument-{{PRODUCT}}-{{YYYYMMDD}}.md",
    "secureByDesignPath": "docs/design/securebydesign-{{PRODUCT}}-{{YYYYMMDD}}.md"
  }
}
```

### 11. Project Manager -> Orchestrator

```json
{
  "payload": {
    "features": [
      {
        "id": "feature-{{FOURDIGITSNUMBER}}",
        "name": "string",
        "requirementsPath": "docs/plan/feature-{{FOURDIGITSNUMBER}}-{{FEATURENAME}}/requirements.md",
        "priority": "0 | 1 | 2 | 3 | 4 | 5",
        "storyPoints": "number"
      }
    ]
  }
}
```

### 12. Orchestrator -> Data Engineer (Phase 6.1)

```json
{
  "payload": {
    "...FeatureContext": "",
    "designDocumentPath": "docs/design/designdocument-{{PRODUCT}}-{{YYYYMMDD}}.md",
    "dataRequest": {
      "entities": ["string"],
      "dataStores": ["string"],
      "migrationStrategy": "versioned | idempotent",
      "existingSchemaRefs": ["string"]
    }
  }
}
```

### 13. Data Engineer -> Orchestrator (Phase 6.1 result)

```json
{
  "payload": {
    "...PhaseResult": "",
    "dataArtifacts": {
      "schemas": [
        {
          "path": "string",
          "entity": "string",
          "description": "string"
        }
      ],
      "migrations": ["string"],
      "dtos": [
        {
          "path": "string",
          "type": "request | response | internal",
          "description": "string"
        }
      ],
      "pipelines": ["string"]
    },
    "contractVersion": "string"
  }
}
```

### 14. Orchestrator -> Software Engineer (Phase 6.2)

```json
{
  "payload": {
    "...FeatureContext": "",
    "dataLayerContext": {
      "schemas": ["string"],
      "dtos": ["string"],
      "contractVersion": "string"
    },
    "acceptanceCriteria": ["string"],
    "codingStandards": {
      "language": "string",
      "standardsPath": ".agents/standards/languages/{{LANGUAGE}}.md"
    }
  }
}
```

### 15. Software Engineer -> Orchestrator (Phase 6.2 result)

```json
{
  "payload": {
    "...PhaseResult": "",
    "softwareArtifacts": {
      "sourceFiles": ["string"],
      "unitTestFiles": ["string"],
      "entryPoints": ["string"]
    },
    "testResults": {
      "total": "number",
      "passed": "number",
      "failed": "number",
      "coverage": "string"
    },
    "dependencies": {
      "added": ["string"],
      "removed": ["string"]
    }
  }
}
```

### 16. Orchestrator -> DevOps Engineer (Phase 6.3)

```json
{
  "payload": {
    "...FeatureContext": "",
    "softwareContext": {
      "entryPoints": ["string"],
      "dependencies": ["string"]
    },
    "dataContext": {
      "dataStores": ["string"],
      "migrations": ["string"]
    },
    "infraRequest": {
      "targetEnvironments": ["string"],
      "iacTool": "terraform | cloudformation | pulumi",
      "existingInfraRefs": ["string"]
    }
  }
}
```

### 17. DevOps Engineer -> Orchestrator (Phase 6.3 result)

```json
{
  "payload": {
    "...PhaseResult": "",
    "cicdArtifacts": {
      "pipelineFiles": ["string"],
      "iacFiles": ["string"],
      "configFiles": ["string"]
    },
    "buildValidation": {
      "makeBuild": "passed | failed",
      "lintPassed": "boolean",
      "testsPassed": "boolean"
    },
    "secretsManagement": {
      "strategy": "env-vars | secrets-manager | vault",
      "secretRefs": ["string"]
    }
  }
}
```

### 18. Orchestrator -> QA Engineer (Phase 6.4)

```json
{
  "payload": {
    "...FeatureContext": "",
    "priorPhaseResults": {
      "dataArtifacts": ["string"],
      "softwareArtifacts": ["string"],
      "unitTestResults": {
        "total": "number",
        "passed": "number",
        "failed": "number"
      }
    },
    "acceptanceCriteria": ["string"],
    "testScope": {
      "integrationTests": "boolean",
      "regressionTests": "boolean",
      "edgeCases": ["string"]
    }
  }
}
```

### 19. QA Engineer -> Orchestrator (Phase 6.4 result)

```json
{
  "payload": {
    "...PhaseResult": "",
    "qaArtifacts": {
      "integrationTestFiles": ["string"],
      "regressionTestFiles": ["string"],
      "acceptanceReport": "string"
    },
    "testResults": {
      "integration": {
        "total": "number",
        "passed": "number",
        "failed": "number"
      },
      "regression": {
        "total": "number",
        "passed": "number",
        "failed": "number"
      },
      "acceptanceCoverage": {
        "criteriaTotal": "number",
        "criteriaCovered": "number",
        "gaps": ["string"]
      }
    },
    "makeTestPassed": "boolean"
  }
}
```

---

## File Storage Convention

SEE: `## File Structure After Completion` in workflow.md

---

## Handoff Validation Rules

Before sending a handoff, the source agent MUST verify:

1. Required Fields: All non-nullable fields are populated
2. Artifact Existence: All referenced file paths exist
3. Data Consistency: Names, metrics, and key facts match across payload
4. Size Limits: Payload summary fields are under 500 characters each
5. Valid Enums: All enum values match allowed options

---

## Error Handoff

If an agent fails or cannot complete its phase:

```json
{
  "payload": {
    "error": {
      "code": "RESEARCH_FAILED | GENERATION_FAILED | VALIDATION_FAILED | USER_CANCELLED",
      "message": "string",
      "partialOutput": {
        /* whatever was completed */
      },
      "recoverySuggestions": ["string"]
    }
  }
}
```

The orchestrator can then:

1. Retry the failed agent
2. Request user intervention
3. Skip to next phase with degraded input
4. Abort workflow

---

## Field Examples

| Field | Example |
| ----- | ------- |
| `tam` | $50B |
| `growthRate` | 12% CAGR |
| `sharedCssPath` | docs/design/smartsearch.css |
| `designTokenContract.cssVariables[].name` | --surface-bg |
| `designTokenContract.cssVariables[].value` | #F4F7FB |
| `designTokenContract.componentClasses[]` | .card, .stat-card, .page-content, .btn-primary |
| `dashboardWidgets[]` | Student progress charts, Admin user table |
| `storyPoints` | 1–13 (Fibonacci) |
| `dataRequest.entities[]` | User, Order, Payment |
| `dataRequest.dataStores[]` | PostgreSQL, DynamoDB, S3 |
| `contractVersion` | 1.0.0 |
| `testResults.coverage` | 85% |
| `dependencies.added[]` | express@4.18.2 |
| `infraRequest.targetEnvironments[]` | dev, staging, production |
| `contextFiles[]` | data/customers.csv, docs/brief.pdf |
