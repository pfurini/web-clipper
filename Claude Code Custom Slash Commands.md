# Claude Code: Custom Slash Commands
[Claude Code: Custom Slash Commands | motlin.com](https://motlin.com/blog/claude-code-utility-commands) 

 Utility commands I use with Claude Code for code maintenance and cleanup, particularly for handling the LLM's tendency to over-comment code.

This post is part of my [Claude Code setup series](https://motlin.com/blog/claude-code-setup).

LLMs write obvious comments. Despite clear instructions not to [in the system prompt](https://github.com/langgptai/awesome-claude-prompts/blob/2be4e1085e8c026cd12ad67fdd2ce61c1c42b2d6/README.md?plain=1#L494) and in my [global configuration](https://motlin.com/blog/claude-code-configuration#-too-many-comments), Claude consistently adds comments like:

```
  
const elapsedTimeMs = Date.now()  - startTime  

```

I've come to accept that comments are crucial to how LLMs "think" and I cannot stop the LLM from writing them, at least in the first draft. Instead, I use these commands to clean them up after the fact.

The `/all-comments` command asks the LLM to clean up redundant comments across the entire codebase. After Claude deletes all the comments, I run [`git absorb`](https://github.com/tummychow/git-absorb) and it's as if the comments never existed in git history.

.claude-prompts/shared/comment-removal-rules.md

```
- Look for comments that are obvious or redundant and remove them. Examples of comments that can be removed include:  
 - Commented out code. - Comments that describe edits like "added", "removed", or "changed" something. - Explanations that are just obvious because they are close to method names.- Do not delete all comments:  
 - Don't remove comments that start with TODO. - Don't remove comments if doing so would make a scope empty, like an empty catch block or an empty else block. - Don't remove comments that suppress linters or formatters, like `// prettier-ignore`- If you find any end-of-line comments, move them above the code they describe. Comments should go on their own lines.  

```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/shared/comment-removal-rules.md)

The `comments` prompt is identical to `all-comments` except it adds the sentence "Look at the local code changes that are not yet committed to git" turning it into a local edit.

.claude-prompts/commands/comments.md

```
🧹 Remove obvious and redundant comments from uncommitted code changes.  
  
📍 Only consider and only edit local code changes that are not yet committed to git.  
  
@../shared/comment-removal-rules.md  

```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/commands/comments.md)

This is my preferred command because it integrates with [my workflow](https://motlin.com/blog/claude-code-workflow-commands) and is perfect for cleaning up after `/todo`.

While I can't get the LLM to stop writing comments during initial implementation, these commands are extremely effective at cleaning them up after the fact.

Emoji[​](#emoji "Direct link to Emoji")
---------------------------------------

This command adds emoji!

.claude-prompts/commands/emoji.md

```
Add appropriate emoji to the content we're working on to make it more engaging and easier to scan.  
  
$ARGUMENTS  
  
## 📍 Placement  
- Add emoji at the beginning of headers before the text  
- Include emoji in lists and navigation elements  
- Code comments  
  
## 🔄 Two-pass approach  
1. First pass: Add the most fitting emoji for each element  
2. Second pass: Replace duplicates with suitable alternatives  
  
# 🎯 Patterns for consistency  
- Match emoji to content's tone and purpose (🎉 celebrations, 🔧 technical, 📚 documentation)  
- Use emoji for status indicators (✅ complete, ⏳ in progress, ❌ error, ⚠️ warning)  
- Establish patterns for consistency (📝 for "Notes", 🔗 for "Links", etc.)  

```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/commands/emoji.md)

### Usage Patterns[​](#usage-patterns "Direct link to Usage Patterns")

The `/emoji` command follows consistent patterns and avoids duplicates by using a two-pass approach to ensure variety and appropriateness.

Learn[​](#learn "Direct link to Learn")
---------------------------------------

When we solve tricky problems, this command helps document the solution for future reference.

.claude-prompts/commands/learn.md

```
📚 Document tricky problems in CLAUDE.local.md  
  
$ARGUMENTS  
  
- Think about whether we ran into any tricky problems and figured out a solution  
 - CLI commands that failed that we had to try about 3 times - Ordering requirements or prerequisites that had to be set up - But we eventually figured it out and know a solution- 📝 Add a brief summary of the problem and its solution in CLAUDE.local.md  

```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/commands/learn.md)

### Knowledge Persistence[​](#knowledge-persistence "Direct link to Knowledge Persistence")

The `/learn` command documents solutions in `CLAUDE.local.md` so the knowledge persists for future sessions, especially useful for CLI commands that required multiple attempts or specific ordering requirements.

Rebase[​](#rebase "Direct link to Rebase")
------------------------------------------

This command systematically resolves merge conflicts during git rebases.

.claude-prompts/commands/rebase.md

```
🔀 Fix all merge conflicts and continue the git rebase.  
  
- Check `git status` to understand the state of the rebase and identify conflicted files  
- For each conflicted file:  
 - Read the file to understand the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) - Analyze what changes are in HEAD vs the incoming commit - Resolve conflicts by choosing the appropriate version or combining changes - Remove all conflict markers after resolution- ✅ After resolving all conflicts:  
 - If project memory includes a precommit check then run it and ensure no failures - Stage the resolved files with `git add` - Continue the rebase with `git rebase --continue`- If the rebase continues with more conflicts, repeat the process  
- ✔️ Verify successful completion by checking git status and recent commit history  

```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/commands/rebase.md)

### Conflict Resolution Strategy[​](#conflict-resolution-strategy "Direct link to Conflict Resolution Strategy")

The `/rebase` command follows a structured approach to understand and fix each conflict, ensuring the rebase completes successfully without missing any issues.

Next Steps[​](#next-steps "Direct link to Next Steps")
------------------------------------------------------

These utility commands complement the main [development workflow](https://motlin.com/blog/claude-code-workflow-commands) and work best with proper [Claude Code configuration](https://motlin.com/blog/claude-code-configuration).