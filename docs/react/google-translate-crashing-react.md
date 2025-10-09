[See article here](https://martijnhols.nl/blog/everything-about-google-translate-crashing-react)

Google Translate is a machine translator that provides users with an easy way of translating webpages from within their browser tab. It achieves this by manipulating the DOM in such a way that it breaks the base app.

The interference manifests as crashes caused by the DOM's element native `removeChild` method, resulting in errors like

```
NotFoundError: Failed to execute 'removeChild' on 'Node': The node to be removed is not a child of this node.
```

But this affects a lot more.

## How Google Translate Works

To understand what Google Translate does, we need to take a close look at the DOM structure before and after translation.

All HTML that is rendered in the browser is represented by the DOM in JavaScript. This is a tree-like structure where each element is a node. HTML elements are represented by Element-nodes and text is represented by a TextNode.

For example, the following html:
```html
<p>There are 4 lights!</p>
```

Would look like
```html
html[lang=en] => ParagraphElement => TextNode
```

When Google Translate activates, it looks for `TextNode`s to translate. These nodesa re then replaced with `FontElement` elements with the new, translated, strings inside. This results in the following HTML.

```html
<p><font>ライト４個あります</font></p>
```

The mounted DOM becomes like this:
```
html[lang=jp] => ParagraphElement => FontElement => TextNode
```

And the unmounted DOM has the TextNode. (the original English node)

What this shows is that the original TextNode is unmounted and replaced with a new `FontElement` with the translated text inside.

### Simulating Google Translate

We can reproduce the issues caused by Google Translate more easily.

The snippet below will search for an element with the id 'translateme' and replace all direct TextNode children with FontElements similar to how Google Translate operates. 

```tsx
useEffect(() => {
  document.getElementById('translateme').childNodes.forEach((child) => {
    if (child.nodeType === Node.TEXT_NODE) {
      const fontElem = document.createElement('font')
      fontElem.textContent = `[${child.textContent}]`
      child.parentElement.insertBefore(fontElem, child)
      child.parentElement.removeChild(child)
    }
  })
})
```

## Google Translate's Interference Issues

### Issue 1: Translated text not updating

When Google Translate unmounts DOM nodes and places its own new ones in their place, the original DOM nodes continue to exist in-memory. Any change to the original DOM nodes will not show up in the user's browsers in any way. The changes will remain in-memory.

React uses a Virtual DOM which only updates the DOM nodes instead of replacing them since replacement is computationally more expensive. The consequence is that in React, any text or number that might change alongside another string is affected.

> When Google Translate is applied, values shown on your page may never update again.

This would lead to the showing of incorrect or outdated data to user. This issue is hard to discover since it doesn't lead to a crash or any error.

### Issue 2: Crashes

Google Translate causes two errors. When either error occurs, React will unmount your tree to the closest error boundary. If you don't have any error boundary, your entire app will crash.

```
NotFoundError: Failed to execute 'removeChild' on 'Node': The node to be removed is not a child of this node.

Failed to execute 'insertBefore' on 'Node': The node before which the new node is to be inserted is not a child of this node.
```


The `removeChild` error usually happens because your app was trying to remove a conditionally rendered `TextNode` fro the DOM that Google Translate unmounted. The `insertBefore` is less common; this usually occurs because something conditionally rendered is trying to appear *before* a `TextNode` that was unmounted by Google Translate.

Crashes might be less important than translated text not updating. Text not updating is less predictable than not showing anything at all as it may mislead users.

For example, take the code

```tsx
const CrashRepro = () => {
  const [lightsOn, setLightsOn] = useState(true)

  const [simulateGoogleTranslate, setSimulateGoogleTranslate] = useState(true)
  useEffect(() => {
    if (!simulateGoogleTranslate) {
      return
    }

    // Not using ref because I want to eliminate all magic and any suggestion
    // that React might be doing something funny
    document
      .getElementById('GoogleTranslateCrashesTernaryRepro-translateme')
      ?.childNodes.forEach((child) => {
        if (child.nodeType === Node.TEXT_NODE) {
          // eslint-disable-next-line @typescript-eslint/no-deprecated
          const fontElem = document.createElement('font')
          fontElem.textContent = `[${child.textContent ?? ''}]`

          child.parentElement?.insertBefore(fontElem, child)
          child.parentElement?.removeChild(child)
        }
      })
  })
  
  return (
	<div id="GoogleTranslate">
		{lightsOn && 'There are 4 lights!'}
	</div>
  )
}
```

Once Google Translate activates, the `TextNode` 'There are 4 lights!' is replaced by a `FontElement` and the original `TextNode` is removed from the DOM.

When `lightsOn` is set to false, React will try to remove the original `TextNode` from the Virtual DOM. However, because it has already been unmounted, it will throw an error because it has no parent.

As there are many ways to vary the amount of `TextNode`s rendered, there are many ways of reproducing the crash. Hence, it makes it hard to find a workaround that solves all cases.

### Workarounds

Several workarounds have been posted, but none provide a quick fix. The below workarounds only focus on the crashes and have no impact on the translated text not updating.

#### 1. Monkey patching `removeChild` and `insertBefore`
There is a workaround to fall the above methods silently when they're called with invalid arguments. While this prevents the crash, it doesn't solve the underlying issue at all. Instead of crashing, it does nothing and the translated text will remain in the DOM until its parent is removed. When the `insertBefore` error is triggered, the newly rendered text won't appear for your user.

#### 2. Surrounding TextNodes with spans
There is a proposed workaround of surrounding all conditionally rendered and adjacent text in `span` elements. This avoids the crashes by ensuring that React doesn't try to remove or insert a `TextNode` directly. This fixes most of the common crashes, but not all of them. It may fix the conditionally rendered `TextNode`s, but not ternary expressions.

Implementing this would also require mangling a lot of existing code. It's not worth the effort for the code quality sacrifice.

#### 3. Self re-rendering error boundaries
An error boundary that just renders the same children again when it runs into an error is a good idea, but components in the subtree will lose their state in the process. It's not a general solution.

### Issue 3: Inconsistent `event.target`
When Google Translate is active, the value of `event.target` becomes unpredictable, as users are likely to click on one of Google Translate's `font` elements instead of the underlying element that the developer created. This could lead to click events not working correctly.


## Not just React
Google Translate's interference affects not just React apps.

Any JavaScript code that manipulates the DOM in a similar fashion is affected. This includes operations such as updating a value of a `TextNode`, adding or removing children, or using `event.target`. These operations are not specific to React.

However, these issues are more commonly observed in React applications since React uses the Virtual DOM. The Virtual DOM keeps references to all DOM nodes to it only has to update parts of the DOM that are actually changed. This allows for high-performance apps as it's more efficient than replacing DOM nodes. React's use of a Virtual DOM to reuse and update nodes rather than constantly replacing them is a natural evolution for frameworks.

## Not just Google Translate
Most machine translators work pretty much the same way as Google Translate, so the issue is not just limited to it. Any browser extensions that manipulate the DOM can interfere.
- Password managers manipulating forms to prefill dropdowns
- Extensions that inject alternative prices on competing webshops
- Adblockers removing an element

Google Translate was originally architectured at a time when the web was different from what it is today. The issues are a result of the web evolving; websites aren't almost exclusively static websites anymore as many of the popular websites today are large and complex webapps.

## No Real Solution (yet)
The only available way to fix this is to disable translation on the app.

Wrapping conditional `TextNode`s in `span`s will solve a large chunk of the crashes, but it is harder for larger codebases.