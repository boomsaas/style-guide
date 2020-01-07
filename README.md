# The BoomSaaS Style Guide

## You are writing code for other people to read

Code should be as understandable as possible. This means we'll have less to explain to our future selves, and to each other. It requires a little more work up front but that pay off because [this code will be read many times more than it is modified](https://blog.codinghorror.com/when-understanding-means-rewriting/). With that in mind, the patterns below have emerged, and this document has been refined by various folk over a few years.

## We are making a product for users not ourselves

Just use 'prettier' and format on save. In VSCode:

    "[javascript]": { "editor.formatOnSave": true }

We use the prettier defaults, except for some small exceptions:

- Use Unix linefeeds
- Don't limit the line length to a particular size, that's what wrapping is for.

This is to avoid [bikeshedding](https://en.wiktionary.org/wiki/bikeshedding) and ensure the the code is a reflection of common standards rather an individual's preferences.

## The rest

### Keep everything as minimal as possible

"It seems that perfection is attained not when there is nothing more to add, but when there is nothing more to remove."

[Antoine de Saint Exupéry](https://en.wikiquote.org/wiki/Antoine_de_Saint_Exup%C3%A9ry)

This helps us read our code, refactor easily, check our work, and have better test coverage. Less code is better.

### Use accurate, real names

Less code doesn't mean giving things the smallest names possible. Giving things single character names increases complexity - **use real variable names, you're not a minifier**. Making other people have to read additional context for every line up to find out where something was defined because `upd = dSrc / data.length` doesn't make sense.

As a loose guideline, `is` or `has` is a often great name for Booleans (`isDeleted`, `hasCat` etc), plurals for arrays (`vehicles`), verbs for functions (`getConfig`, `deploy`).

### Avoid magic numbers, use intermediary variables

eg rather than `await sleep(240000)`

    const FOOAPI_RATE_LIMIT_DELAY = (4).minutes
    await sleep(FOOAPI_RATE_LIMIT_DELAY)

### Delete code instead of commenting it out

This avoids ghost code that doesn't do everything wasting our screens. If you want the deleted code back, search `git log`

### Use `await` instead of callbacks or `.then()`

Self explanatory. You wouldn't want to use callbacks for sync operations, there's no need to use them anymore for async operations either. No pyramid of doom, no importing custom workflow tools to chain callback operations into array.

Just don't forget await!

### Avoid unnecessary 'else'

Just `return` and stop execution:

```javascript
if (err) {
  throw new Error(`The laser ${laserID} is misaligned!`);
  return;
}
// code here runs if no errs
```

### Refactor on first paste.

Avoid copypasting code. This makes it easier to fix and refactor the app.

If you paste two slightly different versions of the thinsame code, the code will diverge over time and inconsistencies will be created.

Someone will, at some point, need to refactor and waste massive amounts of time working out what the differences are.

### Avoid Custom Objects

See [Object Oriented Programming Is Bad ](https://youtu.be/QM1iUe6IofM)
and [Object Oriented Programming Is Embarrassing](https://youtu.be/IRTfhkiAqPw)

### Commit all the time, commit to master, release constantly. Done is better than perfect.

Get features out to customers.

`master` should always work. Short lived feature branches are OK. Long lived feature branches are not OK, as long lived feature brances are a pain to merge.

Actively pursue simplicity.

## Do not commit dead code

Do not commit commented out code. Remove it, using git. If we need it back we can get it from commit history.

### Use globals carefully for boiler plate work

Globals, or things that extend inbuilt prototypes, should be considered carefully. They're not forbidden, but they should gnerally be used for cases where the stdlib is missing something that will be used across the entire codebase.

\*_All_ node globals are in `/src/shared/globals.js`. Browser globals are in `/src/frontend/`. Ensure that anything you import is in here, so others know where to find them and to ensure that work isn't duplicated.

### Done is better than perfect

CS focuses on algorithmic performance, but other concerns take precedence here. Optimise for releasing and code readability over performance. You can't have scaling issues if you don't have customers. Get customers.

### Use ARC @events for background tasks

Self explanatory.

### Decouple UI from processing.

If you have some code that takes some parameters from an HTTP request, or from HTML elements, pass that input to a seperate function that has no idea about HTTP requests or HTML - it simply gets inout values. Then write a test for that seperate function.

### Look on npm before writing a new module.

Use recentness, version numbering and GitHub stars to help you decide.

### Use JS itself rather than underscore.js or moment.js.

Enough said. `array.includes(x)` not `_.contains(array, x)`

`(2).weeks.ago` not importing moment and editing it.

### Throw errors

```javascript
`throw new Error(reason)`;
```

For `catch()` purposes.

## Commit formatting changes seperately from other work

Enough said.

### Comments

#### Comments are for why, not what.

Unless the 'what' is particularly complex. Imagine you sat down with someone for a code review. The things you'd explain to the other person, that's not evident from the code, are comments.

- Give things good names: if you find yourself doing:

```javacript
// Get customers
var banana = function(){...}
```

You should rename 'banana' to 'getCustomers' and remove the comment.

#### Credit data sources

Credit your data sources - eg, if you got a list of country codes from wikipedia, add the URL to a comment.

#### Working around a bug?

Trying to work around a bug? Just link to the bug URL. Someone in the future can see if the bug's been fixed or not and delete the workaround.

## CSS & HTML specific

- Most CSS and HTML belongs in svelte components. Some 'sensible defaults' (styles for text, forms etc that isn't specific to a component) are in `/public/css`.
- Use CSS grid as the preferred `display` value. No need for floats, clearfix hacks, table cell hacks, negative margin hacks, etc.
- Use element classes, avoid element IDs - they end up on the JS `window` object
- Put visual information in CSS, so appearance can be edited and found consistently in CSS.
  `class="login"` (and CSS determines what `login` looks like) not `class="login big blue"`
- Don't add a fake link with a fake destination as a click handler (`a` elements). Use 'button' elements. You can of course use anchors for links.
