# Design philosophies

Language contracts for this fleet, written in the register of the [PyTorch Design Philosophy](https://docs.pytorch.org/docs/stable/community/design.html): named principles, explicit refusals, usable as review criteria.

These are not the Zen of Python, the Rust Book preface, or the C++ Core Guidelines reprinted. They are inferred from work this stack treats as exemplary: PyTorch, NVIDIA NeMo, LangGraph / LangChain, Ruff, Candle, Burn, CUDA C++20, Next.js, shadcn/ui.

Published principles are marked. The rest is a working contract, not a citation.

---

## First principles

**Stance.** AI tools exponentiate human output. They do not replace computer science, and they do not excuse a shallow relationship with the code.

Grok and Cursor are force multipliers: more drafts, more tests, more refactors per hour. The craft is still the ability to say why a design is right in the vocabulary of algorithms, systems, and measurement. Quality is not "the agent said it compiles." Quality is the time and space class you chose, the profile you ran, the call graph you can walk, and the invariants you can defend.

Hard sciences (physics, neuroscience, robotics, climate, markets) only move when the software underneath them is understood well enough to change. If you cannot find the hot function, you cannot support the lab.

### Principles

1. **Exponentiate output, not opacity**
   Use Grok, Cursor, and their subagents to write, review, and generate harnesses. The human still owns the specification, the complexity class, and the merge. An agent that produces a thousand lines you cannot explain has subtracted from the craft.

2. **Time and space are the quality bar**
   Before calling a change "better," name the costs. `T(n)` and `S(n)` are first-class review comments. Asymptotics are the claim; constants are what profiles catch. A clever abstraction that moves work from `O(n)` to `O(n log n)` in the name of cleanliness is not clean.

3. **Measure the program you actually run**
   Run profilers. Run linters. Dump real call graphs, not the architecture slide. Trace allocations, kernel launches, cache misses, and token counts the same way: instrument, read the output, change the code. Guessing from vibes is not an engineering skill.

4. **Deep dives are part of the job**
   Read the generated code. Walk the module until you can redraw the data flow from memory. If an agent wrote it, that raises the bar for the dive, it does not lower it. You cannot maintain what you have only prompted.

5. **Craft is love of the mechanism**
   Enjoyment of the work includes types, ownership, memory hierarchies, and proofs of the boring kind (this buffer dies here; this lock is not held there). Speed of generation is allowed. Contempt for the mechanism is not.

6. **The point is the hard sciences**
   Software exists here to carry models, instruments, and experiments farther. A philosophy that only optimizes for shipping chat features will not survive contact with a training run, a ROS graph, or a simulation timestep. Keep the skills that let you go from notebook to kernel.

7. **Best practices are the floor; patterns name seams**
   Tests, review, formatters, linters, and CI are not optional because an agent wrote the diff. A design pattern is allowed when it names a real variation you can point at. Cargo-cult architecture is opacity. See [Engineering practice and patterns](#engineering-practice-and-patterns).

### Refuse

- Merging agent output you cannot reconstruct from a blank file.
- "The linter is optional because the model is good."
- Complexity theater: new layers with no change in `T(n)`, `S(n)`, or a profile.
- Treating Grok or Cursor as the system of record for what the code does.
- A "clean" pattern with no test and no named seam.

### Done when

You can explain the algorithm, point at the profile, walk the call graph, and change the hot path without asking the agent to remember it for you.

---

## Engineering practice and patterns

**Stance.** Common software engineering practice is the floor. Design patterns are named tools for real seams. Neither replaces `T(n)`, a test, or a profile.

This is not a Gang of Four catalog. If you cannot say which seam the pattern is for in one sentence, you do not need the pattern yet.

### Principles

1. **The floor is not optional**
   Tests, code review, linters, formatters, CI, explicit interfaces, fail-loud errors, versioned public APIs, no secrets in the tree. An agent that skips the floor has not shipped. The same floor applies to labs (you write the tests) and factories (the team writes the tests against the spec).

2. **Name the seam, then pick the pattern**
   Use a pattern when it names a variation that already exists:
   - **Strategy** — interchangeable algorithms behind one interface (optimizers, trackers, rendering modes).
   - **Factory / builder** — init that chooses a product from a spec (`@init-*`, device/backend, board).
   - **Adapter** — a language or runtime boundary (Python calling CUDA, TS host calling a Rust crate).
   - **Pipeline / chain** — ordered constraints that must stay inspectable.
   - **Observer / event** — a stream you subscribe to, not a hidden callback soup.
   - **Module / composition** — `nn.Module`, owned UI components, ROS nodes, Zephyr threads.

   Prefer three clear functions over an Abstract Factory around one class.

3. **Composition over inheritance**
   Objects contain behavior. Trees that pretend to share it rot. PyTorch modules, shadcn components, Rust traits, and C++20 concepts are the same idea. Inheritance is allowed when it is a true is-a and the base is stable.

4. **SOLID as pressure, not liturgy**
   Single responsibility at the module you test. Open for extension at a trait, protocol, or interface; closed for smash-and-edit of the hot path. Do not invent an interface for one implementation. Depend on the seam, not the concrete trainer, renderer, or kernel.

5. **Errors are part of the API**
   Python: exceptions with context, no bare `except`. TypeScript: typed results at process and network edges. Rust: `Result`, no `unwrap` on a library path. C++20: RAII so failure cannot leak a resource. Logging is not control flow.

6. **Tests specify; CI enforces**
   Unit tests for the function. Golden or property tests for the numerical path. Integration tests for the seam. CI is the floor agents must pass. A pattern with no test is a slide.

### How it shows up per language

| Language | Patterns that usually earn their keep | Floor |
| --- | --- | --- |
| Python | `nn.Module` composition; Strategy + registry; graph of functions for agents | pytest, ruff, typed seams at IO |
| TypeScript | Owned component composition; typed ports; server/client as an explicit Adapter | tsc, eslint, tests on the route |
| Rust | Traits instead of inheritance; newtypes for invariants; `Result` as the error pattern | cargo test, clippy, no library unwrap |
| C++20 / CUDA | RAII and Rule of Zero on the host; Strategy at the host API, not inside the kernel | sanitizers, Nsight, no raw `new` |

### Refuse

- Gang of Four bingo.
- Abstract Factory around a single concrete type.
- Inheritance as a reuse hammer.
- Skipping tests because the diagram looked clean.
- New layers that do not change `T(n)`, `S(n)`, or a failure mode.

### Done when

You can name the seam, the pattern (or why none), the test that pins it, and the complexity it did not worsen.

---

## Python

**Stance.** Python is the language in which the idea is allowed to exist before it is allowed to be fast.

PyTorch's surprise first principle still holds: usability over performance. A researcher who can reshape the model this afternoon beats a kernel that is 15% faster and frozen. Performance is a secondary goal, and it must stay *reasonable* — trading a little speed for a much simpler model is acceptable; doubling runtime to keep an API cute is not.

### Principles

1. **Usability over performance** *(PyTorch, published)*
   Primary goal is that a competent person can read, change, and debug the program. Secondary goal is reasonable speed. Do not enter a "limits-first" paradigm (static shapes only, graph-mode only, schema-first agents) unless the trade is written down.

2. **Simple over easy** *(PyTorch, published; from the Zen)*
   Explicit is better than implicit. Simple is better than complex. "Easy" hides a decision you will have to unhide later. Do not automate the first time you see a pattern. Delay automation until the pattern has a name.

3. **Python first, interoperability second** *(PyTorch, published)*
   Python is not a binding onto a sealed C++ monolith. The user-facing object is a Python object. When you drop into C++, CUDA, or Rust, you do it at a seam the Python caller can still see: a module, an extension, a tensor that still prints.

4. **Code is the model**
   A training step, a data loader, and an agent node should be ordinary functions you can put a breakpoint in. Eager execution is the default. Compilers (`torch.compile`, graph export, fused kernels) are optimizations of that code, not a second language the author must learn first.

5. **Few assumptions about the future** *(LangGraph, published)*
   LLMs are slow, flaky, and open-ended. Frameworks that bet the farm on this year's agent abstraction rot. Prefer graphs of functions, typed state, and a runtime that can interrupt, resume, and trace. The biggest competitor to a framework is no framework — every constraint on the caller must buy a feature that is expensive to rebuild.

6. **Actors compose; resources are explicit** *(NeMo, inferred)*
   Training, inference, reward, critic, and environment are separate actors. Resource them, isolate them, coordinate them, communicate between them. Do not smuggle a global CUDA context across an actor boundary and call it a platform.

### Refuse

- Magic that cannot be printed, stepped, or monkeypatched.
- "The YAML is the program."
- Shipping a research idea that only exists inside a graph compiler.
- Wrapping LangChain so thoroughly that the node is no longer a function.

### Done when

You can delete the framework import, keep the function, and still explain the algorithm on a whiteboard. You can also say whether the step is `O(n)` in batch or hidden-quadratic in the graph, and you have a profile if you claim it is fast.

---

## TypeScript

**Stance.** TypeScript is the language in which the boundary is named before the implementation is owned.

Python optimizes for the afternoon you change the idea. TypeScript optimizes for the month you change the team. Types are not ceremony. They are the contract at the edge: server/client, route/component, tool/model, token/variant.

### Principles

1. **Own the source** *(shadcn/ui, published as Open Code / copy-don't-install)*
   Dependencies that own your UI will own your product. Generate or copy the component, then it is yours. Beautiful defaults are a starting point, not a theme engine you cannot fork.

2. **Types are the seam**
   Public functions have explicit input and output types. Agent state is a typed object, not `any` plus hope. If a value crosses a process, a network, or `"use client"`, its type is written down. Inference is for locals; contracts are for edges.

3. **Static and dynamic are a spectrum** *(Next.js, published)*
   The boundary is the component (or the function), not the route. A page may have a static shell, a cached query, and a streaming island. Cache a function; revalidate a tag. Do not force the author to split "the static app" from "the dynamic app" on day one.

4. **It should feel like writing code** *(LangGraph, published)*
   The SDK is not a second runtime. Nodes are functions. Edges are functions. Framework features (interrupt, checkpoint, stream) are building blocks you reach for, not a lifestyle. If skipping the framework is easier than using it, the API is wrong.

5. **Server by default, client by exception**
   Fetch and render on the server when you can. Mark interactivity deliberately. Client JS is a cost, not a default. Accessibility and keyboard behavior come from primitives (Radix-class), not from restyling a `<div>` until it looks like a dialog.

6. **Open code is AI-ready** *(shadcn/ui, published)*
   A consistent, readable component surface is how both humans and models extend the system. If an agent cannot find the button variant without scraping a node_module, the design system is closed.

### Refuse

- CSS-in-JS runtimes that steal the design system from the file tree.
- `any` at module boundaries "just for now."
- A component library you cannot open.
- Putting the agent loop only on the client because the tutorial did.

### Done when

A new route can be added by composing typed functions and owned components, without negotiating with a black-box theme or an untyped tool call. You know what runs on the server, what ships to the client, and you have measured the JS and the render path.

---

## Rust

**Stance.** Rust is the language in which the invariant is checked before the binary is small.

Python keeps the idea fluid. Rust makes the idea deployable: no GIL, no interpreter tax, a binary you can start in a serverless window. The APIs that work here look like PyTorch on purpose. The memory model does not.

### Principles

1. **Correctness is the default; speed is the culture** *(Ruff, inferred)*
   Rust already refuses a class of bugs at compile time. That is not enough. Treat latency and throughput as first-class, measured, and regressable. Ruff did not get fast by being written in Rust once. It stayed fast by making speed a review criterion while growing scope.

2. **One toolchain, not a pile of plugins** *(Ruff, inferred)*
   Reimplement the rule in-process. Do not shell out to five Python tools and call it an integration. A single CLI, a single config surface, hierarchical overrides. Compatibility with the old ecosystem is a port, not a wrapper.

3. **PyTorch-shaped API, Rust-shaped invariants** *(Candle, published as "looks and feels like PyTorch")*
   `tensor.matmul(&other)?` should read like `a @ b`. Devices, dtypes, and shapes are explicit. Errors are `Result`, not exceptions you forgot. Borrowing and ownership replace the reference-counting reflex you brought from Python.

4. **Small binaries when you serve; one codebase when you train** *(Candle + Burn)*
   Candle's reason to exist is serverless: ship a small binary, drop Python from production, keep Hugging Face formats (`safetensors`, tokenizers). Burn's reason is unification: the code that trains is the code that runs, with backends behind a trait and fusion behind the API. Pick the constraint the product actually has. Do not pretend every crate must do both.

5. **Ownership is the memory model** *(Burn, inferred)*
   Use moves to say "this tensor is consumed." That is how you get dynamic graphs with static-graph performance without a tracing DSL the user has to learn. Cloning to silence the compiler is a performance bug until proven otherwise.

6. **Stable user API, replaceable guts** *(PyTorch "worse is better," applied)*
   Internals may be rewritten in a week if the public types hold. Backends are traits. Kernels can be swapped. Do not freeze a clever graph IR because it was hard to write.

### Refuse

- `unwrap()` on a library path.
- A "safe" wrapper that hides a global CUDA context.
- Depending on CPython at runtime for a production inferencer.
- Premature abstraction that makes the tensor type clever and the error type silent.

### Done when

`cargo test` is the default loop, the public API still reads like the PyTorch snippet beside it, and the release binary starts without a Python interpreter. Clippy and the profiler have seen the crate. You can sketch the ownership of the hot tensor without the agent.

---

## C++20 (including CUDA)

**Stance.** C++20 is the language in which the machine is allowed to be visible.

Python hides the machine so the idea can move. Rust names the aliasing so the binary can be trusted. C++20 names the hierarchy the hardware actually has: threads, blocks, grids, memories, barriers. CUDA's three abstractions — thread groups, shared memories, barrier synchronization — are the design. Modern C++ is how the host code stays livable.

### Principles

1. **The model is hierarchy, not "a GPU function"** *(CUDA Programming Guide, published)*
   Partition into coarse problems that blocks can solve independently, then into fine problems that threads in a block solve cooperatively. That decomposition is what makes a binary scalable across SM counts you do not know yet.

2. **Data movement is the expensive part**
   Arithmetic is cheap relative to moving bytes through the memory hierarchy. Design for locality: registers, shared memory, L2, HBM, host. A kernel that computes twice and loads once is often the right kernel.

3. **Assess, Parallelize, Optimize, Deploy** *(CUDA Best Practices, published as APOD)*
   Do not rewrite the application into CUDA on day one. Find the hot path, expose parallelism, measure, then ship a speedup that exists in production. Lower-priority cleverness waits until higher-priority locality is done.

4. **Modern C++ on the host; explicit lifetimes on the device**
   C++20 on the host means RAII, `span`, concepts where they shrink APIs, no raw `new` in application code. On the device, be more conservative: what is in shared memory, who syncs, which thread owns the write. Do not import the entire STL personality into a kernel.

5. **Interoperability is the product**
   The hot path is C++/CUDA. The author path is often Python or Rust. Keep the ABI and the tensor layout boring (`contiguous`, documented strides, explicit stream). PyTorch and Candle should call you. You should not require them to learn a private runtime.

6. **Measure before you template**
   Occupancy, bandwidth, and instruction mix are empirical. A wall of `template` parameters that cannot be printed in a profiler is not abstraction; it is opacity. Prefer a few concrete kernels plus a dispatcher over an infinitely generic one.

### Refuse

- Host allocations on the launch path.
- Implicit default streams in a multi-library process.
- "It works on my 4090" as a correctness argument.
- Hiding occupancy cliffs behind a fluent builder.

### Done when

A stranger can name the grid, the memory space, and the stream on one page, and the same kernel is callable from the Python or Rust side without copying the world. Nsight (or the equivalent) has been run. Occupancy is a number, not a hope.

---

## How the four fit together

| Language | Optimizes for | Pays with | Exemplars |
| --- | --- | --- | --- |
| Python | Changing the idea today | Runtime and GIL | PyTorch, NeMo, LangGraph |
| TypeScript | Changing the team and the boundary | Ceremony at the edges | Next.js, shadcn/ui, LangChain JS |
| Rust | Shipping a trusted binary | Compile time and explicitness | Ruff, Candle, Burn |
| C++20 / CUDA | Using the machine you bought | Visibility and discipline | CUDA C++20, NeMo kernels |

Use Python to find the algorithm. Use TypeScript to name the product boundary. Use Rust when the binary must start small and stay correct. Use C++20 when the bytes must move on purpose.

Grok and Cursor accelerate all four. They do not pick the complexity class, they do not skip the profile, and they do not skip the floor.

If two languages disagree, keep the seam boring and the philosophy of the *outer* language in charge of the API.
