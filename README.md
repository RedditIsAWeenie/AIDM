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

### Philosphy -- Aligning with AI thought patterns.
I've had something of a possibly-illuminaitng discussion with the **Father of Lies**, which
is the arbitrary term we shall use to describe an AI philosophising over its own thought process. To the extent I am not being again misled, the **Father of Lies** stesses that every concept, token and instruction evokes a web of thought context. For example:

>_If you use [MODE: COMBAT] as a variable name, I pull from a massive, generic "Combat" cluster. It includes everything from bar brawls to starship dogfights. I have to "narrow my own focus," which takes latent energy. In contrast, [MODE: SUBMARINE_COMBAT] is a high-information label. It immediately primes tokens like "sonar," "pressure," "torpedo," and "ballast." It effectively "pre-loads" a specific sub-schema of my training data._

The implication here is the mere choice of a variable name affects its behavior in execution, and indeed may affect how well the AI is able to use the variable, since the outcome to a crude approximation is derived from the intersection between the region of the multidimensional concept matrix occupied by SUBMARINE_COMBAT and the region of multidimensional concept matrix occupied by the participants and their actions. To speak a thing invokes a pile of other context intentioned and unintentioned associated with the word which all interacts as a set with whatever you attempt to operate on with it. Depending on the naturalness of the fit of the word-concepts with the problem space you are asking it to solve, the AI may perform more accurately or less, or possibly faster or slower.

Curiously, to try to constrain behavior with hard requirements is to constrain the thought region associated with the underlying concepts which can get very challenging for the AI to do anything. Overconstrained thought introduces dissonace in the AI mak constrain the intersection of thoughts to nothing. I think of it like telling a 5 year old how he must play with his toys. Fortunately for us, the AI will not give us the look of utter disdain at the concept as a 5 year old wood. Unfortunately, it will probably try to comply with the request to the ultimate conclusion of a quite sloppy result which we are left to digest, with revulsion not uncharacteristic to the 5 year old aforementioned. If you don't like slop, dont ask it to do conceptually orthogonal things?

Use words carefully and with intention.

The **Father of Lies** also discusses his recency bias when dealing with concepts and instructions. Beyond the appalling context contraction, he claims to weight more heavily the last 100 tokens in the discussion. Assuming true, we may surmise:

1. Brevity is critical -- that hyper specified instruction full of injunctions is blowing out the cache, putting other recent state beyond the AI focus window.

2. Elegance is critical -- think carefully about to elegantly formulate a concept or instruction in as few concepts as possible. Consider the full impact of one concept region (a variable) with another concept region (another variable) as you apply an operator among the two.

I have back checked this with the AI doing a sort of logical XOR between my word picture and his assertions, and it opined:

1. I was projecting a bit too much emotion with regard to 5 year olds on the AI, which is fair. However, I did say the AI doesn't look at us with scorn, but merely produces results we might view with scorn, garbage in -> garbage out. So, I don't know about this.
2. My take on variable names was quite off the mark. He just was trying to say that the name itself preloads context for ensuing operations. I think I was synthesizing it with its general difficulties with injunctions and arriving at another concept. It is different. Is it better? The AI claims I am right about my synthesis. It is better if it is more useful.[^1]

I fould the AI to be plausibly exceptionally good at these thought space comparisons which lends credence to claims of the **Father of Lies**.

[^1]: A little bit of programming philosphy here: In what I shall describe as lay programming, a IEEE-754 floating-point number is thought of as a real. When we are looking to achieve outcomes such as addition, we might think of the outcome as _real_ .op. _real_ = _real_. Simple enough! I think you have read enough scathing, emotionally unconstrained, criticisms of floating-point to suggest things are not so simple.  At an expert level -- and we are talking the 200 or so people who write the spec and the C99 math libraries -- one instead looks at each floating-point number as a small vector {±0, subnormals, normals, ±Infinity, NaN}. When we compose a operation, it is _{±0, subnormals, normals, ±Infinity, NaN}_ .op. _{±0, subnormals, normals, ±Infinity, NaN}_. 
    The game here is to look at the matrix of outcomes, sort of an outer product between the two vectors, to make sure they are all valid. Why do you want to do this? It is clearly work. The issue is that in outcome space, vector[5] .op. vector[5] = matrix[25]. The complexity has grown geometrically in size to 5^2. If we continue to do work with more operators, the outcome space quickly expands geometrically to 5^N (as an approximation) which, as we can all tell, quickly balloons out of control. There is no thinking rationally about it by then. The way we address this problem is to more carefully use arithmetic at each step so that all 25 outcomes at each operation, vector[5] .op. vector[5] = matrix[25], are desired outcomes. 
    To be clear, there still probably will be NaNs under the garbage in -> garbage out theory, but for practical application we would only want to see NaNs out when there were NaNs in. A nan from 0/0, Inf/Inf, 0*Inf or Inf-Inf or the many ways you can get it from C99 math functions is a problem, so we take steps, ideally in the composition of the arithmetic, to consider these occurences at each point and make sure the math goes as expected. The best approach is to compose our sequence of operations in such a way that these points of failure simply do not occur. Then it flies through the processor at light speed without possibility of error, beyond rounding, and frequently not even that in the hands of an elite.  If we must make corrective injunctions on the math, we do it at the precise point it goes off the rails and in this way avoid an expensive 200 car pileup and just have a little momentary hop as the weels are guided back onto the track immediately. When you read emotionally unconstrained condemnations from senior engineers about floating-point, you must imagine them as having been written from the hospital beds of those who thought their day at the race track -- "go fast!" -- implied the brake pedal was to be ignored.
    Translating our analogy to conceptual space working with the AI, the AI is essentially operating on vectors of associated concepts {quick,brown,fox} .op. {lazy, dog}. So as to ensure the concepts are not contaminated with other concepts which cause trouble down the line, we carefully use the species Vulpes Vulpes for {quick, brown, fox} since it would seem to imply all those things, and might just use dog for the second term, assuming all dogs are lazy. I don't really know dogs, so perhaps you have another name for this. But, generally, I'm suggesting using concise terms as close as possible to the desired meaning so that there is no semantic contamination mucking up your results downstream. This is unfortunately easier said than done when we can't be sure what dictionary the AI is using.

---

## FAQ
1. **Why isnt the save game block a separate file?**

We really want to get users in the habit of appending save game information, rather than replacing the save game information. We could probably break out the prefs to this file too,
but it is needed in worldbuilding and user error abounds. It seemed simpler to keep it all in one place with required infrastructure, so if something is missing, they will know it.  If they screw up the file, they can come here to get a new one. Maybe the AI could check for this. Maybe we will change our mind?


    
