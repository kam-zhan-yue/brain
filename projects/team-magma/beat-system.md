How can I deal with the tempo changes?
- I could split three beat tracks and have them running side by side. When the track switches, we switch the beat track as well. But that brings the problem of how can we set up the UI to change with the tempo? It's going to be hard.
- I think I need to check that when the tempo is changed, the `OnBeat` is changed as well.
- Beat time is not changing, that is problem 1
- Okie it seems like setting `MusicSpeed` is not enough to change the tempo, we also need to expose the tempo parameter and set it through `musicInstance.setParameterByName("MusicSpeed", speedRatio);` or similar using automation