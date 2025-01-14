---
title: "OOP: Review Session"
length: 90
tags: javascript, object oriented programming, oop
module: 2
---

## Learning Goals

By the end of this session, you will be able to:

- Synthesize your understanding of objects and OOP principles
- Compare and contrast several different implementations of a game based on your knowledge of OOP thus far
- Assess the pros and cons of approaching the same problem in a variety of ways
- Identify code smells

## Structure (5 mins)

* 30 mins of research and journaling
* 15 mins of group discussion
* 30 - 40 mins of code review

#### Research (20 minutes)

Take a few minutes to go through the readings listed below. Take note of your thoughts and questions as you read. Make a habit of condensing and paraphrasing what you read. As you finish each section or page, how well can you explain key terms and ideas without reproducing the author’s words? You will have an opportunity to discuss your findings within your small group. 


* [STUPID to SOLID](https://williamdurand.fr/2013/07/30/from-stupid-to-solid-code/)
* [SOLID Principles](https://scotch.io/bar-talk/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)
  
  _Note: Although the code examples are in PHP, these design principles still apply to JavaScript. As of yet, JavaScript does not support `interfaces` without using outside libraries like `Implement.js`. TLDR: both `implements` and `extends` create subtypes. The former sets up a "contract" to implement the methods declared in the supertype, the latter inherits code from the supertype_

* [Bad OOP Design Discussion](https://stackoverflow.com/questions/345698/what-are-the-tell-tale-signs-of-bad-object-oriented-design)
* [Code Smells](https://blog.codinghorror.com/code-smells/)


#### Journal (10 minutes)

After you have finished reading the four articles, answer the following questions:
- What constitutes code that follows good OOP principles?
- Define "code smell." Name at least 3 specific examples of a code smell from a current or previous project.
- What new information have you learned from one (or all) of these readings?
- What questions do you have after reading through these?

#### Discussion (15 mins)

Using the journal questions to guide you, start a discussion within your small groups.

#### Code Review & Group Discussion

[*Jeopardy*](https://github.com/turingschool-examples/oop-review/tree/master/jeopardy)  
[*Wheel of Fortune*](https://github.com/turingschool-examples/oop-review/tree/master/wheel-of-fortune)

*Get in your GameTime groups. You will be reviewing two codebases that have implemented games in OOP*

<!-- WOF groups should be reviewing Jeopardy and vice versa. Family Feud can review either. Goal is to have groups review an unfamiliar codebase -->

While reviewing the code, ask yourself the following questions:

* Is all the code easily understood?
* Is there any redundant or duplicate code?
* Does the code follow the principle of single responsibility?
* Do the names used in the application convey intent?
* Looking back at the reading covering code smells - find two examples of a code smell in either of the codebases. What changes would you make to this particular piece of code to not make it smell?
* Find two examples of good OOP design, based on the readings

### Closing

Within your original group, discuss the following questions - 
* Of the two repos you are reviewing, which does a better job at following the principles of good OOP design? Why?
* What new information have you learned from the readings? From the code review?