# Gwent Replay Data Descriptor

The Gwent Replay Data Descriptor (GRDD) gives a complete structure to record a Gwent game, to be used for step by step replay application.

The datas are stored using the JSON format. An example is available in the Example.json file.


## Game
The **game** root contains basic informations about the game itself.

Any element followed by an asterisc * is optional. If not filled, it will be empty or use a default value depending of the element.

Element | Description | Example
--------|-------------|--------
name* | Name of the game | My last Gwent game
