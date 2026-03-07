# AIDM
Let your AI be your Dungeon Master

## Licenses
MIT License

Wizards of the Coast's Fan Content Policy

## Description
This project provides files which should enable you to play D&D with the AI. The AI will be your dungeon master in a theater of the mind adventure. 

### To Use

#### Setting up a New Game
1. Make a copy of your AISynopsis.md file for the new game, and delete the Saved Game State at the bottom of the file. Save it.
2. Open a new AI session. I use http://gemini.google.com   It is free.
3. Drag the following files onto the AI prompt:
   - AISynopsis.md  (or whatever you changed its name to)
   - fair_dice.md
   - WorldBuilding.md
   - PDF copies of your character sheets
4. Add a cheery message introducing yourself to the AI and Submit it.
5. Follow its instructions.
6. Eventually, it will produce some Game State text, which you will copy and append to the bottom of your AISynopsis.md file.
7. You should end your session now to clear out the AI context window. Continue to [Playing The Game](#playing-the-game)

#### Playing The Game
1. Open a new AI session. I use http://gemini.google.com   It is free.
2. Drag the following files onto the AI prompt:
   - AISynopsis.md  (or whatever you changed its name to)
   - fair_dice.md
   - **Updated** PDF copies of your character sheets. Don't forget to level up if you need to!
3. Add a cheery message to the AI-DM, and hit Submit.

#### Modifying the Game System
All the commands for the AI are in plain English, and it is quite forgiving. It is pretty likely that your changes will work. So, if you feel like you have an idea for changing how the DM does its thing or how world building works, go for it! Just make a backup first. The AI understands the file, so if you have some idea in mind but don't know how to do it, you can just ask the AI and do what it recommends. If you are generous, you can send your ideas in a Issue (bug report) right here on [GitHub](https://github.com/RedditIsAWeenie/AIDM/issues) or via pull request, so everyone else can benefit too! 

### Troubleshooting
Due to its design, AI faces some difficult challenges when dungeon mastering. The details are described in the [Developer Documentation](#developer-documentation) section below, but long story short, due to its inability to deal with missing information constructively, it may appear to lose touch with reality. This can also happen when its short term memory fills up, forcing it to compress its context window in order to continue, losing information and forgetting things. 

Here are some things to try if the AI suffers sudden psychosis:

1. If the game gets really easy and all your attacks hit and all theirs miss, **upload fair_dice.md** to the AI again. It has probably forgotten how to roll dice fairly. This might also help for complex reasons if it seems to get confused about time and/or stops respecting player agency, taking your turn for you. In either case, you should also suspect context window compaction has also occurred.

2. **Context Window compaction** will strike eventually, causing the DM to forget key details, or quietly retcon your progress. This is fixed by updating your saved game progress and restarting the session. However, if the compaction already occurred the memory loss is permanent, so you may have some arguing to do based on the log of play to convince the DM that it forgot. This is work, and since we already know the DM can't remember everything, possibly a somewhat lost cause. Consequently, it is generally a good idea to just **proactively save progress and renew the AI session from time to time**. It is also a good time to update character sheets. I suggest you do this when taking a long rest. This should cause the AI to forget about a pile of now irrelevant historical details that it doesn't need to know anymore -- the important stuff is in its saved state -- and leave room for more adventuring.

___

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

#### Solutions to Low Information Problems
The general solution is to make all information knowable for the AI.

1. **Time:**  We issue specific hard instructions to the AI to classify each player prompt submission into Actions and non-actions. If a non-action is present, it will stop time, handle the non-action and ignore any actions. If the prompt contains only actions, then time is allowed to advance and actions are resolved. This allows the AI to always have actions when it is processing actions.

2. **Dice:** We add a lookup table of perfectly uniformly distributed pre-rolled dice rolls for a variety of die sizes. When the AI discovers it needs to roll a die, it can advance to a new row in the table and get the random number from there. This gives it predictability it needs while still maintaining a level of stochasticity appropriate to human play. The table is 600 entries long. We use AI generated random seeds for both the starting row and the row stride for each successive row which changes over play to provide a stochastic reservoir of 360,000 different outcomes in the worst case (world building), and functionally unlimited in common use. To be clear, because the AI needs predictability to avoid reverting to using trope and plot armor to decide die rolls, the dice simply _can not_ be truly random, at least until the day the Google internal prompt submission machinery includes a random number seed or callable random number generator.  Until then, this will have to be good enough. If this is unacceptable, you can probably ask the AI to let you roll the dice for it, but this is untested, may be tedious and it might be ignoring your work and using trope/plot-armor "dice" instead.

3. **Creativity:**  WorldBuilding.md contains a fairly boring heuristic and table driven random world generator of the sort you may find at some javascript DM utility on the web. Its source of entropy is fair_dice.md. It will roll a bunch of dice, consult some tables, and use the information to populate a region some 10,000 miles square with biomes, cities and points of interest with some minor details about each one. It actually would be nice to just use those DM tools on the web, but the AI seems to have problems using them. If it can't use them, then it falls back on trope-heavy hallucination, so we try to avoid that with our own implementation here. It is asserted by proof of vigorous handwaving that this initial level of uniqueness should allow the rest of the adventure to proceed in an "original and creative" way. 

### Over Constraint
The magic of the AI is its flexibility and ability to come up with solutions on its own without being told how to do things. It is very tempting as programmers to start adding code to the AI instructions and try to force it into interpreter mode for a well defined pseudo-language. However, this reverts the D&D experience into house-of-cards mode like any good C++ program and we lose the flexibility of AI.  It is best to move with a light touch. There are a couple of hard guard rails we need to put into place to stay out of low-information situations. Otherwise, we do best to suggest, rather than require, and then only when we belive the AI is truly misbehaving.

