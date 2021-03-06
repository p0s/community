# EVM on NEAR
This thread contains agenda and/or summary of the regular EVM sync meeting. Please propose agenda items through the PRs and Issues.

Current eng team:
* Evgeny Kuzyakov -- TechLead (running this meeting);
* Bo Yao -- Gas estimates, PR reviews, code audit;
* Mike Purvis -- Launching existing dApps (includes JS, examples);
* Illia Polosukhin -- initial bulk code development;
* Misha Kever -- final PR reviews as an expert;
* Evgeny Kapun -- security-related reviews.

## Action point long list
- [X] 30 Oct, Alex: Loop in Sandi for Betanet setup & deployment of EVM there
- [X] 23 Oct, Eugene: Show during the next meeting the future upgradability approach
  - See here: https://github.com/near/nearcore/issues/3544
- [X] 23 Oct, Alex: to have a call with Eugene on the scope for the Phase 2
- [X] 23 Oct, Mike: to report on the `ECRECOVER` issue on Tuesday, 27th October
- [X] 16 Oct, Alex: to share the roadmap and collect the feedback internally
- [X] 16 Oct, Alex: to sync with Bowen on the importance of the feature flags epic on the protocol level
  - Mainnet guild is fully aware of the blocker and Bowen already created a PR: https://github.com/near/nearcore/pull/3503
- [X] 16 Oct, Alex: to share the RLP encoding findings with bridge team (`EthereumJS` is an appropriate lib to do it, others may do it wrong)
  - Done, detailed internal conversation between EVM & Bridge team started 
- [X] 16 Oct, Max: to sync with Peter with regard to tutorial video from Austin
  - Everybody synced, the video will be produced once the EVM deployed at Crashnet or any other network. As an additional AP: to prepare a set of issues for the community (bounties).

## 06.11.2020
**Mike Purvis**
- Health work on PRs (marking `Draft`, etc.)
- Oracle system of Chainlink price feeds inside the EVM

**Bo Yao**
- Working on multiple PRs: ([1](https://github.com/near/nearcore/pull/3571), [2]( https://github.com/near/nearcore/pull/3564), [3](https://github.com/near/nearcore/pull/3534))

**Arto**
- Upgraded the EVM to 2.6.8
- Reading the codebase
- Starting the next week available full time
- Next step is 2.7.*

**Eugene**
- Rebased the branch to current master
- Waiting for fix from Bowen on the `nighly features`
- CryptoZombies has one failing test(view call), rests of the tests are passing. No understanding why is it failing (wrong ABI?)
- No work on upgradability <- should be done before the release
- Finalised OKRs on the call extension https://near.ai/evm-okrs
  
**Questions**
Betanet release requirements:
- Merge the EVM branch to master:
  - Make sure that we're introducing EVM behind a `nightly feature` Bowen: No problem expected. Just guard all the EVM code by `feature` flag
  - (Internal) upgradability needs to be done
  - Passing CI, not affecting the master
- Mike: Do we need a separate shard for EVM?
  - No need to do it right now, let's think of this closer to the `Testnet` release 


## 30.10.2020
**Mike Purvis**
- Worked on `ECRECOVER` tasks. The decision was to push this to the moment when we want to implement meta-tx. Bo is going to take over this issue.
- Working on the Petshop example
- Working on all additional tooling (`near-api-js`, `near-web3-provider`, `near-cli` - 2 new calls, etc.)
  - `near-cli` PR ready to merge
  - `near-api-js` PR ready to merge

**Eugene Kuzuakov**
- Mostly on vacation
- Need to wrap inner structures in EVM: Precompile `meta-call-args` need to wrap into `enums` with versions (see the upgradability action item from lst weeks' call)

**Bo Yao**
- `ECRECOVER` is correct now
- Issue found: `prepare_meta_call_args` give different msg than `eth-sig-util`'s `TypedDataUtils.encodeData`
  - Fixed `near_erc721_domain` to encode same as `TypedDataUtils`, should be similar to fix entire prepare_meta_call_args
- The plan is to merge [gas PR](https://github.com/near/nearcore/pull/3299) today (after doing "do not change genesis" and "resolve conflict")

**Arto**
- Working on the EVM upgrade taks. Submitted first [PR for the update of the EVM to 2.6.2](https://github.com/near/nearcore/pull/3542)
- Update to a 2.6.8 would be prepared as a next step
- Ethereum 3.0.1 is set as a target for the update. 

**Questions**
- Illia to Evgeny: do we need to make an enum wrapping or not? This will change the logic in many tools.
  - Evgeny: No major problems should occur. See the discussion [here](https://github.com/near/nearcore/issues/3544)
- Alex: does everybody OK with merging Gas PR?
  - No major problems should appear, except to perhaps genesis config or some other side-effects. Data and configs need to be guarded by the feature flags.


## 23.10.2020
**Mike Purvis**
- Relayer has a PR with the meta-txs support
- Rust needs to recover the address of the account, still not working. The test for the `ECRECOVER` is not working.
  - Max: `ECRECOVER` wants to have specific formatting. Mike: This might conflict with `ERC-712`; this is not a problem at this moment.
- Relayer is not charging users until we move from the Betanet
- Petshop example need to have an option for the login (and store the info in the local storage in browser). Need to update the README.

**Eugene Kuzuakov**
- Figuring out what features to ship within Phase 2

**Bo Yao**
- Finalised PR on deducting cost from EVM transactions
- Corrected a couple of scenarios for fees calculation
- Achieved <10% volatility on the gas costs
- Working on the Balancer contract Truffle tests. Multiple issues occured: address unknown for the contract (when using Truffle migrate).

**Arto**
- Should Arto take a look at Gas estimations within updating the EVM code?
  - We just use Parity calculation, so it is assumed that EVM Gas costs are accurate. So the updates should be done.
- Working on the setting up and bringing himself up-to-date


## 16.10.2020

**Mike Purvis**:
- `ECRECOVER` is not working properly. `EthSigUtil` seems to solve this issue
- Migrations are not working properly, added troubleshooting to the README.
- RLP Encoding: `ethereum.js` is the library that should be used:
- Signing in Metamask: how to make it human-readable within Petshop example => translate it into ABI encoding (Illia: methods in Ethereum are just integers)?
  - Possible solution: show method name, parameter, attach the ABI encoding to transaction 
 
**Peter, Community update**:
- Contributor (compilers experience) can help with implementation
- 3 super competent teams that can be used within Bounty programs
- Need to help to Austin Griffin (deployment of his tutorial project in a private network, shot a video out of it)

**Eugene Kuzuyakov**
- Gas calculations are set to higher limits now
- Bo work blocks the PR
- Feature flags as a protocol change from the chain team (assigned to Bowen)
 
**Bo Yao**
- Volatility depends on the size of the contract
- Linear regression seems to be a good approximation
- Massive testing is on the way (gas costs depend on the state size), all works good
- Sometimes costs are below 1 EVM Gas / byte, which is rounded to 0. What to do with this?
  - Illia: We need to have a function depending on the length. Why don't use the gas calculations from Parity and then rescale?

## 09.10.2020

### Status
* Mike Purvis

**Progress**:
- Reworked a JavaScript test from EIP712 into a function that'll do abi.encodePacked for typed data, which is needed to create the digest that is passed to ecrecover.
- Created basic relayer which can recover address and check the validity of signature passed to it
- Switched to using web3's Contract class instead of TruffleContract because of odd inconsistencies
- Getting nonce from EVM account id on frontend

**Need to**:
- Promisify all functions to use async await (I think only one left)
- Refactor providers to mimic how Gas Station Network does it
- Modify typed data "message" to be structured as NearTx which is expected by EVM
- Get relayer using more robust JS packages like nodemon, possibly TypeScript, move functionality out of a single file

**Concerns**:
We'll want to be able to use NEAR Web3 Provider if they've logged in with Wallet, but right now the GCP instance doesn't have Wallet, Explorer, and I assume some indexing between them? Would be great to have the `evm-precompile` branch merged so we can start seeing this example on a "live" network.

* Eugene Kuzyakov -- need to review the new EVM fixes from Illia Polosukhin, and think we would need at least a few months to run it on a separate chain before Mainnet. We might need to hide it behind a separate compilation feature. We should create rewards for runnin EVM network and try to break it. We will preserve the state during hardforks;

* Bo Yao
  * debug genesis-populate hang
  * refactor count evm deploy contract cost to use testlib instead of hack
  * pin size of state to count evm cost to reduce one volatility factor
  * there's another volatility of evm deploy contract cost, found reason is difference between runs is little, but already greater than different contract in the same run. solution is using evm contract has bigger difference in size and count (got it to 4% error volatility).

### Notes
* Alex Shevchenko -- requirement for Mainnet launch is having explorer supporting detailed EVM info;
* Alex Shevchenko: We might not implement GSN exactly the same way they did it. It has a very large scope. Alex Shevchenko and Mike Purvis need to discuss it
* Max Zavershynskyi: If we want EVM to be on Mainnet EOY and Eugene wants to run it on separate network for several months then we need to speed up current code iterations so that Eugene Kuzyakov and Illia Polosukhin are done in less than 1 month. We might need more people working on it.

### AI
* Alex Shevchenko and Max Zavershynskyi need to sync what blockers EVM creates for the bridge;
* Disuss extra network with Infra Team;

## 02.10.2020

### Status
* Not much update from Illia P an Mike P;
* Mike P added a simple express relay for Metamask. Mike P and Illia P to sync on multiple web3-providers :)
* Bo Yao: The fees are volatile, spent week debugging it. The EVM itself is not volatile -- at most 1% volatility. However, finalizing into blocks and writing state creates 30% volatility, which does not decrease when we increase the batch size. Also, note that regular state operations are actually not volatile, so this looks suspicious. Spent 2 days on investigating code. Max Z: Please create GH issues for next cycle and set estimates;
* Illia P: Needs to address comments form Eugene K on PR. Also coordinating with Bowen W to have EVM accounts ready on all networks, i.e. we need `evm` top account (we would have to erase an existing `evm` accoutn on Testnet during hardfork).
* Illia P: Added nonce to metatransactions. We need to register NEAR chain id in Ethereum repos, this is needed to prevent users from replaying the same transacions on multiple chains.
  * We might consider adding extra protection in metatransactions to prevetn replay, e.g. add block hash. To prevent the transaction from being delayed for more than some time. This might also be used instead of chain id, because it is safer;
* Bo Yao has investigated how to test contracts. EVM test suite is already testing Parity crates. Illia P: Web3 provider with truffle test suite already works, and we tested it with our partners.
 * Illia P: Suggests to add tests of some partners to nightly to run with truffle test suit.

### Notes
* 80% of Ethereum endpoints are implemented and others are not that frequently used. We were able to replace most of the stuff with similar semantics;
  * Max Z: We might need to add support of event subscription in the future, for which we might need to use indexer.
  * FYI We don't need proxy right now, it is good to have though. It is currently implemented through implementation of web3 provider.
* Alex Shevchenko: We need to put fees on metatransactions. E.g. charge custom ERC20 token inside NEAR EVM (e.g. bridged DAI) to be paid with, see dicussion in NEP about it. This should be done as a separate PR and assigned to Bo Yao.
* Another work item for Bo Yao: Make sure EVM is upgrade-friendly.

## 25.09.2020

### Status
* See checklist in the PR that needs to be done. Illia Polosukhin is still working on it;
* Mike Purvis is still working on web3js, connecting with partners, and relayer;
* Eugene Kuzyakov -- Reviewed EVM PR and found a bunch of critical bugs. Eugene has a major concern about introduction of remove subtrie operation on storage. When ETH contract suicides we need to remove all keys under it, which is similar to our account deletion. However for NEAR account deletion we use prefix iterator, which it cannot be used directly on EVM because of how Ethereum accounts are mapped to the trie. Deletion and iteration might be different implementations entirely. Also, we want to flatten the trie in the future and maybe replace merklelization which means iterate and delete operation might not be a good choice. Maybe we want to introduce sub-structure fro Ethereum accounts;
  * We cannot support operation to get block has for the given height. We have a hack for it. Evgeny Kapun: EVM only returns blocks up to some number, not up to genesis. This number is 256, which should be super easy to store for us even with GC enabled. It might require some changes in the chain. Unfortunately NEAR skips the block height, maybe we should return empty hash instead;
  * Eugene found a bunch of issues that allow passing data to precompiles and they would blow up the memory. However it is unclear whether EVM has extra protection against passign bad data, e.g. through pre-charging.
  
* Max Zavershysnkyi -- starting familiarizing with meta-transactions.

### Context

* Illia Polosukhin -- we are adding native support of metatransactions in NEAR EVM. (We can recover address of the user of ECDSA signature). In Ethereum smart contracts require extracting the address of the user (e.g. GSN contracts), while on NEAR we are going to have it natively. Our EVM will do the work of GSN, every contract inside EVM will see recovered address as `msg.sender`, unfortunately it requires end-contract to be modified.
  * Alex Shevchenko FYI. In NEAR metatransactions are not exactly needed, because we have allowance and predecessor_id. Evgeny Kapun: in NEAR or even on ETH we can have a smart contract that works as a wallet and proxies meta-transactions into regular operations. Unfortunately the UX flow is not clear.
* The way the flow works is that we are going to have a JS wrapper library on top of Web3js. Alex Shevchenko to write down detailed user flow, with interaction of components.
* The purpose of `msg.origin` in EVM -- is the signer. Currently we replace both `msg.sender` and `msg.origin` with recovered address for metatransactions, for non-metatransactions we use `predecessor_id` (maybe we can pass it as `signer_id` though).

## 18.09.2020

### Status
* Illia P was workign on adding ecrecovery. ecrecovery is critical for many existing Ethereum contracts. ecrecovery -- recover public key from signatures. Metatransaction -- user signs the message and relayer send it on behalf of the user. Go read https://github.com/nearprotocol/NEPs/pull/106 about metatransactions and mapping NEAR-EVM accounts;
* Bo Y finished EVM gas benchmarking, adding number to EVM fee config. Makes sure EVM gas does not include NEAR gas. Need to investigate why `funcall_cost_per_evm_gas` is so 20k times larger than ecrecovery cost.
* Mike P deployed a test netowrk (not to be confused with The Testnet) with EVM and some people are deploying and testing on it right now.

### TODOs
* Max Z, Evgeny Kuzyakov, and Evgeny Kapun to review PR;

## 11.09.2020

Let's define clear criteria for:
* Merging PR;
* Enabling on Betanet (blocked on param estimator and gas meter by Bo. Decide what to do with storage.); 
* Enabling on Testnet;
* Enabling on Mainnet.

It should be relatively safe to merge, because we don't have anyone deployed evm.

### Notes
* Bo approved the PR already. Ready for reviews by Evgeny Kuzyakov and Misha.
* Bo could not connect EVM gas counter correctly yet. Sometimes it fails because it is out of gas.
* Illia: Let's double check that entrance point is fine;
* Evgeny Kapun: Suggests to hardcode that every contract in .evm namespace only has EVMs;
* Illia: Storage is problematic, we need to make every function callt to EVM payable. Evgeny Kapun: What if we don't enforce storage staking on evm.
  * Illia: Things to consider: maybe we should consider how to improve state fees (should be introduce gas into storage?).
  * Let's make sure EVM-type storage is not abusable (receipt cost should prohibit abuse).

## 04.09.2020

### High-level info
* We are replacing EVM contract with native EVM;
* There are no hard deadlines but we need it ASAP.

### Milestones/Releases/Epics
#### 1. EVM precompile is ready to be merged (not ready to be launched) and can be tested on a separate network (requires fees connected);
* Integrate dApps from spreadsheet (assign to Mike P)
* Finish the spec;
* Implement the precompile
* Figure out nightly tests (assign to Bo)
* Fee estimation (assign to Bo) -- use real dApps for fee estimation and use them in nightly tests (will also create comparison to Ethereum)
* Write a blog post on how we integrate EVM

#### 2. Partners running inside EVM with all the tooling in “Testnet” (requires bridge support);
* Interop for EVM->NEAR interaction (will require help from Evgeny for runtime, near-sdk-rs)
* MintableFungibleToken in Solidity
* We need the bridge working with EVM, and we need connectors.

#### 3. Mainnet-ready
* Undecided yet

### Web3js status
Mike: Seems working, Suma says some routes might not be implemented. Logging needs cleanup. We might publish it now to npm.

### Notes
* Illia removed JSON serialization from EVM contract code;
* (orthogonal) Willem Wyndham is pulling out token library for Illia

### Discussion. Two interop ways for ETH->NEAR
* Specific hex(NEAR).bytes() precompile (suggested by Anton) -- will probably go with this one.
* Specific logs. Max Z: We cannot go with this one, because we cannot redefine gas cost for logs;


### Links:
* https://docs.google.com/spreadsheets/d/1kiIR3WtF5XsvfgdHILn48o8mglMN_GikCHuBIjcdEQ0/edit#gid=0
* https://github.com/ilblackdragon/balancer-near
* https://github.com/near/rainbow-token-connector/blob/master/bridge-token/src/lib.rs

