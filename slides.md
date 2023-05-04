---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# page transition
transition: slide-left
# use UnoCSS
css: unocss
---

# Introducing Riffs

Composable Contracts for Soroban

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---

# Background: [Smart Deploy](https://github.com/TENK-DAO/smart-deploy)

Semantic versioning for Wasm binaries

- Not just the rev hashes given by `soroban install`
- Instead: named contracts with semantic versions
- Contracts can be easily upgraded to new versions
- Smart-contract based registry of named & versioned contracts to deploy

---
layout: default
---

# Some challenges

- Right now, if you deploy a contract without redeploy functionality, it can never be upgraded
- How to maintain consistent owners/admins for a contract between versions?
- If the storage keys change between versions, you brick your contract

---

# A solution: [Riffs](https://github.com/loambuild/loam-sdk)

Composability for Soroban Smart Contracts

A Riff:

- reusable chunk of logic for smart contracts
- almost like middleware
- creates an external interface for the contract (methods + args)
- flexible implementation with sensible defaults that allows extending and modifying behavior
- distributed as Rust crates

<!--
  - default implementations called by default
  - methods can be overwritten
-->

---
layout: image-right
image: https://source.unsplash.com/collection/94734566/1920x1080
---

# What makes a Riff a Riff

```toml
[package.metadata.riff]
export = true
```

Example: [loam-sdk-core-riff](https://github.com/loambuild/loam-sdk/blob/5473bb20fb3c818e7c30652fadf66647760a408d/crates/loam-core/Cargo.toml)

---

# How to use a Riff

## Cargo.toml

```toml
loam-sdk-core-riff = { workspace = true }
```

## lib.rs

```rs {all|11}
#![no_std]
use loam_sdk::{soroban_contract, soroban_sdk};
use loam_sdk_core_riff::{owner::Owner, CoreRiff};

pub struct Contract;

impl CoreRiff for Contract {
    type Impl = Owner;
}

soroban_contract!();
```

Example: [loam-sdk/examples/soroban/core](https://github.com/loambuild/loam-sdk/blob/5473bb20fb3c818e7c30652fadf66647760a408d/examples/soroban/core/src/lib.rs)

<!--
`soroban_contract!` checks all dependencies to find any that include
`package.metadata.riff` in their Cargo.toml then generates the needed
`soroban_sdk` code for every method of every riff. `Contract` implements all of
them and is called for each of them.
-->

---

# A larger example

&nbsp;

See how easy it is to combine multiple Riffs in the [Smart Deploy codebase](https://github.com/TENK-DAO/smart-deploy/tree/feat/loam/crates/smartdeploy)
