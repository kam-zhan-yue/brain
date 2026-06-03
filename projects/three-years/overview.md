# Three Years
At this point, I may or may not be in Tokyo? 7th July 2026 is a Tuesday.

In the end, it's a 3-year anniversary, so it makes sense to make a 3D game, right?
- Make a 3D game, have it styled game-boy style
- Two player game? Makes the most sense I guess

Ok fuck it.
Multiplayer Godot Game.

## Brainstorming
A game that captures the everyday mundanity of living together. The players go through a single day, doing chores with each other. As they progress through the chores, they need rely on one another, to complete other chores or to collect things while they are busy. Each person rates each other on how well they complete the chore.

At the end of the day, they clean up the room, make dinner, do laundry, etc. And another day passes. A short and sweet game to capture the joy and mundanity of living together.

### Dialogue System
The game has a main story with a distinct start and end. The dialogue system acts to keep the game in progress, giving cues on what task to do next and who does what.
- The game should be able to stop completely (players have no inputs) and just show dialogue. As the dialogue event completes, the players get a new task and have to complete it together.
- Dialogue boxes should show as each person is speaking. Ideally, they have dialogue arrows, but a box with an icon of their face will suffice.
- Dialogue progresses simultaneously. If the player who is talking progresses their dialogue, it will progress for both players.

### Chores / Tasks
Each player needs to complete a central task. There is no time limit for the task, but they need to it to a standard that the other person is happy with.
- Tidying Up: The room is a mess and both players need to clean up. Here, both players just need to hold E to clean up the room
- Cooking Dinner: One person is making things, while the other person is getting ingredients. The person is given vague instructions on what to get and the dish depends on the ingredients.
- Eat Dinner: They gather around the dinner table and eat food. There is just simple dialogue to express how grateful we are to be able to spend time together.

Total Time: 5-10 minutes

## Game Flow
1. Both players enter the match. Alex is working on the kotatsu and Wato is still asleep
2. Dialogue starts. Wato grudgingly wakes up and both acknowledge that the room is a mess.
3. Dialogue Event: Clean Room. Both players clean up the room, pressing E and removing things. An item that is currently being cleaned cannot be cleaned right now.
4. Dialogue continues. Once the room is cleaned, both players acknowledge that they are super hungry and want to eat. There is an argument on going out or eating in. At the end of the day, Wato decides she wants to get spaghetti.
5. Dialogue Event: Cook Lunch. Alex starts cooking and there is dialogue on what ingredients we need. Alex lists a bunch of things and Wato has to find them. She has the choice on what ingredients to choose. Once the ingredients are chosen, they are added to the pot.
6. The screen fades to black. Both players are sitting around the kotatsu eating. There is talk on how the food tastes, what they're gonna do today, and how happy they are being together at last.
