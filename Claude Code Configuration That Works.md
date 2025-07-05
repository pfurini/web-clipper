# Claude Code: Configuration That Works
[Claude Code: Configuration That Works | motlin.com](https://motlin.com/blog/claude-code-configuration) 

 I worked hard on these prompts and think they're effective. This post covers how I configure `claude` using `CLAUDE.md` files and `settings.json` to control the behavior of the Claude code CLI.

This post is part of my [Claude Code setup series](https://motlin.com/blog/claude-code-setup).

Global CLAUDE.md[​](#global-claudemd "Direct link to Global CLAUDE.md")
-----------------------------------------------------------------------

The global `~/.claude/CLAUDE.md` file sets default behavior for `claude` across all projects. Mine is split into several files, with the [top-level CLAUDE.md](https://github.com/motlin/claude-code-prompts/blob/main/CLAUDE.md) including them all using [@ syntax](https://docs.anthropic.com/en/docs/claude-code/memory#claude-md-imports).

Instructions for Humans[​](#instructions-for-humans "Direct link to Instructions for Humans")
---------------------------------------------------------------------------------------------

### Code Style[​](#code-style "Direct link to Code Style")

I start with code style instructions that are applicable to LLMs and human developers.

.claude-prompts/instructions/code-style.md

```
- Don't write forgiving code  
 - Don't permit multiple input formats - In TypeScript, this means avoiding Union Type (the `|` in types) - Use preconditions - Use schema libraries - Assert that inputs match expected formats - When expectations are violated, throw, don't log - Don't add defensive try/catch blocks - Usually we let exceptions propagate out- Don't use abbreviations or acronyms  
 - Choose `number` instead of `num` and `greaterThan` instead of `gt`- Emoji and unicode characters are welcome  
 - Use them at the beginning of comments, commit messages, and in headers in docs
```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/instructions/code-style.md)

Instructions specific to LLMs[​](#instructions-specific-to-llms "Direct link to Instructions specific to LLMs")
---------------------------------------------------------------------------------------------------------------

### Testing Practices[​](#testing-practices "Direct link to Testing Practices")

When writing tests, LLMs remind me of [Mr. Meeseeks](https://en.wikipedia.org/wiki/Mr._Meeseeks). The LLM will go to any length to get tests to pass, including deleting all of the assertions.

The advice here is applicable to human developers too, but we're getting into LLM quirks now.

.claude-prompts/instructions/tests.md

```
- Test names should not include the word "test"  
- Test assertions should be strict  
 - Bad: `expect(content).to.include('valid-content')` - Better: `expect(content).to.equal({ key: 'valid-content' })` - Best: `expect(content).to.deep.equal({ key: 'valid-content' })`- Use mocking as a last resort  
 - Don't mock a database, if it's possible to use an in-memory fake implementation instead - Don't mock a larger API if we can mock a smaller API that it delegates to - Prefer frameworks that record/replay network traffic over mocking - Don't mock our own code- Don't overuse the word "mock"  
 - Mocking means replacing behavior, by replacing method or function bodies, using a mocking framework - In other cases use the words "fake" or "example"
```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/instructions/tests.md)

### Rhetorical questions?[​](#rhetorical-questions "Direct link to Rhetorical questions?")

Sometimes I ask `claude` why it implemented a feature one way and not another and it answers "You're right! I'll implement it <the other way>." Infuriating!

GPT-4o had [a now-famous bug](https://openai.com/index/sycophancy-in-gpt-4o/) that made it "overly supportive but disingenuous." It's "fixed," but all models can [trend toward sycophancy](https://www.anthropic.com/research/towards-understanding-sycophancy-in-language-models).

Tackling these these together:

.claude-prompts/instructions/conversation.md

```
- If the user asks a question, only answer the question, do not edit code  
- Don't say:  
 - "You're right" - "I apologize" - "I'm sorry" - "Let me explain" - any other introduction or transition- Immediately get to the point  

```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/instructions/conversation.md)

### Long-lived processes[​](#long-lived-processes "Direct link to Long-lived processes")

`claude` handles console output from linters and compiler well. But sometimes it will try to run a long-lived command like `npm run start` and basically hang.

.claude-prompts/instructions/build-commands.md

```
- When a code change is ready, we need to verify it passes the build  
- Don't run long-lived processes like development servers or file watchers  
 - Don't run `npm run dev`- If the build is slow or logs a lot, don't run it  
 - Echo copy/pasteable commands and ask the user to run it- If build speed is not obvious, figure it out and add notes to project-specific memory  

```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/instructions/build-commands.md)

Sometimes Claude still tries to run long-lived commands so I've started to explicitly deny them.

src/.claude-prompts/settings.json (deny section)

```
{  
 "permissions":  { "deny":  [ "Bash(npm run dev:*)", "Bash(npm run serve:*)", "Bash(npm run start:*)", "Bash(npm run start &)", "Bash(git push:*)", "Bash(rm -rf: *)" ] }}  

```

`claude` can search the internet, and can read files from outside the current git repository, but it's an extra hop. I find it convenient to gather files into a subdirectory just for the LLM.

.claude-prompts/instructions/llm-context.md

```
- Extra context for LLMs may be stored in the `.llm/` directory  
 - If `.llm/` exists, it will be at the root directory of the git repository -  `.git/info/exclude` includes `/.llm`, so don't `git add` its contents- Editable context:  
 - If `.llm/todo.md` exists, it is the task list we are working on. - As you complete tasks, mark the checkboxes as complete, like `- [x] The task` - As we work on an implementation, plans will change. Feel free to edit the task list to keep it relevant and in sync with your plans.- Read-only context:  
 - Everything else in the `.llm/` directory is read-only context for the your reference - It contains entire git clones for tools we use - It contains saved documentation
```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/instructions/llm-context.md)

These instructions work well. Though I'm deliberate about telling the LLM to look in `.llm/`.

LLMs write obvious comments.

```
  
const elapsedTimeMs = Date.now()  - startTime  

```

Here are my instructions telling Claude not to, but it really, really likes writing comments. The built-in system prompt flat out says "Do not add comments to the code you write" and that doesn't work either.

.claude-prompts/instructions/llm-code-style.md

```
- Use comments sparingly  
- Don't comment out code  
 - Remove it instead- Don't add comments that describe the process of changing code  
 - Comments should not include past tense verbs like added, removed, or changed - Example: `this.timeout(10_000); // Increase timeout for API calls` - This is bad because a reader doesn't know what the timeout was increased from, and doesn't care about the old behavior- Don't add comments that emphasize different versions of the code, like "this code now handles"  
- Do not use end-of-line comments  
 - Place comments above the code they describe
```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/instructions/llm-code-style.md)

I've come to accept that comments are crucial to how LLMs "think" and I cannot stop the LLM from writing them, at least in the first draft. My [comment cleanup commands](https://motlin.com/blog/claude-code-utility-commands#comments) are great at getting rid of them in a second step.

### Claude's Commit messages[​](#claudes-commit-messages "Direct link to Claude's Commit messages")

Claude code's commit messages are remarkably consistent, to the point where you know what [the system prompt](https://gist.github.com/transitive-bullshit/487c9cb52c75a9701d312334ed53b20c#file-claude-code-prompts-js-L448-L462) is going to say before you read it.

The system prompt includes [inconsistent and incorrect information](https://github.com/anthropics/claude-code/issues/1000) about pre-commit hooks and staging changes, so I give my own corrections.

.claude-prompts/instructions/llm-git-commits.md

```
- 🔧 Run `just precommit` (if a `justfile` exists and contains a `precommit` recipe)  
- 📦 Stage individually using `git add <file1> <file2> ...`  
 - Only stage changes that you remember editing yourself. - Avoid commands like `git add .` and `git add -A` and `git commit -am` which stage all changes- Use single quotes around file names containing `$` characters  
 - Example: `git add 'app/routes/_protected.foo.$bar.tsx'`- 🐛 If the user's prompt was a compiler or linter error, create a `fixup!` commit message.  
- Otherwise:  
- Commit messages should:  
 - Start with a present-tense verb (Fix, Add, Implement, etc.) - Not include adjectives that sound like praise (comprehensive, best practices, essential) - Be concise (60-120 characters) - Be a single line - Sound like the title of the issue we resolved, and not include the implementation details we learned during implementation - End with a period. - Describe the intent of the original prompt- Commit messages should not include a Claude attribution footer  
 - Don't write: 🤖 Generated with [Claude Code](https://claude.ai/code) - Don't write: Co-Authored-By: Claude <noreply@anthropic.com> - But still include the 🤖 emoji as the very first character.- Echo exactly this: Ready to commit: `git commit --message "<message>"`  
- Confirm with the user, and then run the exact same command  
- If pre-commit hooks fail, then there are now local changes  
 -  `git add` those changes and try again - Never use `git commit --no-verify`
```

[View on GitHub](https://github.com/motlin/claude-code-prompts/blob/main/instructions/llm-git-commits.md)

I didn't expect this to work since it contradicts the system prompt, but it works fine. I hope [the system prompt gets fixed upstream](https://github.com/anthropics/claude-code/issues/1000) anyway.

Project-specific CLAUDE.md[​](#project-specific-claudemd "Direct link to Project-specific CLAUDE.md")
-----------------------------------------------------------------------------------------------------

You can override global settings for a specific project by creating a `CLAUDE.md` and `CLAUDE.local.md` in the root of that project. The first is intended to be committed and the second is intended to be ignored.

I don't want to assume my teammates use a specific tool like Claude, so I never create project-specific `CLAUDE.md` files. I don't even want the word Claude to appear in `.gitignore` so instead I ignore the file with:

```
echo CLAUDE.local.md >> .git/info/exclude  

```

I hope a standard emerges. I would be willing to check in a file called `LLM.md`.

The contents of `CLAUDE.local.md` vary. It's a good place to share:

*   What the build tool is, and which commands to run before committing.
*   Libraries to use and APIs to prefer.
*   Whether to use a `justfile` or the language-native build tool.

Next Steps[​](#next-steps "Direct link to Next Steps")
------------------------------------------------------

Now that you've seen how to configure Claude Code's behavior, check out my development workflow in [Workflow Commands](https://motlin.com/blog/claude-code-workflow-commands) and my additional [Utility Commands](https://motlin.com/blog/claude-code-utility-commands).