
# Gwent Replay Data Descriptor v1.2

The Gwent Replay Data Descriptor (GRDD) gives a complete structure to record a Gwent game, to be used for step by step replay application.

* The datas are stored using the JSON format.
* Any element followed by an asterisk ``*`` is optional. If not filled, it will be empty or use a default value depending of the element. Any other element has to be filled.
* Any reference to ``blue`` mean *Player 1*, on the bottom on the screen. Any occurrence of ``blue`` in the following text can be changed by ``red`` as a reference to *Player 2*, on the top of the screen. If an element isn't optional for ``blue.[...]``, then it's not optional for ``red.[...]`` as well.
* Any card is referenced by its **Gwent card ID** integer. The card ID can be followed by the card name, separed by a colon ``:`` , in an effort to make the data content more understandable by humans, but this name is optional : only the ID should be used to get the card infos from a replay application.

## Game
The **game** object contains basic informations about the game itself.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``name``\* | Name of the game | *anything*| A Round Of Gwent
``blue``.``name``\* | Name of the blue player | *anything* | Blue
``blue``.``mmr``\* | Player's MMR during this game | *any positive integer* | *none*
``blue``.``faction`` | Player's faction | ``northern-realms``, ``skellige``, ``nilfgaard``, ``monsters``, ``scoiatael`` | 
``blue``.``leader`` | Player's leader | *any Gwent leader card ID* | 
``red``.``visible`` | Are red hand cards visible ? | ``true``, ``false`` | false
``version`` | The game version number during this replay | *A version number without letters with major, minor, and patch numbers* | *The current version number. For example : ``0.9.12``*
``winner``* | Winner player | ``blue``, ``red`` | *none*

> **Note :** The ``winner`` element is optional, as a Gwent replay file can content just a specific part of the game (a few turns, or even just one turn), and not the whole game itself.

### Example
```json
"game": {
  "name": "Let's play Gwent",
  "blue": {
    "name": "Geralt",
    "mmr": 4051,
    "faction": "northern-realms",
    "leader": "200169:Radovid"
  },
  "red": {
    "name": "Ciri",
    "mmr": 4127,
    "faction": "skellige",
    "leader": "200161"
  },
  "version": "0.9.12",
  "winner": "blue"
}
```

## Rounds
The **rounds** object contains an array of rounds.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``round`` | Number of the round | 1
``coinflip`` | Which player plays first | ``blue``, ``red`` | blue
``crowns``.``blue`` | Number of points the player have at the beggining of the round | ``0`` or ``1`` | 0
``board`` | Board description | *See the **Board** section* |
``turns`` | Array of changes on the board, turn after turn | *See the **Turn** section* |

### Example
```json
"rounds": [
  {
    "number": 1,
    "coinflip": "blue",
    "crowns": {
      "blue": 0,
      "red": 0
    },
    "board": {
      "(...)"
    },
    "turns": {
      "(...)"
    }
  },
  {
    "number": 2,
    "coinflip": "red",
    "crowns": {
      "blue": 1,
      "red": 0
    },
    "(...)"
  },
  {
    "number": 3,
    "coinflip": "blue",
    "crowns": {
      "blue": 1,
      "red": 1
    },
    "(...)"
  }
]
```

## Board
A **board** object contains a full description of the board at the beggining of a round.

* If there is resilient units, it should be a part of the board.
* Before-mulligan actions as *losing resilience*, *automatic resurrection*, *deathwishes on a new round* and others has to be placed in the ``beforeMulligan`` element as **step** elements. ``afterMulligan`` acts the same, especially for actions activated when a card is discarded from the mulligan. Any action executed before the first player gets the "Your turn" Gwent message must be put into one of those space.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``blue``.``playableLeader`` | Show if the leader is still playable | ``true`` if the leader can be played, else ``false`` | false
``blue``.``[list]`` | Player's ``[list]`` **Card-list** | ``[list]`` is a **card-list** name. *See the **Card-list** section* |
``rows`` | **Board-row** effects list | *See the **Board-row** section* |
``mulligan``\* | **Mulligan** sequence | *See the **Mulligan** section* |

### Example
```json
"board": {
  "blue": {
    "playableLeader": true,
    "hand": [
      "(...)"
    ],
    "deck": [
      "(...)"
    ],
    "graveyard": [
      "(...)"
    ],
    "melee": [
      "(...)"
    ],
    "ranged": [
      "(...)"
    ],
    "siege": [
      "(...)"
    ]
  }
  "red": {
    "playableLeader": false,
    "redHand": [
      "(...)",
    ],
    "(...)"
  }
}
```


## Card-list
A **card-list** array contains a list of **cards**.

* The cards are ordered from left to right.
* Any empty object value is considered as a hidden card, which is considered to be inaccessible (e.g. the red deck), simply hidden but can be revealed (e.g. the red hand) or temporary hidden (e.g. an *ambush* card). In any case, a replay tool should show the back of the card.
* Hidden or revelead, a card-list must contain as many values as cards in the list.
* The **card-list** can be a string, designating an entire existing card-list (e.g. when a player has to choose a card to discard from his deck, the **card-list** can be ``"blue.deck"``).
* A card from the **card-list** can be a string, each string designating the position of a card (e.g. when a player has to resurrect a gold card from his graveyard, the **card-list** of the gold cards of the graveyard can be ``["card.red.graveyard.2", "card.red.graveyard.6"]``).
* A card from the **card-list** can be a **card** object.

## Board-row
A **board-row** object contains the description of the rows.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``blue``.``[list]``\* | Status of the row | ``frost``, ``fog``, ``rain``, ``drought``, ``ragh-nar-roog``, ``golden-froth``, ``clean`` | clear

### Example
```json
"board": {
  "rows": {
    "blue": {
      "melee": "frost"
    },
    "red": {
      "ranged": "drought",
      "siege": "fog"
    }
  },
  "(...)"
}
```


## Card
A **card** object contains the current statut of the card.

* Except the card ID, all the elements are optionals.
  * The variable elements of the card (token, current strength...) are provided only if the card is concerned.
  * The constant elements of the card (original base strength...) aren't necessary if the replay tool used can get those elements from a card database, according to the game version provided in the ``game`` object. However it's recommended to provide it to keep the replay consistant in the long run.
  * The 3 strengths values are optionals because they are needed for unit cards, but not for others cards.
 
  
Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``id`` | Gwent card ID | *Positive integer ID* |
``originalStrength``\* | Original base strength when playing a copy of the card | *Positive integer* | 
``baseStrength``\* | Current base strength | *Positive integer* | 
``strength``\* | Current strength | *Positive integer* | 
``spyToken``\* | Spy token | ``true``, ``false`` | false
``resilienceToken``\* | Resilience token | ``true``, ``false`` | false
``lockToken``\* | Lock token | ``true``, ``false`` | false
``shieldToken``\* | Shield token | ``true``, ``false`` | false
``armor``\* | Current armor value | *Positive integer* | 0
``countdown``\* | Current countdown value | *Positive integer* | 0
``revealed``\* | The card is visible to the other player | ``true``, ``false`` | false
``type`` | Card type | ``bronze``, ``silver``, ``gold`` | 

> Note : if the `strength` is ommited, the card is considerated as a *special card*. If the `baseStrength` is ommited, a replay tool must take the `strength` value as the `base-value`. If the `originalStrength` value is ommited, a replay tool must take the `base-value` as the `originalStrength` value.

### Example
The next card has been renforced by 2, then boosted by 3.
```json
{
  "id": "132313:Ekimmara",
  "originalStrength": 6,
  "baseStrength": 8,
  "strength": 11,
  "resilienceToken": true,
  "shieldToken": true,
  "type": "bronze"
}
```

## Mulligan
A **mulligan** object contains the change appening before, during and after the mulligan sequence.

The ``mulligan``.``blue`` is an array of cards changes. It happens using the ``blue.deck`` and ``blue``.``hand`` cards-lists specified at the begining of a **round** element.

* The "new card" comes from a **position** (if the card object in the deck is not empty) or is a card object.
* The "new card" takes the position of the "old card". The "old card" is put in the deck.
* If the "new card" **position** in the deck is known, it is recommended that the "old card" takes it. Else, the "old card" new position should be the first empty card object.

The ``before`` and ``after`` are arrays of **steps**. It contains the different **steps** that can happen before (e.g. a unit is automatically resurected at the end of a round) or after a mulligan (e.g. a unit appears on the board if it has been discarded).

Element | Description | Possible values
--- | ---
``before``\* | Array of **step** elements | *See the **Steps** section*
``after``\* | Array of **step** elements | *See the **Steps** section*
``blue`` or ``red`` | Array of mulligan old/new cards changes |
``blue``.``oldCard`` | Card **position** in ``blueHand`` | *See the **position** section*
``blue``.``newCard`` | Card **position** in ``blueDeck`` or **card** object | *See the **position** section or **card** section*

### Example
```json
"board": {
  "mulligan": {
    "before": [
      {
        "step": 1,
        "action": "move",
        "beforeAfter": {
          "type": "position",
          "before": "card.red.graveyard.13",
          "after": "board.red.melee.1"
        }
      },
      {
        "step": 2,
        "(...)"
      }
    ],
    "blue": [
      {
        "oldCard": "card.blue.hand.3",
        "newCard": "card.blue.deck.3"
      }
    ],
    "red": [
      {
        "oldCard": "card.blue.hand",
        "newCard": "card.blue.deck"
      }
    ],
    "after": [
      {
        "step": 1,
        "action": "move",
        "beforeAfter": {
          "type": "position",
          "before": "card.blue.hand.3",
          "after": "board.blue.hand.2"
        }
      }
    ]
  },
  "(...)"
}```


## Turns
A **turn** object contains a list of **steps** played during a player turn.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``turn`` | Turn number | *Integer greater than 0* |
``player`` | Current player | ``blue``, ``red`` | blue
``passed``\* | Passed info | ``true`` if the player has passed, else ``false`` | false
``steps`` | Array of steps | |

> **Note :** Even if a player has passed, some actions can happen during his turn.

## Step
A **step** object contains an independant change during the game.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``step`` | Step number | *Integer greater than 0* |
``source``\* | Source of an action | *See the **Positions** section* |
``action``\* | Type of action | *See **List of actions*** |
``target``\* | Target of an action | *See the **Positions** section* |
``beforeAfter``\* | Variable value before the action | *See the **Befores/afters** section* |
``card``\* | Card object | *See the **Card** section* |
``cardList``\* | Cards-list when necessary | *See the **Card-list** section* |
``cardChoice``\* | ID of the card for an action choice | *Integer* |
``chosen``\* | Array of chosen choices from a card-list | *See the note below* |

> **Note :** When a player have choices (from a card-list, from actions...), ``chosen`` is an array of integers, each one designating the *nth* choice (from left to right) that has been done by the player. Therefore, if a player can choose 2 between 5 spells to play, the 5 cards are showed from the ``cardList``, and (in the same **step**), the ``chosen`` array can be ``[3, 5]``, picking the 3rd and 5th choices of the list.
When the choices are from a card's action (and not "choose to spawn *card A* or *card B*"), the card ID is put to ``cardChoice``, so the replay tool can show the card instead of a card list. The ``chosen`` element must contain the number of the choice (1st, 2nd, etc) the same way in an array (for example, *[2]* for taking the 2nd action).

### Position object :
A **position** string contains the position of a source or target of an action. It can designate a card or an element of the board.

*  The three elements of a position are separated by a dot, ordered as **player** (board side), **card-list** (row, hand or card pile) and **location** (a card or a spot). For example, the 4th card of the blue melee is ``"blue.melee.4"``.
*  When the position is an entire row (``type "board"``) or the original leader position (``type "card"``), the location is optional (or *0*).
*  When the position is a specific card (``type "card"``), the location is the number of the card, from left to right.
*  When the position is between cards (``type "board"``), the location is the spot on the left of the card number. For example, on a row with 6 cards, deploying a card on 1st position is ``"board.blue.melee.1"`` (on the left of 1st card). Between the 3rd and 4th card, it becomes ``"board.blue.melee.4"`` (on the left of 4th card). And after the 6th card, it's ``"board.blue.melee.7"`` (on the left of non-existent 7th card).
*  The *0* (or absence of) location can be used for a ``type`` ``"card"`` to designate the first empty card object of a list. For example, when **red** plays a card from his hidden hand, the position ``"board.red.hand"`` designates the first empty (unknown) card object of red hand.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``type`` | Type of position | ``card``, ``board`` |
``region`` | Area of choice | ``blue.leader``, ``blue.hand``, ``blue.deck``, ``blue.graveyard``, ``blue.melee``, ``blue.ranged``, ``blue.siege``, ``blue.play``, ``blue.banned``, ``generated`` |
``location``\* | Exact position of the target. Not needed if it targets a full row for example | *Integer. See above for the details* | 0

> **Note :** ``"blue.play"`` is the position of a spell being played, on the right side of the board. This helps to show which card is played before its sends to graveyard. For example, if a player action call a spell from the deck to be played immediatly, the spell card can't be added to the hand before it's played. Generally, you never have to target the ``play`` region : it exists for the replay tool, but as a "waiting for action" card position it shouldn't be designated. The ``play`` action makes the moving action of a card to the ``blue.play`` position, then the graveyard, meaningless (see the **actions** section).
> The ``"generated"`` position is when a card doesn't come from the board (e.g. a card spawned by a spell) but you still need to give an origin.

### Examples
```
Target the 4th card of blue ranged : "card.blue.ranged.4"
Pick a hidden card in red hand : "card.red.hand"
Deploy a weather on blue melee : "board.blue.melee"
Deploy a card on the 3rd position of red siege : "board.red.siege.3"
Creating a new card from a spell : "generated"
```


### List of actions

#### Actions on cards
Gwent action | GRDD action code | Note
--- | --- | ---
Transform a card into another | ``transform`` | The changed card is in **step.target**. The new card is in **step.card**.
Move a card | ``move`` | Move a card from anywhere to anywhere. It's used to deploy, pick a card from the deck, move to graveyard or banned pile... You can generate a new card by setting the ``before`` position as ``generated``.
Play a card | ``play`` | Shortcut for spells to move a card to the ``"blue.play"`` this turn, and be considered and moved to the graveyard at the end of the step. **Important :** when using ``play``, ``source`` must be the played spell, because ``target`` designates the potential target of the spell. It's not a problem as the replay tool must move the card to the play and graveyard region by itself.
Play and ban | ``playban`` | Similar as ``play``, but the spell card is moved to the ``banned`` card-list after been played instead of the graveyard.
Change a token / an element | ``change`` | The changed element, its previous and its new status are in **step.beforeAfter**
Destroy a card | ``destroy`` | Shortcut to move a unit card to its side's graveyard last place. The previous and new state of the card are on the **step.beforeAfter**.
Display a card | ``display`` | Display a hidden card (ambush, red hand, etc). **step.card** must contain the informations of the showed card.
Play the player's leader | ``leader`` | This action, which has no ``source`` nor ``target`` nor ``beforeAfter``, must be followed by a ``move`` action with ``generated`` source for the deployed card : the leader card is different (at least as different ``ingameid`` according to Gwent standard), to the card deployed on the board.

> **Note :** When a card is moved from a pile of unknow position, the position *0* is used. The pile must then remove one empty element.
When the **red player** plays a hidden card, the card information must be filled. The *0* position can be used as a shortcut to the first possible hidden card of the hand.


#### Actions on rows
Gwent action | GRDD action code | Note
--- | --- | ---
Change the status | ``changeRow`` | The name of the boon or the hazard is in **step.before/after**. Cleaning a row is applying the ``clear`` status.

#### Others actions
Gwent action | GRDD action code | Note
--- | --- | ---
Choice in a list of cards | ``choice`` | The ``cardList`` element must be provided. This action shows a list of cards to choose for a player, so the replay viewer can see what the player could choose. The cards of the list are not moved from/to any position and exists only for display.
Discover a card | `` discover`` | *See the Note below*
Targeting something | ``targeting`` | When anything needs to show it targets something else (using ``source`` and ``target``), but there is no specific action except the targetting display (e.g. one deployed card can make another card repeat its deploy action : the first must ``targetting`` the second, before the second does its deploy action again)

> **Note :** Sometimes, one can't know some informations in advance. For example, making a replay by watching a video, if the red player discards some cards during some turns, but blue doesn't check the graveyard soon enough, one can't know when this or that card has been discarded. In this kind of situation, the cards are moved hidden to graveyard when discarded, and when the information is known, the ``discover`` action ``target`` each card and use the ``card`` information of the step to "reveal" it to the replay tool, filling what was previously an empty card object. As this doesn't count as a real "ingame action", the ``step`` number of a ``discover`` action is always 0 : it's not counted by the replay tool, and you can't go to this specific moment. Still, ``discover`` should be used as little as possible, one should fill card informations everytime he can get it during the game. In this "discard" example, the cards can be specified during the ``move`` action if the order of the discarded cards isn't important.


#### Player actions
Gwent action | GRDD action code | Note
--- | --- | ---
Pass | ``pass`` |
Surrender | ``surrender`` |
Out of time | ``rope`` | When the player is out of time, this action must be followed by a step to discard the random card from his hand, according to Gwent rules

## Before/After
A **before/after** is an object containing the specific change of an action. This element is particulary important has it's a way to go back to the board statut before an action takes place.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``type`` | Type of change | ``position``, ``[card-element]``, ``baseStrengthStrength``, ``row``, ``card`` |
``before`` | Value before change | *See list below* |
``after`` | Value after change | *See list below* |

* Any ``[card-element]`` (except the ``id``) can be changed using the same element name (``spyToken``, ``baseStrength``...) as type. See the **Card** section to check all the possibilities and the possible values.
* The ``position`` is a position object. See the **Position** section for more informations.
* The ``row`` before/after values must be one of these : ``frost``, ``fog``, ``rain``, ``drought``, ``ragh-nar-roog``, ``skellige-storm``, ``golden-froth``, ``clean``
* The ``card`` type is necessary when you need to keep the full state of a card before ``transform`` or ``destroy``. Note that the ``id`` is necessary for ``transform`` (the card identity should be different), but not for ``destroy``.
* ``baseStrengthStrength`` is a shortcut when you need to change the strength and the baseStrength of a unit from the **same** ``before`` value to the **same** ``after`` value. It helps for example when you need to *reset-and-weaken* an unit.


## Massive assignations
**Step** elements has to be atomic : when you do some damages to a unit which has some armor, the lost of the armor is one step, the lost of strength is another one. But sometimes, the change of an action happens to multiple cards. For example, when you play an effect that boost by 2 the strength of 10 cards on your board at the same time, it would be long for the replay viewer to pass 10 steps with "boost a card by 2", and it would increase the size of the replay file.

When you need to make multiple changes at one, as long as the action is a ``change``, a ``move`` or a ``destroy`` type, you can have an array of ``target`` to have multiple targets. Then the ``before`` and the ``after`` elements need to be array as well, and each elements of the array must be on the same index as the others one (e.g. ``target[3]`` on a move action has its previous position in ``before[3]`` and the destination in ``after[3]``).

Don't forget that it concerns only one action at the time. If you needs to increase strength and armor of multiple units, first increase the strength, then armor. Also, be careful about what should be massive assignations : for example, sometimes Gwent allows you to push multiple units from one row to another, but sometimes it changes multiple units positions one after the other.

Note that, for the ``destroy`` action, the targets are going to their graveyards following the ``target`` order (in a blue graveyard with 8 cards, first blue target of ``destroy`` takes the 9th place, next is 10th, etc). If one or more targets has to be banned, it should be done once its in the graveyard as the next step.


### Examples
*  **(Step 1)** The card **Alzur's Double Cross**, in the *blue player hand, 3rd card*, is being *played*.
*  **(Step 2)** It picks the random strongest unit from the deck. The card (an **Ekimmara** here (base strength 6), which was *boosted* by 2 (strength 8) and has a *shield*, thanks to an earlier **Quen Sign**) is boosted by 2 when spawned (strength 10), and deployed (*moved*) from an unkown position in the deck (``card.blueDeck``) to the blue ranged row between the 1st and 2nd card (the *board.blue.ranged.2* position)
*  **(Step 3)** It earns *resilience*,
*  **(Step 4)** and consumes (*destroy*) the card (a **Nekker** which has a strength of 7) on the *blue melee row on 3rd position*.
* **(Step 5)** The Ekimmara is *boosted* again by 7.
* **(Step 6)** As the Ekimmara consumed a card, all invisible nekkers in the blue deck are *boosted*. As we don't know the position of each card, we will call twice the *card.blue.deck* position, as there is two nekkers in the deck. And because we don't know the current strength of the nekker (but may be known, for example with GwentUp), we don't use the ``beforeAfter`` here.
* **(Step 7)** Because the Nekker died and can call another one with his *deathwish*, another Nekker is *moved* from the deck (now with a strength of 8) on the 5th of the melee row.
```json
"turns": {
  "turn": 4,
  "player": "blue",
  "steps": [
    {
      "step": 1,
      "source": "card.blue.hand.3",
      "action": "play"
    },
    {
      "step": 2,
      "action": "move",
      "source": "card.blue.deck",
      "target": "board.blue.ranged.2",
      "card": {
          "id": "132313:Ekimmara",
          "originalStrength": 6,
          "baseStrength": 6,
          "strength": 10,
          "shieldToken": true,
          "type": "bronze"
      }
    },
    {
      "step": 3,
      "action": "change",
      "target": "card.blue.ranged.2",
      "beforeAfter": {
        "type": "resilienceToken",
        "before": false,
        "after": true
      }
    },
    {
      "step": 4,
      "source": "card.blue.ranged.2",
      "action": "destroy",
      "target": "card.blue.melee.3",
      "beforeAfter": {
        "type": "card",
        "before": {
          "id": "132305:Nekker",
          "baseStrength": 3,
          "strength": 7,
          "type": "bronze"
        },
        "after": {
          "id": "132305:Nekker",
          "strength": 3,
          "type": "bronze"
        }
      }
    },
    {
      "step": 5,
      "action": "change",
      "target": "card.blue.ranged.2",
      "beforeAfter": {
        "type": "strength",
        "before": 10,
        "after": 17
      }
    },
    {
      "step": 6,
      "action": "change",
      "target": ["card.blue.deck", "card.blue.deck"]
    },
    {
      "step": 7,
      "action": "move",
      "beforeAfter": {
        "type": "position",
        "before": "card.blue.deck",
        "after": "board.blue.melee.5"
      }
      "card": {
          "id": "132305:Nekker",
          "originalStrength": 3,
          "baseStrength": 3,
          "strength": 8,
          "type": "bronze"
      }
    }
  ]
}
```



