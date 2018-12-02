## Moon: a peer-to-peer operating system

A purely peer-to-peer implementation of an operating system should allow complete applications to freely operate without going through a centralized institution. Ethereum partly solved that problem in 2014, but early design choices lead to a series of difficulties that affected its scalability, security and usability. Building upon its ideas, we present the complete design of a decentralized operating system ready for mass adoption. For the consensus mechanism, we use a proof-of-work tokenless blockchain. Decentralized applications are then constructed as mere offline interpretations of that data, allowing fees and miner rewards to be paid in any currency within the system. Interaction combinators, a parallel model of computation, are used to ensure good computational properties, and Curry-Howard's isomorphism is exploited to ensure trustless security.

### Features

#### Tokenless consensus network

Instead of a monolithic blockchain with a built-in sequential, stack-based virtual machine with numerous primitive opcodes, a complex state-management machinery that requires everyone to process everyone else's transactions, and a default token with built-in miner reward and fee systems, Moon's decentralized ledger is just a dead-simple, tokenless, ever-growing store of global events. Proof-of-Work is used as the consensus mechanism, and fees and miner rewards can be paid in any currency within the system. Decentralized applications are posteriorly constructed as mere offline interpretations of those events, not affecting the consensus-relevant part of the system, which is kept extraordinarily simple, performant and future-proof.

#### Computational optimality

Computation is realized through a new, optimal and minimal model of computation, interaction combinators, which combine the best aspects of Turing Machine and the Lambda Calculus. This allows states to be computed lazily, in parallel and optimally. In other words, users only need to compute the state of applications they're interested in, performing the minimal amount of work necessary to do so. Moreover, full nodes do not need to compute anything at all.

#### Security

From a security point of view, instead of low-level, bug-prone languages for smart-contracts, we use a powerful, high-level language capable of exploiting Curry-Howard's isomorphism to construct mathematical models of its own programs. This enables developers to publish proofs about the behavior of their applications, which users can verify offline, allowing them to trust the apps they use without needing to trust their developers.

#### Usability

From an usability point of view, instead of having separate languages for back-end logic and user-facing interfaces, which has been shown to have negative effects for productivity and adoption, the same language is used on both sides. As a result, complete decentralized applications (back-end and front-end logic) can be specified as single, coherent, self-contained documents, which are then presented to the user in an unified package, Moon, which feels complete and well-integrated.

#### Scalability

Despite not proposing any ground-breaking scalability solution, we can actually reach reasonably high transactions per second (tps) by sheer simplicity of the system. With a 1 TB blockchain size growth in 2019, for example, and 200-bit transactions using newer signature schemes, we could reach 1500 on-chain tps, with a cost of roughly $50 plus internet to run a full node that year. If we allowed the blockchain to grow little bit more, that number would be even higher.

Keep in mind that, since applications are just normal offline computations, we can exploit zk-starks to build short proofs of their latest states, allowing light clients to download a minimal amount of data. As such, it is acceptable if full-nodes are a little bit bigger than the average user's HDs, and maintained by small datacenters and enthusiasts.

#### Simplicity and political neutrality

Despite sounding complex, the entire system is intended to be 1. astonishingly simple, in the sense a regular developer following this document should be able to implement all of it in a month of work, and 2. complete, in the sense it includes everything that is required to build a worldwide computer, with extensions coming from uploaded code, not protocol updates, which should never be needed. This also addresses the common problem of core developers having too much influence, by simply not having core developers at all.

#### Ethereum compatibility

Compatibility with Ethereum is maintained through a multiway bridge, which is possible due to Turing completeness on both sides. In other words, Moon shouldn't be seen as a separate, competing network, but as part of the whole.

### History

In 2009, Satoshi Nakamoto released the first decentralized application: Bitcoin, an electronic cash that operated in a purely peer-to-peer fashion, with no single party holding any exclusive power or higher authority over the execution of the system. Bitcoin was designed to eternally operate following the rules initially specified by its code, being thus forever immune to any attempt of corruption, censure or sabotage. Almost 10 years later, that goal has been marvelously successful: not a single bitcoin has ever been created outside the planned schedule, not a single user-balance has been alterated in unexpected manners, not a single entity has obtained power to disturb the system's planned functioning, despite numerous attempts and enormous interest; a fact that can be easily verified by any of its users. Bitcoin is, to all intents and purposes, a global application that perpetually operates following its own rules, independent of any human decision. 

The same properties that made Bitcoin so resilient, though, also made it very inflexible. Using Bitcoin to do anything other than what was originially defined, i.e., sending and receiving digital coins, is hard. As impactiful as Bitcoin was as a digital currency and a store of value, it is, in the end, just a global map of user balances, and, as such, very limited. Such inflexibility lead to the increasing development of alternate currencies covering features that Bitcoin didn't. Namecoin, for example, was a fork of Bitcoin which allowed users to register domains; Monero was a fork with private transactions. Eventually, dozens of similar applications were created, and, while all of those were decentralized themselves, they couldn't interact with each other to construct a fully integrated decentralized economy. As an example, a decade later you can't trade Bitcoin for Monero without going through a centralized thirdy party exchange. In fact, most services around Bitcoin are still centralized. This demonstrates it takes more than a decentralized currecy to build a decentralized economy: what we need is a decentralized computer.

Under that understanding, Vitalik Buterin, in 2013, proposed Ethereum, a distributed computing platform featuring user-defined scripting capabilities. The key insight is that, by including, as part of the system, a virtual machine capable of performing arbitrary computations, users are able to extend it with unpredictably new functionalities. Name registars such as Namecoin, for example, could be added to the system a posteriori, and they were: [ENS](https://ens.domains/) (Ethereum Name System) being the most successful implementation. Private transactions such as Monero's could be realized with posterior incorporation of technologies such as zero-knowledge proofs. Countless other features such as games with fully-autonomous virtual economies, prediction markets and even governments could be designed to live inside Ethereum and, as such, (...)

### Implementation Summary

#### Blockchain as a token-less, ever-growing ledger of time-ordered data

The decentralized ledger used by Moon is very similar to Bitcoin, except with no native token. Users can, at any time, generate arbitrary transactions and broadcast. Transactions are, simply, byte arrays containing arbitrary information. Every 10 minutes, miners build a block by gathering transactions by priority, and hashing the whole block, similarly to Bitcoin. Moon's "blockchain" can, in essence, be seen as no more than an ever-growing store of time-ordered data. In order to avoid uncontrollable growth, the size of a block is limited inverselly proportional to the decreasing costs of storage hardware, aiming to make the cost of storing the entire blockchain data constant over time.

Transaction priority is, initially, defined as the value of its hash. Eventually, as popular smart contracts give transactions meaning, miners can redefine priority as they wish. For example, if a popular onchain token allows users to pay miner rewards for arbitrary transactions, then miners can consider that as part of their priority computations. In other words, users would be able to pay fees in multiple concurrent tokens, by merely including that information on their transactions.

#### Decentalized applications as offline interpretations of that time-ordered data

As opposed to Ethereum, computing and smart-contract functionally is not part of the decentralized ledger or the consensus mechanism. In fact, smart-contracts are fully invisible to the blockchain. They are, instead, mere interpretations of the otherwise meaningless data stored on the blockchain. As an example, imagine that the blockchain, in a given moment, contained the following blocks:

```
block 0: 
  transaction 0: '["myDRPG", "register", ["alice"]]'
  transaction 1: 'send_gold_token('john', 'mary', 10)"
  transaction 2: '["myDRPG", "buy_item", ["alice", "mana potion"]]'
  transaction 3: 'I'm a cool person'
block 1
  transaction 0: '大キライだったそばかすをちょっと'
  transaction 1: 'breed_kitten{name: "ed"}'
  transaction 2: '["myDRPG", "buy_item, ["alice", "epic sword"]]'
  transaction 3: 'kdsjf lkajfl jlfadj lkafj'
block 2:
  transaction 0: 'LISP_EXEC (cons 1 (cons 2 nil))'
  transaction 1: '["myDRPG", buy_item", ["alice", "fire rune"]]'
  transaction 2: 'Y29uc2lkZXIgeW91cnNlbGYgd2ViLWh1Z2dlZA=='
```

Note that, at this point, transactions are just arbitrary strings. If you inspect visually, you can see some transactions look like JSON objects, others look more like text in random languages, others resemble nothing familiar. That's fine, because transactions aren't meaningful by themselves: it is up to application developers to assign meaning to them, by creating programs that interpret those in a specific way. For example, to build a game, one could have developed this function:

```
function mydrpg_app(transactions) {
  var game_state = {
    players: {}
  };

  for (var j = 0; j < transactions.length; ++j) {
    try {
      var [target, call, args] = JSON.parse(blocks[i][j]);
    } catch (err) {
      continue;
    }

    // This transaction isn't to our contract!
    if (target !== "d&d") continue;

    // Perform the contract logic
    switch (call) {
      "register":
        game_state.players[args[0]] = {items: []};
        break;
      "buy_item":
        game_state.players[args[0]].push(args[1]);
        break;
    }
  }

  return game_state;
}
```

If you call that function on the blockchain data above, it will return `{"alice": ["mana potion", "epic sword", "fire rune"]}`. In other words, we're able to interpret the bulk data on the blockchain as a series of time-ordered commands to our game, and, as such, derive its state. Data that is irrelevant to this application and misformatted transactions are, simply, ignored. With this ability, we're able to build anything: tokens, games, cassinos, exchanges and so on. In other words, smart-contract functionality doesn't need to be implemented on the blockchain in order to have smart-contracts, as controvertial as this may sound. A decentralized ledger of plain data is sufficient.

##### What about gas costs?

In Ethereum, smart-contracts are part of the system. Ethereum nodes are, thus, responsible for computing the final state of the entire network. This approach has a huge benefit: those nodes are able to construct short proofs of subsets of that state. This allows "light clients" to interact with applications they're interested in without downloading the entire set of transactions. This also has an obvious drawback, though: full nodes need to compute everything! As such, Ethereum includes a "gas" system, which taxes transactions weighted by their computational costs. This is required to avoid attacks where a malicious actor calls a script that never halts, or that takes too long to halt, essentially causing an irreversible denial-of-service on the entire network.

Moon's approach is admitedly dumber than that: smart-contracts can't stop the network because there are no smart-contracts at all: decentralized applications are just offline interpretations of raw, time-ordered global events. But what if such application enters an infinite loop? Then it will be dead, but others will not be affected. In other words, applications are responsible for keeping their own computational costs low. What Moon does, though, is limit the growth of the blockchain itself. As an example, consider the `mydrpg_app` above. If the decentralized ledger suddenly added billions of new transactions overnight, this would cause average users to no longer be able to participate on it. Since, though, the transaction count growth is controlled to follow the growth of computing power in an average user's computer, and since MyDRPG performs only simple constant-time operations per transaction, then it follows that `mydrpg_app()` will always take roughly the same time to compute the final state of the game.

### Implementation

- Formality

<TODO>

- Document (interface renderer)

<TODO>

- Store (local events)

<TODO>

- Ledger (global events)

<TODO>
