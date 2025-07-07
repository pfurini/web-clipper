# Production-Grade Agentic Coding Practices: Lessons from Claude Code, Codex and Gemini CLI by Agent Native
[Production-Grade Agentic Coding Practices: Lessons from Claude Code, Codex and Gemini CLI | by Agent Native | Jul, 2025 | Medium](https://agentissue.medium.com/production-grade-agentic-coding-practices-lessons-from-claude-code-codex-and-gemini-cli-8721ff6aefca) 

 We recently dived deep into Claude Code, Codex CLI and Gemini CLI, and we reverse-engineered their **system prompts, guardrails, and memory tricks**, then battle-tested the findings in real use cases.

The result is **10 reusable patterns** that turn a weekend prototype into a fully-instrumented, policy-compliant _multi-agent assembly line,_ whether you’re parsing contracts, triaging lab results, or squeezing CTR out of ad spend.

Think JSON-first responses, phase-tagged telemetry, sandboxed tool calls, auto-extracted style schemas, the stuff frontier labs treat like Terraform for language models.

We packed it all into a one blog post you can copy-paste today and look like you invented tomorrow.

Grab a coffee and let’s hot-rod your agents!

![](https://github.com/pfurini/web-clipper/blob/main/images/7-7-2025,%2016-46-33/ca82976d-33e5-4b1e-9cbc-e931a4586ab2.png?raw=true)

It’s a daunting task to orchestrate an LLM-powered supply-chain of prompts, tools, memory, policies, and runtime sandboxes, especially in domain specific applications.

However if you look at the internals of state-of-the-art coding assistants today (Claude Code, Codex CLI and Gemini Code Assist), they all have converged on a core loop but diverge in how they hard-code guardrails, expose plans, and learn user taste.

These frontier labs treat system components like source-controlled infra-code, with peer review, A/B tests, and runtime metrics.

It might be confusing the take it all at once, that’s why we want to break our findings down to 10 patterns that you can leverage today.

Implementing the right control flow can be a challenge based on the domain, but in general they help you to achieve a few things:

*   **Debuggability:** you can pinpoint which phase slowed or hallucinated.
*   **Policy hooks:** each phase gates different permissions (read-only vs mutate).
*   **Live UX:** the UI can stream progress because phases/firehose events are explicit.

All three agents implement a _ReAct-style_ finite-state machine:

```
┌───────────────────────────────────────────────────────────┐  
│ 1. Acquire-Context                                        │  
│    Goal : Pull only the information the task needs        │  
│    Hook : semantic search · EHR/FHIR fetch · repo globbing │  
└───────────────┬────────────────────────────────────────────┘  
                │  
                ▼  
┌───────────────────────────────────────────────────────────┐  
│ 2. Plan                                                  │  
│    Goal : Produce an ordered, typed task graph           │  
│    Hook : ReAct · Plan-then-Solve · LangGraph DAGs       │  
└───────────────┬────────────────────────────────────────────┘  
                │  
                ▼  
┌───────────────────────────────────────────────────────────┐  
│ 3. Act                                                   │  
│    Goal : Invoke tools or sub-agents                     │  
│    Hook : function-calling · tool routers                │  
└───────────────┬────────────────────────────────────────────┘  
                │  
                ▼  
┌───────────────────────────────────────────────────────────┐  
│ 4. Verify                                                │  
│    Goal : Run deterministic + LLM-based checks           │  
│    Hook : dual-LLM · schema validators · guards          │  
└───────────────┬────────────────────────────────────────────┘  
                │  
                ▼  
┌───────────────────────────────────────────────────────────┐  
│ 5. Reflect                                               │  
│    Goal : Self-critique & repair before user output      │  
│    Hook : Reflexion · tree-search loops                  │  
└───────────────┬────────────────────────────────────────────┘  
                │  
                ▼  
┌───────────────────────────────────────────────────────────┐  
│ 6. Deliver                                               │  
│    Goal : Respond with the right explanation level       │  
│    Hook : progressive disclosure                         │  
└───────────────┬────────────────────────────────────────────┘  
                │  
                ▼  
┌───────────────────────────────────────────────────────────┐  
│ 7. Learn                                                 │  
│    Goal : Write back to memory & observability store     │  
│    Hook : vector DB · metrics · traces                   │  
└───────────────────────────────────────────────────────────┘
```

This flow is somewhat identical in Claude Code’s CLI, Gemini ADK flows and Codex-derived copilots, only the wrappers differ.

The question is, how do we convert such ReAct flow into typed and observable finite state machine.

Here’s a simple reference:

```
  
from enum import Enum, auto  
from typing import Callable, Awaitable  
import asyncio, logging

log = logging.getLogger(\_\_name\_\_)

class Phase(Enum):  
    ACQUIRE=auto(); PLAN=auto(); ACT=auto()  
    VERIFY=auto(); REFLECT=auto(); DELIVER=auto(); LEARN=auto()

Handler = Callable\[\[\], Awaitable\[None\]\]

class AgentFSM:  
    def \_\_init\_\_(self):  
        self.handlers: dict\[Phase, Handler\] = {}  
        self.phase = Phase.ACQUIRE

    def on(self, phase: Phase):  
        def register(fn: Handler): self.handlers\[phase\] = fn; return fn  
        return register

    async def run(self):  
        while self.phase:  
            log.info("➡ %s", self.phase.name)  
            await self.handlers\[self.phase\]()  
            self.phase = Phase(self.phase.value + 1) \\  
                         if self.phase != Phase.LEARN else None


```

We will later see how each handler wraps in an OpenTelemetry span.

Brevity is good, but **structured brevity** is better.

```
  
from typing import Literal, Union  
from pydantic import BaseModel, Field

class RAnswer(BaseModel):  
    kind: Literal\["answer"\] = "answer"  
    value: str              = Field(..., max\_length=1\_000)

class RCommand(BaseModel):  
    kind: Literal\["command"\] = "command"  
    shell: str

class RPatch(BaseModel):  
    kind: Literal\["patch"\] = "patch"  
    diff: str              = Field(..., max\_length=20\_000)

class RYesNo(BaseModel):  
    kind: Literal\["yesno"\] = "yesno"  
    value: bool

class RLoc(BaseModel):  
    kind: Literal\["loc"\] = "loc"  
    file: str  
    line: int

AgentResponse = Union\[RAnswer, RCommand, RPatch, RYesNo, RLoc\]


```

This is useful in many ways:

*   Enables IDE adapters to render rich UIs (diff viewers, terminal panes).
*   Compresses tokens, every enum value is ~1 token instead of 5–15 words.

```
import orjson, tiktoken, asyncio, websockets

enc = tiktoken.encoding\_for\_model("gpt-4o-mini")

async def stream\_response(ws\_uri:str, resp:AgentResponse):  
    raw = orjson.dumps(resp.model\_dump())  
      
    tokens = enc.encode(resp.model\_dump\_json())  
    if len(tokens) > 600:                   
        resp.value = summarise(resp.value)    
        raw = orjson.dumps(resp.model\_dump())  
    async with websockets.connect(ws\_uri) as ws:  
        await ws.send(raw)


```

*   Makes post-hoc analytics trivial (count kinds per session, latency, success rate).
*   Ensure each vertical to ship templates tuned to local signal-to-noise:

```
@dataclass  
class ResponseRules:  
    max\_char: int  
    required: list\[str\]  
    style: Literal\["bullet", "table", "json"\]

RULES = {  
    "healthcare": ResponseRules(280, \["finding","next\_step"\], "bullet"),  
    "legal":      ResponseRules(320, \["issue","citation","action"\], "bullet"),  
    "marketing":  ResponseRules(420, \["insight","metric","cta"\], "table"),  
}

def enforce\_rules(resp:RAnswer, vertical:str):  
    rule = RULES\[vertical\]  
    assert len(resp.value) <= rule.max\_char  
    for slot in rule.required:  
        assert slot in resp.value.lower()


```

As a tip, you should call `enforce_rules` in **Verify** phase.

If it fails, bounce to **Reflect** for auto-shrink or slot repair.

_Claude_ exposes **low-level todos**; _Gemini_ exposes **high-level phases** (Understand → Plan → Implement → Verify) and asks for user approval before entering _Act_ mode.

This is important because busy users want **one glance**: “what phase are we in?”, and engineers want **fine-grained telemetry**: which micro-step failed?

Let’s think of a data model for a second that will help us expose such information:

```
  
from \_\_future\_\_ import annotations  
from enum import Enum, auto  
from collections import deque  
from dataclasses import dataclass

class Phase(Enum):  
    UNDERSTAND = auto(); PLAN = auto()  
    IMPLEMENT = auto(); VERIFY = auto()

@dataclass  
class Task:  
    id:  int  
    txt: str  
    status: str  

@dataclass  
class AgentState:  
    phase : Phase  
    tasks : deque\[Task\]

    def as\_dict(self):                  
        return {"phase": self.phase.name.lower(),  
                "tasks": \[t.\_\_dict\_\_ for t in self.tasks\]}


```

We can now render `AgentState` in the sidebar, let the user tick tasks or reorder them to get free RLHF signals on the plan quality.

```
  
import json, websockets, asyncio  
from agent.state import AgentState

async def push\_state(ws\_uri:str, state\_q:asyncio.Queue\[AgentState\]):  
    async with websockets.connect(ws\_uri) as ws:  
        while (st := await state\_q.get()):  
            await ws.send(json.dumps(st.as\_dict()))


```

Your front-end (React / Vue) simply re-renders on each payload, and dragging a card emits `PATCH /agent/task/{id}` which you funnel back into the queue → **RLHF reward = Δordering**.

This way, you can also emit a **typed todo list** that maps 1-to-1 to graph nodes:

```
\[ \] gather\_lab\_results          ← Acquire-Context  
\[~\] compare\_to\_guidelines       ← Act   (runtime)  
\[ \] cross-check drug doses      ← Verify  
\[ \] draft SOAP note             ← Deliver
```

Each DAG node **owns** exactly one `Task.id`:

```
graph node             sidebar line  
───────────────        ──────────────────────────  
ctx.fetch\_lab  ────▶   \[ \] gather\_lab\_results  
act.compare     ────▶  \[~\] compare\_to\_guidelines
```

So when the executor marks a node _done_, the same event ticks the UI **in real time,** zero extra plumbing.

Reviewers down-vote PRs that break their lint rules.

Courts throw out briefs written in the wrong citation format.

Hospitals reject notes that violate EHR templates.

That why ALL assistants stress “follow the repo’s style”, for example:

```
def infer\_conventions(root:Path)->Conventions:  
    deps = parse\_deps(root)            # req\*.txt, package\*.json  
    exts = Counter(f.suffix for f in root.rglob('\*') if f.is\_file())  
    lints = detect\_linters(root)       # eslint, flake8, go vet …  
    fmt   = detect\_formatters(root)    # prettier, black, clang-format  
    return Conventions(deps, exts, lints, fmt)
```

They call this **before** planning patches.

To apply this to domain specific contexts, you should treat “style” as structured data. Here’s what the pipeline will look like:

1.  **Bootstrap** with a hundred real artefacts (contracts, discharge notes, ad briefs).
2.  Extract latent patterns → `spacy`, `textstat`, embedding clustering.
3.  Materialise as **JSON Schemas** the model must fill.
4.  Feed those schemas to the function-calling layer.

```
import re, json, subprocess, pathlib  
from collections import Counter  
from typing import NamedTuple  
from gensim.models import Phrases  
import spacy, textstat, jsonschema, json, pathlib

class Conventions(NamedTuple):  
    deps: list\[str\]  
    exts: dict\[str, int\]  
    linters: list\[str\]  
    formatters: list\[str\]

def parse\_deps(root:pathlib.Path)->list\[str\]:  
    dep\_files = list(root.glob("\*\*/requirements\*.txt")) + \\  
                list(root.glob("\*\*/package\*.json"))  
    pkgs = \[\]  
    for f in dep\_files:  
        pkgs += re.findall(r"^\[A-Za-z0-9\_\\-\]+", f.read\_text(), re.M)  
    return sorted(set(pkgs))

def detect\_tools(root:pathlib.Path, patterns:list\[str\]):  
    return \[p for p in patterns if (root / p).exists()\]

def infer\_conventions(root:pathlib.Path)->Conventions:  
    return Conventions(  
        deps=parse\_deps(root),  
        exts=Counter(p.suffix for p in root.rglob("\*") if p.is\_file()),  
        linters=detect\_tools(root, \["flake8", ".eslintrc", ".golangci.yml"\]),  
        formatters=detect\_tools(root, \[".prettierrc", "pyproject.toml",  
                                       ".clang-format"\])  
)

def schema\_from\_corpus(files:list\[pathlib.Path\])->dict:  
    docs = \[f.read\_text() for f in files\]  
      
    bigram = Phrases(\[d.split() for d in docs\]).export\_phrases(\[\[\]\])\[1\]  
      
    avg\_len = int(sum(map(len, docs))/len(docs) \* 1.2)  
      
    return {  
        "type":"object",  
        "properties":{  
            "body":{"type":"string",  
                    "maxLength":avg\_len,  
                    "pattern":"|".join(bigram)}  
        },  
        "required":\["body"\]  
    }

  
func\_spec = {"name":"draft\_brief","schema":schema\_from\_corpus(corpus)}  
resp = llm.chat(model="gpt-4o-mini", functions=\[func\_spec\])  
jsonschema.validate(resp\["arguments"\], func\_spec\["schema"\]) 
```

This auto-learns thousand-line firm-specific clause banks or hospital abbreviations without human tagging.

The result is the model literally _cannot_ produce off-brand copy. No manual review loops.

> We publish “how-to” guides and thought pieces for building agents.
> 
> We pour our **passion, expertise, and countless hours into creating content** that we believe can make a difference in your journey.
> 
> But only 1% of our readers follow or engage with us on Medium.
> 
> **If you ever found value in our content,** it would mean a lot if you could [**follow Agent Native on Medium**](https://agentissue.medium.com/subscribe), give this article a clap, and drop a hello in the comments!
> 
> **It’s a small gesture but it tremendously helps us deliver much better content and guides for you!**
> 
> Thank you for taking your time to be here, we _really appreciate it._

**_Gemini_** requires an explicit allow/deny for any mutating tool; read-only ops are auto-approved.

**_Codex_** is executed in a directory-confined sandbox with networking disabled.

Mirror this with a **kernel-level policy layer**:

```
bwrap --ro-bind /project /workspace \\  
      --tmpfs /tmp           \\  
      --unshare-net          \\  
      --proc  /proc          \\  
      --dev  /dev
```

They probably expose a `sandox_info()` syscall so the model can adapt commands (e.g., avoid `sudo`, write to `/tmp`).

To apply this to domain-specific context, you should stop relying on “don’t jailbreak” strings, and apply the **Sandboxed-Mind patterns** instead**.**

*   **Action-Selector is a** narrow toolset (e.g., EHR read-only) which is safest but least flexible
*   **Plan-Then-Execute** is for complex pipelines with extra latency
*   **Dual-LLM** is untrusted input (email, web) but doubles cost
*   **Code-Then-Execute** is for financial / infra ops which requires runtime sandbox

For example, a typical `policy.yaml` for healthcare agent would look like:

```
allowed\_files:  
  \- /workspace/patient/\*.json  
blocked\_patterns:  
  \- "SELECT .\* FROM transactions"
```

The verifier reads this file and kills any command that violates the regex.

**_Gemini’s_** docs recommend running linters & tests **every loop** but community tips insist on “add unit tests and then call them”.

For example:

```
async def verify():  
    await run("npm run lint --silent")  
    await run("pytest -q")  
    await run("mypy . || true") 
```

If any step fails, you can summarize the diff / traceback and re-enter **Plan** with the failure context.

In healthcare context, you can similarly add domain guards before DELIVER phase:

```
async def medical\_guard(plan:dict) -> None:  
    schema.validate(plan)                           # JSONSchema  
    if await async\_gpt\_check("off-label-use", plan):  
        raise ValueError("Off-label recommendation.")  
    await guidelines\_lut.assert\_compliant(plan)  
    if plan\["risk\_score"\] > 0.7:  
        raise HumanReviewNeeded()
```

Codex-derived agents embed similar guards for package-manager writes and Gemini 2.0’s doc-extraction recipe externalises rule tables so analysts edit Excel, **not** prompts.

Memory and context management in agentic applications is very important due to several factors

*   **Latency vs. recall:** Flat vector stores grow unbounded and slow.
*   **Regulation:** GDPR/AI Act force _purpose limitation_ & _storage minimisation_.
*   **Prompt-token tax:** Every extra byte re-ingested costs cash and latency

Which makes “Pyramid Memory” suitable as a design pattern:

```
┌────────────┐  
│  L1 Cache  │  Scratchpad   (ephemeral CoT, cleared ↻ every step)  
├────────────┤  
│  L2 Store  │  Session dict (Redis, 30 min TTL)  
├────────────┤  
│  L3 RAG DB │  Vector DB    (docs, past tickets) – per\-repo / per\-tenant  
├────────────┤  
│  L4 Lake   │  Long\-term    (Parquet/S3, analytics only, opt\-in retention)  
└────────────┘
```

We basically persist upward, search downward.

Here’s a very rough implementation:

```
  
from typing import Iterable, Any  
from chromadb import Client as Chroma  
import redis, json, time, uuid

class PyramidMemory:  
    def \_\_init\_\_(self, redis\_url:str, chroma\_url:str, ttl:int=1800):  
        self.l2 = redis.StrictRedis.from\_url(redis\_url)  
        self.l3 = Chroma(path=chroma\_url).get\_or\_create\_collection("rag")  
        self.ttl = ttl                          

      
    def recall(self, q:str, k:int=3) -> list\[str\]:  
        if hit :\= self.l2.get(q):  
            return json.loads(hit)  
        docs, \_ = self.l3.query(q, n\_results=k)  
        self.l2.set(q, json.dumps(docs), ex=self.ttl)  
        return docs

    def persist(self, items:Iterable\[str\]):  
          
        ids = \[str(uuid.uuid4()) for \_ in items\]  
        self.l3.add(ids=ids, documents=list(items))


```

Here you can additional implement compliance hook to run `gdpr_purge()` nightly to evict expired L3 vectors:

```
def gdpr\_purge(days:int=30):  
    cutoff = time.time() - days \* 86\_400  
    ids = \[id\_ for id\_, meta in self.l3.get(include=\["metadatas"\]).items()  
           if meta\["timestamp"\] < cutoff and not meta\["retention\_ok"\]\]  
    self.l3.delete(ids)
```

Wall-clock time is dominated by _longest_ branch of your task DAG (“critical path”), and most tool invocations are I/O-bound, that are perfect for `asyncio.gather`.

In such cases, speculative execution squeezes idle tokens, call _candidate_ tools in parallel, and drop losers.

Let’s have a look at a simple code snippet to understand this better.

This is what a simple broker look like:

```
  
import asyncio, heapq  
from typing import Awaitable

class Task:  
    def \_\_init\_\_(self, name:str, coro:Awaitable, deps:set\[str\]):  
        self.name, self.coro, self.deps = name, coro, deps

async def dispatch(tasks:list\[Task\]):  
    graph = {t.name: t for t in tasks}  
    ready = \[t for t in tasks if not t.deps\]  
    running, done = set(), set()

    async def runner(t:Task):  
        running.add(t.name)  
        try:  
            return await t.coro  
        finally:  
            running.remove(t.name)  
            done.add(t.name)

    while ready or running:  
          
        awaitables = \[runner(t) for t in ready\]  
        ready.clear()  
        if awaitables:  
            await asyncio.gather(\*awaitables, return\_exceptions=True)  
          
        for t in tasks:  
            if t.name not in done and t.deps.issubset(done):  
                ready.append(t)


```

And here’s speculative variant:

```
from contextlib import suppress

async def speculative(first:Awaitable, \*maybes:Awaitable):  
      
    first\_done, pending = await asyncio.wait(  
        \[first, \*maybes\], return\_when=asyncio.FIRST\_COMPLETED  
    )  
    for p in pending:  
        p.cancel()  
        with suppress(asyncio.CancelledError):  
            await p  
    return list(first\_done)\[0\].result()


```

As a tip, you should log both **critical-path** and **wall-time**, auto-split a big task if CP ≪ wall-time.

Git-aware ops provide you a few advantages over vanilla setup:

*   **Reproducibility**: you tie every change back to a hash.
*   **Style consistency**: happier reviewers, lower diff noise.
*   **Pre-commit**: last guard before dangerous code ships.

To achieve these three, you can implement the following workflow:

1.  **Dry-run** (`git status --porcelain`) – bail if dirty working tree.
2.  **Scope file list** → `.agent/scope.txt`.
3.  Run static scans / policy checks on that list.
4.  Let LLM generate commit message **template**, keepter edits it.
5.  Auto-amend if LLM mutated files (stopgap till large refactor).

Here’s a sample pre-commit hook:

```
import subprocess, sys, json, re, pathlib

root = pathlib.Path(\_\_file\_\_).resolve().parents\[2\]

def shell(\*cmd): return subprocess.check\_output(cmd).decode()

files = shell("git diff --cached --name-only").splitlines()  
(root/".agent").mkdir(exist\_ok=True)  
(root/".agent/scope.txt").write\_text("\\n".join(files))

  
from agent.policy import scan\_files  
violations = scan\_files(files)  
if violations:  
    print("\[ABORT\] policy violations:")  
    for v in violations: print(" •", v)  
    sys.exit(1)

  
if "--fix-style" in sys.argv:  
    from agent.llm import style\_fix  
    style\_fix(files)  
    shell("git add -u") 
```

and a auto-commit message generator:

```
def commit\_message(diff:str) -> str:  
    prompt = f"Write an imperative commit title (≤50 chars) " \\  
             f"and bullet body for the diff:\\n{diff}\\n"  
    msg = llm\_chat(prompt, model="gpt-4o-mini")  
    # enforce style  
    title, \*body = msg.strip().splitlines()  
    title = re.sub(r"\[.\]$", "", title).capitalize()  
    return title + "\\n\\n" + "\\n".join(body)
```

As you have noticed, we have many layers to instrument

![](https://github.com/pfurini/web-clipper/blob/main/images/7-7-2025,%2016-46-33/2fb78fb9-6229-4560-9352-ff0084b46e6c.png?raw=true)

Good news is we can easily instrument all of these layers with OpenTelemetry:

```
  
from opentelemetry import trace  
from functools import wraps  
tracer = trace.get\_tracer("agent")

def span(name:str, \*\*attrs):  
    def deco(fn):  
 @wraps(fn)  
        async def wrapper(\*a, \*\*kw):  
            with tracer.start\_as\_current\_span(name) as sp:  
                for k, v in attrs.items(): sp.set\_attribute(k, v)  
                sp.set\_attribute("args.len", len(a)+len(kw))  
                return await fn(\*a, \*\*kw)  
        return wrapper  
    return deco

  
@span("agent.tool\_call", tool="ehr.fetch")  
async def fetch\_ehr(id\_:str): ...


```

And a few dashboarding tips will be handy here:

*   **Gantt by phase** chart (span duration stacked) will catch slow tools.
*   **Guard-trip heatmap** over time will show drift/regressions.
*   Pipe traces to **Grafana Cloud** or **Honeycomb**, alerts on p95 latency ↑20 %.

Cool, if you have come this far, congratulations!

Lastly, let’s have a look a simple skeleton that you can use as a reference:

```
  
from \_\_future\_\_ import annotations  
from pathlib import Path  
from collections import deque  
from enum   import Enum, auto  
from typing import Any

class Phase(Enum): OBSERVE=auto(); PLAN=auto(); ACT=auto()  
                  VERIFY=auto(); REFLECT=auto(); RESPOND=auto()

class State:  
    def \_\_init\_\_(self): self.phase=Phase.OBSERVE; self.tasks=deque()

class Agent:  
    def \_\_init\_\_(self, repo:Path):  
        self.repo   = repo  
        self.state  = State()  
        self.ctx    = infer\_conventions(repo)  
        self.memory = PyramidMemory(  
            redis\_url="redis://localhost:6379/0",  
            chroma\_url=str(repo/".agent/chroma")  
        )

      
    async def step(self, user:str) -> str:  
        obs   = await Observer(self).run(user)  
        plan  = await Planner(self).run(obs)  
        acts  = await Actor(self).run(plan)  
        ok    = await Verifier(self).run(acts)  
        await Reflector(self).run(ok, acts)  
        return await Responder(self).run()

  
class Planner:  
    def \_\_init\_\_(self, agent:Agent): self.agent=agent

    async def run(self, obs:Any) -> Plan:  
        prompt = make\_plan\_prompt(obs, self.agent.ctx)  
        raw    = await llm\_chat(prompt, model="gpt-4o-mini")  
        plan   = parse\_plan(raw)                 
        self.agent.state.tasks = deque(topo\_sort(plan))  
        return plan


```

Having said all of that, there is always a fine balance between shipping and optimizing and that’s where you need to decide the trade-off and pull the trigger based on user requirements and go-live timeline.

![](https://github.com/pfurini/web-clipper/blob/main/images/7-7-2025,%2016-46-33/4649c705-87d0-4aff-a2f8-287fc90995dc.png?raw=true)

That’s it, hope you enjoyed the post.

Make sure you drop your own patterns in the comments too!

And don’t forget to have a look at some practitioner resources that we published recently:

Thank you for stopping by again, see you around.