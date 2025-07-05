# Claude Code: The Perfect Development Loop | motlin.com
[Claude Code: The Perfect Development Loop | motlin.com](https://motlin.com/blog/claude-code-workflow-commands) 

 I've written two slash commands for Claude Code that form the core of my development workflow: `/todo` implements one task and `/commit` commits it.

This post is part of my [Claude Code setup series](https://motlin.com/blog/claude-code-setup).

Understanding Slash Commands[​](#understanding-slash-commands "Direct link to Understanding Slash Commands")
------------------------------------------------------------------------------------------------------------

Claude Code comes with built-in slash commands like `/pr-comments`, which are essentially [shortcuts for specific prompts](https://gist.github.com/transitive-bullshit/487c9cb52c75a9701d312334ed53b20c#file-claude-code-prompts-js-L591-L597). You can also [create custom slash commands](https://docs.anthropic.com/en/docs/claude-code/tutorials#create-custom-slash-commands).

*   **Global commands**: Place them in `$HOME/.claude/commands/example.md`
*   **Project commands**: Place them in `./.claude/commands/example.md`

Both are invoked with `/example`. If a command exists in both locations, the project-specific version takes precedence.

The Development Loop[​](#the-development-loop "Direct link to The Development Loop")
------------------------------------------------------------------------------------

When building larger features, I work through a task list in `.llm/todo.md`:

1.  Run `/todo` to implement the next task
2.  Test and debug manually
3.  Run `/commit` to commit the changes
4.  Run `/compact` to clean up the conversation history
5.  Repeat

Creating the Task List[​](#creating-the-task-list "Direct link to Creating the Task List")
------------------------------------------------------------------------------------------

When planning larger changes, I tend to chat with a thinking model in the browser. At the end, I ask for a task list:

> Write a task list that we can work through to implement this idea, as a markdown checklist.
> 
> Each checkbox should represent a task that we can implement and commit on its own.
> 
> Arrange the tasks in the order we need to implement them, not in order of importance.
> 
> Example format:
> 
> *   Add a test case for feature ABC.
>     *   It's ok for the test to be failing at this point.
> *   Implement feature ABC.
> *   Delete DEF
>     *   Replace usages of DEF with ABC.

I save this list to `.llm/todo.md`. I've [configured Claude](https://motlin.com/blog/claude-code-configuration#extra-context-just-for-you) to ignore the `.llm` directory in version control.

`/todo`[​](#todo "Direct link to todo")
---------------------------------------

This command tells Claude to implement one task from the list.

.claude-prompts/commands/todo.md

```
📋 Find and implement the next incomplete task from the project todo list.  
  
- Read the file `.llm/todo.md`  
 - The file will only exist in this directory or in the main repository if we're in a worktree. - First try reading `./.llm/todo.md` - If that doesn't exist, use `git rev-parse --git-common-dir` to find the main repository and check if `.llm/todo.md` exists there. - Don't look in other locations. Don't look in the home directory.- Find the first line with an incomplete task, with `- [ ] <task>`  
 - Keep in mind that the completed tasks might not be contiguous, since it's common to prepend new tasks at the top- Echo context to the user including the previous completed task and the current task we just found  
 - Use the format:  
⏺ The previous completed task was:  
 - [x] Add feature ABC.  
 The next incomplete task is: - [ ] Replace DEF with ABC.  
- Think hard about the plan  
- Confirm the plan with the user before proceeding, with "🤔 Proceed? ➡️  [y/n]"  
- Implement the task  
- Focus ONLY on implementing this specific task  
- Ignore all other tasks in the `.llm/todo.md` file or TODOs in the source code  
- Work through the implementation methodically and completely, addressing all aspects of the task  
- Run appropriate tests and validation to ensure the implementation works  
- ✅ After the implementation is complete and verified, update `.llm/todo.md` to mark the completed task as done by changing `- [ ]` to `- [x]`  
  

```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/commands/todo.md)

"Think hard" is [a magic word](https://simonwillison.net/2025/Apr/19/claude-code-best-practices/) that triggers extended reasoning.

`/commit`[​](#commit "Direct link to commit")
---------------------------------------------

After implementing and testing a task, I use `/commit` to create a commit:

.claude-prompts/commands/commit.md

```
📝 Commit the local changes to git.  
  
- 🔧 Run `just precommit` (if a `justfile` exists and contains a `precommit` recipe)  
- 📦 Stage individually using `git add <file1> <file2> ...`  
 - Only stage changes that you remember editing yourself. - Avoid commands like `git add .` and `git add -A` and `git commit -am` which stage all changes- Use single quotes around file names containing `$` characters  
 - Example: `git add 'app/routes/_protected.foo.$bar.tsx'`- 🐛 If the user's prompt was a compiler or linter error, create a `fixup!` commit message.  
- Otherwise:  
- Commit messages should:  
 - Start with a present-tense verb (Fix, Add, Implement, etc.) - Not include adjectives that sound like praise (comprehensive, best practices, essential) - Be concise (60-120 characters) - Be a single line - Sound like the title of the issue we resolved, and not include the implementation details we learned during implementation - End with a period. - Describe the intent of the original prompt- Commit messages should not include a Claude attribution footer  
 - Don't write: 🤖 Generated with [Claude Code](https://claude.ai/code) - Don't write: Co-Authored-By: Claude <noreply@anthropic.com> - But still include the 🤖 emoji as the very first character.  
🚀 Run git commit without confirming again with the user.  
  
- If pre-commit hooks fail, then there are now local changes  
 -  `git add` those changes and try again - Never use `git commit --no-verify`
```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/commands/commit.md)

You don't need a custom command to commit - and Claude will offer to commit without being asked. But this gives me control over timing while leveraging Claude's ability to write good summaries.

Managing the Context Window with `/compact`[​](#managing-the-context-window-with-compact "Direct link to managing-the-context-window-with-compact")
---------------------------------------------------------------------------------------------------------------------------------------------------

The `/compact` command is built in but [not well documented](https://docs.anthropic.com/en/docs/claude-code/costs#reduce-token-usage). It's built on [a markdown prompt](https://www.reddit.com/r/ClaudeAI/comments/1jr52qj/here_is_claude_codes_compact_prompt/) to summarize the conversation, then replaces Claude's context with that summary.

After completing a task, I need to make room in the context window for the next task. I used to run `/compact` if the next task was related, and `/clear` if unrelated. Now I just always run `/compact`.

This is also a good time to restart with `claude --continue` if it has been running too long or if there's a new version available.

Summary[​](#summary "Direct link to Summary")
---------------------------------------------

This workflow breaks large features into manageable steps.

These commands work best when combined with proper [Claude Code configuration](https://motlin.com/blog/claude-code-configuration).

For more custom commands, see [Claude Code Utility Commands](https://motlin.com/blog/claude-code-utility-commands).