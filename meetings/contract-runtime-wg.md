# Contract Runtime Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular Contract Runtime sync meeting. Please propose agenda items through the PRs and Issues.

The scope of the contract runtime is the following:
* Wasm execution on the host:
  * Wasm VM and glue code around Wasmer+singlepass;
  * Code preparation tools, like gas injection and static checks;
  * Approaches to efficient caching of compilation;
* Gas metering:
  * How to make it faster while keeping safety;
  * Platform-independent gas fees estimation for Wasm operations;
* Host-guest interfacing:
  * How to pass complex structures efficiently between host and guest;
  * Contract-language specific mechanisms, e.g. Rust/AssemblyScript glue code needed to communicate across boundary;
* Rust/AssemblyScript to Wasm compilation:
  * Hiding low-level Wasm and glue code behind high-level contract interface. E.g. macros for Rust, compiler hooks for AssemblyScript;
  * Language-specific structure of the contract, how developers indicate contract methods, contract state, etc;
  * Best practices and the guidelines for contract development and compilation. Guidelines for optimizing contract for: Wasm size, gas usage, safety.

Short- and medium-term goal: Safe and highly performant contract execution runtime for Near;
Long-term goal: Near-independent and non-specific to Near contract execution runtime for general blockchains.

## 05.06.2020

### Current focus
* Adding another runtime -- Wasmtime to check consistency of Wasm execution. Nikolay I had a lot of progress prototyping binding Wasmtime with our node. Unfortunately, there is a critical bug in Wasmtime which prevents of calling our host functions. Wasmtime can only handle signed types in the their interface, while we use unsigned types. The integration seems to be feasible, so far. We need to understand how to do arguments passing and return values handling. As a side effect we are making our contract runtime Wasmer agnostic, e.g. we are not importing Wasmer dependencies.
* Cost estimation of contract invocation based on the contract size (see https://app.zenhub.com/workspaces/chainmiddleware-5cea2bcf78297c385cf0ec81/issues/nearprotocol/nearcore/2778);
* Nikolay I finished FAQ for contract runtime. Currently blocked by Max Z.

### Status update
* Nikolay I: Discussed with Willem how to split their current work items.
* Willem W: Pushed changes to aspect. Met with DevX team and brainstormed various projects we can set bounties for, e.g. add `include_bytes` into AssemblyScript. Started writing issues for the bounties and started looking into adding the fee.

### Note
* Evgeny K: Wasmer actually casts unsigned integers to signed behind the scenes, btw;
* Nikolay I: Wasmer thinking about providing interface to wrap other runtimes consistent with their interface.
* Alexey F: How about fusing Wasmer and Wasmtime and run both for each contract execution? Nikolay I: We might run it at a special mode. We might not even take a hit on TPS because it is very parallelizable.
* Willem W: Can we run V8 safely? Nikolay I: He might be the most familiar with V8, but it might not be easy to embed.
* Nikolay I: We should fuzz these machines. Max Z: We can repurpose fuzzers that some security audit companies provided for us.
* Bowen W: Bowen talked about contract migration with Dev X. The problem statement: user updates the contract code and they want to continue using the old state. Max Z: We might solve it by having rigorous best practices. Alexey F: We can constraint a bunch of stuff on near-sdk level. Evgeny K: We might do some work on near-sdk, e.g. we can include version of the contract into the key that we use to store the contract state. Nikolay I: Overall it might be hard to automate, because the translation is semantic in many cases.

## 29.05.2020

* Work group goals:
  * Coordinate the contract runtime work at NEAR
  * Less hard external dependencies, ownership of the contract runtime
  * Improve the runtime quality
  * More testing
  * Bugfixing
  * More specs
  * New runtime/compiler development
  * Collect runtime/compiler expertise at NEAR
  * Coordinate with the WASM blockchain community
  * Participate in WASM design processes, wherever relevant (i.e gas metering standards, GC proposal, etc)
  * Safety is an important priority

* Work group non-goals:
   * Define architecture of the blockchain from the runtime side, more reactive to the actual designs from the blockchain side
   * Be the only source of truth for runtime decisions at NEAR, more like advisory board

* Work group directions:
   * Maintain and improve Wasmer runtime
   * Understand the future contract runtime engine components, (i.e. stick with Wasmer, switch to something else, in-house development) 
   * Better understand AssemblyScript role at NEAR
   * Code analysis tools
   * Compilers for other languages
   * Near SDK?
   * Standardized testing environment for contracts
   * Best practises for contract devs via tooling
   * Verifiable reproducible builds
   * Verify VM correctness by running a node with an alternative VM implementation
   * Benchmarking for selected set of configs: interpreter, singlepass, cranelift, smth else

* Work group members:
   * Nikolay
   * Willam
   * Max as goals/priority provider from the blockchain side
   * Shall be generally open to other people as a 20% project, fluid per interest of people and the company
   * No runtime teams boundaries for sure
   * Can temp contract people on as needed basics

* Action items:
   * Agree on short/mid/long term goals
   * Use github https://github.com/orgs/near/teams/nearcore-middleware discussion group as a communication channel