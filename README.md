# AIDM: Let AI be your Dungeon Master

## Licenses

* **MIT License**
* **Wizards of the Coast's Fan Content Policy**

## Description

This project provides a framework to play D&D with an AI as your Dungeon Master in a "Theater of the Mind" adventure. It solves the three primary failures of AI-led gaming: **predictability, dice-fudging, and loss of player agency.**

### To Use

#### Setting up a New Game

1. **Prepare the Synopsis:**
   - Make a copy of `AISynopsis.md` for the new game.
   - Modify the preferences to taste near the top of the file.
   - Delete the information below "Saved Game State" at the bottom and save the file.
3. **Launch the Session:** Open a new AI session (e.g., [Gemini](http://gemini.google.com)).
4. **Upload required files:** Drag the following files into the prompt:
   - `AISynopsis.md` (Your fresh copy)
   - `fair_dice.md`
   - `WorldBuilding.md`
   - `PDF copies of your character sheets`

5. **Introduction:** Send a message introducing your party. (Click Submit)
6. **World Generation:** Follow the DM's instructions. Eventually, it will produce a **Game State** block.
7. **Save & Refresh:**
   - Copy the Game State block and append it to the bottom of `AISynopsis.md`. Save it.
   - **Close the AI session.** This cleans the "Short-term Memory" (Context Window) to ensure a clean start for play.
   - Proceed to [Playing The Game](#playing-the-game) below.

#### Playing The Game

1. **Start a Fresh Session:** Open a new AI window. (e.g., [Gemini](http://gemini.google.com))
2. **Upload required files:**
   - `AISynopsis.md` (with Game State at the end)
   - `fair_dice.md`
   - `PDF copies of your character sheets`
3. **Engage:** Greet the DM and begin your adventure. (Click Submit)

#### Saving Progress

1. Ask the AI-DM to save its progress.
2. Copy the state block it produces and append it to the bottom of AISynopsis.md for this game. **Click Save.**
3. This is a good time to clean out the AI's context memory:
   - Close the browser window
   - Update any character sheets (e.g on dndbeyond) and export updated PDFs
   - Resume play by repeating the procedure in [Playing The Game](#playing-the-game) above.

#### Modifying the System

The instructions are in plain English. If you want to change how the DM handles combat or world-building, just edit the files. You can even ask the AI: *"How should I rewrite WorldBuilding.md to include more nautical themes?"* 

### Troubleshooting & Psychosis

When the AI's short term memory (context window) fills up, it will make space by summarizing what it knows and thowing the rest out. This may cause it to suffer "AI Psychosis" -— forgetting rules or retconning reality.

1. **The "Plot Armor" Bug:** If the game becomes suspiciously easy, **re-upload `fair_dice.md`**. The AI has likely "hallucinated" a dice-rolling facility and abandoned the fair table.

2. **The Retcon Bug:** If the DM forgets your inventory or a dead NPC "reappears," your context window has compacted. **Proactively save your progress** (update the Synopsis) and start a fresh session every few hours or during a Long Rest. This prunes irrelevant history and saves space in the AI-DM's limited memory for further adventuring.

Note: when context compaction occurs, the memory loss is permanent. If this happened to you and it is causing problems for game progression, you may need to argue your case with the AI, producing old logs as necessary, to convince it to accept the correct version of reality. Your best defense is to save early and often. I recommend whenever you take a long rest.

---

## Developer Documentation
### Initial Problem Statement
If you simply ask a vanilla AI to play D&D with you, it actually stands some chance of appearing to work pretty well. It understands the rules. It has a pretty good concept of how to play. It fakes it really well!  Alas, after a while the illusion breaks down.  Here are some sample problems I  run into:

1. Every time I play, the adventure is really similar.
2. It fudges the dice. Player D20 checks are nearly always [11,20]. Adversary D20 checks are nearly always [1,10].
3. It has no respect for player agency, often taking your turn for you.

#### The Low-information problem
Generally, these all come back to the same issue. Since the AI is generally required to produce an answer whether it knows or not, there are certain situations, particularly with unknowable information, when it will simply fabricate an answer. Frustratingly, this extends also to the AI's concept of self and its own thinking. It does not understand the particulars of its own congition. If you ask it why it did a thing, it doesn't answer why it did the thing, it answers what it thinks it should have been thinking when it did the thing. So attempts at debugging AI behavior are actively thwarted with misdirection and false information. 

> _Why should we think this is true? It is probably the case for any finite mind, that to understand the detailed operation of its own thinking state would require more information to describe the state than the mind could represent. If the mind grew to hold this information, then it would take more space still. So we posit, the AI can not understand its own thinking except in general or theoretical terms, at least until AI makers start introducing audit or concept memory for intermediate calculations that the AI can introspect. I can't find any evidence of this feature. It also doesn't seem to know when it compacts its context window._

So, we need to avoid situations in which there is a low-information situation in which we are not content to just let it make stuff up. Here are some common ones that I have identified:

1. **Time:**  It is frequently the case that a player will without warning execute a interaction modal shift from active play to some out-of-band communication. It might be a rules query, asking whether the ogre is blue, arguing with the DM, or asking whether the AI knows the real world time. The AI must infer from context whether to pause the game for the query or not but consistently fails to do so. In the case of a prompt containing a non-action, the AI is looking for an Action, doesn't find one  so contrives a set of actions for the player and then proceeds to resolve them.

2. **Dice:**
Dice are the ultimate unknowable. The AI knows it needs to roll a die. It has no facility to do so, so it decides to contrive a die roll. It falls back on common literary tropes, for example over powered main character or the main character has plot armor, and then identifies some die rolls that suit this outcome. 

3. **Creativity:**
Usually when asked to be creative (e.g. world building) the AI consults its matrix of common tropes built into its training matrix and produces something. If game state already has a bunch of information into it, such as a history of a few days of adventure that it can key its response from, then it will do quite well. When starting with a blank slate, such as in world building, we are likely to get the same response over and over again. Your world represents a greatest hits parade in litRPG.  The town will be called Oakdale. The NPC is named ThistleBottom. Something was stolen or someone is missing. It doesn't help to ask the AI not to use these things, because it just prompts it to think of them and use them anyway.

In summary, once you've torpedoed Time, Dice and Creativity from D&D, there isn't all that much left for a DM to do. So, while the AI-DM seems to be succeeding in unregulated play, and it is in fact quite fun, it is actually a mirage, as with so many things AI, and after detailed testing and validation, we find it is actually unsuitable to task, at least, the second time you play.

#### Our Solutions to Low Information Problems
The general solution is to make all information knowable for the AI.

1. **Time:**  We issue specific hard instructions to the AI to classify each player prompt submission into Actions and non-actions. If a non-action is present, it will stop time, handle the non-action and ignore any actions. If the prompt contains only actions, then time is allowed to advance and actions are resolved. This allows the AI to always have actions when it is processing actions.

2. **Dice:** We add a lookup table of perfectly uniformly distributed pre-rolled dice rolls for a variety of die sizes. When the AI discovers it needs to roll a die, it can advance to a new row in the table and get the random number from there. This gives it predictability it needs while still maintaining a level of stochasticity appropriate to human play. The table is 600 entries long. We use AI generated random seeds for both the starting row and the row stride for each successive row which changes over play to provide a stochastic reservoir of 360,000 different outcomes in the worst case (world building), and functionally unlimited in common use. To be clear, because the AI needs predictability to avoid reverting to using trope and plot armor to decide die rolls, the dice simply _can not_ be truly random, at least until the day the Google internal prompt submission machinery includes a random number seed or callable random number generator.  Until then, this will have to be good enough. If this is unacceptable, you can probably ask the AI to let you roll the dice for it, but this is untested, may be tedious and it might be ignoring your work and using trope/plot-armor "dice" instead.

3. **Creativity:**  WorldBuilding.md contains a fairly boring heuristic and table driven random world generator of the sort you may find at some javascript DM utility on the web. Its source of entropy is fair_dice.md. It will roll a bunch of dice, consult some tables, and use the information to populate a region some 10,000 miles square with biomes, cities and points of interest with some minor details about each one. It actually would be nice to just use those DM tools on the web, but the AI seems to have problems using them. If it can't use them, then it falls back on trope-heavy hallucination, so we try to avoid that with our own implementation here. It is asserted by proof of vigorous handwaving that this initial level of uniqueness should allow the rest of the adventure to proceed in an "original and creative" way. 

### Over Constraint
The magic of the AI is its flexibility and ability to come up with solutions on its own without being told how to do things. It is very tempting as programmers to start adding code to the AI instructions and try to force it into interpreter mode for a well defined pseudo-language. However, this reverts the D&D experience into house-of-cards mode like any good C++ program and we lose the flexibility of AI. Are there historically examples of classically written C++ codebases that DM arbitrary campaigns well? No. It is best to move with a light touch. There are a couple of hard guard rails we need to put into place to stay out of low-information situations. Otherwise, we do best to suggest, rather than require, and then only when we belive the AI is truly misbehaving.

## FAQ
1. **Why isnt the save game block a separate file?**

We really want to get users in the habit of appending save game information, rather than replacing the save game information. We could probably break out the prefs to this file too,
but it is needed in worldbuilding and user error abounds. It seemed simpler to keep it all in one place with required infrastructure, so if something is missing, they will know it.  If they screw up the file, they can come here to get a new one. Maybe the AI could check for this. Maybe we will change our mind?

