Instead of rendering glass, we can now render mirrors.

```c++
glEnable(GL_BLEND);
glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);
```

However, the transparent parts of the window are occluding the windows in the background.

![[24-3-occlusion.png]]

This is because depth testing works differently with blending. When writing to the depth buffer, the depth test does not care if the fragment has transparency or not. The result is that the background windows are tested on depth, ignoring transparency. Even though the transparent part should show the windows behind it, the depth test discards them.

> The windows in the front are drawn first. Then when the windows behind are drawn, they are discarded.

We can't render the windows however we want and expect the depth buffer to solve these issues. To make sure the windows show the windows behind them, we need to draw the windows in the background first.

We have to manually sort the windows from furthest to nearest and draw them accordingly ourselves.