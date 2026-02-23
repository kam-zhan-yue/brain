useMemo and useCallback are not only used for memoizing complex calculations, but also for keeping stable references which can prevent unnecessary re-renders

The performance hit of wrapping every callback in useCallback is so negligible that being overly defensive by using them everywhere is better than keeping track of every usage of the callback downstream, in fear that it will cause thousands of re-renders for nothing.

You passed the existing DOM callback to a child component that you just created, forgetting that now you have to wrap the function in useCallback? Well good luck finding out that now you have a huge performance hit. Fast forward 4 months later: "hmmm, why is my app sluggish?? oh, it's because I didn't want to hurt my performance by microseconds 4 months earlier"
