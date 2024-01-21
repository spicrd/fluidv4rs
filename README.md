# fluidV4rs
Create responsiveness inside your CSS variables

## What is it?
Fluidv4rs is a SCSS mixin that allow the creation of CSS variables that each contains a responsive value for the it's utility, it can be a font-size, a spacing, etc... Those variables will make the responsive part of a Front-end project easier to do.

## How it works
The mixin takes a minimum and maximum value as well as a minimum and maximum viewport value. Those values represent pixels, but will be converted to rem in the variable. The responsiveness of those variables is due to a calculation inside a clamp, the formula is:
```
min + (max - min) * ((100vw - minViewport) / (maxViewport - minViewport))
```

and this is translated into scss as follows:
```scss
--#{varName}: clamp(#{min}rem, #{$min}rem + (#{$max} - #{$min}) * ((100vw - #{$minViewport}rem) / (#{$maxViewport} - #{$minViewport})), #{max}rem);
```

The mixin must be initialized inside the `:root {...}` selector. It takes two params: a name for the variables and a list of every variable value needed. The list takes strings or maps and can be mixed.

### String
The string value will be two or four numbers separated by dashes. The numbers represent the minimum value, maximum value, minimum viewport value and maximum viewport value. For example, it can be `"16-200"` or `"16-200-320-1920"`.
```
"min-max-minViewport-maxViewport"
```

### Map
There is six properties that exist for a map value.
| Property | Type | Example | Description |
| -------- | ---- | ------- | ----------- |
| name | string | "custom-name" | A custom name that replace the default one |
| min | number | 30 | The minimum value |
| max | number | 50 | The maximum value |
| minViewport | number | 320 | The minimum viewport value |
| maxViewport | number | 1920 | The maximum viewport value |
| values | string | "30-50-320-1920" | It is a shorthand for the min, max, minViewport and maxViewport |
> **Warning:** `values` can not be use with one of the following properties: `min`, `max`, `minViewport` and `maxViewport`.

## How to use it
Import the `core.scss` and `function.scss` in your project.

> :memo: **Note**
You can change the default minimum and maximum viewport value in `core.scss`.

> :memo: **Note**
The default rem value used in the formula is 16. You can change it if your base font-size is not that value.

In a SCSS file, declare the root selector, import fluidv4rs and use it with the right arguments. Here's an example that cover every use case.
Example:
```scss
:root {
    @include fluidV4rs("spacing",
        "50-200",
        "50-210-320-1920",
        (
            "min": 50,
            "max": 220,
        ), 
        (
            "min": 50,
            "max": 230,
            "minViewport": 320,
            "maxViewport": 1920,
        ),
        (
            "name": "custom-spacing-1",
            "min": 50,
            "max": 240,
            "minViewport": 320,
            "maxViewport": 1920,
        ),
        (
            "name": "custom-spacing-2",
            "values": "50-250-320-1920",
        ),
        (
            "values": "50-260-320-1920"
        )
    );
}

.container {
    padding-right var(--spacing-50-200);
    padding-left var(--spacing-50-210-320-1920);
    padding-top var(--custom-spacing-1);
    padding-bottom var(--spacing-50-260-320-1920);
}
```
Output:
```css
:root {
    --spacing-50-200: clamp(3.125rem, 3.125rem + 9.375 * ((100vw - 37.5rem) / 82.5), 12.5rem);
    --spacing-50-210-320-1920: clamp(3.125rem, 3.125rem + 10 * ((100vw - 20rem) / 100), 13.125rem);
    --spacing-50-220: clamp(3.125rem, 3.125rem + 10.625 * ((100vw - 37.5rem) / 82.5), 13.75rem);
    --spacing-50-230-320-1920: clamp(3.125rem, 3.125rem + 11.25 * ((100vw - 20rem) / 100), 14.375rem);
    --custom-spacing-1: clamp(3.125rem, 3.125rem + 11.875 * ((100vw - 20rem) / 100), 15rem);
    --custom-spacing-2: clamp(3.125rem, 3.125rem + 12.5 * ((100vw - 20rem) / 100), 15.625rem);
    --spacing-50-260-320-1920: clamp(3.125rem, 3.125rem + 13.125 * ((100vw - 20rem) / 100), 16.25rem);
}

.container {
    padding-right var(--spacing-50-200);
    padding-left var(--spacing-50-210-320-1920);
    padding-top var(--custom-spacing-1);
    padding-bottom var(--spacing-50-260-320-1920);
}
```

