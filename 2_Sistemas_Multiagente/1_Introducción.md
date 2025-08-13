
# Introducción

MARL is based on *reinforcement learning* (LR), in which agents learn **optimal decision policies** by trying actions and receiving rewards, with the goal of choosing actions to maximize the sum of received rewards over time. While in single-agent LR the focus is on the learning an optimal policy for a single agent, in MARL the focus is on learning optimal policies for multiple agents and the unique challenges that arise in this learning process. 

## Multi-Agent Systems
A multi-agent system consists of an *environment* and multiple decision-making *agents* that interact in the environment to achieve certain goals. The agents can observe information about the environment and take actions to achieve their goals. The image below shows a general schematic of a multi agent system. 

![[Pasted image 20250326160011.png]]

### Environment
An environment is a physical or virtual world whose state evolves over time and is influenced by the actions of the agents that exist within the environment. The environment specifies the actions that agents can take at any point in time as well as the observations that individual agents receive about the state of the environment.

The states of the environment may be defined as discrete or continuous quantities, or a combination of both. Similarly, actions may be discrete or continuous. Multi-agent environments are often characterized by the fact that agents only have a limited and imperfect view of the environment. **This means that individual agents may only observe some partial information about the state of the environment, and different agents may receive different observations about the environment.**


### Agents
An agent is an entity that receives information about the state of the environment and can choose different actions in order to influence the state. Agents may have different prior knowledge about the environment, such as the possible states that the environment can be in and how states are affected by the actions of the agents. 

Importantly, agents are goal-directed in the sense that agents have specified *goals* and choose their actions in order to achieve their goals. In MARL, such goals are defined by *reward functions* that specify scalar rewards signals that agents receive after taking certain actions in certain states. 

The term ***policy*** refers to a function used by the agent to select actions (or assign probabilities to selecting each action) given the current state of the environment. **If the environment is only partially observed by the agent, then the policy may be conditioned on the current and past observations of the agent.**


    ---

The defining characteristic of a multi-agent system is that the agents must coordinate their actions with (or against) each other to achieve their goals. In a fully cooperative scenario, the agents' goals are perfectly aligned so that the agents need to collaborate toward achieving a shared goal.


Most solution concepts are anchored in some notion of *equilibrium*, which means that no individual agent can deviate from its policy in the solution to improve its outcome.


## Multi-Agent Reinforcement Learning

MARL algorithms learn optimal policies for a set of agents in a multi-agent system. As in the single-agent counterpart, the policies are learned via a process of trial-and-error to maximize the agents' cumulative rewards, or *returns*. 

![[Pasted image 20250326163301.png]]

Image above describes a set of *n* agents receiving individual observations about the state of the environment, and choosing an action to modify the state of the environment by putting them together, referred to as the *joint action*. The joint action changes the state of the environment according to the environment dynamics, and the agents receive individual rewards as a result of this changes as well as individual observations about the new environment state. This loop continues until a terminal criterion is satisfied (such as one agent winning a game of chess) or indefinitely. 

A complete run of this loop from the initial state to the terminal state is called and *episode*. The generated data produced from multiple independent episodes — that is, the experienced observations, actions, and rewards in each episode— are used to continually improve the agents' policies. 


### Classification

| Dimension                      | Questions                                                                                                                                                                                                                                                                   |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Size                           | How many agents exist in the environment? Is the number of agents fixed, or can it change? How many states and actions does the environment specify? Are states/actions discrete or continuous? Are actions defined as single values or multi-valued vectors?               |
| Knowledge                      | Do agents know what actions are available to themselves and to other agents? Do they know their own reward functions, and the reward functions of other agents? Do agents know the state transition probabilities of the environment?                                       |
| Observability                  | What can agents observe about their environment? Can agents observe the full environment state, or are their observations partial and noisy? Can they observe the actions and/or the rewards of other agents?                                                               |
| Rewards                        | Are the agents opponents, with zero-sum rewards? Or are agents teammates, with common (shared) rewards? Or do agents have to compete and cooperate in some way?                                                                                                             |
| Objective                      | Is the agents’ goal to learn an equilibrium joint policy? What type of equilibrium? Is performance during learning important, or only the final learned policies? Is the goal to perform well against certain classes of other agents?                                      |
| Centralization & Communication | Can agents coordinate their actions via a central controller or coordinator? Or do agents learn fully independent policies? Can agents share/communicate information during learning and/or after learning? Is the communication channel reliable, or noisy and unreliable? |

MARL algorithms can be categorized based on a number of dimensions. For example, this includes assumptions about the agents' rewards (e.g., fully cooperative, competitive, or mixed), what type of solution concept the algorithm is designed to achieve (e.g., Nash equilibrium), and what agents can observe about their environment. Algorithms can also be categorized based on assumptions made during the learning of agent policies ("training") versus assumptions made after learning ("execution"). 

Centralized training and execution assumes that both stages have access to some centrally shared mechanism of information, such as sharing all observations between agents. For example, a single central agent may receive information from all other agents and dictate the actions to the agents. Such centralization can help to improve coordination between agents and alleviate issues such as non-stationarity.

In contrast, decentralized training and execution assumes no such centrally shared information, and instead requires that the learning of an agent's policy as well as the policy itself only use the local information of that agent.

The third major category, centralized training with decentralized execution, aims to combine the benefits of the two aforementioned approaches, by assuming that centralization is feasible during training (e.g., in simulation) while producing policies that can be executed in a fully decentralized way. 

## Challenges of MARL

### Non-stationarity cause by learning agents
An important characteristic of MARL is non-stationarity caused by the continually changing policies of the agents during their learning processes. This non-stationarity can lead to a moving target problem because each agent adapts to the policies of other agents whose policies in turn also adapt to changes in other agents, thereby potentially causing cyclic and unstable learning dynamics. 

### Optimality of policies and equilibrium selection
When are the policies of agents in a multi-agent system *optimal*? In single-agent RL, a policy is optimal if ti achieves maximum expected return in each state. However, in MARL, the returns of one agents' policy also depend on the other agents' policies, and thus we require more sophisticated notions of optimality.  

In addition, while in the single- agent case all optimal policies yield the same expected return for the agent, in a multi-agent system (where agents may receive different rewards) there may be multiple equilibrium solutions, and each equilibrium may entail different returns for different agents.

### Multi-agent credit assignment
Temporal credit assignment in RL is the problem of determining which past actions contributed to a received reward. in MARL, this problem is compounded by the additional problem of determining *whose* action contributed to the reward. 

### Scaling in number of agents
In a multi-agent system, the total number of possible actions combinations between agents may grow exponentially with the number of agents. This is particularly the case if each added agent comes with its own additional action variables. Even with today’s deep learning-based MARL algorithms, it is common to use a number of agents between 2 and 10.