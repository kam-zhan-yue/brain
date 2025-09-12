- The profiler will explain how many times your React application rendered.
- At each render, it shows a bunch of bars. Each bar tells you how long something took

There are three different views you can do when inspecting a render frame.

## Flamegraph
Shows the overall hierarchy of how everything is rendered. 

If you want to find out why a component was rendered, you need to tick a setting

Components that are coloured were re-rendered.

Components will say (1.9ms of 3ms)
- The component itself took 1.9ms
- Its children took the remaining time
## Ranked
Shows the slowest component renders.

## Timeline
Tells you what happens at a specific time.
- Top sections show why a change happened
- Bottom shows what got re-rendered

### Helpful
You can hide commits that are less than a specific amount of milliseconds.