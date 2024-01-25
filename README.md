# fluidv4rs
Create responsiveness inside your CSS variables!

## What is it?
Fluidv4rs is a SCSS mixin that allow the creation of CSS variables. Each contains a responsive value, it can be useful for font-size, spacing, etc... Those variables will make the responsive part of a Front-end project easier to do.

## How it works
The mixin takes four arguments: a default name, a default minimum viewport value, a default maximum viewport, and a list of strings, maps or both:
```scss
@mixin fluidv4rs($name, $minViewport, $maxViewport, $items...) { ... }
```
The items is a list composed of a minimum and maximum value as well as a minimum and maximum viewport value. Those values represent pixel, but will be converted to rem in the variable. The responsiveness of those variables is due to a calculation inside a clamp, the formula is:
```
min + (max - min) * ((100vw - minViewport) / (maxViewport - minViewport))
```

and this is translated into scss as follows:
```scss
--#{varName}: clamp(#{min}rem, #{$min}rem + (#{$max} - #{$min}) * ((100vw - #{$minViewport}rem) / (#{$maxViewport} - #{$minViewport})), #{max}rem);
```

The mixin must be initialized inside a selector.

### String
The string can contains a custom name at the beginning followed by two or four numbers. Everything is separated by slash.The numbers represent the minimum value, maximum value, minimum viewport value and maximum viewport value. For example, it can be `"custom-name/16/200"`, `"16/200"`, `"custom-name/16/200/320/1920"` or `"16-200-320-1920"`.
```
name/min/max/minViewport/maxViewport
```

### Map
There's six properties that exist for a map value.
| Property | Type | Example | Description |
| -------- | ---- | ------- | ----------- |
| name | string | "custom-name" | A custom name that replace the default one |
| min | number | 30 | The minimum value |
| max | number | 50 | The maximum value |
| minViewport | number | 320 | The minimum viewport value |
| maxViewport | number | 1920 | The maximum viewport value |

## How to use it
Import the core.scss and function.scss in your project.

> :memo: **Note:** The default rem value used in the formula is 16. You can change it if your base font-size is not that value.

In a SCSS file, declare the root selector, import fluidv4rs and use the right arguments. Here's an example that cover every use case:
```scss
:root {
    @include fluidv4rs("spacing", 600, 1920,
        "50/200",
        "custom-name-1/50/210",
        "50/220/320/1920",
        "custom-name-2/50/230/320/1920",
        (
            "min": 50,
            "max": 240,
        ), 
        (
            "min": 50,
            "max": 250,
            "minViewport": 320,
            "maxViewport": 1920,
        ), 
        (
            "name": "custom-name-3",
            "min": 50,
            "max": 260,
            "minViewport": 320,
            "maxViewport": 1920,
        ),
    );
}

.container {
    padding-right: var(--spacing-50-200);
    padding-left: var(--spacing-50-220-320-1920);
    padding-top: var(--custom-name-1);
    padding-bottom: var(--custom-name-2);
}
```

Output:
```css
:root {
    --spacing-50-200: clamp(3.125rem, 3.125rem + 9.375 * ((100vw - 37.5rem) / 82.5), 12.5rem);
    --custom-name-1: clamp(3.125rem, 3.125rem + 10 * ((100vw - 37.5rem) / 82.5), 13.125rem);
    --spacing-50-220-320-1920: clamp(3.125rem, 3.125rem + 10.625 * ((100vw - 20rem) / 100), 13.75rem);
    --custom-name-2: clamp(3.125rem, 3.125rem + 11.25 * ((100vw - 20rem) / 100), 14.375rem);
    --spacing-50-240: clamp(3.125rem, 3.125rem + 11.875 * ((100vw - 37.5rem) / 82.5), 15rem);
    --spacing-50-250-320-1920: clamp(3.125rem, 3.125rem + 12.5 * ((100vw - 20rem) / 100), 15.625rem);
    --custom-name-3: clamp(3.125rem, 3.125rem + 13.125 * ((100vw - 20rem) / 100), 16.25rem);
    --fontsize-36-60: clamp(2.25rem, 2.25rem + 1.5 * ((100vw - 37.5rem) / 82.5), 3.75rem);
    --fontsize-28-48-400-1800: clamp(1.75rem, 1.75rem + 1.25 * ((100vw - 25rem) / 87.5), 3rem);
}

.container {
    padding-right: var(--spacing-50-200);
    padding-left: var(--spacing-50-220-320-1920);
    padding-top: var(--custom-name-1);
    padding-bottom: var(--custom-name-2);
}
```

