In order to sync the dialogue between the frontend and backend, we can have them read the same Ink files. 

- Dialogue is synced
- Players wait for choices


### Ink Script Handling
- Should both player have access to the ink script, or should it be completely dealt with server side? I like the idea of server side. But how much can they really receive? Unless they query for the json file from the server and build an ink script out of that
- If we want neutral nodes to work (full on conversations), then both players will need to have their own copies of the script and work their way through it.
- Unless they receive all of the data in a websocket.... That could really work.


### Proposal 1: Websocket Handling

The dialogue flow is handled entirely server side. Server sends a list of dialogue nodes that need to be completed. as well as a dialogue id for this. When the client finishes rendering, it will send their state through. The server then waits until both are there before continuing. 

- Sever sends a bunch of data
- Client handles the data and processes them one by one

### Proposal 2: Shared Ink File

There is one ink file that is shared between the server and the clients. Ahhh, but then the server would have to continue story until it reaches something... The server would have to progress the story as well. It would be much better if we can just do that all on the server side and send them through in a single websocket response.