### Purpose
- This file descirbese the Probability Decision Record of the the v1 agent

- We will get the answers to the questions like  : why did the agent choose this action ? what evidence did the agent have at this point of time ? what was the feedback the agent got after implementing the action ? and so on...

- This file describes all the variables like Hidden states, prior probabilities, posterior probabilities, feedback, etc, and in cases of failure, this information can be used to analyze where exactly the agent failed, along with the timestamp.


### CASE 1 - The Agent was confident towards the true root cause

- We will be considering the Case 2 from the 40 evaluation cases which our agent was tested upon.

## Initial Evidence observed by the Agent 

- The failed pipeline stage was **Build**.
- The specific failed step was **Build with Ant and Ivy**.

## Hidden States 

- **Code** — The failure is caused by a recent code change.
- **Test** — The failure is caused by a test-related issue.
- **Dependency** — The failure is caused by a dependency or dependency-related change.
- **CI/Config** — The failure is caused by a CI, build, or environment configuration issue.

- All these hidden state represent a probability of the root cause, and the agent is not aware of the true root cause yet, it only has some amount of belief that these might be the root causes.

## Prior Probability / Beliefs 

- Before the agent observes new evidence, there is a certain amount of belief which the agent has towards each hidden state, for our agent this initial beliefs are empirical and they have been calculated from the Historical data of the 375 data records.

- The prior probabilities of the Agent towards each hidden state is : 

**Code** - 17.7%,
**Test**: - 39.34%,
**Dependency**: - 29.84%,
**CI/Config**: - 13.11%

- The sum of these probabilites is 100 %

- These are the prior probabilities which the agent has before a new evidence has even arrived, these have to be caluclated precisely according to the dataset which is being used because there can be cases where the dataset is too sparse for a hidden state and that may lead to unreliable or biased initial beliefs, so it is important to consider a dataset which has a good representation of all hidden states.

## Event

- The event of interest is determining which hidden state is the most probable root cause of the observed CI failure.

- For this case, the agent must determine whether the failure is most likely caused by:

- Code
- Test
- Dependency
- CI/Config

The objective of the decision is to identify a root cause with sufficient probability to justify reporting it.

## Actions

- After the agent has observed the evidence, it chooses a set of actions which are related to the failed stage so that it can perform those actions to increase or decrease its uncertainty over the possible hidden states.

- The agent chose the following Actions which it can execute (Note - It hasn't executed these actions yet.)

1. Inspect pipeline logs
2. Inspect failed pipeline stage
3. Compare with last successful run
4. Inspect recent code/test changes
5. Inspect dependency changes
6. Inspect CI/environment configuration
7. Search previous incidents/runbooks
8. Inspect Docker image/cache changes
9. Inspect server/session state

## Costs

- The cost is considered in terms of the time it takes for the diagnostic action to complete

1. Inspect pipeline logs - 1
2. Inspect failed pipeline stage - 2
3. Compare with last successful run - 2
4. Inspect recent code/test changes - 1
5. Inspect dependency changes - 1
6. Inspect CI/environment configuration - 2.5
7. Search previous incidents/runbooks - 1
8. Inspect Docker image/cache changes - 1.5
9. Inspect server/session state - 1.5

(Note - The costs of the actions are represented qualitatively where a cost of 1 represents a relatively low diagnostic effort, while higher values represent greater expected effort.)

Correct root-cause report
→ useful diagnosis - In cases of useful diagnosis where the agent was able to decrease the entropy among the hidden states, the time spent to perform the actions doesn't go waste because it leads us to a useful certainty 

Incorrect root-cause report
→ wrong diagnosis - In case of wrong diagnosis where the agent could not converge to the true root cause, the time spent here goes waste because the agent did not suggest the true root cause and this could result in confusion to the human reviewer and they have to review all the failures manually again which could be a tedious task. This kind of reports are very expensive.

Escalation
→ human review is required - This action may or may not be efficient, because in cases where the agent does not have a strong evidence, or when its still very uncertain among the hidden states, its more efficient to escalate to a human rather than wasting time by performing actions which do not give a considerable change in the certainty of the hidden states.

## Policy

- The agent follows the EIG ( Expected information gain ) in order to rank the actions and perform the best actions first
- The policy calculates the EIG / Cost ratio for all the actions and then starts executing the actions with the highest ratio first

- The policy is reponsible for 3 tasks, which are : 

1. Action-selection rule
The agent calculates EIG for the available actions and ranks them using:

EIG / cost

It performs the highest-ranked action first.

2. Reporting threshold
If any hidden state reaches:

≥ 90% probability

the agent reports that root cause.

3. Escalation rule
If the agent exhausts its available candidate actions without any hidden state reaching 90%, it escalates to a human.


## Decision 

- Action Performed : Inspect recent code/test changes 
- outcome - No relevant change found
- Likelihoods:
    P(outcome | Code) = 0.0220
    P(outcome | Test) = 0.0090
    P(outcome | Dependency) = 0.9880
    P(outcome | CI/Config) = 0.9720

- Probabilities after Action 1 got executed : 
    Code: 0.0135
    Test: 0.0077
    Dependency: 0.7664
    CI/Config: 0.2124


- Action Performed : Inspect dependency changes
- outcome - No dependency-related change
- Likelihoods:
    P(outcome | Code) = 0.9780
    P(outcome | Test) = 0.9910
    P(outcome | Dependency) = 0.0120
    P(outcome | CI/Config) = 0.9720
- Probabilities after Action 2 got executed : 
    Code: 0.0560
    Test: 0.0322
    Dependency: 0.0389
    CI/Config: 0.8729


- Action Performed  : Inspect CI/environment configuration
- outcome : Configuration/environment issue found
- Likelihoods:
    P(outcome | Code) = 0.0220
    P(outcome | Test) = 0.0090
    P(outcome | Dependency) = 0.1690
    P(outcome | CI/Config) = 0.9720
- Probabilities after Action 3 got executed :
    Code: 0.0014
    Test: 0.0003
    Dependency: 0.0077
    CI/Config: 0.9905 

- Likelihoods

| Action                               | Outcome                               |   Code |   Test | Dependency | CI/Config |
| ------------------------------------ | ------------------------------------- | -----: | -----: | ---------: | --------: |
| Inspect recent code/test changes     | No relevant change found              |  2.20% |  0.90% |     98.80% |    97.20% |
| Inspect dependency changes           | No dependency-related change          | 97.80% | 99.10% |      1.20% |    97.20% |
| Inspect CI/environment configuration | Configuration/environment issue found |  2.20% |  0.90% |     16.90% |    97.20% |

- Posterior after each action

| After action |  Code |  Test | Dependency |  CI/Config |
| ------------ | ----: | ----: | ---------: | ---------: |
| Action 1     | 1.35% | 0.77% | **76.64%** |     21.24% |
| Action 2     | 5.60% | 3.22% |      3.89% | **87.29%** |
| Action 3     | 0.14% | 0.03% |      0.77% | **99.05%** |




### Final Decision

After the third action, the posterior probability of CI/Config reached 99.05%, which exceeded the 90% reporting threshold.

The agent therefore reported **CI/Config as the root cause**.

The decision was based on the posterior probability crossing the predefined reporting threshold.


## Audit Data

**Time:** August 20, 2026, 12:38 PM IST

**Data version:** `ai-native-ai-engineering/week1/deliverables/my-agent/data/replication-labeling.xlsx`

**Model version:** Agent v1

**Policy version:** EIG/cost policy used for Agent v1 evaluation.




