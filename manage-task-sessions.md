The problem is like so:

- When we first try to click, we are too early and react-select is not ready. So, we fail the click.
- Upon failing the click, we stop checking if the select has closed.
- Then, we check if the value is already there
	- If the value has already been set, then it will show

So, it actually flakes when react-select is already loaded and 