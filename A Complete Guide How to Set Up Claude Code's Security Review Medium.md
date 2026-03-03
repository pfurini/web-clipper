# A Complete Guide How to Set Up Claude Code's Security Review | Medium
[A Complete Guide How to Set Up Claude Code's Security Review | Medium](https://alirezarezvani.medium.com/how-to-set-up-claude-codes-new-security-review-the-complete-practical-guide-0f900a680eb1) 

 Both the /security-review command and GitHub Action — setup, customization, real findings, and honest limitations
-----------------------------------------------------------------------------------------------------------------

I ran `/security-review` on one of my own repositories last Thursday night. No setup. No configuration. Just the command.

Almost 1 minute later, Claude flagged an authorization bypass I had missed for months. A middleware function that checked user roles but did not validate token expiry on a specific edge case. The kind of bug that passes every code review because it looks right at first glance.

Press enter or click to view image in full size

![](https://github.com/pfurini/web-clipper/blob/main/images/3-3-2026,%2015-30-57/6c78f669-944a-481f-ac9c-585e14b42555.png?raw=true)

Claude Code Security Review | Image: AI-generated illustration with Gemini 3.1 Pro ©

_Disclosure: AI tools assisted with research on Claude Code Security Feature. The hands-on testing, setup process, and findings documented here are from my direct experience with the tool._

That single finding convinced me to spend the next few hours setting up the full pipeline — the local command for my daily workflow and the GitHub Action for automated PR scanning. This guide walks you through both, with the exact configuration I’m running in production.

What Claude Code Security Review Actually Is
--------------------------------------------

Anthropic shipped two complementary security tools on February 20, 2026. They solve the same problem from different angles.

The `**/security-review**` **slash command** runs inside your Claude Code terminal session. You type it, Claude reads your codebase, and it reports vulnerabilities with severity ratings and fix suggestions. Think of it as your pre-commit security check — something you run before pushing code.

The **GitHub Action** (`anthropics/claude-code-security-review`) triggers automatically on every pull request. It analyzes the diff, identifies security issues in the changed code, filters false positives through a multi-stage verification process, and posts inline comments on the PR with findings and remediation guidance.

**Here is why this matters:** traditional static analysis tools (SAST) match code against known vulnerability patterns. They catch exposed passwords and outdated encryption. But they miss complex issues — broken business logic, subtle authorization flaws, context-dependent vulnerabilities. Claude doesn’t pattern-match. It reads and reasons about your code the way a human security researcher would, tracing data flow and understanding how components interact.

**_That authorization bypass in my repo?_** _Semgrep wouldn’t have caught it. The logic was technically correct — just incomplete under a specific token state._

Part 1: The `/security-review` Command
--------------------------------------

This is the fastest way to start. If you already have Claude Code installed, you are five seconds away from your first scan.

Prerequisites
-------------

*   Claude Code installed and up to date (run `claude update` if unsure)
*   An active Claude Code subscription (Pro, Max, or API Console account)

That is it. No additional packages, no configuration files, no API key setup for the local command.

Running Your First Scan
-----------------------

Open your terminal in any project directory and launch Claude Code:

```
  
cd ~/projects/your-repo

  
claude

  
/security-review


```

Claude will scan your entire codebase — not just the diff, the whole thing. Depending on project size, this takes anywhere from 30 seconds to a few minutes.

What the Output Looks Like
--------------------------

Claude reports findings with four key pieces of information:

1.  **Vulnerability type** — SQL injection, XSS, broken auth, hardcoded secrets, etc.
2.  **Severity rating** — HIGH, MEDIUM, or LOW
3.  **Explanation** — Why this is a vulnerability, with an exploit scenario
4.  **Recommendation** — Specific fix with code examples

The tool focuses on HIGH and MEDIUM findings only. This is a deliberate design choice — better to miss some theoretical issues than flood you with noise. Each finding is something a security engineer would confidently raise in a PR review.

Fixing Issues In-Place
----------------------

**Here is what makes this workflow powerful:** after Claude identifies vulnerabilities, you can ask it to implement fixes right there in the same session.

```
  
"Fix the SQL injection vulnerability in src/api/users.ts"
```

Claude understands the context from the security review, knows exactly which finding you’re referencing, and can apply the patch. You review the change, commit, done. Security review and remediation in one loop — no context switching to another tool.

Customizing the Command
-----------------------

The default `/security-review` command works well out of the box. But if you need organization-specific rules — say you work in healthcare and need HIPAA-specific checks, or you have custom authentication patterns — you can customize it.

**Copy the official command file to your project:**

```
  
mkdir -p .claude/commands/
```

```
\# Copy the security-review.md from Anthropic's repo  
\# (or create your own based on the official template)
```

Then edit `.claude/commands/security-review.md` to add your rules. For example, you could add:

```
\## Additional Organization Rules  
\- Flag any direct database queries that bypass the ORM layer  
\- Check that all API endpoints require authentication middleware  
\- Verify that PII fields use the encryption wrapper from @company/crypto
```

Claude will incorporate these instructions into every security scan for that project.

Part 2: GitHub Action Setup
---------------------------

The local command is great for your personal workflow. The GitHub Action enforces security review across your entire team — every PR, automatically.

### Step 1: Get Your API Key Ready

This is the one gotcha that trips people up. Your Anthropic API key needs to be enabled for **both** the Claude API **and** Claude Code usage. A standard API-only key won’t work. Check your API Console settings at `console.anthropic.com` to verify.

### Step 2: Add the Secret to Your Repository

```
  
  
  

```

Never commit API keys directly to your repository. Always use GitHub Secrets.

### Step 3: Create the Workflow File

Create `.github/workflows/security-review.yml` in your repository:

```
name: Security Review

permissions:  
  pull-requests: write    
  contents: read  
on:  
  pull\_request:  
jobs:  
  security:  
    runs-on: ubuntu-latest  
    steps:  
      \- uses: actions/checkout@v4  
        with:  
          ref: ${{ github.event.pull\_request.head.sha || github.sha }}  
          fetch-depth: 2

            \- uses: anthropics/claude-code-security-review@main  
        with:  
          comment-pr: true  
          claude-api-key: ${{ secrets.CLAUDE\_API\_KEY }}


```

Commit this file, open a PR, and watch the action run. That’s the minimal setup — and it’s already useful.

### Step 4: Configure for Your Needs

The action supports several configuration options worth knowing:

```
\- uses: anthropics/claude-code-security-review@main  
  with:  
    comment-pr: true  
    claude-api-key: ${{ secrets.CLAUDE\_API\_KEY }}

          
    exclude-directories: "node\_modules,dist,coverage,\_\_tests\_\_"

          
    claude-model: "claude-opus-4-1-20250805"

          
    claudecode-timeout: "30"

          
    upload-results: true


```

The `exclude-directories` option is important for keeping scan times reasonable and avoiding false positives from generated code or test fixtures.

What Happens on a PR
--------------------

**Once configured, the workflow follows this sequence:**

1.  A pull request is opened or updated
2.  Claude analyzes the diff to understand what changed
3.  It examines changes in context — understanding purpose and security implications
4.  Security issues are identified with explanations, severity ratings, and remediation guidance
5.  A multi-stage false positive filter removes low-impact findings
6.  Remaining findings are posted as inline PR comments on the specific lines of code

The inline comments include exploit scenarios and fix recommendations. Your team reviews them alongside the code changes, approves or dismisses findings, and merges when satisfied.

Part 3: Customization That Actually Matters
-------------------------------------------

### Custom Scanning Instructions

If your project has specific security concerns, create a text file with additional instructions and point the action to it:

```
\- uses: anthropics/claude-code-security-review@main  
  with:  
    claude-api-key: ${{ secrets.CLAUDE\_API\_KEY }}  
    custom-security-scan-instructions: ".github/security-scan-rules.txt"
```

In `.github/security-scan-rules.txt`:

```
Focus especially on:  
\- Any changes to authentication or authorization middleware  
\- Database queries that accept user input  
\- File upload handling and path traversal risks  
\- API rate limiting on public endpoints  
\- Ensure all new API endpoints validate JWT tokens
```

This extends Claude’s default scanning with your organization’s specific concerns.

### False Positive Tuning

Every codebase has patterns that trigger false positives. You can customize the filtering:

```
\- uses: anthropics/claude-code-security-review@main  
  with:  
    claude-api-key: ${{ secrets.CLAUDE\_API\_KEY }}  
    false\-positive-filtering-instructions: ".github/security-fp-rules.txt"
```

In `.github/security-fp-rules.txt`:

```
Ignore the following patterns for this project:  
\- Our internal rate limiter handles DoS protection at the gateway level  
\- The admin API is only accessible via VPN, so CSRF on admin endpoints is acceptable risk  
\- Test fixtures in /fixtures/ contain intentionally vulnerable code for testing
```

This is where the tool really shines compared to traditional SAST. Instead of writing complex YAML rule exceptions, you write plain English. Claude understands context.

### Path-Specific Scanning

**For larger teams, combine the security review with path-specific triggers:**

```
name: Critical Path Security Review

on:  
  pull\_request:  
    paths:  
      \- "src/auth/\*\*"  
      \- "src/api/\*\*"  
      \- "src/middleware/\*\*"  
      \- "config/security.\*"  
jobs:  
  security:  
    runs-on: ubuntu-latest  
    steps:  
      \- uses: actions/checkout@v4  
        with:  
          ref: ${{ github.event.pull\_request.head.sha || github.sha }}  
          fetch-depth: 2

            \- uses: anthropics/claude-code-security-review@main  
        with:  
          claude-api-key: ${{ secrets.CLAUDE\_API\_KEY }}  
          custom-security-scan-instructions: ".github/security-scan-rules.txt"


```

This runs the security review only when sensitive files change — keeping CI costs down while protecting the code that matters most.

### What It Actually Catches

The scanner covers a broad range of vulnerability types. From my testing, the categories where it performs strongest:

*   **Injection attacks** — SQL injection, command injection, NoSQL injection, XXE
*   **Authentication flaws** — Broken auth, privilege escalation, insecure direct object references, session issues
*   **Data exposure** — Hardcoded secrets, sensitive data in logs, PII handling violations
*   **Cryptographic issues** — Weak algorithms, improper key management
*   **Business logic flaws** — Race conditions, TOCTOU issues

> **It automatically excludes low-impact findings:** denial of service, rate limiting concerns, memory exhaustion, generic input validation without proven impact, and open redirects. This keeps the signal-to-noise ratio high.

What impressed me most: the multi-stage verification. Claude does not just flag an issue — it re-examines each finding, attempting to prove or disprove its own conclusions. Findings that survive this self-review come with a confidence rating. This is fundamentally different from a SAST tool dumping 200 findings where 180 are false positives.

The Limitation Nobody Mentions
------------------------------

Let me be direct about what this tool isn’t.

> **It is not hardened against prompt injection.**

**_The official documentation says this explicitly:_** the GitHub Action should only be used to review trusted PRs. If someone opens a malicious PR with crafted code comments or README content designed to manipulate Claude, the scanner could be misled. Configure your repository to require approval for external contributors before the workflow runs.

> **It won’t replace your security team.**

This catches code-level vulnerabilities. It doesn’t do infrastructure security, network scanning, or penetration testing. It’s one layer in defense-in-depth, not the whole stack.

> **API costs are real.**

Each scan consumes API tokens. For a team running this on every PR across multiple repos, monitor your usage. The default model is Opus 4.1 — effective but not cheap. For high-volume repos with frequent small PRs, consider using path-specific triggers instead of scanning every change.

> **Context window limits apply.**

On very large PRs touching dozens of files, the scanner may miss issues in files that fall outside the context window. Break large PRs into smaller, focused ones — which is good practice anyway.

Rolling This Out to Your Team
-----------------------------

From a leadership perspective, here is what I would recommend for a phased rollout:

**Week 1:** Run `/security-review` locally on your most critical repository. Evaluate the findings. Get a feel for false positive rates and finding quality.

**Week 2:** Set up the GitHub Action on one repository. Let it run on real PRs for a week. Tune false-positive filtering based on what your team sees.

**Week 3:** Add custom scanning instructions based on your codebase patterns. Roll out to remaining repositories.

**Ongoing:** Review findings weekly in the first month. Adjust filtering rules. Add path-specific triggers for repos where full scanning is too noisy or expensive.

The setup itself takes about 15 minutes per repository. The tuning takes longer — but that is time well spent. Every false positive you filter out is one less interruption for your engineers.

_✨ Thanks for reading! If you’d like more practical insights on AI development tools, hit subscribe to stay updated._

_Have you tested Claude Code’s security review on your projects yet? I’d love to hear what it caught — or missed — in your codebase._

### **About the Author**

I’m Alireza Rezvani (Reza), CTO building AI development systems for engineering teams. I write about turning individual expertise into collective infrastructure through practical automation.

**Connect:** [Website](https://alirezarezvani.com/) | [LinkedIn](https://linkedin.com/in/alirezarezvani) **Read more on Medium:** [Alireza Rezvani](https://medium.com/@alirezarezvani)