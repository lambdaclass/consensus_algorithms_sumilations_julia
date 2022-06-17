# Leader Election Simulation

## Installing Dependencies

```
make install
```

## Run the Simulation
Before running this directive make sure that you have created `./data/balance`, `./data/leader_election` and `./data/leader_election_simulation` directories in order to be able to store the simulation results.

```

```
make run
```

## Simulation

In these scenarios, we want to simulate every validator's wealth evolution given whether it was elected as leader and whether its block proposal was accepted or not.

## Parameters

### Constant (some are arbitrary, others are based on Ethereum implementation)
- Block reward $\rightarrow$ 2
- Validators count $\rightarrow$ 31
- Round count $\rightarrow$ 1000000
- Admission stake $\rightarrow$ 32
- Slash penalty $\rightarrow$ 1 

### Variable

- The proportion of honest nodes in the network $\rightarrow$ $\Big\{\frac{1}{3}, \frac{2}{3}, 1\Big\}$
- Weighted leader election $\rightarrow$ $\{True, False\}$
- Even initial stake for all the validators $\rightarrow$ $\{True, False\}$
- $\mathbb{P}("\text{reinvest reward}")$ $\rightarrow$ $\Big\{\frac{1}{2}, 1\Big\}$
- $\mathbb{P}("\text{timeout}")$ $\rightarrow$ $\Big\{0, \frac{1}{10}\Big\}$
- $\mathbb{P}("\text{getting wise}")$ $\rightarrow$ $\Big\{0, \frac{1}{2}, 1\Big\}$

(Getting wise is the chance of a honest node to realize that the leader is byzantine).

## Setup

The setup consists on creating the pool of validators that are going to participate in our simulated network.

For this we need `VALIDATORS_COUNT` , `EVEN_INITIAL_STAKE`, and `HONEST_NODE_PROPORTION` parameters.

If the stake is even then $n$ honest nodes are created and $f$ byzantine nodes are created. Where 
$$n = \text{validators_count} \cdot \text{honest_nodes_proportion}$$ and
$$f = \text{validators_count} - n$$

## What is simulated round by round
*WIP*
### Leader Election

This can be modeled as a weighted random choice based on the validator's stake or as a equiprobable random choice. This is represented by the parameter **Weighted leader election**.

### Block Proposal

For now, it only increments by one the amount of proposals that a validator $v$ has proposed.

### Vote of the proposed block

Every validator $v \in \mathcal{V}\backslash\{l\}$ votes on the proposal.

$v_{f}$ is byzantine then 
- If the leader is byzantine it will accept the proposal.
- If the leader is not byzantine it will decline the proposal.


$v_{h}$ is honest then
- If the leader is honest it will accept the proposal.
- If the leader is byzantine it has a `GET_WISE_PROBABILITY` to decline the vote.

For now, this is a generalization because we are assuming that every byzantine node is owned by one byzantine party and every honest.

#### Timeout

We can model this as a random variable with Bernoulli distribution with probability $p$ of success such that

$$
TO: \text{"Validator had a timeout"} \\
TO \thicksim Bernoulli(p) 
$$

Every node has a `TIMEOUT_PROBABILITY` of timeout. In the future if a timeout occurs then the node is slashed with `SLASH_PENALTY`.

### Voting Result

$\frac{2}{3}$ of votes are needed to accept a proposal.

#### Reward

If $\frac{2}{3}$ are achieved then the leader $l$ increases its stake with `BLOCK_REWARD` with probability `REWARD_REINVESTMENT_PROBABILITY`.

#### Slash
For now, only proposers are slashed by `SLASH_PENALTY` and iff their proposal is not accepted.

### Reward Reinvestment

We can model this as a random variable with Bernoulli distribution with probability $p$ of success such that

$$
RR: \text{"Leader reinvests proposal's reward"} \\
RR \thicksim Bernoulli(p) 
$$
