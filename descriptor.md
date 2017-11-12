# Gwent Replay Data Descriptor

The Gwent Replay Data Descriptor (GRDD) gives a complete structure to record a Gwent game, to be used for step by step replay application.

* The datas are stored using the JSON format. An example is available in the Example.json file.
* Any element followed by an asterisk **\*** is optional. If not filled, it will be empty or use a default value depending of the element.
* Any reference to *blue* mean *Player 1*, on the bottom on the screen. Any occurrence of *blue* in the following text can be changed by *red* as a reference to *Player 2*, on the top of the screen.

## Game
The **game** root contains basic informations about the game itself.




Element | Description | Example | Default Value
--- | --- | --- | ---
name\* | Name of the game | My last Gwent game | A Round Of Gwent
blue-name\* | Name of the blue player | swim | Blue
blue-mmr\* | Player's MMR during this game | 4050 | *none*
