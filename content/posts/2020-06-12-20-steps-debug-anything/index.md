---
title: 20 simple steps to debug anything
author: Dennis Brotzky
date: 2020-06-12
hero: ./images/hero.jpg
excerpt: Debugging is an everyday task. Your webpack build failed locally? Debug it. Your CI failed? Debug it. You wrote some code and broke everything? Debug it. Bob from the marketing team called you because you MUST fix a bug into production right now? Debug it. You want to learn how to debug? Be our guest.
---

Debugging is an everyday task. Your webpack build failed locally? Debug it. Your CI failed? Debug it. You wrote some code and broke everything? Debug it. Bob from the marketing team called you because you MUST fix a bug into production right now? Debug it.

You want to learn how to debug? Be our guest.

## 1. Don't panic

Keep a cool head, it makes the hard bugs simple and the impossible ones possible. So, if you struggle, remove your hand from the keyboard. Take a breath, get a glass of water. And go back to your seat.

## 2. SERIOUSLY, do NOT panic

Even if you have to debug your production server. Stress causes mistakes, mistakes cause more bugs, more bugs cause more stress.

As you will see later, debugging is a scientific process, so it is necessary to keep your head cool.

By the way... If your production is down, it's time to roll back!

## ‌3. Verify the bug is not actually a feature

As features and edge cases grow it becomes impossible for one developer to keep all this complexity in their head. You should spend the time to check the defect you are seeing is outside the expectations of the application.

## 4. Reproduce the error where it occurred

Don’t wait for the failure to reproduce by itself. Make it fail again as it was reported. If it was in production, don’t start by reproducing the failure locally: do it in production (unless it involves dropping your database). Don't focus on making your staging data look like the production environment, you'll waste time and be misled by the wrong bugs.

Bugs are scarcely random. Try as hard as possible to find a set of initial conditions and/or actions which lead to a deterministic way to reproduce the failure. It helps.

## 5. Use the logs, Luke

If there is information about your bug, it’s in the logs. So, find the logs, and use them. “Where are they?” you may ask. Look at the docs or the config of your failing system and you’ll know.

Then, reproduce the error to see the logs in real time.

You must mentally bind the action producing the failure to a system reaction. Otherwise, you could be biased and take an error log for something unrelated to your problem.

No logs? Go to step 10.

## 6. Read the error message, from left to right, from top to bottom

An error is text. Read it like text. Don't try to wild guess the cause by jumping right to the end of the stack trace. Being calm helps take the time to acknowledge the whole error message.

## 7. Read the error, again

The first time, you decipher. The second time, you understand.

## 8. Solve the bug

In most of the cases, the error message is crystal clear. And maybe the cause is trivial. Hence, interrogate the usual suspects: seek typos, verify you are not debugging the wrong server or the wrong git branch, ensure your libraries are up to date, clear your application cache, check your keyboard is actually plugged in your computer... And look at the last thing you changed before the failure (now you know why your teammates blame you when you push a 43874 line changes Pull Request).

You made a mistake. It happens. Fix it. And go to step 19. If it is actually a difficult bug, keep hope and go to the next step.

## 9. Search the error message online

Remember: you are not alone (unless you are coding in a weird language). Use your favorite search engine and search for your error. You might find a StackOverflow thread or a GitHub issue helping you to solve the bug you are facing. No results? Ask your team if someone has already encountered this bug.

Otherwise, move forward to the next step.

## 10. Setup a clean debugging workspace

You have eliminated the obvious causes, and the bug is not fixed yet? Brace yourselves, debugging is coming!

- Warn your team, you may spend some time on this and they should know. You also want them to keep you updated regarding your bug in case they encounter it in the wild.
- Home sweet home, try to reproduce the bug on your local environment where it is the most comfortable place to debug. Setup your favorite debugger if it is not already done.
- Even better, find the *smallest* set of actions/data that make your system fail deterministically and automate it to have the feedback loop as short as possible.
- Fix the bugs you are aware of, they can mask what is really happening or even be sidekicks of your main bug.
- Finally, prepare a place to log your debugging expedition. You'll see later why it's useful.

## 11. Setup a clean debugging mindset

You reduced the noise in your tools, now you need to reduce it in your brain. Remember it is more a cognitive process rather than a technical one.

Doubt more than Descartes, especially your own assumptions. It’s rarely because of a Linux Kernel bug, face it. Remove your beliefs, you know the issue is not really random, you just don’t know when it is triggered. Unless you can prove it is true, replace your “*cannot*” with “*shouldn’t*”.

## 12. Create a model of your system

You have the tool, you have the mind, now you need the map.

Make a diagram of how your system is supposed to work, from one end to another, from your end user to the place where data is stored. Here you should see your frontend, your backend, the server and the cache systems between them, the data stores, every cog that is part of your machine. Display the way these parts communicate with one another. Be exhaustive, but not too precise. “All models are wrong, but some are useful” to quote George Box.

If you don’t have the knowledge to make that diagram, don’t go any further until you are able to do so, it is mandatory for your everyday job. Would you trust a mechanic to repair your car if they don’t know how the engine’s power is transmitted to the wheels?

## 13. Make educated guesses

Now you have a diagram of your system behaving correctly. You already know how to reproduce your bug, hopefully with an error message indicating what is wrong with your system.

By putting together your model, the facts and your experience, you should be able to formulate the most likely causes about what is going wrong. Jot them down your notes.

If you only have weird patterns and coincidences instead of a deterministic way to reproduce your bug in step 4, don’t be carried away by them. For instance, an error happening only at certain periods of time can be linked to the ongoing traffic on your server or a cron.

## 14. Run and devise an experiment

Here, we want to confirm or refute our previous hypotheses, as fast as possible. Yes, this is the step where we have to talk about dichotomy.

Choose one of your hypotheses and start from there. If you think the issue comes from the call to your DB, don’t bother looking into your frontend code. Put a debugger breakpoint straight at the suspicious SQL query building.

You think the bug is in the code? Don’t read all the code. Checkout another branch and see if it still fails. The bug is merged on master for a while and you don’t know where to look? Learn how to use [git-bisect](https://git-scm.com/docs/git-bisect), now. Then, find the faulty commit in no time and reduce the number of lines of code to explore.

Verify your hypotheses one after each other (remember humans are not well wired for parallel computing). Strike out the wrong ones on the list you previously wrote.

For each experiment, write down the cause you wanted to assess, what you did, in what order, and what happened as a result. It will ensure you are not doing wild guesses and give you material in case of backtracking or if you ask for help.

If you have not found the cause of the bug yet, you are likely closer than you think. You might have hypotheses that you do not know how to test yet. Or you remain stuck in a part of your system and are still looking for a path to reach the bedrock. So, prepare to dive.

## 15. We need to go deeper

The model built previously was good enough to approach the bug cause but not to touch it. Because you can’t reason about something you don’t perceive, refine the model to sharpen your sight of the problem.

Most of the time the obstacle lies at a system boundary, like a function from a vendor library, an API provided by another team or even two modules inside your own software. So here are some ideas to scout through the fog:

- **Read the manual.** Even if it looks big, the manual contains knowledge about how to use your vendor. Sometimes, you have to stop looking at your code to find the truth. And when the docs is really good, it provides you the small details that are actually very important for your issue. *Disclaimer: read manuals the same way you read your error logs*. ;-]
- **Read your libraries source code.** Don’t be afraid even if it is not *your code*, it’s still code. You are able to read it. Understand it might take more time because libraries have their own internal idioms. It will lead you closer to the bug cause. And make you a better developer.
- **Look at the signal.** If the problem happens between two nodes, the information you need to observe is on the wire. Learn how to read a [tcpdump](https://www.tcpdump.org/manpages/tcpdump.1.html)/[wireshark](https://www.wireshark.org/) trace. Follow your data with a debugger. Look at what your garbage collector keeps in the heap to understand a memory leak. Unravel your hanging process thanks to [strace](https://strace.io/). And don’t trust your understanding of the ORM you use, read the generated SQL query and execute it by yourself.
- **Know your concurrency fundamentals.** Deadlocks, live locks, shared memory, race conditions… Those are the worst and unless your software runtime includes a race detector, you need to use 100% of your brain to get rid of concurrency bugs. They  deserve their own article.

In any case, search for depth. Don’t go halfway to learn something new or your intuition will add more fog. Educated guesses are for mastered topics.

If you are not able to delve anymore, don’t get lost in limbo. Tag a partner!

## 16. Ask for help

You’ve dug as much as you could and still cannot pinpoint what’s wrong? That can happen, it’s okay, we are fallible beings.

You may have not seen the smoking gun because you are not lucid anymore. Sometimes all you need is to take a step back, and that can be offered by something as simple as [a rubber duck](https://en.wikipedia.org/wiki/Rubber_duck_debugging).

If the duck doesn’t make the cut, set aside your ego and your shyness to ask for help from a fellow developer. Search first for an expert in the area you are investigating, but any help is welcome here.

Thanks to your debug log, present to them your debugging process. State all the facts, even the weird ones and keep away any results from your inductive reasoning as you do not want to influence them.

If the bug is still there after you’ve consulted every person likely to help in your team or surrounding ones, go online. Post a small portion of your code that can exhibit your problem. Describe how to reproduce your bug and ask politely for help. People like puzzles, are eager to help and will collaborate to find a solution. [70% of StackOverflow and 78% of ServerFault questions have at least one answer with upvotes](https://stackexchange.com/sites).

You have cast your hook to the water, now wait for the help to bite and give you a fix to try.

## 17. Ensure *this* failure is fixed

Here, we suppose you have found something that made your bug disappear.

Software engineering is *mostly* deterministic: you must have done something. A bug cannot simply vanish in the air. (If it happened don't celebrate and go back to step 10 instead.)

Assess your fix with the same scientific mindset as before:

- Keep only what you think is necessary for your fix to work.
- Use the test from the step 10 to check that the bug is gone with the fix.
- Remove the fix and use the same test to check that the bug is back.

If the fix doesn’t pass this test, the two main hypotheses are that:

- Your fix is incomplete and need other changes made during the process. Something considered unnecessary might be required.
- You had a lapse in concentration, your fix never really worked.

In both cases, use this new data to refine your understanding of the problem and go back to step 13. Stay strong, you’re closing in!

If the fix passes the previous test, good job, the bug is dead!

## 18. Make it easier next time

*This* bug is dead. But:

1. It could have been a trivial bug solved at step 8.
2. Bugs have friends.

Face it, software engineers spend more time understanding why things do not work as intended than coding features. If you reduce the time to debug issues, you will coincidentally increase the time where you are creating value instead of repairing it.

Keep all the things you added into the codebase you wish you had at the beginning of your bug quest: log messages at debug levels with input/output values, HTTP status code respecting the RFC semantics, an actual crash in a previously silent catch block...

The same goes for tool created to investigate your problem, don’t discard it. Document why it’s here and how it works, and commit it with the bugfix.

Anyway, the easiest bug is the one that does not occur. You put out the fire, now make your code fireproof.

## 19. Ensure the bug never comes back

Before doing anything to prevent a bug to come back, ask yourself why it happened in the first place. The causes are often the same: an unmanaged edgecase, a developer does not know how to use one of the tools, an abstraction is not representing the domain enough… In security you think of attack surface. Here, look at what is error prone. And it’s not always in the code.

*Disclaimer: each of the following parts needs its own article or book.*

**Remove other occurrences**

If a misconception introduced the bug, there are chances the same mistake has been made somewhere else. Search the bad snippet and replace it with the good one. It’s not enough but it’s a good start.

**Refactor**

Good code is mistake-proof. Some people think "no abstraction is better than bad abstraction". But both lead to bugs. Your job is to write good abstractions that last. So when a bug occurs, an engineer might have failed to bring such abstraction. Take the time to repair the broken parts. [Investing in internal software quality leads to better external quality](https://martinfowler.com/articles/is-quality-worth-cost.html).

**Add non regression testing**

If you can’t eradicate the bug cause, at least make it impossible to reach your master branch or production again. Basically, write the right test suite which detects the failure and run it on every commit/pull request. The test must fail if you stash your patch.

**Write a standard**

You learned how to fix the bad with the good. But to share the good, it must be written somewhere. Write down the pitfalls, give examples of the wrong way and the right way to use a tool, and share it to your team.

**Train your team**

Reading a standard is not enough to fully learn a skill. Take time to present the standard and use it in a pair programming session next time a fellow developer has to code by practicing what you just wrote.

**Automate the standard**

We can’t remember everything and the most error prone system in this chaos is our human selves. Automate good practices to reduce the risk of making mistakes. Use tools like code generation, declarative scripting, new linter rule...

## 20. Celebrate!

This bug is now definitely behind you. This is good, isn’t it?

To share this bliss, we encourage you to write down what happened and memorize it. War stories are always fun to tell, fun to hear. They are also a great way for others to remember obscure behaviors to look at when they have ruled out common hypotheses.

Now you’re ready to fight those bugs!
