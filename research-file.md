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

-How should the agent decide which diagnostic action to take next?

At any point of time, the agent has the following : 
1.failure
2.prior probabilities
3.likelihood model
4.evidence ( it may or may not have evidence )
5.posterior probabilities ( depending upon the baseline the agent has in case there is no evidence)

since the agent has a bunch of diagnostic actions it can take, depending upon the failure, we can narrow down the possible set of diagnostic actions which our agent can take, for ex - if we could identify from the logs that it is a dependency related issue then it is more likely that its related to the actions: a. check dependencies with last green run, b. check CI config and environment, etc; the agent can also look at historic data to see which kind of actions have helped when a similar kind of error occured.

in case the agent is not able to narrow down to a set of actions, then it can use the historic evidence to find out what kind of actions have worked for this kind of failure in the past

once the agent has narrowed down the set of diagnostic actions which are suitable for the current failure, now the agent chooses one action with the highest hit ratio, the one which gives us the maximum information at ideally a low cost; just because an action might give us more clarity, we cannot choose it if it is high cost, we have to keep the cost in mind and choose the one with low cost and best information for its cost.

after performing a set of action and getting the feedback ,the agent should be able to narrow down the root causes of the failure, and if it isn't able to narrow it down and if the uncertainity is still high( when it doesn't have the right evidence ) then it should escalate to a human rather than iterating over and over again.


## Failure Conditions

The agent may be able to localize a failure without identifying its exact root cause.



## Agent Architecture 

### VERSION 1

-Our agent consists of 6 main components which are
        CI Failure
            ↓
        Evidence Collector
            ↓
        Belief Engine
            ↓
        Diagnostic Policy
            ↓
        Diagnostic Actions
            ↓
            ↓
        Feedback
            ↺
        Belief Engine

            ↓
       Human Escalation

- Evidence Collector : This component collects evidences from the failed pipeline, it inspects the logs and previous historic data to find out what could be the probable cause, this is a very crucial component as it sets the context for the rest of the agent.

- Belief Engine : This is the probabilistic model of the agent, the agent assigns probabilities to the possible set of causes based on the evidence it receives, the diagnostic actions depend upon this engine as this gives us a set of highliy probable causes for the agent to inspect further

-Diagnostic Policy - this is the part which defines the set of rules on what action is to be done among all the possible set of actions, it has rules for deciding which action has more information for the given cost, and it tries to choose an action which has the highest ratio of information gain with respect to the price, very crucial step because it helps narrow down the actions by choosing the most useful action.

-Diagnostic Actions - the set of actions with ranking according to their usefulness, the agent can perform these actions.

-Feedback - after the agent performs an action ,the result of the action is considered as feedback, and this feedback is fed into the belief engine in order to update probabilities based on the feedback, this is also very crucial as it helps narraow down the set of possible causes of the failure.

-Human Escalation - Even after performing a set of actions and using feedbacks, if there is still high uncertainity about the root cause, then the agent escalates the problem for a human evaluation since multiple iterations have not helped in reducing the uncertainity and further actions also result in high uncertainity


Now lets see where the LLM fits in our architecture in Version 2.


### VERSION 2 - With LLM

        CI failure
            ↓
        Evidence Collector
            ↓
           LLM
            ↓
        Structured evidence
            ↓
        Belief Engine
            ↓
        Diagnostic Policy
            ↓
        Diagnostic Action
            ↓
        Feedback
            ↺


| Component             | Responsibility                        |
| --------------------- | ------------------------------------- |
| **LLM**               | Interpret messy/unstructured evidence |
| **Belief Engine**     | Maintain/update probabilities         |
| **Diagnostic Policy** | Decide the best next action           |
| **Diagnostic Tools**  | Execute the action                    |
| **Feedback**          | Return the action's result            |


Note - i dont want the LLM to tell me -"The root cause is xyz because i identified abc in the logs", i want the LLM to structure the logs so that our agent can analyze it better, we already have the bayesian network to give us the probabilities, we dont want the root cause from the LLM





