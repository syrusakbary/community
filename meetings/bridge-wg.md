# Ethereum-Near Bridge
This thread contains agenda and/or summary of the regular Bridge sync meeting. Please propose agenda items through the PRs and Issues.

Led and executed by Anton B.

## 15.05.2020

* Anton B: Two P0 tasks -- bonds/deposits of the relayers.  Maybe we can change bonds because right now the delay is one hour. Max Z: suggests to go with the simplest design possible. Anton B: making it work in such way that multiple headers can be submitted in a row is easy, but let's keep the simple one for now. We have two constants now: lock duration and one lock eth ammount. Second P0 task -- implement verification of elliptic curves that do not exist in Solidity ATM, ED25519. There were a lot of parts missing for signatute verification. Needs SHA512 is used internally for ED25519 verification. Anton B already knows where to get SHA512 implementation, see smartpool implementation.

Anton B: there is a bug that implementation does not borshify. Bowen W: Unfortunately the change is the protocol level change. TODO: File issue for adding borsh.

* Max Z: Alex S found bugs in proof extractor and Eth2NearProver. It seems like these are all bugs, but we need to polish some code around nibbles and paths in extension/leaf. Going to be working on tests and CI;
* Bo Y: productionized some devops, e.g. added docker.

### Notes

* Anton B and Max Z: Noticed some strange behaviour when trying to get `LightClientBlockView` for block indices far from the head and close to genesis. TODO: File issue;
* Anton B need to pair program ED255129 and SHA512 verification in Solidity see https://github.com/near/rainbow-bridge/issues/42 ;

## 08.05.2020

### High-level goals
* Anton B: NearClient testing -- we need to extract light client borsh objects and write tests with them;
* Anton B: NearProver -- next step;
* Missing components from the DevOps:
  * Productionize it;
  * Connect NearClient?
  * There is a Web3 bug.
* EthProver -- fixing on Max Z;


### Progress
* (copied from Middleware Sync) Anton B: Switched from EthProver. EthProver has some issues with validating nodes of the trie. Switched to NearClient, read already the spec of the Light Client. Updated all the structures that are Borsh deserialized. Partially implemented light client verification;

AntonB: Updated the code to the new light client spec, following the spec. Rewrote some deserialization code. Added ability to calculate hash of raw serialized bytes. Wrote light client verification code, but we cannot test yet.

## 01.05.2020

### Progress
* Pair programming with Misha. verify_trie_proof seems to be fixed. However it seems like not paths in that function are getting executed. Anton B is working on created more tests that excersize all paths.
* Currently using index.js to print stuff to plug it in later into tests.rs;

### Action items

* Brush up the rainbowup Zenhub board
* Extract various proofs from Mainnet and submit it to the EthProver to have proper tasting. The file called extract.js
* Rework index.js into a testing service and use rainbowup.

After this is done:
* Anton B will continue working on NearClient. It is all about implementing the client spec (The borsh decoding should be working already);
* For NearProver we need to decide what exactly we want to prove.

* Should also rename ethbridge and nearbridge to *client;

### Useful links for EthProver
* https://github.com/ethereum/wiki/wiki/Patricia-Tree#example-trie
* https://ethereum.stackexchange.com/questions/268/ethereum-block-architecture/6413#6413



## 24.04.2020

Since Middelware Sync:

* Anton B: Worked on slides for DeFi primitives demo. He tried to have a focus on DeFi aggregation. Currently working on: extracting proof, and deserializing ethereum structures in Near contract (some issues on this line: https://github.com/nearprotocol/near-bridge/blob/ethprover-test/ethprover/src/lib.rs#L113 . There are two different types of receipt serialization, one before Byzantine fork and one after). Some difficulties with reproducibility of commands on Ganache. After working on deserialization issues will work on proof issues;
* Max Z: Rainbowup can do prepare, run, stop, clear for Near node and Ganache. Attaching relayers.

### Notes
* If there is a security issue in one of our libraries and we fix it we need to notify everyone who depends on our libraries.

## 17.04.2020
* Eth event prover -- Anton B: some progress on DevOps, got stuck, needs help from Evgeny K. Has problems when bash script is composed of other bash scripts, the traps are not working when they are defined in multiple bash scripts.

### Notes
* Add nearup to npm;
* FYI Max Z is working on bridgeup;
* Anton B: Wrote script that extracts events from Ganache. Will extract proofs and use them for testing so that we don't need to relaunch. This will allow us to parallelize work on Eth prover fixes and DevOps;

## 10.04.2020
* Eth event prover -- beginning of the next week;
* Near result prover -- not implemented yet. (Important PR with fields reordering was merged by Alexey F);
* Near light client -- waiting for the new spec from Alex S. (Depending on the spec will take from several days to several weeks);
* Token example -- will reuse components from Eth event prover testing.
* Cross-contract calls potentially built by community. 

### Notes
* Anton B: In the future, we will introduce additional smart contracts for IBC compatibility on Eth bridge;
* Anton B: Discovered OpenZeppelin tool, OZ, for Ethereum contract compilation, deployment, etc. Will have with DevOps and will replace current bash scripts. Would need to loop in Bo Y or/and Vlad F to help with DevOps;
* Anton B: Getting close to write test that certain Eth event was included in the block;
* Anton B: Refactoring Bash scripts;
* Anton B: Cross-contract calls might require additional security, like checking whether the call is performed through callback.

## 27.03.2020
* Verifiers QA - March 25 - 29;
* Near light client - plus 2-3 weeks. Still reading and researching spec;
* Token example. April 5 - April 12;
* Cross-contract calls.

### Notes
* Some progress with calling verifiers from other contracts. A couple of more days needed to wrap up;
* Just made script to work with local node;
* Max Z: Ask Alex S to provide formal verification rules;
* Anton B: Instantiation id for the contract deployment. And then pass additionaly instantiation id with account id, as predecessor instantiation id. This would be useful for sharded contract calls mental model that we can build the course around. Can be added past Mainnet;
* Anton B: Still considering multicontract per account, post Mainnet. Try to add some code to make it forward-compatible;
* Anton B: Important thing to take care of: Make sure our contracts are not breaking when we change fees. Interesting idea: if we really need to increase the cost of one operation we need to lower everything else to amortize the total cost;
* Anton B: We should consider having a write-up on rules an guarantees like: we will not increase fees, etc;
* Anton B: Consider being able to run old contracts with old runtimes. TON blockchain had an idea of TON runtime;
* Anton B: An article on how blockchain contracts should not be used: https://medium.com/bitclave/how-to-cook-blockchain-right-dedaed1da57d

## 20.03.2020
Pushing deadlines by one more week.
* Verifiers QA - March 25-29;
* Near light client - plus 2-3 weeks;
* Token example. March 29- April 5;
* Cross-contract calls.

### Notes
* Soliciting ideas for the online hackathon;
* We use Ethash proof software provided by kyber to compute DAG proofs. The tool operates with 32-bit manipulations, maing it hard to understand.
* We have different source of pseudorandomness for header verification: https://github.com/ethereum/go-ethereum/blob/525116dbff916825463931361f75e75e955c12e2/core/blockchain.go#L863 It should be fine if we just ignore it.
* We need to describe in detail what we want to implement on the top of the bridge;
* gsn.openzeppelin.com --  nice documentation on interacting with the contract (skip part of docs related to approvals);
* There is Solidity contract with Borsh library. Trustful contract already has elements of trustless contract;
* Illia P, Anton B: In the far future, consider building sharded DeX on near that interacts with its users in p2p manner.

### Action items
* Evgeny K to send token standard NEP to Anton B;
* Evgeny K and Anton B discuss sharded contract standard.

## 13.03.2020

### Agenda
Pushing deadlines by one week.
* Verifiers QA - March 18-22;
* Near light client - plus 2-3 weeks, need to check the lightclient spec again;
* Token example - requires contracts for both sides and nodejs scripts. March 22-27;
* Cross-contract calls - will try to engage community. Maybe run a hackathon, e.g. online hackathon. Plan to discsuss it with Ash, Alexandra, Anais. Maybe bounty program, maybe plus online hackathon.

### Notes
* Helpful reading info: https://github.com/nearprotocol/near-bridge/wiki
* Online hackathons. Unfortunately, online hackathons are not regular. We need to simply google the list. What makes the online hackathon famous is the status of the main company that leads them. Some hackathons last for few weeks, some for few months. We should aim for short hackathons.
* Bounty programs. We should consider gitcoin. The task needs to be very specific and well-defined. The specificity is really important. The motivation for doing bounty is usually about fun and challenge.
* Anton B to think of list of things we would like to fix in third-party dependencies for bounties.

## 06.03.2020

### Agenda

High-level focus with notes:
* ETAs;
* Bridge spec - discuss ETA together with Sherif and Anton B;
* Verifiers QA - March 11-15;
* Near light client - plus 2-3 weeks, need to check the lightclient spec again;
* Token example - requires contracts for both sides and nodejs scripts. March 15-19;
* Cross-contract calls - will rely heavily on incentives, like GSN. First we need to come up with design and someone else can implement it. Near->Eth call can be achieved first because of GSN, design by the end of the March for design+implementation. The opposite Eth->Near call will require implementing GSN, plus one month for design and implementation.


Other notes:
* Anton B. Discussing spec with Sherif. Discussing renaming to disambiguate. Core structure of eth bridge does not cover economic incentives, but we will be able to add this alter on top to allow more use cases;
* Anton B & Evgeny K. Discuss with Sherif the instructions on how to run and test the bridge;
* Anton B also sketched sharded fungible token, following the discussion with Alexey F.
