keyframes.less
==

CSS keyframes generator for LESS (http://lesscss.org/). Works with:

- **less.js** 0.3.1+ and tools based on it (Node.js usage, WinLess, etc)
- **lessphp** 0.4.0+ (http://leafo.net/lessphp/)

Inspired with tools and solutions like:
- https://github.com/kuus/animate-me.less/
- http://stackoverflow.com/a/16147018
- http://stackoverflow.com/a/14853591

Key features
------------

- Cross-browser keyframes generation *(Firefox 5+, Chrome 3+, Safari 4+, Opera 12+, IE 10+)*
- Up to 16 timepoints in each `keyframes` rule (and the number can be easily augmented, if needed)
- Mixins, variables and functions can be used for styling timepoints
- Keyframes are created separately from `animation` rules, so:
    - multiple `animation` rules can use the same keyframe with different values for timing, repeating, etc,
    - multiple animations can be used within same `animation` rule
    - animations can be applied (not created!) inside any parent selector
- Lightweight and (almost) neat LESS code

Current limitations
-------------------
*Please fork and fix!*

- Keyframes cannot be generated inside any parent selector (top-level only)
- First timepoint must have at least one resulting style rule
- Resulting CSS code is not so neat:
    - some newlines are missing in *lessphp* version,
    - timepoint indents missing in both versions,
    - some necessary hacks produced (`/* fully commented ugliness */` for *less.js* version, and null-selector `x:not(x)` for *lessphp* version)

Usage examples (included)
-------------------------


**Download and import the library** into your LESS file (for *lessphp*, use `keyframes_lessphp.less` instead):
 
```less
@import 'keyframes.less';
```

For using keyframes, assuming you also have an animation LESS mixin like this (not included in library):

```less
.animation(@value) {
    -webkit-animation: @value;
    -moz-animation: @value;
    -o-animation: @value;
    animation: @value;
}
```


*Example 1.* **Basic usage**

```less
// Preparing styles for animation points
.keyframes-item(fadeIn, 0%) {
    opacity: 0;
}
.keyframes-item(fadeIn, 100%) {
    opacity: 1;
}
// Generating keyframes
.keyframes(fadeIn);

// Applying animation to fade-in block in 1.5 seconds
.myBlock {
    .animation(fadeIn 1.5s);
}
```


*Example 2.* **Three animation points**

```less
// Now we need styles for more points
.keyframes-item(colorChange, 0%) {
    color: red;
}
.keyframes-item(colorChange, 37%) {
    color: green;
}
.keyframes-item(colorChange, 100%) {
    color: blue;
}
// Generating keyframes (note the timepoints other than 0%, 100% must be passed explicitly)
.keyframes(colorChange, 0%, 37%, 100%);

.parentBlock {
    // Yes, you can APPLY (not create!) keyframes animation at any level of selectors tree
    .colorBlock {
        .animation(colorChange 2s);
    }
}
```


*Example 3.* **Hold opacity at 0, then fade-in**

```less
// (Assuming you've also generated fadeIn keyframes from example #1)

// Note '0%, 100%' are passed as SINGLE param, and so quouted together
// Even replacing comma with semicolon doesn't help
.keyframes-item(holdTranparent, '0%, 100%') {
    opacity: 0;
}
// Note the empty string ('') passed as second param
.keyframes(holdTranparent, '0%, 100%', '');

// Note the semicolon at the end of multiple animations list
.wait-2s-then-appear {
    .animation(holdTransparent 2s, fadeIn 1s 2s ease-out;);
}
```


*Example 4.* **Some crazy animation**

```less
.keyframes-item(crazy, 0%) {
    // first item cannot be empty, or keyframes could not be generated
    font-size: 72em;
}
.keyframes-item(crazy, 12%) {
    .my-mixin(round(@myVar)); // you can use mixins, functions and variables
}
.keyframes-item(crazy, 21%) {
    // more
}
.keyframes-item(crazy, 29%) {
    // and more
}
.keyframes-item(crazy, 36.6%) {
    // you can use fractional percents (if you really want to)
}
.keyframes-item(crazy, 62.5%) {
    // but anyway, it must have a percent sign!
}

// (there could be more; up to 16 timepoints in all)

.keyframes-item(crazy, 100%) {
    // finally!
}

// Generating the keyframe rules... ufff!
.keyframes(crazy, 0%, 12%, 21%, 29%, 36.6%, 62.5%, 100%);

// And applying this (and maybe more) animations at some poor element
.crazy-jumper {
    // (some other styles here, maybe)
    // Repeat our crazy animation for 44 times, simultaneously fading that in for 18 times and changing colors
    .animation(
        crazy 10s 44,
        fadeIn 0.9s ease-in-out 18,
        colorChange 4.5s 2s cubic-bezier(0.42, 0, 0.58, 1) infinite;
    );
}
```
