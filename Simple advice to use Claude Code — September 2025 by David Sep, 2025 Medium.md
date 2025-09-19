# Simple advice to use Claude Code — September 2025 | by David | Sep, 2025 | Medium
[Simple advice to use Claude Code — September 2025 | by David | Sep, 2025 | Medium](https://medium.com/@dasfacc/simple-advice-to-use-claude-code-september-2025-c7bfcd2dd8ac) 

 [

![](https://github.com/pfurini/web-clipper/blob/main/images/9-19-2025,%2011-03-43/28799e9a-db78-4e65-b86f-074cfdb7dd3e.jpeg?raw=true)






](https://medium.com/@dasfacc?source=post_page---byline--c7bfcd2dd8ac---------------------------------------)

Claude Code is probably the most widely used ai coding tool today, but using it ineffectively will ironically slow you down and — even worse — make you hate coding with ai.

_Pro tip:_ If you’re just starting with claude code there’s a lot of [alpha](https://www.investopedia.com/terms/a/alpha.asp) in investing 1–5 hours listening to the experts.

I’ll give you my 2 cents and then reference the real experts.

Opus plan mode: Run `/model` and choose the fourth option.
----------------------------------------------------------

(and of course, use plan mode before every code implementation that is beyond trivial)

Press enter or click to view image in full size

![](https://github.com/pfurini/web-clipper/blob/main/images/9-19-2025,%2011-03-43/8ac4509e-7039-4b99-8af0-99b600af351a.png?raw=true)

Select model menu

Use multiple CLAUDE.md files
----------------------------

Instead of packing all your project’s rules, patterns, and guidelines into one massive `CLAUDE.md` file at the root of your repo, create various specific `CLAUDE.md` files inside subfolders. That will declutter claude’s context and make it more focused on the current task.

_Pro tip:_ if you already have a huge CLAUDE.md file, ask claude to break it down into new CLAUDE.md files in the relevant subfolders.

Don’t compact, clear instead
----------------------------

Anthropic engineers use claude code internally and apparently they aggressively clear. This goes hand in hand with a lot of people’s experience, that performance degrades a lot after compacting, in my experience it becomes unusable 90% of the time and I end up just starting over. [This guy](https://x.com/DannyAziz97/status/1945958948227461329) even built an `npx` command to keep only the first 15 messages.

Press enter or click to view image in full size

![](https://github.com/pfurini/web-clipper/blob/main/images/9-19-2025,%2011-03-43/67afc19c-8b2d-482c-a234-364639bb2cdc.png?raw=true)

7% context left before auto-compact

Press enter or click to view image in full size

![](https://github.com/pfurini/web-clipper/blob/main/images/9-19-2025,%2011-03-43/c1514c17-41a0-49ec-8e06-a6c08396ca37.png?raw=true)

/clear option highlighted

### Why does this happen?

You could argue that the reason why you’re hitting the limit is because you 1. didn’t define small enough steps or 2. you didn’t define the task specifically enough or 3. it’s a hard task and the model met a lot of foot guns and dead ends while looking for the right solution.

Press enter or click to view image in full size

![](https://github.com/pfurini/web-clipper/blob/main/images/9-19-2025,%2011-03-43/d56df3da-811d-4b76-9de7-19216a8dd405.png?raw=true)

depiction of an agent that meets dead ends and foot guns on the search for the solution

### So what to do?

If your situation fits one of the above cases you can try undoing all or [some of the changes](https://www.anthropic.com/engineering/claude-code-best-practices#:~:text=or%20expand%20instructions.-,Double%2Dtap%20Escape,-to%20jump%20back) and:

*   breaking up the task in smaller tasks
*   defining the task more specifically
*   specifying solutions that will **not** work and the agent **shouldn’t** try

### What if it’s on the right path but there’s more work to be done?

In that case:

*   if you had a task document: make it update the document to reflect progress, knowledge gained and next steps (do this before you’re under 10%)
*   if you didn’t: make it write a task document with a thorough description of the task and all of the above. You are now able to mark documents with a ‘@’, so you can just /clear and start the next session with “ok claude let’s continue with ‘@task-document.md’

### And if I tried all that and claude can’t tackle the problem?

Make it read this article to see if it finds ways to break up the task or more narrowly specify it :). If it really can’t tackle your problem then it’s your turn to do it manually. Use it as a chance to identify gaps in the model’s skills so you are better prepared to use them in the future for similar situations.

Press enter or click to view image in full size

![](https://github.com/pfurini/web-clipper/blob/main/images/9-19-2025,%2011-03-43/e9da025c-86a3-4bae-a13c-8dc35b3cded8.png?raw=true)

Uncle claude wants you to get your bottoms off your couch and code

Lastly, to go with the last sentence
------------------------------------

Make sure to have patterns in your codebase that claude can emulate. If you end up manually writing the code, as in the last paragraph, it’s probably because your codebase didn’t contain that pattern before. Make sure to include this new file as a reference for future and similar features