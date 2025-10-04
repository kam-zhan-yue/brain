
## Build Your Own Adventure Game
Already been built before using `dndai.com`

LLMs are only good for generating text and images. The only game it would be good for is like a mystery game?

AH detective game :)

Trying to solve a detective game against an AI. Seems like this is not what they want today.

## Learning Plan with a Roadmap, etc

High Level Summary: An AI helper to produce a learning

Already Exists, but it would be good to learn how to use such a tool. 

Inputs would be:
- What can I help you learn?
- Answer these questions for a personal roadmap

What should the main pages be? Let's keep it really simple.

To send to Devin

## Mystery Murder AI Game
### Overview
- A short text-based interactive game inspired by _Cluedo_.  
- The user selects a location and a weapon. The system generates characters, a murder scenario, and alibis.  
- The user interrogates characters (AI agents) to gather evidence and then prosecutes one suspect.
## Game Flow
### 1. Setup
- **Inputs** (from user):
    - `location` (string) — e.g., "Library", "Garden", "Mansion Hall".
    - `weapon` (string) — e.g., "Candlestick", "Dagger", "Revolver".
- **AI generates**:
    - `characters`: 4–6 characters, each with:
        - `name` (string)
        - `title/role` (string, e.g., “Professor”, “Maid”)
        - `personality` (short description, affects responses)
        - `alibi` (structured text, possibly suspicious)
    - `murder_scenario`: a short narrative describing how/when the murder occurred.
### 2. Interrogation Phase
- Each character is an **AI agent** with:
    - Memory of:
        - Their alibi
        - Relationships with other characters
        - Knowledge of the crime scene
        - Potential lies (for the murderer)
    - Ability to:
        - Answer open-ended user questions
        - Stay consistent with personality and knowledge
        - Hide or reveal clues naturally
- User flow:
    - Select a character to interrogate
    - Ask freeform text questions
    - Receive character responses
    - Option to “exit” interrogation and return to character selection
### 3. Prosecution Phase
- After interrogations, the user chooses:
    - `suspect_to_prosecute` (from list of characters)
- AI reveals:
    - Whether the prosecution was correct
    - A summary of the true events

## Core Components

### Character Generator
- Input: `location`, `weapon`
- Output: Array of character profiles (JSON structure)
```json
{
  "name": "Dr. Helena Gray",
  "title": "Historian",
  "personality": "Meticulous, nervous when pressed",
  "alibi": "I was cataloguing books in the east wing library all evening."
}
```
### Murder Scenario Generator
- Takes `location` and `weapon`
- Outputs:
    - Victim identity
    - Scene description
    - Time of death
    - Clues (to distribute among characters)
### Interrogation Engine
- Each character runs as an agent:
    - Receives user input
    - Responds in character
    - Keeps consistency with alibi/personality
    - May deflect, lie, or implicate others if they are the murderer
### Prosecution Engine
- Receives selected suspect
- Compares with murderer
- Outputs:
    - Verdict (guilty / not guilty)
    - Narrative reveal
## States & Transitions
1. **Setup**  
    → User inputs `location` + `weapon`  
    → AI generates scenario + characters
2. **Character Selection**  
    → User selects one character
3. **Interrogation**  
    → User inputs freeform questions  
    → Agent responds  
    → User can “exit” to return to character selection
4. **Prosecution**  
    → User selects a suspect  
    → Verdict + story reveal
### JSON Schema
```json
{
  "murder_mystery": {
    "location": "string",
    "weapon": "string",
    "victim": {
      "name": "string",
      "role": "string",
      "description": "string"
    },
    "scenario": {
      "time_of_death": "string",
      "scene_description": "string",
      "clues": [
        {
          "id": "string",
          "description": "string",
          "related_character": "string (optional)"
        }
      ]
    },
    "characters": [
      {
        "id": "string",
        "name": "string",
        "title": "string",
        "personality": "string",
        "alibi": "string",
        "relationships": [
          {
            "with": "string (character id)",
            "relation": "string"
          }
        ],
        "knowledge": {
          "saw": ["string"],
          "heard": ["string"],
          "knows_about": ["string"]
        },
        "is_murderer": "boolean"
      }
    ]
  }
}
```

### Example
```json
{
  "murder_mystery": {
    "location": "Library",
    "weapon": "Candlestick",
    "victim": {
      "name": "Lord Henry Blackwood",
      "role": "Estate Owner",
      "description": "Wealthy and respected, but had many enemies."
    },
    "scenario": {
      "time_of_death": "9:45 PM",
      "scene_description": "The victim was found slumped in an armchair in the library, the candlestick lying nearby with traces of blood.",
      "clues": [
        {
          "id": "clue1",
          "description": "A torn piece of fabric caught on the library door handle.",
          "related_character": "char3"
        },
        {
          "id": "clue2",
          "description": "An empty glass with fingerprints not belonging to the victim.",
          "related_character": "char2"
        }
      ]
    },
    "characters": [
      {
        "id": "char1",
        "name": "Dr. Helena Gray",
        "title": "Historian",
        "personality": "Meticulous, nervous when pressed.",
        "alibi": "I was cataloguing books in the east wing library all evening.",
        "relationships": [
          { "with": "char2", "relation": "Colleague" }
        ],
        "knowledge": {
          "saw": ["char2 leaving the study around 9:30 PM"],
          "heard": ["raised voices in the hallway before the murder"],
          "knows_about": []
        },
        "is_murderer": false
      },
      {
        "id": "char2",
        "name": "Mr. Victor Hale",
        "title": "Butler",
        "personality": "Polite, evasive when questioned about the master.",
        "alibi": "I was preparing tea in the kitchen.",
        "relationships": [
          { "with": "char1", "relation": "Worked together often" }
        ],
        "knowledge": {
          "saw": ["char3 near the library at 9:40 PM"],
          "heard": [],
          "knows_about": ["victim had planned to change his will"]
        },
        "is_murderer": true
      }
    ]
  }
}
```
### Tech Stack
- [ ] Django webapp with a React typescript frontend.
- [ ] Tanstack Router for routing and all purposes

## Murder Mystery Screens


## Roadmap App
I want to create a roadmap app that is similar to roadmap.sh. The user should have three inputs:
- What they want to learn
- Their current experience with the subject
- Where they want to be with the subject

Afterwards, it will generate a simple roadmap using node graph with uni-directional arrows connecting the nodes. Each node and arrow should relate to a dependency. For example, if the user wants to learn about Python, they would need to learn the Python syntax before learning about arrays.

The AI should generate a clear structure of nodes that dependencies and display them to the user. Upon clicking on one of these nodes, there is a checklist of things to learn / do, along with any related articles, practice questions, etc as links. This will open as an offcanvas container on the right, exactly like neetcode.io. For example, if the subject is "Arrays" and there are 10 things to do, then there should be 10 things in the task list. Upon checking one of them, it will go to 1/10 and the progress bar on the node should fill with 10% capacity.

The aim of the app is not to provide all of the learning materials in one place, like learnanything.com, instead it should only point you to a roadmap and allow you to come back to it whenever. No learning plan, just a simple roadmap with nodes and tasks.

### Canvases
Each 'roadmap' would be a canvas. Users can create new canvases and save them to the cloud (or locally for now). When loading into the app, there is a navigation bar on the top with "Create", "View Canvases". Create will lead to the prompt page, while "View Canvases" will lead to a page that lists all of the canvases. Each list entry should just have the canvas name and point to the 
### Configuration
I want the user to be able to create new nodes and new tasks within the canvas 

### Tech Stack
### Front Page (Querying)
- User can access the navigation tab and see their previously saved data.
- User can input how much experience they have with the subject

### Nice To Haves
- [ ] Multi-language support (switching between Japanese and English)
- [ ] Dark mode / light mode