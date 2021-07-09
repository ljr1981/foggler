# Foggler
A BDD ([Behavior Driven Development](https://en.wikipedia.org/wiki/Behavior-driven_development)) Spec to [Eiffel](https://www.eiffel.org) Class and Test Class Generator

## Introduction
Foggler has a single purpose: input BDD specification text files and then output raw Eiffel class code `{*_IMP}` in `*.e` files.

Foggler was inspired by [Cucumber and Gherkin](https://cucumber.io/tools/cucumberstudio/?&utm_medium=ppcg&utm_source=aw&utm_term=cucumber%20gherkin&utm_content=481541387919&utm_campaign=%7bcampaign%7d&awsearchcpc=1&gclid=Cj0KCQjw8vqGBhC_ARIsADMSd1C8NC_IDG3G7b9DaN1h1eIJOXL8lt5Lt-gUc5JUu2q7CseQBNBHjkUaAsDGEALw_wcB&gclsrc=aw.ds), but is being adapted for generating Eiffel code for consumption in [EiffelStudio™](https://www.eiffel.com/eiffelstudio/).

From the command-line, call `foggler.exe` with the name of your BDD spec `*.txt` file (e.g. `foggler my_bdd_spec.txt`). Within seconds, you will have a matching generated Eiffel class file that you can include in your EiffelStudio™ project!

## Future Plan

    Release notes are found at the end of this readme.

Your feedback is welcome! Plus—use the facilities of GitHub to make your suggestions for new features or to report bugs and defects. I will respond with changes as fast as I can (I have a life just like you, right?) 🦖

From where I sit (for my purposes):

1. **Arguments** — ensure all defined routine arguments are parsed and well-formed in the generated code.
2. **Generate TDD** ([Test Driven Development](https://en.wikipedia.org/wiki/Test-driven_development)) (EiffelStudio™ AutoTest™) unit and integration test code that will compile, but initially fails (in keeping with the TDD paradigm of write-failing-test-first).
3. **Include Data** — standardize "test data" representation grammar in the source BDD specification and teach Foggler how to generate and consume it in tests (if possible).
4. **GUI** — write a GUI app for both Windows and Linux
5. **Linux** — create a Linux CLI app with installer

## Fun Facts
1. **3 Days Zero-to-Hero** — The initial version (0.0.0.1) of Foggler was created from an idea and scratch in just 3 days (really about 3 1/2, all told).
2. **Function Points** — Foggler presently consists of a 2 Function Points, which are the input file (with specification and parser/lexer) and the output (Eiffel class).
3. **Win Binary** — small size matters! The Windows binary is but 1.9MB in size. As such, it is very fast. Also, the dead-code stripping is set to all (not just features), which makes the binary even smaller still!
4. **CI/CD Pipeline** — because this is a CLI app, you ought to be able to include it in a CI/CD pipeline. This might mean that Foggler needs some logging for metrics purposes. 

## Acknowledgements
None of this would be possible without the facilities of the Gobosoft Gobo libraries! Specifically, I used the [Gelex parser/lexer](http://www.gobosoft.com/eiffel/gobo/gelex/index.html) to specify and generate the scanner code, which parses the BDD specification text files.

## A Little BDD
**BDD** is really just **TDD** (Test Driven Development) "explained well", according to **Mr. Dave Farley**, who ought to know because he was there when it was invented. See *YouTube* and search for [Dave Farley of Continuous Development, LLC](https://www.youtube.com/c/ContinuousDelivery) for more information.

In Eiffel terms, BDD is really just TDD (AutoTest™ tool and code) coupled with Eiffel Design-by-Contract™ (or DbC for short). TDD people will understand DbC as `checking`, except DbC brings the `checking code` internal to the production code as assertions directly applied to classes, routines, and loops.

Traditionally, BDD generally consists of Scenarios. Each Scenario consists of steps, broken down as Given/When/Then. Each step can consist of one or more steps. For example, one can write `Given this And not that`, where `this` and `that` are Boolean assertions. In this case, `this` evaluates to `True` and `that` evaluates to `False`. In this case, the `this` assertion is a Given step, and the `that` is an "and'd" substep.

So—we have BDD Specifications, consisting of one or more Scenarios, which (in turn) can consist of Given/When/Then steps, and each of those will have one step and perhaps more substeps.

The Given and Then steps (and substeps) are Boolean assertions, whereas the When step (part) corresponds to the `do ... end` code in Eiffel. Therefore, the When step(s) are Command features (Method routines). The Given and Then steps are Query features (Function routines).

## First Example
Consider the following example BDD specification.

Illustrating Specification using Examples:
```
Scenarios are specified using the Given-When-Then structure to create a testable specification −

Given <some precondition>
And <additional preconditions> Optional
When <an action/trigger occurs>
Then <some post condition>
And <additional post conditions> Optional
```

### Quick Markdown Guide
```
  ' ... ' = Descriptive text (i.e. comments, and so on)
  < ... > = Class name (i.e. goes with Title:)
  # ... # = Comment
  [ ... ] = Command (Method) name
  ( ... ) = Arguments (of Command Method)
  ? ... ? = Query (Function) name (e.g. ?query_name:[True|False]?)
```
NOTE: Comments are completely ignored (e.g. they are not a part of the generated code).

### Example Specification
Our Customer wants to create a "Guess the Word" game. Our Business Analyst (BA) is responsible for writing one or more BDD specifications that capture the needs and wants of the Customer.

In this case, the game consists of two players—the Maker and the Breaker. The Maker creates a random word and the Breaker has to guess it. The goal is to guess the word in as few tries as possible.

NOTE: Do not read too much into the BDD specification given. It is not here to be "correct" or "complete", but is here to demonstrate Queely's ability to consume the BA's BDD specification and output an Eiffel class based on it. As such, the BDD specification (below) is rather incorrect and incomplete, so (for the moment) we will just ignore these glaring and obvious facts!

```
Title: 'Guess the word game.'
        <WORD_GUESS_GAME>

Feature: 'Word guess game.'

#The first example has three steps.#
Scenario: 'Maker starts a game.'
           [maker_start_game] (target_word:"silky")
  Given: 'no game is started.' 
          ?is_game_started:False?
     And: 'has maker' ?has_maker:True?
     But: 'not started by auto-magic' 
           ?is_auto_magic_started:False?
  When: 'the Maker starts a game.'
     And: 'old breaker is reset' 
           ?is_break_reset:True?
  Then: 'the Maker waits for a Breaker to join.' 
         ?is_awaiting_join:True?

#The second example has three steps.#
Scenario: 'Breaker joins a Makers game.' 
           [breaker_join_game]
  Given: 'the Maker has started a game with the word "silky".' 
          ?is_started_with_word:True?
  When: 'the Breaker joins the Makers game.'
  Then: 'the Breaker must guess a word with 5 characters.' 
         ?is_valid_guess:True?
    And: 'the Breaker must guess the exact word!'
    		?is_same_word:True?
```

## By the Numbers
Let's break that down, showing each BDD specification part with the corresponding generated code.

### Title Part & Class Header
The `Title:` part generates the Eiffel class header.

```
Title: 'Guess the word game.'
        <WORD_GUESS_GAME>
```
Results in ...
```
--|BDD Specification
note
	warning: "DO NOT EDIT--THIS IS GENERATED CODE!!!"
	description: "Guess the word game."

deferred class
	WORD_GUESS_GAME_IMP
```
The `Guess the word game.` string is used for the class `note` description. The note clause also includes a `warning` against editing the class as subsequent modifications to the source BDD specification and subsequent regeneration will possibly overwrite the class text, thereby destroying your edits. It is better to inherit from the `IMP` class and then modify the descendent (sub) class.

### Scenario Part & Generated Code
Each BDD Scenario part will generate some corresponding code. For example:

```
Scenario: 'Maker starts a game.'
           [maker_start_game] (target_word:"silky")	   
```
This BDD (above) generates an Eiffel Command (Method) feature:
```
	maker_start_game (a_target_word: STRING) 
			-- Maker starts a game.
```
The `[maker_start_game]` is used to create the feature (routine) and the `(target_word:"silky")` becomes the argument(s) passed to it.

Note that `"silky"` has been translated by Queely into `STRING`. Queely understands the notion of "basic types" (like STRING), using the enclosing double-quotes as a signal to compute the corresponding argument type. Queely also knows about CHARACTER, INTEGER, REAL, and BOOLEAN. Anything else is translated as type {ANY}.

### Given Part(s) & Generated Code
Each BDD Given part consists of one step and one or more possible substeps. The substeps are either And or But. The intent of the But substep is negation, but use it only if it makes sense to you as you can write `And: 'not this' ?is_this:False?`, which wil result in `and not is_this`. The "But version" would look like: `But 'not this' ?is_this:False?`. Therefore, you get to choose which reads more accurately to you.

```
  Given: 'no game is started.' 
          ?is_game_started:False?
     And: 'has maker' ?has_maker:True?
     But: 'not started by auto-magic' 
           ?is_auto_magic_started:False?
```
The resulting code looks like:
```
		require
			not is_game_started   -- no game is started.
			and then has_maker   -- has maker
			and then not is_auto_magic_started   -- not started by auto-magic
```
Notice that the Descriptive parts (e.g. `'no game is started.'`) is generated as a comment after the DbC contract assertion (e.g. `require`). Each Query (Function) routine specification (e.g. `?is_game_started:False?`) is generated as a call to a Boolean Query (Function) feature in Eiffel (e.g. `not is_game_started `).

This brings us to where we must also mention that Queely also generates the Query (Function) deferred features. In the example above, there are three:
```
	is_game_started: BOOLEAN
			-- no game is started.
		deferred
		end
```
```
	has_maker: BOOLEAN
			-- has maker
		deferred
		end
```
```
	is_auto_magic_started: BOOLEAN
			-- not started by auto-magic
		deferred
		end
```
Therefore, when Queely sees the Boolean assertions of the Given and Then parts, it not only generates the corresponding assertion contracts in the Scenario Command (Method) feature, but also the corresponding Query (Function) features. Both the Command (Method) and Query (Function) features are generated as deferred. This means that you must provide implementation code in a descendent class to properly effect the code. In terms of BDD, we say that the `{*_IMP}` class is the `What?`, whereas the descendent (sub) class you write is the `How?`.

## The ENTIRE Generated Class
Here is the entire class generated by Queely from the BDD specification show above.
```
--|BDD Specification
note
	warning: "DO NOT EDIT--THIS IS GENERATED CODE!!!"
	description: "Guess the word game."

deferred class
	WORD_GUESS_GAME_IMP
	
feature -- Access



feature -- Settings



feature -- Status Reports

	has_maker: BOOLEAN
			-- has maker
		deferred
		end

	is_auto_magic_started: BOOLEAN
			-- not started by auto-magic
		deferred
		end

	is_game_started: BOOLEAN
			-- no game is started.
		deferred
		end

	is_break_reset: BOOLEAN
			-- old breaker is reset
		deferred
		end

	is_awaiting_join: BOOLEAN
			-- the Maker waits for a Breaker to join.
		deferred
		end

	is_started_with_word: BOOLEAN
			-- the Maker has started a game with the word "silky".
		deferred
		end

	is_same_word: BOOLEAN
			-- the Breaker must guess the exact word!
		deferred
		end

	is_valid_guess: BOOLEAN
			-- the Breaker must guess a word with 5 characters.
		deferred
		end



feature -- Basic Operations

	maker_start_game (a_target_word: STRING) 
			-- Maker starts a game.
		require
			not is_game_started   -- no game is started.
			and then has_maker   -- has maker
			and then not is_auto_magic_started   -- not started by auto-magic
		deferred
			-- the Maker starts a game.
		ensure
			is_awaiting_join   -- the Maker waits for a Breaker to join.
		end

	breaker_join_game  
			-- Breaker joins a Makers game.
		require
			is_started_with_word   -- the Maker has started a game with the word "silky".
		deferred
			-- the Breaker joins the Makers game.
		ensure
			is_valid_guess   -- the Breaker must guess a word with 5 characters.
			and then is_same_word   -- the Breaker must guess the exact word!
		end



feature -- Constants



note

end
```
# External Links & References
There are a number of sources and resources that have inspired this work and continue to shape it.

1. [AB Testing Podcast](https://open.spotify.com/show/1CrWfV0KNH9HevtsFut1iI)
2. [Theory of Constraints](https://en.wikipedia.org/wiki/Theory_of_constraints)
3. [How to Measure Anything](https://www.google.com/books/edition/How_to_Measure_Anything/F0i6pwAACAAJ?hl=en)

# Release Notes
## version 0.0.0.7 — Added support of commas and ampersand chars in feature multiline notes
- The specification now includes support for comma and ampersand character in the Feature part multiline sentence notes.
## version 0.0.0.6 — Fixed a rather icky/sticky/tricky CAT-call bug
- CAT call bugs are very difficult to find. Thankfully, Eiffel gave me the raw clues I needed to find and fix. The symptom was that foggler was not producing the right output code from the BDD spec. It was stopping short. This is because it was failing on a CAT-call and then dumping out to an exception trace log file. Those two items pointed the way to finding and fixing the bug, which was pretty simple once I saw it—that is—the code has type-anchor features that were not properly set for the new BDD_FEATURE_PART class and its parent BDD_TITLE_PART.
## version 0.0.0.5 — Added Feature part to specification grammar
- It is now a requirement to have a `Feature:` keyword followed by a `'sentence ...'` to wrap and group `Scenario:` parts.
## version 0.0.0.4 — Bug Fix - Added missing _imp on generated file name
- When giving Foggler just an input file name and letting it compute the output file name from the specifiction, the generated file was missing the "_imp" on the end. This was causing a warning in EiffelStudio at compile-time. The file name now matches the class name.
## version 0.0.0.3 — Added Logging
- Added logging facility code. Nothing is presently being logged, but the capacity is there. Now, it is time to decide what would be helpful to know from a log file (`system.log`) for Foggler.
## version 0.0.0.2 — Bug Fix
- Fixed an indenting issue with the `end` keyword at the end of command (method) routines.
## version 0.0.0.1 — Initial Release
