## Problem Statement : 
An agent which identifies the cause of a CI pipeline failure and performs necessary tests to identify the root cause of the failure.


## Technical Terms

* **CI/CD:** Continuous Integration and Continuous Delivery/Deployment.
* **Root Cause Analysis (RCA):** The process of identifying the underlying cause of a CI failure.
* **Fault Diagnosis:** Identifying the underlying fault responsible for an observed failure. Basically identifying all the possible realities.
* **Diagnostic Action:** An action the agent takes to obtain more information about a failure, such as rerunning a test or inspecting dependencies.
* **Hidden State:** The actual cause of the CI failure that is not directly observable by the agent.
* **Uncertainty:** The agent's lack of certainty about which hidden state is responsible for the failure.
* **Belief:** The probability assigned to each possible hidden state.
* **Prior Probability:** The belief about possible causes before considering the evidence from the current case.
* **Posterior Probability:** The updated belief after considering new evidence.
* **Diagnostic Policy:** The decision rule used by the agent to select its next diagnostic action.


## Search Queries

### CI Failure Diagnosis

* "how engineers diagnose CI pipeline failures"
* "common causes of CI pipeline failures"
* "software CI failure troubleshooting methods"

### Automated Diagnosis

* "automated root cause analysis for CI failures"
* "automated fault diagnosis software failures"
* "machine learning for software failure diagnosis"

### Diagnostic Decision Making

* "selecting next diagnostic action under uncertainty"
* "sequential diagnosis decision making"
* "active diagnosis software systems"

### AI and CI

* "AI agent for CI failure diagnosis"
* "LLM based CI failure diagnosis"
* "AI assisted software debugging and root cause analysis"


## Verified Reddit Communities

1. **r/devops** — Relevant for CI/CD pipelines, infrastructure, deployment failures, and practical troubleshooting.

2. **r/softwaretesting** — Relevant for test failures, flaky tests, test reliability, and debugging.

3. **r/programming** — Relevant for broader software engineering and debugging discussions.

4. **r/ExperiencedDevs** — Relevant for practical experiences and approaches used by professional developers when diagnosing software problems.

5. **r/QualityAssurance** — Relevant for software testing, quality assurance, and understanding different causes of test failures and how to solve problems while keeping quality in mind.

These communities will be used to gather human perspectives about CI failure diagnosis, including hidden failure causes, diagnostic actions, decision costs, evidence, and situations where human intervention may be necessary.

## Root causes

1. Code / application bug
2. Test failure / flaky test
3. Dependency problem
4. CI environment / configuration problem
5. Infrastructure problem
6. External service failure


## Diagnostic Actions

A1: Inspect pipeline logs
A2: Inspect failed pipeline stage
A3: Compare with last successful run
A4: Inspect recent code/test changes
A5: Inspect dependency changes
A6: Inspect CI/environment configuration
A7: Search previous incidents/runbooks
A8: Reproduce the failure
A9: Ask a human
A10: Stop and report 



