---
date: 2024-07-13T04:40:15.636668719Z
title: Cosmos SDK Transaction Lifecycle
---

In this article, we\'ll majorly talk about the transaction lifecycle and
try to explain how the nodes create, transfer, validate and commit
transactions in the Cosmos SDK based blockchain.

# Blockchain Node Basic structure

First let\'s look at the basic structure of the [Blockchain](/posts/blockchain/) node. The
cosmos-sdk-based blockchain node consists of two parts, one part is
state-machine as an application layer, and the other part is Terndermint
core which contains a consensus layer and a networking layer. While
these two parts connect through the Application Blockchain Interface
(ABCI).

![20220401-132218_screenshot.png](/assets/20220401-132218_screenshot.png)
## State-machine

A state machine is a machine that has multiple states, but only one
state at any given time. While transactions trigger the state
transitions. For example, by given a state `S`{.verbatim} and a
transaction `T`{.verbatim}, then the state machine returns a new state
`S'`{.verbatim} (S prime).

![20220328-233638_screenshot.png](/assets/20220328-233638_screenshot.png)

In practice, the transactions are bundled in blocks to make the process
more efficient.

![20220328-235126_screenshot.png](/assets/20220328-235126_screenshot.png)

In a blockchain context, a note participates in the network, initializes
its state machine, connects with other nodes and updates its state
machine as new blocks come in.

So at its core, a blockchain is a replicated deterministic state
machine. It means that if a node is started at a given state and replays
the same sequence of transactions, it will always end up with the same
final state.

### Transaction

In Cosmos SDK-based blockchain each transaction contains different types
of messages. Here is an example of a transaction with several messages.

``` javascript
{
  body: {
    messages: [
      MsgSend,
      MsgExec,
      MsgExecuteContract,
      /*...*/
    ],
    memo: '100059323',
    timeout_height: '0',
    extension_options: [],
    non_critical_extension_options: []
  },
  auth_info: {
    signer_infos: [
      {
        public_key: {/*...*/},
        mode_info: {/*...*/},
        sequence: '6747'
      },
    ],
    fee: {
      amount: [
        {denom: 'uband', amount: '10000'},
      ],
      gas_limit: '200000',
      player: '',
      granter: '',
    },
  },
  signatures: [
    '...'
  ]
}
```

And here is the content of Msg \"MsgSend\".

``` javascript
{
  '@type': '/cosmos.bank.v1beta1.MsgSend',
  from_address: 'address...',
  to_address: 'to-address...',
  amount: [
    {
      denom: 'uband',
      amount: '6257900000',
    }
  ]
}
```

### State transition

![20220401-114000_screenshot.png](/assets/20220401-114000_screenshot.png)

1.  Decode

    In blockchain node, the application (state manche) receives a
    transaction from the underlying tendermint core, the transaction is
    still in its **encoded byte** form and needs to be unmarshaled to be
    processed.

2.  Extract Messages

    After decoding, the application extracts messages from the
    transaction.

3.  Route

    The \"Msg Service Router\" routes each message to a differnt
    application module.

4.  Perform a state transition

    In the module, the \"msgServer\" handles the message to use a keeper
    to access the state store then perform the state transitions.

## Tendermint Core

![20220401-133203_screenshot.png](/assets/20220401-133203_screenshot.png)

Tendermint Core performs **Byzantine Fault Tolerant (BFT) State Machine
Replication (SMR)**.

It means Tendermint is software for **securely** and **consistently**
replicating an application (State-machine) on many nodes by transferring
blocks of transactions.

-   By \"**securely**,\" means Tendermint works even if up to 1/3 of
    nodes fail in arbitrary ways.
-   By \"**consistently**,\" means that each non-faulty node sees the
    same transaction log and computes the same state.

## Application Blockchain Interface

![20220401-182219_screenshot.png](/assets/20220401-182219_screenshot.png)

Application BlockChain Interface (ABCI) is the interface between
Tendermint (a state-machine replication engine) and the application (the
actual state machine) being replicated.

The Tendermint acts as a client to send requests and the application
acts as a server to reply responses. The application needs to implement
the API to be able to integrate with Tendermint. Through this way, we
can build business modules in application layer without knowing the
details about consensus layer and networking layer.

We can run the application in the same process as the Tendermint, or in
a separate process.

When running the application in the same process, Tendermint call the
APCI methods in the application directly as the regular Golang method
calls.

When running the application in a separate process, Tendermint opens 4
connections to the application.

-   Consensus connection
-   Mempool connection
-   Info connection
-   Snapshot connection

The Consensus connection is driven by a consensus protocol and it\'s
responsible for the block execution.

The node\'s \"Mempool\" is an in-memory pool of transactions that are
candidates to be included in the next block. The Mempool connection is
used for validating new transactions before sending them to Mempool.

The Info connection is responsible for initialization and queries from
the users.

The Snapshot connection is responsible for serving and restoring state
snapshots.

Here are the API method calls for each connection

-   Consensus connection: InitChain, BeginBlock, DeliverTx, EndBlock,
    Commit
-   Mempool connection: CheckTx
-   Info connection: Info, Query
-   Snapshot connection: ListSnapshots, LoadSnapshotChunk,
    OfferSnapshot, ApplySnapshotChunk

And there are two special method

-   Flush method that is called on every connection
-   Echo method that is just for debugging.

Additionally, in next generation of the Application Blockchain Interface
(ABCI++), Tendermint has changed the most method calls for consensus
connection

-   Consensus connection: ListSnapshots, LoadSnapshotChunk,
    OfferSnapshot, ApplySnapshotChunk

# Simplified Transaction lifecycle

At this point, let\'s look at the simplified transaction lifecycle.

-   First, Nodes create transactions.
-   Tendermit
    -   Broadcasts the new transactions to all the nodes
    -   Bundles the transations into blocks
    -   Transfers the blocks to all the nodes securely and consistently
    -   Involves Validators (some special nodes) to validate
        transactions and blocks durring broadcasting and transferring.
-   At last, all the nodes commit the blocks of transactions to finalize
    the state change in the same way.

# Application perspective

From the application perspective, the application performs operations
includes

-   Creating Transactions
-   Validating Transactions
-   Committing Transactions

## Creating Transactions

Cosmos SDK provides 3 ways to create a transaction: command line, gRPC,
or Rest interface.

Here is an example of using the command line to send a transaction to
transfer 1000uatom from one address to another.

``` shell
appd tx send <recipientAddress> 1000uatom \
  --from <senderAddress> \
  --gas auto \
  --gas-adjustment 1.5 \
  --gas-prices 0.025uatom
```

-   `--gas`{.verbatim}, refers to how much gas the current transaction
    consumes. Usually, it\'ll be set to \"auto\" to let the application
    automatically estimate the amount of gas.
-   `--gas-adjustment`{.verbatim}, sometimes in practice, the real
    consumed gas is more than the estimated one, so the parameter
    `--gas-adjustment`{.verbatim} is used to scale the gas up to avoid
    underestimating.
-   `--gas-prices`{.verbatim}, specifies how much the user is willing to
    pay per unit of gas. The validators will compare it with their local
    setting `min-gas-prices`{.verbatim}, and they will reject
    transactions that \"gas prices\" are not high enough for the sake of
    anti-spam.

## Validating Transactions

The node performs **stateless** checks, then **stateful** checks on the
transaction to reject an invalid transaction as early as possible to
avoid wasting computation.

### Stateless checks

Stateless checks do not require nodes to access state and are thus less
computationally expensive. Stateless checks include making sure
addresses are not empty, enforcing nonnegative numbers, and other logic
specified in the definitions.

### Stateful checks

Stateful checks validate transactions and messages based on a committed
state. Examples include checking that the relevant values exist and can
be transacted with, the address has sufficient funds, and the sender is
authorized or has the correct ownership to transact.

### Validate one transaction

As mentioned previously, a node creates a new transaction and broadcast
it to other nodes. After receiving this new transaction, all the other
nodes need to validate it first before sending it to the \"MemPool\".

### Validate one block of transactions

After bundling the transactions into blocks, the node needs to validate
the transactions inside one block all together.

To fully validate one block, the node needs to execute all the
transactions in it and perform the state transitions.

For example, one block contains two transactions. Both transactions
withdraw cryptocurrency from the same account. One withdrawal is 100,
the other withdrawal is 120, while the account balance is 200. The
balance is sufficient for each of them separately, but not enough for
total of them together.

## Store Branching

The cosmos SDK maintain a main state and 2 primary volatile states. The
main state is called \"CommitMultiStore\". The two volatile states,
named \"CheckState\" and \"DeliverState\", are derived from the main
state by using a mechanism called store branching.

During validation, the application only made changes in the volatile
states.

![20220401-173228_screenshot.png](/assets/20220401-173228_screenshot.png)

## Commiting Transactions

The final step is for nodes to commit the block of transactions and
finalize state changes.

During Commit all the state transitions that occurred in the volatile
state are finally written to the mainn state \"CommitMultiStore\".

# Tendermint Consensus

![20220401-182518_screenshot.png](/assets/20220401-182518_screenshot.png)

Consensus is all about transferring transactions securely and
consistently. Tendermint consensus is the BFT(Byzantine fault tolerance)
Consensus Algorithm.

After synchronizing with others to the same block height (H) of
blockchain, a node will enter into a process of deciding the next block.

The process consists of several rounds (R) and two special steps (S).

-   Each round contains 3 steps (S) Propose, Prevode, and Precommit.
-   The two special steps are NewHeight and Commit Steps

NewHeight -\> \[rounds: (Propose-\>Prevote-\>Precommit) -\>
(Propose-\>Prevote-\>Precommit) -\> ...\] -\> Commit -\> NewHeight -\>
...

In each round, the number of validators remains the same. Tendermint
selects a proposer from the validators at the beginning.

The proposer collects a number of transactions from the \"MemPool\" with
its own preference, bundles them into a block, and then sends the
proposal of this new block to the other nodes.

The other validators receive the new block, validate all the
transactions in it, then decide whether to send \"prevote\" for this new
block to represent agree or disagree on the this proposal.

After sending \"prevote\", each validator keep listening for \"prevote\"
from other validators. When the total number of \"prevote\"s is more the
two thirds (2/3) of validators, the validator send \"Precommit\" for
this new block, which means the validator tell the others that it found
more 2/3 of validators agreed on the current proposal.

Meanwhile, all the nodes keep listening the \"precommit\" from
validators. When the number of \"precommit\" is more than 2/3 of
validators, which means 2/3 of validators conclude that they found 2/3
of validators agree on the current proposal, so at this point it\'s safe
for the node to \"Commit\" the new block of transactions to finalize the
state change, and then goto the step \"NewHeight\" to increase the block
height of blockchain.

Sometimes, it may fail to commit the new block in one round. Maybe some
of the step reach the timeout due to the network delay, or the Proposer
suddenly went offline, or the proposal was from a malicious node, and so
on.

So at this point, Tendermint needs to repeat the round several more
times.

# Proposer selection procedure

At its core, the proposer selection procedure uses a weighted
round-robin algorithm. Let\'s quickly understand the algorithm through
the simple pseudo-code.

``` shell
def ProposerSelection (vset):
  // compute priorities and elect proposer
  for each validator i in vset:
    A(i) += VP(i)
  prop = max(A)
  A(prop) -= p // p = VP(1) + VP(2) + ...
```

-   \"vset\" is the validator set.
-   \"VP(i)\" is the voting power of validator i.
-   \"A\" is a priority queue \"A(i)\" is accumulated priority for
    validator i
-   \"P\" is total voting power of set, P = VP(0) + VP(1) + VP(2) ...
-   \"prop\" is the proposer

At each round, the validator set remains the same, and each validator
increases its priority by its voting power, and then the Tendermint
selects the validator with the highest priority as proposer. After
selecting, the proposer\'s priority will be decreased by P the total
voting power of the set.

\"A\" is a priority queue. The validators move ahead in this queue
according to their voting power (the higher the voting power the faster
a validator moves towards the head of the queue).

Of cause, this only is a simplified version, in real code needs
adjustment for adding and removing validators.

# Summary

-   At its core, blockchain is **Replicated deterministic state
    machine**
-   Tendermint performs **Byzantine Fault Tolerant (BFT) State Machine
    Replication (SMR)**
-   By implementing ABCI, developers can build business modules without
    knowing details about consensus and networking
-   In Node, application performs creating, validating and committing
    transations.
-   Tendermint consensus is BFT (Byzantine Fault Tolerance) Consensus
    Algorithm. It help nodes archive consensus on new block through the
    steps: NewHeight, Propose, Prevote, Precommit, and Commit.
