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

Inspect pipeline logs
Inspect failed pipeline stage
Compare with last successful run
Inspect recent code/test changes
Inspect dependency changes
Inspect CI/environment configuration
Search previous incidents/runbooks
Ask a human
Stop and report 
Compare local build with CI build
Check Docker image/architecture changes
Rebuild without cached Docker layers
Inspect hosting environment
Inspect server/session state
Potentially inspect the machine directly

Potential environment-level diagnostics: When CI-level evidence is insufficient, the agent may need access to deeper infrastructure diagnostics. The exact permissions and tools available to the agent will be determined during architecture design.


## Observations 

| Observation                                          | What it might suggest                          |
| -----------------------------------------------------| ---------------------------------------------- |
| Failure occurs during unit tests                     | Code bug / flaky test                          |
| Failure occurs during dependency installation        | Dependency problem                             |
| Same code passes locally but fails in CI             | Environment/config or flaky test               |
| Failure occurs only intermittently                   | Flaky test / external service / infrastructure |
| GitHub runner unavailable                            | Infrastructure                                 |
| External API times out                               | External service                               |
| Same failure happened before                         | Historical evidence for the previous diagnosis |
| Local build fails when CI build fails                | Code/build configuration problem               |
| Dependency changed compared with previous deployment | Dependency-related regression                  |
| CI/config changed recently                           | Configuration problem                          |
| Docker image/architecture changed                    | Container or architecture issue                |
| Rebuilding without cached Docker layers fixes it     | Stale/corrupted Docker cache                   |
| Hosting/server session behaves abnormally            | Infrastructure/host issue                      |
| Instance repeatedly reboots                          | Host/infrastructure failure                    |



Prior:
Our belief about each possible root cause before observing new evidence.

Likelihood:
How likely a particular observation would be if a particular root cause were actually true.

Posterior:
Our updated belief about each root cause after incorporating the observation.

The agent repeatedly updates its beliefs as it gathers new evidence.


Initial beliefs (priors) will initially be estimated from the historical CI failure dataset rather than arbitrarily assigned. A uniform-prior baseline may also be evaluated for comparison.


## likelihood

| Observation                | Code bug | Flaky test | Dependency | Env/config |     Infra | External service |
| -------------------------- | -------: | ---------: | ---------: | ---------: | --------: | ---------------: |
| Unit test failure          |     High |       High |     Medium |        Low |       Low |              Low |
| Dependency install failure |      Low |        Low |       High |     Medium |    Medium |              Low |
| Runner unavailable         | Very low |   Very low |   Very low |        Low | Very high |              Low |
| External API timeout       |      Low |     Medium |        Low |     Medium |    Medium |        Very high |


-The actual probability values are not yet assigned at this point, and they're only termed as Very High/High/Medium/Low/Very Low

-Likelihoods will primarily be estimated from the historical CI failure dataset where sufficient examples exist. Sparse categories will require smoothing, grouping, or another justified fallback rather than treating small-sample frequencies as reliable probabilities.

## Diagnostic Actions and Cost

-Inspect pipeline logs
Estimated Cost : Low
Gives error messages, can help localize the errors since logs give a detail on which part failed and helps keep track of the execution timeline if we have logs in place at each part of the code

-Inspect failed pipeline stage
Estimated Cost : Medium ( In terms of the time which an engineer spends on inspecting the pipeline)
A pipeline has different stages like build, test, debug etc, so identifying the exact part where the pipeline failed can help us get to the error faster and will save alot of time.

-Compare with last successful run
Estimated Cost : Medium ( In terms of the time which an engineer spends on inspecting the pipeline)
Mostly in times where there isn't enough evidence, comparing with the last successfull run might reveal alot of things about where the pipeline might have failed, for ex If the dependencies haven't changed, this provides evidence against a dependency-change-related cause, but it does not completely eliminate dependency problems.

-Inspect recent code/test changes
Estimated Cost : Low ( Easier for an engineer to inspect their recent changes since they are aware of what changes they made, or what changes a new commit has)
its easier to inspect the recent changes since those changes could most probably be the reason for the pipeline failure, helps identify flaky tests or code errors or parts of code which is dependent on an external service

-Inspect dependency changes
Estimated Cost : Low
dependencies can be inspected with the last versions, and any dependency errors like wrong dependencies or wrong version numbers can easy be fixed by comparing

-Inspect CI/environment configuration
Estimated Cost : Medium to High ( inspecting the environment and the configuration is a time taking process and making changes to the config is not an easy task and must be done without any errors and it is a task within itself)
sometimes it could be possible that the configuration of the pipeline or the environment is wrong, maybe the env doesn't support a dependency or a service, or maybe the config must be handling something wrong

-Search previous incidents/runbooks
Estimated Cost : Low
similar incidents which have occured in the past can be searched and if the errors are similar then the root cause can be analyzed quicker

-Ask a human
Estimated Cost : Medium to High ( Can be time consuming, and usually requires waiting till the human responsd)
asking a human could answer alot of questions since they are aware of the architecture and the behaviour of the code, and since they can do the things which are not accesible by the agent

-Report and Escalate
Estimated Cost : Low
in cases where the agent is not able to find the cause or not able to take an action, its better to stop and report to a human rather than iterating over the error over and over again

-Compare local build with CI build
Estimated Cost : Low
A difference between the local and CI builds can help distinguish code-related problems from CI/environment-specific problems.

-Check Docker image/architecture changes
Estimated Cost : Low to Medium
the failure could occur due to a false docker image and hence changes the point of action drastically, we might want to analyze the docker service

-Rebuild without cached Docker layers
Estimated Cost : Low
when cached docker images produce a failure, it could solve that issue, If stale or incorrect cached layers are suspected, rebuilding without the cache can test whether the cache is contributing to the failure. 

-Inspect hosting environment
Estimated Cost : Medium
when the other actions are all relavent, then inspecting the hosting environment is a good choice because it provides insights on the speed and the capabilities of the environment, maybe the environment needs an upgrade or maybe it isn't able to handle new code

-Inspect server/session state
Estimated Cost : Low to Medium
gives insights on the server session times, if the server times out then the probable cause is to increase the timeout speed or better state management


## Diagnostic Policy

Action prioritization: Diagnostic actions should be ordered according to the context of the failure and their expected usefulness ("hit ratio"), rather than being executed in a fixed order. The failed pipeline stage should first narrow the set of relevant actions.

## Failure Conditions

The agent may be able to localize a failure without identifying its exact root cause.