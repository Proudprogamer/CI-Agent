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



### Research Question

This CI Agent analyses the root cause of a CI pipeline failure by analysing the most probable causes depending upon the evidence it receives; the agent chooses an action based on the type of failure and the highly suggested causes by comparing the failure with historic evidence. Every action depends on a context rather than a fixed set of instructions.


### Hidden States


1. Code bug :
  Why is it a distinct state : This state depends on the changes in code which could have caused the failure, and this is a good base point to start our agent's investigation, and this state could give us more certainty about where the error could lie, because if the error is not in the code, then most probably(there is still probability that the error is with the code ) the error is with something else like a flaky test or dependencies, etc.

2. Test failure / flaky test : 
  Why is it a distinct state : flaky tests can help recude the uncertainty by a big factor since are actions will be narrowed down to mostly around tests.

3. Dependency issue : 
  Why is it a distinct state : dependency issues could be because of newer versions in the dependencies, and they are different from code bugs, because we never know when dependency issues could happen, a dependency could be depricated or removed, etc, hence these issues also help us narrow down our search space

4. CI/environment configuration :
  Why is it a distinct state : Sometimes the CI environment like github actions or jenkins could have the wrong configuration which does not support your current version of the code, maybe hardware config or other sorts of config, and this issue can help narrow down the space by lot ,because now our search space mostly shifts to the cloud platform, but config issues can occur locally aswell when we change a config in the code which is not suitable for our pipeline.

5. Infrastructure : 
  Why is it a distinct state : these issues could relate to Docker files and docker images, and also server level issues, it helps narrow down the actions to docker level actions and Cloud provider actions which could be more probable than others.

6. External service : 
  Why is it a distinct state : External services include the api's we use in our codebase, or maybe database services which we use, these external services could have their downtime which could cause us the error, so the most probable actions would be inspecting the code and adding logs where the external service is called, and we could also check if they have a downtime going on or not, this could still relate to the config error, if the db urls are not whitelisted with the provider, etc, etc


### Observable Evidence 

Failed pipeline stage - Identifies at what stage the failure occured and helps narrow down the actions to that particular stage.

Pipeline logs / error messages - Identifies at which exact part the pipeline failed.

Test results - helps identify the results of every test and helps the agent understand which test went wrong

Recent code/test changes - helps the agent compare the code to the previous code and check if any new changes caused the failure, and helps increase/decrease the certainty of a code error

Dependency changes - helps the agent identify if the dependencies have an error, maybe the existing dependencies got depricated, or maybe the version number was wrong, helps narrow down to dependency checks and environment checks.

CI/environment configuration changes - sometimes the pipeline config could be the reason, maybe the config does not support a particular dependency or maybe an external service timed out, etc, or maybe if there are config differences in the local env and in the pipeline config

Difference from the last successful run - helps directly compare the config, code, dependencies, tests, with the last sucessfull run and helps narrow down the possible cause 

Local build result vs CI build result - if the local build passes and CI build fails, maybe there could be a config error or maybe docker error

Docker image/cache changes - helps identify if the docker file has a problem, maybe the docker file could be referring to an old package.json file which could not be present in the lastest commit, or maybe the docker cached images could cause the problem so the images can be rebuilt

Previous similar incidents / runbooks - helps the diagnostic policy decide while choosing an action, if there was a prior similar kind of error, then the decision making could rank the possible actions to be taken

Hosting/infrastructure information - the cloud provider could run into a problem, maybe the cloud is down, etc.

Server/session state - the server could be occupied with another service which might not be configured properly and could effect the pipeline, or maybe the server does not have enough hardware to run the new commits, etc



### Evidence → Hidden State Relationships (Qualitative evidence-state hypotheses)

↑↑ strongly increases likelihood
↑ moderately increases likelihood
→ little/no direct evidence
↓ decreases likelihood
↓↓ strongly decreases likelihood
C = conditional; depends on the specific observation


| Observable Evidence                         | Code Bug | Test / Flaky Test | Dependency | CI / Config | Infrastructure | External Service |
| ------------------------------------------- | :------: | :---------------: | :--------: | :---------: | :------------: | :--------------: |
| **Failed pipeline stage**                   |     C    |         C         |      C     |      C      |        C       |         C        |
| **Pipeline logs / error messages**          |     C    |         C         |      C     |      C      |        C       |         C        |
| **Test results**                            |     ↑    |         ↑↑        |      ↑     |      →      |        ↑       |         ↑        |
| **Recent code/test changes**                |    ↑↑    |         ↑↑        |      C     |      C      |        →       |         ↑        |
| **Dependency changes**                      |     ↑    |         →         |     ↑↑     |      ↑      |        →       |         ↑        |
| **CI/environment configuration changes**    |     ↑    |         ↑         |      ↑     |      ↑↑     |       ↑↑       |         ↑        |
| **Difference from last successful run**     |     C    |         C         |      C     |      C      |        C       |         C        |
| **Local build succeeds, CI fails**          |     ↓    |         ↑         |      ↑     |      ↑↑     |       ↑↑       |         ↑        |
| **Docker image/cache changes**              |     C    |         C         |      ↑     |      ↑↑     |       ↑↑       |         →        |
| **Previous similar incidents / runbooks**   |     C    |         C         |      C     |      C      |        C       |         C        |
| **Hosting / infrastructure information**    |     →    |         ↑         |      →     |      ↑      |       ↑↑       |         ↑        |
| **Server / session state**                  |     →    |         ↑         |      →     |      ↑      |       ↑↑       |         ↑        |
| **External-service error/timeout evidence** |     →    |         ↑         |      ↑     |      ↑      |        ↑       |        ↑↑        |


### Prior Probability Strategy

Prior probability strategy: Use contextual priors rather than one generic prior. The context for the prior should come from information available before analyzing the current failure's diagnostic evidence, such as the type of pipeline/stage and historical failure distributions.

Historical evidence: Historical CI failure data should be used to estimate the prior probabilities and potentially stage-specific priors.

Current failure logs: Information extracted from the current failure logs should not set the prior. It becomes evidence that is used by the likelihood model to update the prior into posterior probabilities.

Numerical values: Ideally derive them from labeled historical CI failure data rather than arbitrarily choosing percentages.

Comparison: We should test multiple prior strategies—such as a generic prior, stage-specific prior, and empirically derived prior—to determine how sensitive diagnostic performance is to the choice of prior.


### Prior Strategies

1. Uniform prior
   Equal probability assigned to every hidden state.
   Used as a simple baseline.

2. Global empirical prior
   Probabilities estimated from the overall distribution of historical
   CI failure causes.

3. Stage-specific empirical prior
   Probabilities estimated separately for each pipeline failure stage.
   The failed stage determines which prior distribution is used.

### Prior Strategy Comparison

We will compare the three prior strategies to determine whether
context-specific historical priors improve diagnostic performance
over a uniform or global prior.



## Dataset Evaluation

V1 uses a strict label-mapping strategy. Only dataset categories that can be mapped clearly to the predefined hidden states are included in the quantitative experiment. This results in 305 usable cases out of 375. Categories involving quality checks, vulnerabilities, network failures, version control, GitHub-specific limitations, unresolved actions, and performance degradation are excluded from V1 because they do not map cleanly to the current state space.

Although Infrastructure and External Service have valid mappings, each contains only two cases in the resulting dataset. Therefore, these states are considered data-sparse and will not be used for reliable quantitative probability estimation in V1. The primary quantitative experiment will focus on Code, Test/Flaky Test, Dependency, and CI/Configuration. Infrastructure and External Service remain part of the conceptual architecture and may be evaluated in a future expanded dataset.

### Dataset: replication-labeling.xlsx

The dataset contains 375 labeled CI failure jobs. It provides root-cause descriptions, sub-categories, broader categories, pipeline steps, and associated actions/scripts.

The dataset appears suitable for the project because it provides labeled CI failures that can potentially be mapped to the hidden states used by the Bayesian diagnosis model.

However, the dataset's 16 sub-categories do not map perfectly to our initial six hidden states. Categories such as quality-check failures, security/vulnerability failures, performance degradation, network issues, GitHub API limits, and third-party service failures require further analysis before deciding whether they should be mapped to an existing state, treated as separate states, or excluded from the first experiment.

### Dataset: replication-surveyAnswers.xlsx

The survey contains 152 engineer responses concerning the frequency and experience of different CI failure categories.

The survey will be treated as supporting human evidence rather than direct ground-truth frequency data. Reported frequency of experiencing a failure type cannot directly be interpreted as the actual proportion of CI failures caused by that category.

### Dataset Decision

The labeling dataset will be evaluated as the primary dataset for estimating the Bayesian model's priors and likelihoods. Before assigning probabilities, the failure categories will be mapped to the model's hidden states and the number of usable examples in each state will be calculated.

No probabilities will be assigned until this mapping has been completed.


### V1 Empirical Prior

After applying the strict hidden-state mapping, 305 of the 375 labeled
failure cases were retained for the quantitative experiment.

The empirical prior was calculated from the frequency of each retained
hidden state:

| Hidden state      |   Cases | Empirical prior |
| ----------------- | ------: | --------------: |
| Code bug          |      54 |      **17.70%** |
| Test / flaky test |     120 |      **39.34%** |
| Dependency        |      91 |      **29.84%** |
| CI / Config       |      40 |      **13.11%** |
| **Total**         | **305** |        **100%** |


These probabilities represent the distribution of root causes within
the included subset of the replication dataset and should not be
interpreted as universal CI failure probabilities.


### Stage Mapping

The dataset does not provide a standardized pipeline-stage field.
Therefore, V1 derives stages from the pipeline step names using
high-confidence mappings.

Steps explicitly associated with build, test, setup/dependency,
quality/analysis, or deployment/publishing are assigned to the
corresponding stage.

Steps containing multiple activities or ambiguous terminology are
classified as multi-stage/ambiguous and are excluded from the
stage-specific prior calculation rather than being assigned
arbitrarily.

The resulting stage × hidden-state distribution will be inspected
for sufficient sample sizes before stage-specific priors are used.

### Stage-Specific Prior Analysis

The dataset does not provide a standardized pipeline-stage label, so
high-confidence stages were derived from pipeline step names. Ambiguous
and unknown steps were excluded from the stage-specific prior analysis.

Among the four V1 hidden states, the resulting high-confidence
stage/root-cause counts were:

- Build: 113 cases
- Test: 100 cases
- Setup/Dependency: 30 cases
- Quality/Analysis: 9 cases
- Deploy/Publish: 7 cases

Build and Test contain sufficient examples to support stage-specific
prior estimation. Quality/Analysis and Deploy/Publish are too sparse
for reliable stage-specific priors.

The V1 experiment will therefore compare the global empirical prior
against stage-specific priors for Build and Test failures. A global
prior will serve as the fallback when a sufficiently supported
stage-specific prior is unavailable.



### Likelihood Model

The V1 model represents `failed_stage` as a single categorical
observable variable.

The possible values are:

- Build
- Test
- Setup / Dependency
- Quality / Analysis
- Deploy / Publish
- Ambiguous
- Unknown

Ambiguous and Unknown are retained as valid evidence categories rather
than being removed from the likelihood calculation. This prevents the
model from assuming that the failed stage will always be identifiable.

For each hidden state, the likelihood is estimated as:

P(failed_stage | hidden_state)

using the labeled dataset.

The resulting likelihood distribution is:

| Root cause ↓ / Failed stage → | Build |  Test | Setup | Quality | Deploy | Ambiguous | Unknown |
| ----------------------------- | ----: | ----: | ----: | ------: | -----: | --------: | ------: |
| **Code**                      | 57.4% | 18.5% |  7.4% |    1.9% |     0% |      7.4% |    7.4% |
| **Test**                      | 35.8% | 53.3% |    0% |    5.8% |     0% |      3.3% |    1.7% |
| **Dependency**                | 42.9% | 13.2% | 17.6% |    5.5% |   3.3% |      7.7% |    9.9% |
| **CI / Config**               | 27.5% | 10.0% | 12.5% |    5.0% |   5.0% |     15.0% |   25.0% |


These likelihoods are empirical estimates derived from the labeled
dataset and should not be interpreted as universal CI failure
statistics.

### Decision: Retain Ambiguous and Unknown Stages

Ambiguous and Unknown stage classifications are retained in the
likelihood model because they represent valid observations that the
diagnostic agent may encounter. Removing them would artificially
increase the apparent certainty of the stage evidence and discard
information from the dataset.


### Bayesian Model Sanity Check

Let us try to calculate the posterior probability with what we currently have
 
Prior : 
| Root cause |  Prior |
| ---------- | -----: |
| Code       | 17.70% |
| Test       | 39.34% |
| Dependency | 29.84% |
| CI/Config  | 13.11% |

Observed evidence : failed_stage = Build

Likelihoods : 
| Root cause | `P(Build \| cause)` |
| ---------- | ------------------: |
| Code       |               57.4% |
| Test       |               35.8% |
| Dependency |               42.9% |
| CI/Config  |               27.5% |


Bayesian updating is done using this formula : prior x likelihood 

Code:
0.1770 × 0.574 = 0.1016

Test:
0.3934 × 0.358 = 0.1408

Dependency:
0.2984 × 0.429 = 0.1280

CI/Config:
0.1311 × 0.275 = 0.0360

now we normalize and then calculate posterior probabilities 

Posterior probabilities : 
| Root cause     | Posterior after `Build` |
| -------------- | ----------------------: |
| **Code**       |               **25.0%** |
| **Test**       |               **34.6%** |
| **Dependency** |               **31.5%** |
| **CI/Config**  |                **8.9%** |


As we can clearly observe, after the evidence arrived, our probabilities have changed to :

Test        34.6%  ↓
Dependency  31.5%  ↑
Code        25.0%  ↑
CI/Config    8.9%  ↓

Although we could not reduce or increase probabilities by a major factor but now the policy will be able to choose the action based on the probability ranking

This is a very high level sanity check since we're only using the failure stage as the evidence variable, future versions of our agent will have multiple evidence variables with a proper structure.



### V1 Bayesian Model Scope 

For the initial quantitative experiment, the model will use
`failed_stage` as the primary observable evidence variable because
this is the evidence that can be reliably derived from the available
dataset.

Additional evidence variables will be incorporated in future versions
when datasets containing those observations are available.

### V1 Evidence Model Scope

Using only the failed pipeline stage as evidence is insufficient for
the intended diagnostic agent because the stage provides coarse
localization but does not provide enough information to distinguish
between multiple plausible root causes.

The failed stage will therefore remain as the first empirically
validated evidence variable, but the final agent will incorporate
additional evidence variables such as structured error patterns,
test results, dependency changes, configuration changes, comparisons
with previous successful runs, local-vs-CI results, Docker changes,
and infrastructure signals.

The current dataset does not contain sufficient information to
estimate likelihoods for most of these variables. Additional data
sources will therefore be investigated before finalizing the
Bayesian network and its likelihood parameters.

Dataset limitation and evaluation strategy:
No single publicly available dataset was identified that provides all required observable evidence variables together with reliable root-cause labels. The available replication dataset provides sufficient labeled failures to empirically estimate the initial prior and failed-stage likelihood model, but does not contain raw logs, change history, dependency changes, Docker state, or infrastructure state. Rather than forcing unrelated datasets into a single model, the project will use the labeled dataset for empirically grounded components and construct controlled diagnostic scenarios from real failure cases to evaluate richer evidence variables and the diagnostic policy. Synthetic evidence will be explicitly distinguished from observations directly obtained from the dataset.



### Policy under incomplete EIG information

The diagnostic policy ranks relevant actions based on their expected
information gain relative to their diagnostic cost.

When sufficient historical data is available to estimate the EIG of an
action, the action is ranked using:

Score(action) = EIG(action) / Cost(action)

When sufficient historical data is not available to estimate the EIG of
an action, the action is not discarded. Instead, the policy ranks such
actions according to their cost, preferring lower-cost actions first.

This is appropriate for the current CI/CD setting because the primary
cost represents diagnostic time and effort rather than a fixed monetary
budget. A low-cost action with unknown information value can therefore
be investigated with relatively low downside, while a high-cost action
with unknown information value represents a greater risk of wasting
diagnostic effort.

The absence of an EIG estimate does not affect the stopping criterion.
The stopping criterion is based on the resulting belief/uncertainty
after diagnostic actions have been considered and their feedback has
been incorporated. If sufficient confidence is not achieved after the
useful available diagnostic actions have been exhausted, the case is
escalated to a human.


## Dataset for evaluation of the V1 Agent

The dataset does not directly record diagnostic action outcomes. Therefore, P(o∣H,E,a) was estimated using action-specific observable proxies derived from historical step and sub-category fields. Actions with weak proxies (e.g. comparison with a successful run, server/session state) have lower evidential validity. These probabilities are used for simulation, not claimed as directly observed action outcomes.