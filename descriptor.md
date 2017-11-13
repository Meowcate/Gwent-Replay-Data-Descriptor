# Gwent Replay Data Descriptor

The Gwent Replay Data Descriptor (GRDD) gives a complete structure to record a Gwent game, to be used for step by step replay application.

* The datas are stored using the JSON format. An example is available in the **Example.json** file.
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
The **rounds** object contains an array of round, from 1 to 3.

* The rounds should have the same order as in the game.
* A round root is its number.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``coinflip`` | Which player plays first | ``blue``, ``red`` | blue
``blue-point`` | Number of points the player have at the beggining of the round | ``0`` or ``1`` | 0
``board`` | Board description | *See the **Board** section* |
``turns`` | List of changes on the board, turn after turn | *See the **Turn** section* |

### Example
```json
"rounds": {
  "1": {
    "coinflip": "blue",
    "blue-point": 0,
    "red-point": 0,
    "board": {
      "(...)"
    },
    "turns": {
      "(...)"
    }
  },
  "2": {
    "coinflip": "red",
    "blue-point": 1,
    "red-point": 0,
    "(...)"
  },
  "3": {
    "coinflip": "blue",
    "blue-point": 1,
    "red-point": 1,
    "(...)"
  }
}
```

## Board
A **board** object contains a full description of the board at the beggining of a round.

* If there is resilient units, it should be a part of the board.
* Before-mulligan actions as *losing resilience*, *automatic resurrection*, *deathwishes on a new round* and others has to be placed in the ``before-mulligan`` element as **step** elements. ``after-mulligan`` acts the same, especially for actions activated when a card is discarded. Any action executed before the first player gets the "Your turn" Gwent message must be put into one of those space.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``blue-used-leader`` | Show if the leader can be use or not | ``0`` if the leader can't be used, else ``1`` | 1
``blue-hand`` | Player's hand **Card-list** | *See the **Card-list** section* |
``blue-deck`` | Player's deck **Card-list** | *See the **Card-list** section* |
``blue-graveyard`` | Player's graveyard **Card-list** | *See the **Card-list** section* |
``blue-melee`` | Player's melee **Board-row** | *See the **Board-row** section* |
``blue-ranged`` | Player's ranged **Board-row** | *See the **Board-row** section* |
``blue-siege`` | Player's siege **Board-row** | *See the **Board-row** section* |

### Example
```json
"board": {
  "blue-used-leader": 0,
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
  "red-used-leader": 1,
  "red-hand": [
    "(...)",
  ],
  "(...)"
}
```


## Card-list
A **card-list** array contains a list of cards.

* The cards are ordered from left to right.
* The number of values is the number of cards in the list.
* Any empty value is considered as a hidden card, which is considered to be inaccessible (e.g. the red deck), simply hidden but can be revealed (e.g. the red hand) or temporary hidden (e.g. an *ambush* card).

## Board-row
A **board-row** object contains the description of a row.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``statut``\* | Statut of the row | ``frost``, ``fog``, ``rain``, ``drought``, ``ragh-nar-roog``, ``golden-froth`` | *none*
``card-list`` | Card-list of the row | | *none*

## Card
A **card** object contains the current statut of the card.

* Except the card ID, all the elements are optionals.
  * The variable elements of the card (token, current strength...) are provided only if the card is concerned.
  * The constant elements of the card (original base strength...) aren't necessary if the replay tool used can get those elements from a card database, according to the game version provided in the ``game`` object. However it's recommended to provide it to keep the replay consistant in the long run.
  
Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``id`` | Gwent card ID | *Positive integer ID* |
``original-base-strength``\* | Original base strength | *Positive integer* | *Current version-based original value*
``base-strength``\* | Current base strength | *Positive integer* | *Current version-based original value*
``strength``\* | Current strength | *Positive integer* | 
``spy``\* | Spy token | ``1`` if the spy token is present, else ``0`` | 0
``resilience``\* | Resilience token | ``1`` if the resilience token is present, else ``0`` | 0
``lock``\* | Lock token | ``1`` if the lock token is present, else ``0`` | 0
``shield``\* | Shield token | ``1`` if the shield token is present, else ``0`` | 0
``armor``\* | Current armor value | *Positive integer* | 0
``countdown``\* | Current countdown value | *Positive integer* | 0
``revealed``\* | The card is visible to the other player | ``1`` if the card is revealed, else ``0`` | 0
``type``\* | The card type, only useful if the card has been demoted | ``bronze``, ``silver``, ``gold`` | 

> **Note :** If the ``strength`` value is provided because it has changed, it's a good idea to provide the ``base-strength`` value as well to see if the unit is currently boosted or damaged.

### Example
```json
{
  "id": "132313:Ekimmara",
  "original-base-strength": 6,
  "base-strength": 6,
  "strength": 8,
  "resilience": 1,
  "shield": 1
}
```

## Turns
A **turn** object contains a list of **steps** played during a player turn.

* ``number`` is optional, and on a full game, a replay application could count that number. It's mostly used for a partial replay, or a solo example.

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``player`` | Current player | ``blue``, ``red`` | blue
``passed``\* | Passed info | ``1`` if the player has passed, else ``0`` | 0
``number``\* | Turn number | *Integer more than 0* |
``steps`` | Array of steps | |

> **Note :** Even if a player has passed, some actions can happen during his turn.

## Step

A **step** object contains an independant change during the game.

* ``number`` is optional, and on a full game, a replay application could count that number. To find a very specific moment, one can use a **round-turn-step** combinaison (e.g. **1-15-2** for the 2nd step of the 15th turn of the 1st round).

Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``number``\* | Step number | *Integer more than 0* |
``origin`` | Origin of an action | *See **List of origins*** | player
``source``\* | Source of an action | *See the **Positions** section* |
``action``\* | Type of action | *See **List of actions*** |
``target``\* | Target of an action | *See the **Positions** section* |
``before-after``\* | Variable value before the action | *See the **Befores/afters** section* |
``choices``\* | Cards-list of the choices | *See the **Card-list** section* |
``chosen``\* | Number-positions of the chosen cards from a ``choices`` card-list | Array of strictly positive integers |

### List of origins :
* ``player`` : Player action is when a player makes a decision. *The player has played a card, has made a choice...*
* ``random`` : Random action is when something random happens. Even if it's because of a player action, the player doesn't choose how a random action is resolved. *A random card has been picked from the graveyard after 2 turns, beucase of a player action a random card has been targeted on the board...*
* ``fixed`` : Predictible automated action, which happens without the intervention of the player, and the result isn't random. *A card damages automaticaly the strongest enemy each turn, a card consumes the card on its right after 2 turns*

### Position object :
A **position** object contains the position of a source or target of an action. It can designate a card or an element of the board.
Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``type`` | Type of position | ``card``, ``board`` |
``region`` | Area of choice. It must be one of the board area : ``blue-hand``, ``blue-deck``, ``blue-graveyard``, ``blue-melee``, ``blue-ranged``, ``blue-siege``, ``blue-play``, ``banned`` |
``location``\* | Exact position of the target. Not needed if it targets a full row for example | *See below for the details* |

##### ``location`` values
``location`` is an positive integer.
* In the ``type`` is ``card`` :
  * If it's *0*, it targets an unseen card (when a card in the deck is boosted for example).
  * If it's *1*, it means the first card (from left to right, or top to bottom), if it's *5*, it's the 5th card...
  * If the number is superior to the number of cards, the last possible card position must be chosen
* In the ``type`` is ``board`` :
  * If it's *0*, it means the position is meaningless (e.g. targeting the ``blue-melee`` with a weather effect don't need a position).
  * If it's *1*, it means the first position, so before the first card (from left to right, or top to bottom), if it's *5*, it's the 5th position...
  * If the number is superior to the number of cards, the position after the last card must be chosen

> **Note :** ``blue-play`` is the unique card position when a card is in the player's hand and can't be anywhere else. For example, when a card A generates another card B with a ``spawn`` action, A is the source, and B is the target. On the next step, with B in hand ready to be played (unit to be deployed, spell to be played...), the source of the step is this position :
```json
{
  "type": "board",
  "region": "blue-play"
}
```
The ``blue-play`` is considerated as a **card-list** as well, even if in a vast majority of the time, it concerns only one card. If the ``location`` isn't specified, the first (generaly only) card in the ``blue-play`` is selected.


### List of actions

#### Actions on strength
Gwent action | GRDD action code | Note
--- | --- | ---
Damage an unit | ``change-strength`` |
Boost an unit | ``change-strength`` |

#### Create/remove cards
Gwent action | GRDD action code | Note
--- | --- | ---
Spawn a card | ``spawn:card-id`` | e.g. : using the name as well, ``spawn:132313:Ekimmara``. The card is spawned with the default values. If something must be added during this spawn (boost, resilience...), do it on the next step.
Destroy an unit | ``destroy`` | The targeted card will loose its ``Armor``, ``Shield``, ``Lock`` and ``Revelead`` values
Play a card | ``play`` | The ``play`` code means the called card is in the ``blue-play`` card-list

#### Change statut of card
Gwent action | GRDD action code | Note
--- | --- | ---
Demote an unit | ``demote`` | 

#### Row statuts
Gwent action | GRDD action code | Note
--- | --- | ---
Apply an hazard | ``hazard:name`` | The name of the hazard must be one of these : ``frost``, ``fog``, ``rain``, ``drought``, ``ragh-nar-roog``, ``golden-froth``
Clear all hazards | ``clear`` |

#### Tokens
Gwent action | GRDD action code | Note
--- | --- | ---
Toggle Resilience | toggle-resilience |
Toggle spy | toggle-spy |
Toggle lock | toggle-lock | If a unit is locked, a replay application has to remember some tokens can be removed from the target
Toggle 

#### Others actions
Gwent action | GRDD action code | Note
--- | --- | ---
Choice in a list of cards | ``choice`` | The ``choices`` element must be provided. If ``chosen`` is empty, no choice has been made. The ``choice`` action exists only to give a feedback to anyone watching this replay. Whatever the action of the choice is (spawn a card between 5, pick 3 cards and discard one...), it should exist in the next step with the same ``source`` as the ``choice`` ``source``.

## Before/After
A **before/after** is an object containing the specific change of an action. This element is particulary important has it's a way to go back to the board statut before an action takes place.
On a replay application, it could show something like *« The [origin] [card] [action] changed [target] from [before value] to [after value] »*
Element | Description | Possibles values | Default Value
--- | --- | --- | ---
``type`` | Type of change | ``strength``, ``position``, ``card-type``, ``token`` |
``before`` | Value before change | *See list below* |
``after`` | Value after change | *See list below* |

> **Note :** In an action has multiple changes, only the first change should be given a proper origin/source/target, then all the others changes must have a ``fixed`` origin and be treated as individual steps.
For example, if a card played by player divides all the strengths on a row, the initial step gives ``player`` as origin, and the *row* as target. But after that, every card on this row must get an individual step where the origin is ``fixed``, the origin being the played card, and the target being the current step card of the row.


### Examples
The card **Alzur's Double Cross**, in the *blue player hand, 3rd card*, is being *played* by the *player*. It picks the *random* strongest unit from the deck, *boost* it by 2 points, and *put it into ``blue-play``*. The card (an **Ekimmara** here) will then be *deployed* by the *player* on the *blue ranged row between the 1st and 2nd card*, and *consumes* the card (a **Nekker**) on the *melee row on 3rd position*. The Nekker *dies*, the Ekimmara is *boosted* again. Because a Nekker dies, another Nekker will be *called from the deck* (without specific position, because we don't know the position of each card in the deck) on the *last position on the right (the 5th) of the melee row*.
```json
"turns": [
  {
    "origin": "player",
    "source": {
      "type": "card",
      "region": "blue-hand",
      "location": 3
    },
    "action": 
  },
]
```



