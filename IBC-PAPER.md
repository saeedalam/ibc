# From Prompt Chaos to Predictable Output – My Experiments with Intent-Behaviour Contracts

> *A personal field-note on taming coding LLMs in everyday development*

---

## Abstract  
Large-language-model (LLM) code assistants are astonishingly capable **and** astonishingly unpredictable.  After months of juggling elaborate prompts, rule files and countless back-and-forth edits, I tried a lightweight, spec-first approach I call an **Intent-Behaviour Contract (IBC)**.  This post documents *why* I tried it, *how* it works, what went well, where it still hurts, and why you might want to experiment with something similar.

*(Spoiler: it is not a magic bullet, but it noticeably reduced "LLM whiplash" for my team.)*

---

## 1  The Pain Points I Kept Hitting

1. **Context drift**  – multi-turn chats would lose track of project conventions or previous files.  
2. **Prompt ambiguity** – the same request phrased slightly differently produced wildly different code.  
3. **Style inconsistencies** – generated files broke naming or folder rules, forcing tedious clean-up.  
4. **Unwanted edits** – the AI sometimes "helpfully" touched files it should never modify.  
5. **Verification overhead** – grafting tests on afterwards felt like an after-thought.

### Existing Counter-measures I Tried

| What I Tried | Helped? | Still Missing |
|--------------|---------|---------------|
| **Prompt engineering** (very long, structured instructions) | ✅ Some | ❌ fragile, not reusable |
| **IDE rule files / context snippets** | ✅ Stops accidental edits | ❌ no per-feature intent |
| **OpenAPI / Swagger for APIs** | ✅ Great for endpoints | ❌ not general feature logic |
| **Gherkin BDD specs** | ✅ Good for tests | ❌ doesn't drive file layout / deps |

I wanted something cheap, generic, and LLM-friendly that combined the best of rule files *and* behaviour specs.

---

## 2  Enter Intent-Behaviour Contracts

An **IBC** is just a YAML file with two goals:
1.  Provide the LLM with **structured intent** – what the feature must do and how we will judge success.  
2.  Define **behaviour scaffolding** – data models, step-by-step operations, constraints, test outlines.

I ended up with two file types:

* `base.yaml` – the never-changing, repo-wide ground rules (tech stack, naming, forbidden paths…).
* `task-*.yaml` – one per feature, inheriting from the base and adding intent / behaviours / tests.

That's it – no exotic tooling.  I just paste the YAML path in my prompt:
```text
"Implement the login behaviour described in .ibc/task-user-auth.yaml."
```
The assistant loads the base for global context, the task file for specifics, and gets to work.

---

## 3  What Improved (and What Didn't)

### Wins
* **Fewer Surprise Edits** – forbidden/allowed globs protected critical files.  
* **Consistent Naming & Folders** – thanks to the base rules.  
* **Quicker Convergence** – clear behaviour steps meant fewer "actually I meant..." corrections.  
* **Tests Up-Front** – asking the LLM to satisfy explicit test outlines increased pass-rate.

### Still Rough Edges
* **Spec Authoring Time** – writing a good YAML contract takes real effort. Small one-liners feel over-specified.  
* **Tooling** – I hand-crafted YAML; a VS Code extension that scaffolds and validates would help.  
* **LLM Parsing Robustness** – some models choke on long YAML; chunking is occasionally required.

---

## 4  When It Seems Worth It

| Scenario | IBC Helpful? |
|----------|--------------|
| Tiny refactor / typo fix | 🚫 probably overkill |
| New micro-feature touching many files | ✅ reduces churn |
| On-boarding junior devs to code-with-AI | ✅ gives clear framework |
| Green-field prototype week | ➖ depends – may slow you down |

---

## 5  Open Questions & Next Steps

* **Automation:** CLI to generate boilerplate `task-*.yaml` from a template.  
* **Validation:** CI check to flag code that diverges from its contract.  
* **Editor support:** real-time suggestions when YAML is incomplete.  
* **Community schema:** converge on a standard so assistants can natively understand any IBC.

If you have ideas or want to swap war stories, ping me on LinkedIn – always keen to learn from other AI-augmented developers.

---

## 6  TL;DR

*LLMs excel at code generation but need guard-rails.*  A minimal pair of YAML files – one global, one per feature – gave me clearer prompts, more predictable output, and fewer "why did it touch that file?!" moments.  
**Not a silver bullet, but a useful pattern** worth trying if you feel your prompts are spiralling out of control.

*Happy coding – may your AI stay on the rails!* 