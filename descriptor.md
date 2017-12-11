# Gwent Replay Data Descriptor

The Gwent Replay Data Descriptor (GRDD) gives a complete structure to record a Gwent game, to be used for step by step replay application.

* The datas are stored using the JSON format.
* Any element followed by an asterisk ``*`` is optional. If not filled, it will be empty or use a default value depending of the element. Any other element has to be filled.
* Any reference to ``blue`` mean *Player 1*, on the bottom on the screen. Any occurrence of ``blue`` in the following text can be changed by ``red`` as a reference to *Player 2*, on the top of the screen. If an element isn't optional for ``blue-[...]``, then it's not optional for ``red-[...]`` as well.
* Any card is referenced by its **Gwent card ID** integer. The card ID can be followed by the card name, separed by a colon ``:`` , in an effort to make the data content more understandable by humans, but this name is optional : only the ID should be used to get the card infos from a replay application.

## Game
The **game** object contains basic informations about the game itself.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``name``\* | Name of the game | *anything*| A Round Of Gwent
``blue-name``\* | Name of the blue player | *anything* | Blue
``blue-mmr``\* | Player's MMR during this game | *any positive integer* | *none*
``blue-faction`` | Player's faction | ``northern-realms``, ``skellige``, ``nilfgaard``, ``monsters``, ``scoia-tael`` | 
``blue-leader`` | Player's leader | *any Gwent leader card ID* | 
``version`` | The game version number during this replay | *A version number without letters with major, minor, and patch numbers* | *The current version number. For example : ``0.9.12``*
``winner``* | Winner player | ``blue``, ``red`` | *none*

> **Note :** The ``winner`` element is optional, as a Gwent replay file can content just a specific part of the game (a few turns, or even just one turn), and not the whole game itself.

### Example
```json
"game": {
  "name": "Let's play Gwent",
  "blue-name": "Geralt",
  "blue-mmr": 4051,
  "blue-faction": "northern-realms",
  "blue-leader": "200169:Radovid",
  "red-name": "Ciri",
  "red-mmr": 4127,
  "red-faction": "skellige",
  "red-leader": "200161",
  "version": "0.9.12",
  "winner": "blue"
}
```

## Rounds
The **rounds** object contains an array of rounds.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``number`` | Number of the round | 1
``coinflip`` | Which player plays first | ``blue``, ``red`` | blue
``blue-point`` | Number of points the player have at the beggining of the round | ``0`` or ``1`` | 0
``board`` | Board description | *See the **Board** section* |
``turns`` | Array of changes on the board, turn after turn | *See the **Turn** section* |

### Example
```json
"rounds": [
  {
    "number": 1,
    "coinflip": "blue",
    "blue-point": 0,
    "red-point": 0,
    "board": {
      "(...)"
    },
    "turns": [
      "(...)"
    ]
  },
  {
    "number": 2,
    "coinflip": "red",
    "blue-point": 1,
    "red-point": 0,
    "(...)"
  },
  {
    "number": 3,
    "coinflip": "blue",
    "blue-point": 1,
    "red-point": 1,
    "(...)"
  }
]
```

## Board
A **board** object contains a full description of the board at the beggining of a round.

* If there is resilient units, it should be a part of the board.
* Before-mulligan actions as *losing resilience*, *automatic resurrection*, *deathwishes on a new round* and others has to be placed in the ``before-mulligan`` element as **step** elements. ``after-mulligan`` acts the same, especially for actions activated when a card is discarded from the mulligan. Any action executed before the first player gets the "Your turn" Gwent message must be put into one of those space.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``blue-playable-leader`` | Show if the leader is still playable | ``true`` if the leader can be played, else ``false`` | false
``blue-hand`` | Player's hand **Card-list** | *See the **Card-list** section* |
``blue-deck`` | Player's deck **Card-list** | *See the **Card-list** section* |
``blue-graveyard`` | Player's graveyard **Card-list** | *See the **Card-list** section* |
``blue-melee`` | Player's melee **Board-row** | *See the **Board-row** section* |
``blue-ranged`` | Player's ranged **Board-row** | *See the **Board-row** section* |
``blue-siege`` | Player's siege **Board-row** | *See the **Board-row** section* |

### Example
```json
"board": {
  "blue-used-leader": true,
  "blue-hand": [
    "(...)"
  ],
  "blue-deck": [
    "(...)"
  ],
  "blue-graveyard": [
    "(...)"
  ],
  "blue-melee": [
    "(...)"
  ],
  "blue-ranged": [
    "(...)"
  ],
  "blue-siege": [
    "(...)"
  ],
  "red-used-leader": false,
  "red-hand": [
    "(...)",
  ],
  "(...)"
}
```


## Card-list
A **card-list** array contains a list of **cards objects**.

* The cards are ordered from left to right.
* Any empty value is considered as a hidden card, which is considered to be inaccessible (e.g. the red deck), simply hidden but can be revealed (e.g. the red hand) or temporary hidden (e.g. an *ambush* card).
* Hidden or revelead, a card-list must contain as many values as cards in the list. 

## Board-row
A **board-row** object contains the description of a row.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``status``\* | Status of the row | ``frost``, ``fog``, ``rain``, ``drought``, ``ragh-nar-roog``, ``golden-froth`` | *none*
``card-list`` | Card-list of the row | | *none*

## Card
A **card** object contains the current statut of the card.

* Except the card ID, all the elements are optionals.
  * The variable elements of the card (token, current strength...) are provided only if the card is concerned.
  * The constant elements of the card (original base strength...) aren't necessary if the replay tool used can get those elements from a card database, according to the game version provided in the ``game`` object. However it's recommended to provide it to keep the replay consistant in the long run.
  * The 3 strengths values are optionals because they are needed for unit cards, but not for others cards.
 
  
Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``id`` | Gwent card ID | *Positive integer ID* |
``original-base-strength``\* | Original base strength when playing a copy of the card | *Positive integer* | 
``base-strength``\* | Current base strength | *Positive integer* | 
``strength``\* | Current strength | *Positive integer* | 
``spy-token``\* | Spy token | ``true``, ``false`` | false
``resilience-token``\* | Spy token | ``true``, ``false`` | false
``lock-token``\* | Spy token | ``true``, ``false`` | false
``shield-token``\* | Spy token | ``true``, ``false`` | false
``armor``\* | Current armor value | *Positive integer* | 0
``countdown``\* | Current countdown value | *Positive integer* | 0
``revealed``\* | The card is visible to the other player | ``true``, ``false`` | false
``type`` | Card type | ``bronze``, ``silver``, ``gold`` | 

### Example
The next card has been renforced by 2, then boosted by 3.
```json
{
  "id": "132313:Ekimmara",
  "original-base-strength": 6,
  "base-strength": 8,
  "strength": 11,
  "resilience-token": true,
  "shield-token": true,
  "type": "bronze"
}
```

## Turns
A **turn** object contains a list of **steps** played during a player turn.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``number`` | Turn number | *Integer greater than 0* |
``player`` | Current player | ``blue``, ``red`` | blue
``passed``\* | Passed info | ``true`` if the player has passed, else ``false`` | false
``steps`` | Array of steps | |

> **Note :** Even if a player has passed, some actions can happen during his turn.

## Step
A **step** object contains an independant change during the game.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``number`` | Step number | *Integer greater than 0* |
``source``\* | Source of an action | *See the **Positions** section* |
``action``\* | Type of action | *See **List of actions*** |
``target``\* | Target of an action | *See the **Positions** section* |
``before-after``\* | Variable value before the action | *See the **Befores/afters** section* |
``card``\* | Card object | *See the **Card** section* |
``card-list``\* | Cards-list when necessary | *See the **Card-list** section* |

### Position object :
A **position** string contains the position of a source or target of an action. It can designate a card or an element of the board.

*  The two elements of a position are separated by a dot. For example, the 4th card of the blue melee is *blue-melee.4*.
*  When the position is an entire row, the location is *0*.
*  When the position is a specific card, the location is the number of the card from left to right.
*  When the position is between cards, the location is the number of the card to the right. For example, on a row with 6 cards, deploying a card on 1st position is **board.blue-melee.1**. Between the 3rd and 4th card, it becomes **board.blue-melee.4**. And after the 6th card, it's **board.blue-melee.7**.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``type`` | Type of position | ``card``, ``board`` |
``region`` | Area of choice | ``blue-hand``, ``blue-deck``, ``blue-graveyard``, ``blue-melee``, ``blue-ranged``, ``blue-siege``, ``blue-play``, ``banned``, ``generation`` |
``location``\* | Exact position of the target. Not needed if it targets a full row for example | *See below for the details* | 0

> **Note :** *blue-play* is the position of a spell being played, on the right side of the board. This helps to show which card is played before its sends to graveyard.
> *generation* is a non-existent position for **step:source** when a card is not moved from one position to another, but generated from nowhere (created, duplicated, etc).


### List of actions

#### Actions on cards
Gwent action | GRDD action code | Note
--- | --- | ---
Spawn / Generate a card | ``spawn`` | The card information is in **step.card**.
Transform a card into another | ``transform`` | The changed card is in **step.target**. The new card is in **step.card**.
Move a card | ``move`` | Move a card from anywhere to anywhere. It's used to deploy, pick a card from the deck, move to graveyard or banned pile...
Play a card | ``play`` | Shortcut for spells to move a card to the ``blue-play`` this turn, and be considered and moved to the graveyard at the end of the step
Change a token / an element | ``change`` | The changed element, its previous and its new status are in **step.before-after**
Destroy a card | ``destroy`` | Shortcut to move a unit card to its side's graveyard last place, then reset its strength, and remove tokens and armor (excepted the *countdown-token*).
Display a card | ``display`` | Display a hidden card (ambush, red hand, etc). **step.card** must contain the informations of the showed card.

> **Note :** When a card is moved from a pile of unknow position, the position *0* is used. The pile must then remove one empty element.
When the **red player** plays a hidden card, the card information must be filled. The *0* position can be used as a shortcut to the first possible hidden card of the hand.


#### Actions on rows
Gwent action | GRDD action code | Note
--- | --- | ---
Change the status | ``change-row`` | The name of the boon or the hazard is in **step.before/after**. Cleaning a row is applying the ``clear`` status.

#### Others actions
Gwent action | GRDD action code | Note
--- | --- | ---
Choice in a list of cards | ``choice`` | The ``card-list`` element must be provided. This action shows a list of cards to choose for a player, so the replay viewer can see what the player could choose. The cards of the list are not moved from/to any position and exists only for display.

## Before/After
A **before/after** is an object containing the specific change of an action. This element is particulary important has it's a way to go back to the board statut before an action takes place.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``type`` | Type of change | ``position``, ``[card-element]``, ``row``, ``card`` |
``before`` | Value before change | *See list below* |
``after`` | Value after change | *See list below* |

* Any ``[card-element]`` (except the ``id``) can be changed using the same element name (``spy-token``, ``base-strength``...) as type. See the **Card** section to check all the possibilities and the possible values.
* The ``position`` is a position object. See the **Position** section for more informations.
* The ``row`` before/after values must be one of these : ``frost``, ``fog``, ``rain``, ``drought``, ``ragh-nar-roog``, ``skellige-storm``, ``golden-froth``, ``clean``
* The ``card`` type is necessary for some situations, like a transformation.



### Examples
The card **Alzur's Double Cross**, in the *blue player hand, 3rd card*, is being *played*. It picks the random strongest unit from the deck (*moved* from an unkown position (0) card in ``blue-deck`` to ``blue-play``) and *boost* it by 2 points. The card (an **Ekimmara** here, which was *boosted* by 2 and has a *shield*, thanks to an earlier **Quen Sign**) will then be deployed (*moved*) on the blue ranged row between the 1st and 2nd card (the *board.blue-ranged.2* position), earns *resilience*, and consumes (*destroy*) the card (a **Nekker** which has a strength of 7) on the *blue melee row on 3rd position*. The Ekimmara is *boosted* again by 7. As the Ekimmara consumed a card, all invisible nekkers in the blue deck are *boosted* (as we don't know the position of each card, we will call twice the *card.blue-deck.0* position, as there is two nekkers in the deck). Because the Nekker died, another Nekker will be *called from the deck* (with a strength of 8 so) on the *last position on the right (the 5th) of the melee row*.
```json
"turns": [
  "player": "blue",
  "number": 4,
  "steps": [
    {
      "source": "card.blue-hand.3",
      "action": "play",
      "target": "card.blue-play.1"
    },
    {
      "source": "card.blue-deck.0",
      "action": "move",
      "target": "card.blue-play-1",
      "card": {
          "id": "132313:Ekimmara",
          "original-base-strength": 6,
          "base-strength": 6,
          "strength": 8,
          "shield-token": true,
          "type": "bronze"
      }
    },
    {
      "action": "change",
      "target": "card.blue-play.1",
      "before-after": {
        "type": "strength",
        "before": 8,
        "after": 10
      }
    },
    {
      "source": "type": "card.blue-play.1",
      "action": "move",
      "target": "board.blue-ranged.2"
    },
    {
      "action": "change",
      "target": "card.blue-ranged.2",
      "before-after": {
        "type": "resilience-token",
        "before": false,
        "after": true
      }
    },
    {
      "source": "card.blue-ranged.2",
      "action": "destroy",
      "target": "card.blue-melee.3"
    },
    {
      "action": "change",
      "target": "card.blue-ranged.2",
      "before-after": {
        "type": "strength",
        "before": 8,
        "after": 15
      }
    },
    {
      "action": "change",
      "target": "card.blue-deck.0"
    },
    {
      "action": "change",
      "target": "card.blue-deck.0"
    },
    {
      "source": "card.blue-deck.0",
      "action": "move",
      "target": "board.blue-melee.5",
      "card": {
          "id": "132305:Nekker",
          "original-base-strength": 3,
          "base-strength": 3,
          "strength": 8,
          "type": "bronze"
      }
    }
  ]
]
```



