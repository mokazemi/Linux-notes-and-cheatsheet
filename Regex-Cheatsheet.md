# Regular Expressions simple cheatsheet

The syntax of Regext is like `\(PATTERN)\` which the pattern is what we want to match in the srtings.

**Example:** `\cat\` matches the string "cat" (no matter what is before and after it).

* But the thing is that, by default regex pattern only returns to the first match that it finds. We can use **g** (Global) flag at its end to to change this behaviour. So our string would become something like `\cat\g`.
There's also another flag **i** which make the pattern insensitive (both lowercase and capital).

I intented to write a more complex cheatsheet here, But I found a lot of good and complete resources :) So I'll just introduce them.


## Useful resources:

- https://regex101.com/ (to test and practice)
- https://regexlearn.com/cheatshee
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions/Cheatsheet

