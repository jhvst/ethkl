---
author: From Smart Contracts to Smart Arrays - Juuso Haavisto juuso.dev
---

# From Smart Contracts to Smart Arrays

**Rethinking the World Computer Paradigm with Array Programming Languages**

---

# In this talk

- what
- why

---

# Programming has a disconnect between _communication_ and _compute_

"implicit parallelization":

- _compute_: how to use multi-core systems efficiently (type systems, parallel programming, GPUs, array programming languages)
- _communication_: how could computer networks do scheduling for us (systems theory, NixOS, SMT solvers)

Ethereum, an abstracted programming paradigm:

- the developer writes code but does not need to care where it runs
- yet, they get guarantees of proper execution

How can this be incorporated into new general-purpose languages?

---

# From _telecommunications_ to formal methods and _programming languages_

5G and beyond networks are similar to Ethereum:

- both are distributed
- both share a vision of the world computer: a network of composable computing

Huawei's "Da Vinci" Project:

- IoT and mobile devices "loan" computing from the _base stations_ and _other devices_
- the size of this network: probably quite big

Ethereum:
- storage and compute from _node-runners_, to which you pay in the form of transaction fees
- ethernodes.org claims there exists around 7.5k execution layer nodes

---

# The next Big Score in programming languages

A language that facilitates the creation of a new App Store for general-purpose computing

1. how can the computation executor trust that the computation will not hack their computer
2. how can the computation requester trust that the computation is done properly

- For 1., you can sandbox software or provide formal proofs about the correctness of the program
- For 2., much harder, the best bet is game theoretical guarantees

-> requires distribution, or "program slicing" built into the language

Array programming languages help with both points because they are constrained -- the only datatype is a rank polymorphic array, which allows to reason about code "no stinking loops"

Instead of the programmer describing how to execute the program, the infrastructure works as a "lens" which chooses the nodes and ways to slice the program for you

---

# My projects

1. IoT vacuum cleaner project (_compute_ offloading)
2. Translucent AR tablet (_communication_ protocols between devices)
3. 5G network in which you could provision software to base stations (infrastructure)
4. Array programming languages, APL on GPUs
5. Static semantics of APL in Idris
6. SMT solvers for scheduling
7. NixOS for infrastructure integration (integrates compute, communication, and infrastructure)
8. _Smart language_: implicit parallelization of BQN operations that use the SMT solvers which integrate with NixOS infrastructure

---

# Lens: a view into a data structure

The lens is an essential notion in the following slides because it connects what we expose of our infrastructure to tools

- from category theory
- "represents a bidirectional stateful computation that describes the way some systems expose and update their internal state"
- in NixOS: OS image creation ~~is~~ could be an identity arrow
- in BQN: Under ⌾ ("a donut")

```bash
cbqn -e '•Show ⟨"ab", "cde", "fg"⟩ ⊣ ⌾ ∾ ⟨"---", "----"⟩'
```


---

# NixOS: functional declaration of Linux configurations

- NixOS combines computing and communication in a single package
- NixOS vs. Docker: Docker only provides computing
- NixOS vs. Tailscale: Tailscale only provides communication

## NixOS makes Linux configurations into "legos" via Flakes
- Flakes is an interface to declare and compose Flakes from different git repositories
- Flakes can contain multiple different NixOS configurations
- Nix computes a fixed point for __all__ of the declared NixOS servers on evaluation
- -> a _cluster_ of servers can dynamically integrate as nodes are added

## What software (and why) is installed on my Linux?

```bash
nix-store --query --graph /nix/store/i287dwd5sfskij2xnn45lrysv2z23nlr-nixos-system-nixos-22.11.20221130.4d2b37a | head -5
```

---

# NixOS meets Ethereum

HomestakerOS
- a project from my company to manage Ethereum nodes
- homestakeros.com, and github.com/ponkila/homestaking-infra
- initial funding from Ethereum Foundation

## What is the endpoint URL of my execution layer client?

```bash
nix eval --raw github:ponkila/homestaking-infra#nixosConfigurations.dinar-ephemeral-alpha.config.lighthouse.endpoint
```

---

# NixOS meets Ethereum

Notice: I ran the command on my developer machine: it is not part of the cluster, but can still query information from it!

Allows dynamic environments to be built and various toolings on top of it.
- `direnv`: integrates environment triggers into `cd` command
- -> `cd` into your project, and a "lens" to your infrastructure is automatically created
- thanks to Nix, any required software for your shell environment can also be automatically installed!

## What's the current block?

```bash
curl -X GET "http://$(nix eval --raw github:ponkila/homestaking-infra#nixosConfigurations.dinar-ephemeral-alpha.config.lighthouse.endpoint):5052/eth/v1/beacon/headers/head" -H  "accept: application/json"
```

---

# NixOS meets Ethereum

Why I find this interesting:
- complete server configurations can be public, which helps others to replicate systems
- it integrates computing with communication, I don't need to know where my nodes are
- -> bridges the gap between the ease of use of using a 3rd party hosting service for RPC endpoints

The category theoretical concept of a lens is strong here:
- what I can query can be impartial of the whole underlying infrastructure: ACL
- I am not limited to read-only access; I may have read-write capabilities
- all these _constraints_ can be managed by NixOS configurations itself ...
- ... public _constraints_: I know my lens and what kind of lenses others have

---

# Constraint Programming: Meet SMT solvers

AI is more than ML

Combining constraints might create emergent properties which inhibit artificial intelligence:
- constraints: the vacuum cleaner needs to charge at some point, so it may decline your request
- optimization: vacuum cleaner might need dust-tokens to charge, so it prioritizes dust collection

The vacuum cleaner works under very basic principles but might make decisions that seem intelligent to an outside observer.

Another example: SDN Enhanced Resource Orchestration of Containerized Edge Applications for Industrial IoT
- constraint: RAM, disk space, network bandwidth
- optimization: load balancing amongst N nodes

... see where this connects to?

---

# Rivi

- my attempt at a world computer language
- GPU only: Rust-based Vulkan scheduler
- open-source: github.com/periferia-labs/rivi-loader
- todo: integrate with BQN

```bash
/Users/juuso/github/rivi-loader/target/debug/examples/capabilities
```

---

# Rivi

- taking the constraints from Vulkan, we can give a _model_ of some array operation, like a sum reduction, to get a schedule
- disclaimer: the extensibility of this is unknown, the overhead is a lot; for which it is research

```bash
cbqn -e "•Show +´ 1¨⟜↕4096"
```

```bash
nix run github:periferia-labs/rivi-scheduler -- models/reduce.eprime vk-instances/m1.param -run-solver -solutions-to-stdout
```

---

# Takeaways

## We showed how we can do _compute_ with array programming language, which is distributed with _SMT solver_ and uses _NixOS_-based lens to abstract the infrastructure

- communication is arguably more important than computing nowadays
- new languages should integrate _communication_ and _compute_ together
- -> instead of building the web3 stack up, how could existing protocols be used more efficiently?

---

# BQN "Big Question Notation"

Descendant of APL, A Programming Language

Array programming languages
- a single datatype: a rank polymorphic array
- rank (e.g., scalar, vector, matrix, cuboid) polymorphism (greek: many-forms)

```APL
  1 + 1 2 3 4
2 3 4 5

  1‿2‿3 + 3‿3⥊↕9
┌─
╵ 1  2  3
  5  6  7
  9 10 11
          ┘

  +´ ⥊ 1‿2‿3 + 3‿3⥊↕9
54
```

