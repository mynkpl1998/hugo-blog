+++
title = 'Dissecting Policy Optimization'
date = 2020-06-27T01:47:28-07:00
draft = false
tags = [ "RL", "Actor-Critic"]
+++

> I assume the reader is familiar with the basics of Reinforcement learning and has a basic understanding of statistics and a bit of calculus. One should be comfortable with manipulating value functions, policy, and bellman equations. 

The main idea of writing this blog post is to summarize and extend the understanding of reinforcement learning methods that directly optimizes policy. More or less, this blog post is a summary for me to revisit the concepts and various tricks that are helpful while dealing with Policy-based optimization. 

# Notation Used

1. $$ k $$ - Policy parameters update step.
2. $$ t $$ - Episode/Agent's interaction time step.
3. $$ S $$ - Set of state space.
4. $$ A $$ - Set of action space.
5. $$ \mu(s) : S \rightarrow [0, 1] $$ - Initial state distribution.
6. $$ P(s_{t+1} \vert s_{t}, a_{t}): S \times A(s) \times S \rightarrow [0, 1] $$ - Environment dynamics. 

# Introduction

Policy-based optimization methods work by directly approximating the policy (which we care for) and therefore eliminates the need for a value-function during action selection. Unlike, the value-based methods like Q-learning and SARSA, where the action selection requires access to Q-values. 

For these methods, the policy $$ \pi $$, is a function that maps the state $$ s $$, to an action $$ a $$, $$ \pi = f : S \rightarrow A(s) $$. The policy can be represented using a linear or non-linear family of functions (such as deep neural networks) parameterized by weights $$ \theta $$, $$ a = \pi_{\theta}(s) $$. 

In practice, the actions seen by the agent during learning are highly influenced by the initialization of the parameters of the policy. To encourage exploration, a stochastic mapping or a distribution over actions is preferred instead of a deterministic mapping. Sampling actions from this distibution allows for some randomness and thus helps the agent to explore during learning without going off-policy. Therefore, it is common to learn a parameterized distribution (pdf/pmf) over actions, conditioned on the state for such methods, $$ \pi_{\theta}(a \vert s) $$. 

> NOTE: The policy mapping could be any function as long as it is differentiable w.r.t to parameters $$ \theta $$ and $$ \nabla_{\theta} \pi_{\theta}(a \vert s) $$ always exists and is finite, $$ \forall s \in S, a \in A(s) $$.


The goal of these methods is to learn the parameters for the policy that maximizes the expected return of the trajectories, $$ J(\pi_{\theta}) = \mathbb{E}[ R(\tau) ] $$, where $$ R(\tau) $$ corresponds to the *undiscounted sum of rewards* of the trajectory $$ \tau $$. The trajectory $$ \tau $$, corresponds to the sequence of states and actions $$ (s_{0}, a_{0}, s_{1}, a_{1}, ..., a_{T-1}, s_{T}), $$ which agent experiences. The expectation is over the initial state-distribution, $$ s_{0} \sim \mu(s) $$, environment dynamics, $$ \bar{s} \sim P(s_{t+1} \vert s_{t}, a_{t}) $$ and actions sampled using policy, $$ a \sim \pi_{\theta}(a_{t} \vert s_{t}) $$.

> NOTE : We assume, no discounting ($$ \gamma $$ = 1) and finite horizon (episodic) task. However, the same is true for $$ \gamma \neq 1 $$ but may not apply to non-episodic tasks. For infinite horizon (non-episodic) tasks, performance is defined in terms of the average rate of reward per time step. For more details, please have a look at [Introduction to Reinforcement Learning, Sutton & Barto, Second Edition, Draft 2017, Chapter-13.6](http://incompleteideas.net/book/bookdraft2017nov5.pdf).

> NOTE: From now on, we will use $$ k $$ to denote the policy parameters update step and $$ t $$ to denote the episode time step.

The parameters of the policy are updated by running the gradient ascent on $$ J(\pi_{\theta_{k}} ) $$. The parameter update rule is given by equation $$ (\ref{policy_update_rule}) $$, where $$ \alpha $$ is the learning rate. It is possible to use $$ \alpha_{k} $$ instead of fixed learning rate. Most of the state-of-the-art optimizers like Adam, RMSProp, etc., uses an adaptive learning rate to scale the learning rate based upon the statistics of the past gradients.


$$
\begin{equation}
\label{policy_update_rule}

\theta_{k+1} = \theta_{k} + \alpha \nabla_{\theta} J(\pi_{\theta_{k}})
 
\end{equation}
$$

<br>

# Deriving policy gradient, $$ \nabla_{\theta_{k}} J(\pi_{\theta_{k}}) $$

Let's derive some useful results which will be used in policy gradient derivation.

* Probability of a trajectory $$ \tau $$, $$ P(\tau \vert \theta_{k}) $$.

The trajectory $$ \tau = (s_{0}, a_{0}, s_{1}, ... a_{T-1}, s_{T})$$, is a sequence of the state and action pair which agent experiences, given that actions are sampled from $$ \pi_{\theta_{k}} $$. The probability of trajectory $$ \tau $$, gives the likelihood of seeing the trajectory $$ \tau $$ out of all the possible set of trajectories.

$$
\begin{equation}
\label{prob_of_a_trajectory}
P(\tau \vert \theta_{k}) = \mu(s_{0}) \pi_{\theta_{k}} (a_{0} \vert s_{0}) P(s_{1} \vert s_{0}, a_{0}), ... ,\pi_{\theta_{k}}({a_{T-1} \vert s_{T-1}}) P(s_{T} \vert s_{T-1}, a_{T-1}) 

\\

= \mu(s_{0}) \prod_{t=0}^{T} \pi_{\theta_{k}}(a_{t} \vert s_{t}) P(s_{t+1} \vert s_{t}, a_{t})
\end{equation}
$$

* Log gradient trick


$$
\begin{equation}
\label{log_grad_trick}
\frac{\nabla_{\theta_{k}} P(\tau \vert \theta_{k})}{P(\tau \vert \theta_{k})} = \nabla_{\theta_{k}} \log P(\tau \vert \theta_{k})
\end{equation}
$$

Equation ($$ \ref{policy_update_rule} $$), summarizes the update rule to update the parameters of the policy. In equation, all we need to do is to plug-in the policy gradient. Therefore, Let's derive it.

$$

\nabla_{\theta_{k}} J(\pi_{\theta_{k}}) = \nabla_{\theta_{k}} \mathbb{E} [R(\tau)]
\\
= \nabla_{\theta_{k}} \int_{\tau} P(\tau \vert \theta_{k}) R(\tau) d\tau
$$

Now, let's take the gradient inside the intergal. Since, $$ R(\tau) $$ is not a function of $$ \theta_{k} $$, it acts as a constant.

$$
\nabla_{\theta_{k}} J(\pi_{\theta_{k}}) =  \int_{\tau} \nabla_{\theta_{k}} P(\tau \vert \theta_{k}) R(\tau) d\tau
$$

$$
=  \int_{\tau} P(\tau \vert \theta_{k}) \nabla_{\theta_{k}} \log P(\tau \vert \theta_{k}) R(\tau) d\tau \;\;\;\; \text{From, (} \ref{log_grad_trick} \text{)}

\\
$$

$$
=  \int_{\tau} P(\tau \vert \theta_{k}) \nabla_{\theta_{k}}\left[ \log \mu(s_{0}) + \sum_{t=0}^{T} \log \pi_{\theta_{k}}(a_{t} \vert s_{t}) + \log P(s_{t+1} \vert s_{t}, a_{t})\right] R(\tau) d\tau \;\;\;\; \text{From, (} \ref{prob_of_a_trajectory} \text{)}

$$

Again, $$  \log \mu(s_{0}) $$ and $$ \log P(s_{t+1} \vert s_{t}, a_{t}) $$ are not the functions of $$ \theta_{k} $$. Therefore, $$ \nabla_{\theta_{k}} \log \mu(s_{0}) = 0 $$ and $$ \nabla_{\theta_{k}} \log P(s_{t+1} \vert s_{t}, a_{t}) = 0 $$.

$$
=  \int_{\tau} P(\tau \vert \theta_{k}) \sum_{t=0}^{T} \nabla_{\theta_{k}} \log \pi_{\theta_{k}}(a_{t} \vert s_{t}) R(\tau) d\tau
$$

$$
\label{policy_gradient_eqn}
\nabla_{\theta_{k}} J(\pi_{\theta_{k}}) =  \mathbb{E}_{\tau \sim P(\tau \vert \theta_{k})} \left [ \sum_{t=0}^{T} \nabla_{\theta_{k}} \log \pi_{\theta_{k}}(a_{t} \vert s_{t}) R(\tau) \right]
$$

Equation ($$ \ref{policy_gradient_eqn} $$), is the expression for the policy gradient. The expression is an expectation over the set of all possible trajectories. To calculate this expectation exactly, we need to know the set of all possible trajectories and their probablities which can be calculated using equation ($$ \ref{prob_of_a_trajectory} $$). However, the probability of a trajectory depends upon $$ \mu(s) $$ and $$ P(s_{t+1} \vert s_{t}, a_{t}) $$, which are typically unknown or difficult to obtain (like for complex environments). In practice, all we need is an good estimate of this gradient and 