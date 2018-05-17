# sass-helpers
**WIP**

A library of helpers and mixins for helping with building responsive stylesheets.

## Breakpoints

### add-breakpoint
Defines a breakpoint to be called using the breakpoint mixin at a later point.

| Argument    | Type   | Optional | Example        |
| ----------- | ------ | -------- | -------------- |
| $name       | string | false    | `mobile`       |
| $dimensions | string | false    | `768px 1024px` |

#### Usage
```scss
$mobile: 420px;
$tablet: 768px;
$tablet-landscape: 1024px;
$desktop: 1366px;
$desktop-large: 1560px;

@include add-breakpoint('mobile', 0px $mobile);
@include add-breakpoint('mobile-large', $mobile $tablet);
@include add-breakpoint('tablet', $tablet $tablet-landscape);
@include add-breakpoint('tablet-landscape', $tablet-landscape $desktop);
@include add-breakpoint('desktop', $desktop $desktop-large);
@include add-breakpoint('desktop-large', $desktop-large 99999px);
```
---
### breakpoint
Generates a breakpoint and wraps your rule declaration or properties with it.

| Argument   | Type   | Optional | Example |
| ---------- | ------ | -------- | ------- |
| $name      | string | false    | `mobile`|
| $direction | string | false    | `up`    |

#### Usage
```scss
@include breakpoint(tablet, down) {
  .cls {
    background: red;
  }
}

.cls-2 {
  @include breakpoint(tablet, up) {
    color: green;
  }
  
  @include breakpoint(tablet, below) {
    color: purple;
  }
}

@include breakpoint(tablet-portrait, above) {
  .cls-3 {
    text-decoration: underline;
  }
}
```

##### will render:
```css
@media (max-width: 1023px) {
  .cls {
    background: red;
  }
}

@media (min-width: 768px) {
  .cls-2 {
    color: green;
  }
}

@media (max-width: 767px) {
  .cls-2 {
    color: purple;
  }
}

@media (min-width: 1367px) {
  .cls-3 {
    text-decoration: underline;
  }
}
```
---

## Fluid Properties
These helpers will output calculations and clamping breakpoints to blend a CSS property between two defined points, and two clamping positions.

### fluid-property

| Argument            | Type    | Optional | Default      | Example      |
| ------------------- | ------- | -------- | ------------ | ------------ |
| $property           | string  | false    | n/a          | `margin-top` |
| $min                | string  | false    | n/a          | `10px`       |
| $max                | string  | false    | n/a          | `20px`       |
| $clip-low           | string  | true     | `420px`      | `768px`      |
| $clip-high          | string  | true     | `1366px`     | `1024x`      |
| $clip               | boolean | true     | `true`       | `false`      |
| $clip-at-start      | boolean | true     | `true`       | `true`       |
| $clip-at-end        | boolean | true     | `true`       | `true`       |
| $viewport-direction | string  | true     | `horizontal` | `vertical`   |

#### Usage
```scss
.cls {
  @include fluid-property(margin-top, 10px, 20px);
  @include fluid-property(padding-bottom, 5px, 20px, $clip-high: 800px, $clip-at-start: false, $viewport-direction: 'vertical');
}
```

##### will render:
```css
.cls {
  margin-top: calc(10px + 10 * ((100vw - 420px) / 946));
  padding-bottom: calc(5px + 15 * ((100vh - 420px) / 380));
}

@media (max-width: 420px) {
  .cls {
    margin-top: 10px; 
  }
}

@media (min-width: 1366px) {
  .cls {
    margin-top: 20px;
  }
}

@media (min-height: 800px) {
  .cls {
    padding-bottom: 20px; 
  } 
}
```
---

### fluid-font-size
Will output fluid font sizes based on a [modular scale](#modular-scale) system, driven by your [base font size](#base-font-size). This system works by providing an upper and a lower [ratio](#ratios) and a multiplier for your font size.

#### fluid-font-size helper arguments

| Argument             | Type    | Optional | Default           | Example      |
| -------------------- | ------- | -------- | ----------------- | ------------ |
| $low-multiplier      | number  | false    | n/a               | `1.5`        |
| $high-multiplier     | number  | true     | `$low-miltiplier` | `2`          |
| $clip-low            | string  | true     | `420px`           | `768px`      |
| $clip-high           | string  | true     | `1366px`          | `1024x`      |
| $modular-scale-small | number  | true     | `$major-second`   | `1.125`		   |
| $modular-scale-large | number  | true     | `$perfect-fourth` | `1.333`      |
| $clip                | boolean | true     | `true`            | `false`      |
| $clip-at-start       | boolean | true     | `true`            | `true`       |
| $clip-at-end         | boolean | true     | `true`            | `true`       |
| $output-px           | boolean | true     | `false`           | `true`       |


#### Example
```scss
h1 {
	@include fluid-font-size(4);
}
h2 {
	@include fluid-font-size(3);
}
h3 {
	@include fluid-font-size(2);
}
h4 {
	@include fluid-font-size(1);
}
h5 {
	@include fluid-font-size(0);
}
h6 {
	@include fluid-font-size(-1);
}
```

##### will create a fluid property that blends between the two sizes:

(With a [base font size](#base-font-size) of 16px and default modular scale settings (1.125, 1.333)

| Multiplier | Font Size Small          | Font Size Large          |
| ---------- | ------------------------ | ------------------------ |
| 0          | 1rem (16px)              | 1rem (16px)              |
| 1          | 1.125rem (18px)          | 1.333rem (21.3px)        |
| 2          | 1.265625rem (20.3px)     | 2.368593037rem (28.4px)  |
| 3          | 1.423828125rem (22.8px)  | 3.1573345183rem (37.9px) |
| 4          | 1.6018066406rem (25.6px) | 4.2087269129rem (50.5px) |
| -1         | 0.8888888889rem (14.2px) | 0.8888888889rem (12px)   |

[Demo example - resize browser window to see in effect](https://codepen.io/bameyrick/full/EvxoQx/)

---
### modular-scale
The modular scale helper is used by the [fluid-font-size](#fluid-font-size) helper to generate font size based of your [base font size](#base-font-size), a supplied multiplier, and a modular scale [ratio](#ratios).

| Argument | Type    | Optional | Default     | Example |
| -------- | ------- | -------- | ----------- | ------- |
| $scale   | number  | false    | n/a         | `1.125` |     
| $amount  | number  | false    | n/a         | `2`     |
| $size    | string  | true     | 1rem (16px) | `1rem`  |

---
## get-unit
Will return the unit for a value.

| Argument     | Type   | Example |
| ------------ | ------ | ------- |
| $measurement | string | `24px`  |

#### Example
```scss
$unit: @get-unit(2em); // => em
```
---

## Margins
Will generate [fluid](#fluid-property) margins.

### Available helpers
* margin
* margin-horizontal
* margin-vertical
* margin-top
* margin-bottom
* margin-right
* margin-left

| Argument                                                            | Type    | Optional | Default  | Example |
| ------------------------------------------------------------------- | ------- | -------- | -------- | ------- |
| $small                                                              | string  | true     | `15px`   | `10px`  |
| $large                                                              | string  | true     | `30px`   | `20px`  |
| $vretical (not on margin-horizontal, margin-left, and margin-right) | boolean | true     | `true`   | `false` |
| $narrow (will half $small and $large)                               | boolean | true     | `false`  | `true`  |
| $clip-low                                                           | string  | true     | `420px`  | `768px` |
| $clip-high                                                          | string  | true     | `1366px` | `1024x` |
| $clip                                                               | boolean | true     | `true`   | `false` |
| $clip-at-start                                                      | boolean | true     | `true`   | `true`  |
| $clip-at-end                                                        | boolean | true     | `true`   | `true`  |

#### Usage
```scss
.my-cls {
  @include margin-horizontal($narrow: true);
}

.my-cls-2 {
  @include margin-top(10px, 20px);
}
```

##### will render:
```css
.my-cls {
  margin-left: calc(7.5px + 7.5 * ((100vw - 420px) / 946));
  margin-right: calc(7.5px + 7.5 * ((100vw - 420px) / 946));
}

.my-cls-2 {
  margin-top: calc(10px + 10 * ((100vh - 420px) / 946));
}

@media (min-width: 1366px) {
    .my-cls {
      margin-left: 15px;
    }
    
    .my-cls {
      margin-right: 15px;
    }
}

@media (max-width: 420px) {
    .my-cls {
      margin-left: 7.5px;
    }
    
    .my-cls {
      margin-right: 7.5px;
    }
}

@media (max-height: 420px) {
    .my-cls-2 {
      margin-top: 10px;
    }
}

@media (min-height: 1366px) {
    .my-cls-2 {
      margin-top: 20px;
    }
}
```
---

## Paddings
Will generate [fluid](#fluid-property) paddings.

### Available helpers
* padding
* padding-horizontal
* padding-vertical
* padding-top
* padding-bottom
* padding-right
* padding-left

| Argument                                                               | Type    | Optional | Default  | Example |
| ---------------------------------------------------------------------- | ------- | -------- | -------- | ------- |
| $small                                                                 | string  | true     | `15px`   | `10px`  |
| $large                                                                 | string  | true     | `30px`   | `20px`  |
| $vretical (not on padding-horizontal, padding-left, and padding-right) | boolean | true     | `true`   | `false` |
| $narrow (will half $small and $large)                                  | boolean | true     | `false`  | `true`  |
| $clip-low                                                              | string  | true     | `420px`  | `768px` |
| $clip-high                                                             | string  | true     | `1366px` | `1024x` |
| $clip                                                                  | boolean | true     | `true`   | `false` |
| $clip-at-start                                                         | boolean | true     | `true`   | `true`  |
| $clip-at-end                                                           | boolean | true     | `true`   | `true`  |

#### Usage
```scss
.my-cls {
  @include padding-horizontal($narrow: true);
}

.my-cls-2 {
  @include padding-top(10px, 20px);
}
```

##### will render:
```css
.my-cls {
  padding-left: calc(7.5px + 7.5 * ((100vw - 420px) / 946));
  padding-right: calc(7.5px + 7.5 * ((100vw - 420px) / 946));
}

.my-cls-2 {
  padding-top: calc(10px + 10 * ((100vh - 420px) / 946));
}

@media (min-width: 1366px) {
    .my-cls {
      padding-left: 15px;
    }
    
    .my-cls {
      padding-right: 15px;
    }
}

@media (max-width: 420px) {
    .my-cls {
      padding-left: 7.5px;
    }
    
    .my-cls {
      padding-right: 7.5px;
    }
}

@media (max-height: 420px) {
    .my-cls-2 {
      padding-top: 10px;
    }
}

@media (min-height: 1366px) {
    .my-cls-2 {
      padding-top: 20px;
    }
}
```
---

## px-to-rem
Will convert px values to rem based on your [base font size](#base-font-size)

| Argument | Type   | Example |
| -------- | ------ | ------- |
| $size    | string | `24px`  |

#### Example
```scss
.my-cls {
  font-size: @px-to-rem(24px);
}
```

##### will render:
```css
.my-cls {
    font-size: 1.5rem;
}
```
---


## rem-to-px
Will convert rem values to px based on your [base font size](#base-font-size)

| Argument | Type   | Example  |
| -------- | ------ | -------- |
| $size    | string | `1.8rem` |

#### Example
```scss
.my-cls {
  font-size: @rem-to-px(1.8rem);
}
```

##### will render:
```css
.my-cls {
    font-size: 28.8px;
}
```
---

## remove-unit
Will remove units from values leaving you with only the number

| Argument     | Type   | Example |
| ------------ | ------ | ------- |
| $measurement | string | `24px`  |

#### Example
```scss
$font-size-no-unit: @remove-unit(24px); // => 24
```
---

## str-replace
Will replace matches in a string

| Argument | Type    | Optional | Default      | Example |
| -------- | ------- | -------- | ------------ | ------- |
| $string  | string  | false    | n/a          | `40rem` |     
| $search  | string  | false    | n/a          | `rem`   |
| $replace | string  | true     | Empty string | `px`  |

#### Example
```scss
$replaced-string: @str-replace('40rem', 'rem', 'px'); // => 40px;
```
---
