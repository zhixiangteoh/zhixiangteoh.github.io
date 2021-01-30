---
title: eCardnomics
layout: post
show_footer: true
github_link: https://github.com/AY2021S1-CS2113-T14-2/tp/releases
demo_link: https://ay2021s1-cs2113-t14-2.github.io/tp/
tags:
  - Java
  - OOP
  - Team
  - CLI
  - Docs
---

eCardnomics is a **desktop flashcard application to quickly create, manage, and access new flashcards via a Command Line Interface (CLI)**. eCardnomics is targeted at economics students in Junior College in Singapore, and aims to enhance students’ study experience as an efficient and handy aid for active recall.

The main goals of this application are to help students store and segment their economics subject syllabus into different decks, consolidate bite-sized information within each topic by way of flash cards, and offer a fun way for students to study and revise.

## Takeaways

This was a National University of Singapore (NUS) computer science module introducing key ideas and real-world applications of software engineering concepts, taught in Java, culminating in a 5-man team project.

Through this project, I applied concepts of abstraction and encapsulation, Java 8 Streams, and unit and integration testing in a team Command Line Interface (CLI) project. I wrote *3500 out of 6000 lines of code*, including the application's main Game Mode, and wrote *40% of user and developer documentation*. I also managed issues and releases, and [authored 40 PRs](https://github.com/AY2021S1-CS2113-T14-2/tp/pulls?q=is%3Apr+is%3Aclosed+author%3Azhixiangteoh+) in two months.

## Code Contribution

[Reposense dashboard summary](https://nus-cs2113-ay2021s1.github.io/tp-dashboard/#breakdown=true&search=zhixiangteoh)
[Detailed writeup](https://ay2021s1-cs2113-t14-2.github.io/tp/team/zhixiangteoh.html)

v1.0:

- Half of Deck Mode commands, for v1.0
- JUnit tests for DeckParser, NormalParser, Ui classes

v2.0: 

- UG for [Game Mode](https://github.com/AY2021S1-CS2113-T14-2/tp/commit/207103b41966691a768502f899e1b6d4c23a3950), [Help Commands](https://github.com/AY2021S1-CS2113-T14-2/tp/commit/15b51255ecde6085d941e0825180668e5d1a149d)
- DG for [Commands](https://github.com/AY2021S1-CS2113-T14-2/tp/commit/072d00d0e703b9d90d486f0c8ab35bc9f6bf7261) and [Game Mode](https://github.com/AY2021S1-CS2113-T14-2/tp/commit/ba39ebbb46ce52e55001857c0c8a65e57af053a3) sections
- Implementation of [Game Mode](https://github.com/AY2021S1-CS2113-T14-2/tp/commit/f3975adca5c43c6172d4cc7d7d5a6452493368d5)
- JUnit tests for Game Mode

## Game Mode Feature

Implemented [Game Mode feature](https://github.com/AY2021S1-CS2113-T14-2/tp/pull/94). For this, our team originally thought it would be just another command implemented as part of Normal Mode or Deck Mode, but I felt it deserved its own mode. I created a separate package, `game`, and parser class `GameParser` for Game Mode, with the `game` package abstracting away the entire Game Mode implementation from the rest of the application.

So the resulting `game` package is structured as such:

```cli
game
  | Game
  | GameEngine
    | runGameLoop(), update()
  | GameStorage
    | originalDeck, deque, retestStore
```

Here, I also tried as much as possible to incorporate the Single Responsibility Principle, both within the `game` classes and the SRP-ness of the existing classes.

## User Guide Contribution

Snippet:

![eCardnomics UG Game Mode](/images/ecardnomics_ug_game_mode.png)

[Read docs](https://ay2021s1-cs2113-t14-2.github.io/tp/UserGuide.html#features---game-mode)

## Developer Guide Contribution

Snippet:

![eCardnomics DG Game Mode](/images/ecardnomics_dg_game_mode.png)

[Read docs](https://ay2021s1-cs2113-t14-2.github.io/tp/DeveloperGuide.html#game-mode)

<!-- ---
{: data-content="footnotes"} -->
