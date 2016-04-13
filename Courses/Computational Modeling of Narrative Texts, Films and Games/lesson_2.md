
# Lesson #2

## Can a computer understand stories?

### State of the art 

* Mani: `NarrativeML`
* Eric Muller: `Discret Event Calculus and Common Sense` (lesson 3). A introduit une notion d'inertie narrative => préciser à l'avance ce qu'un changement/événement est suceptible de changer dans le monde
* Graesser: `QUEST` (lesson 4) => introduciton d'un formalisme destiné à tester la compréhension d'une histoire (qu'est-ce qu'« avoir compris une histoire » ?)
* Mark Finlayson (MIT): `Sheherazade` => système d'annotation destiné à l'explication d'une histoire (spécifiquement : les contes russes)
* David Elson (Columbia University): `Sheherazade` (spécifiquement : les contes d'Esope)

### Current state

* A written story is made of propositions (`PropBank`) -> résolution de l'analyse syntaxique & sémantique du texte
* Can be annotated with `NarrativeML`
* Can be translated to Discourse Representation Theory


### `FrameNet` vs. `PropBank`

#### `FrameNet`
**[buyer]** Chuck bought **[goods]** a car **[seller]** from Jerry **[payment]** for $1000.

#### `PropBank`
**[arg0]** Chuck bought **[arg1]** a car **[arg2]** from Jerry **[arg3]** for $1000.

## Characters in `NarrativeML`

* Narrator: name, form, exist, accessibleto, order, distance, perspective
* Place: name, form, exist, accessibleto
* Character: name, attributes, form, exist, accessibleto
* Event: type, exist, goal, duration
* Goal: id, parent, leaf, type
* Evaluation: event, character, audience, polarity (`POSITIVE`, `NEGATIVE`, `NEUTRAL`)

### Characters in The Fox and the Crow

```
A crow has found a piece of cheese 
and retired to a branch to eat it. 

A fox, wanting it for himself, flatters 
the crow, calling it beautiful and 
wondering whether its voice is as 
sweet to match.

When it lets out a caw, the 
cheese falls and is devoured by the fox.
```

* The crow: 
    * find cheese (action)
    * retire (action)
    * eat (goal)
    * caw (action)
    * cheese falls (csq)
    * failure of the goal
* The fox: 
    * see crow (action)
    * want the cheese (goal)
    * flatter (action)
        * call beautiful
        * ask about its voice (plan)
    * devour (action)
    * success of the goal
* Cheese
* Branch

#### Goals and intentions

* Eat cheese 
* Get cheese 
* Release cheese
* ...

### Mindreading

* Understand character's goals and actions in terms of their beliefs, desires and intentions
* Using different cues
    * Words in written stories
    * Images in comics
    * Motion and sound in films
    * Interaction in games

### Belief-Desire-Intention Model
* Characters as intentional agents
* Stories as plans executed by multiple agents
* Understood in terms of their beliefs, desires and intentions

*Bratman, M. 1987 - Intention, Plans and Practical Reason. Cambridge, MA: Harvard University Press*

#### Beliefs

* Beliefs represent the informational state of the agent, in other words its beliefs about the world (including itself and other agents).
* Beliefs can also include inference rules, allowing forward chaining to lead to new beliefs.
* Using the term *belief* rather than knowledge recognizes that what an agent believes may not necessarily be true (and in fact may change in the future).

#### Desires

* Desires represent the motivational state of the agent.
¨They represent objectives or situations that the agent would like to accomplish or bring about.
* Examples of desires might be: find the best price, go to the party or become rich.

#### Goals

* A goal is a desire that has been adopted for active pursuit by the agent.
* Usage of the term *goal* adds the further restriction that the set of active desires must be consistent.
* For example, one should not have concurrent goals to go to a party and to stay at home - even though they could both be desirable.

#### Intentions

* Intentions represent the deliberative state of the agent - what the agent has chosen to do.
* Intentions are desires to which the agent has to some extend committed.
* In implemented systems, this means the agent has begun executing a plan.

#### Plans

* Plans are sequences of actions (recipes or knowledge areas) that an agent can perform to achieve
one or more of its intentions.
* Plans may include other plans: my plan to go for a drive may include a plan to find my car keys.
* This reflects that in Bratman's model, plans are initially only partially conceived, with details being filled in as they progress.

#### BDI loo for autonomous agents

* `option-generator(event-queue)`
* `selected-options: deliberate(option)`
* `update-intentions(selected-options)`
* `execute()`
* `get-new-external-events()`
* `drop-unsuccessful-attitudes()`
* `drop-impossible-attitudes()`

Les personnages de jeux vidéos ont ce genre de boucles pour générer leurs actions.

### Theory of mind

* What I believe, desire and intend
* What I believe others believe, desire and intend
* What the audience believes I believe, desire and intend
* What the audience believes I believe about others, etc.

*Friends season 5 episod 14 - Where everybody finds out*

Des chercheurs disent que les récits nous servent de simulation pour développer notre capacité à comprendre les intentions d'autres gens.

### Question-answering in the context of stories

*Graesser & Franklin: `QUEST` Model, 1990*

### Characters in Little Red Riding Hood
* Red Riding Hood
* The wolf

### Characters and story generation

* Minstrel
* Versu 
* Tale Spin 
* Facade 
* Universe 
* Nothing for dinner

*Price and Stern: Madlibs (1958) - generation par templates*

*John Meehan: Tale Spin (1976) - The metanovel: Writing stories by Computer*


#### Universe storytelling algorithm

1. Pick a goal with no missing pre-condition.
1. Pick a plot fragment for that goal, achieveing extra goals if possible.
1. "Execute" the plan, including adding new goals to the goal graph and "telling" (producing output), if appropriate.

Author's goals:

Maintain romantic-tension:

* In-love (A, B)
* Available (A)
* Not available (B)

Where

* not available (A) if and only if married (A, X) or engaged (A, X) for some X


#### Scott Turner: Minstrel (1992)

* A model of the author as a problem solver
* Author's goals: theme, consistency, drama and presentation
* Solved with case-based reasoning (CBR)

Story structure:

* Introduction scene: introductory scenes which appear at the beginning of the story
* Body: the main body of the story, ie scenes that illustrate the theme of the story
* Denouement-scene: ...

