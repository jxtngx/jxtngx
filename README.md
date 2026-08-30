# Justin (`jxtngx`)

I build **Cursor-first** labs and factories: learn a stack by typing it, or specify a product and let a Cursor team ship it.

Trajectory: deep-learning contributor and developer advocate at [Lightning AI](https://lightning.ai) → **AI/ML engineer for applied AI and autonomy**.

Stack: [PyTorch](https://pytorch.org), [NVIDIA](https://www.nvidia.com), [Cursor](https://cursor.com), [Grok](https://grok.com), [LangChain](https://www.langchain.com) / [LangSmith](https://smith.langchain.com).

---

## Cursor-first

The IDE is the operating system. Grok and Cursor subagents are mentors or a staffed team — never a mystery box that owns the repo.

| Surface | Job |
| --- | --- |
| **Lab** | You write every line of domain code. Agents explain, quiz, review. Official docs are the spine. Definition of done: you can recreate it from a blank file. |
| **Factory** | You write the spec (`@init-*`, discovery). Chief Architect, SME, Scrum, engineers **implement the spec**. Not a lab. |
| **Plugin** | Cursor plugin (rules, skills, agents, commands). Ships into the editor, not as a product repo the user types by hand. |

Commands start work (`@start-lesson`, `@init-extension`). Commands do not silently finish it in a lab.

---

## Labs

Beginner → advanced in one language and topic. Harnesses do **not** do the exercise.

| Repo | Topic |
| --- | --- |
| [cursor-rust-lab](https://github.com/jxtngx/cursor-rust-lab) | Rust |
| [cursor-cuda-lab](https://github.com/jxtngx/cursor-cuda-lab) | CUDA C++20 |
| [cursor-robotics-lab](https://github.com/jxtngx/cursor-robotics-lab) | C++20 + ROS 2 |
| [cursor-rtos-lab](https://github.com/jxtngx/cursor-rtos-lab) | Zephyr RTOS (`native_sim` / QEMU) |
| [cursor-langchain-lab](https://github.com/jxtngx/cursor-langchain-lab) | LangChain agents (Python / TypeScript) |
| [cursor-data-engineering-lab](https://github.com/jxtngx/cursor-data-engineering-lab) | Python DE for AI/ML |
| [cursor-ibkr-tws-lab](https://github.com/jxtngx/cursor-ibkr-tws-lab) | Paper IBKR via `ib-interface` |

---

## Factories

Spec-driven boilerplate. Init interviews, then a Cursor team ships tickets.

| Repo | Product |
| --- | --- |
| [cursor-fullstack-factory](https://github.com/jxtngx/cursor-fullstack-factory) | Fullstack app |
| [cursor-deep-learning-factory](https://github.com/jxtngx/cursor-deep-learning-factory) | PyTorch / Hugging Face |
| [cursor-agent-factory](https://github.com/jxtngx/cursor-agent-factory) | LangChain + LangSmith (Harbor knobs) |
| [cursor-extension-factory](https://github.com/jxtngx/cursor-extension-factory) | Cursor/Open VSX extension (TS host + Rust crate) |

---

## Plugins

Cursor plugins: `.cursor-plugin/plugin.json`, rules, skills, agents. Not labs. Not factories. Not VS Code/Open VSX extensions (those go through [cursor-extension-factory](https://github.com/jxtngx/cursor-extension-factory)).

| Repo | What it is |
| --- | --- |
| [cursor-tws-plugin](https://github.com/jxtngx/cursor-tws-plugin) | Quant/algo skills for [ib-interface](https://github.com/jxtngx/ib-interface) and IBKR TWS; local KYC profile, paper-first |

---

## Developer Tools

Products you run, not curricula.

| Repo | What it is |
| --- | --- |
| [dgx-lab](https://github.com/jxtngx/dgx-lab) | Developer platform for NVIDIA DGX Spark |
| [ib-interface](https://github.com/jxtngx/ib-interface) | Python API for IBKR Trader Workstation (protobuf / official `ibapi`) |
