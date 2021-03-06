# Style Chemistry

Style Chemistry is a simple and open-ended system to help you keep your UI Component's styling consistent and harmonious.
The system acts as the **Single Source of Truth** of your stylesheet and is comprised of "Sets", the building blocks of the system, with 3 levels of abstraction: Elements, Compounds, and Mixtures.

- [Style Chemistry](#style-chemistry)
  - [Set and SuperSet](#set-and-superset)
    - [Set](#set)
      - [set](#set-1)
      - [selectors](#selectors)
      - [default](#default)
    - [SuperSet](#superset)
  - [Getters](#getters)
  - [Elements](#elements)
  - [Compounds](#compounds)
  - [Mixtures](#mixtures)

## Set and SuperSet

### Set

A Set is the "building block" of the Style Chemistry system. There are two modes for sets: "lenient" or "strict". It is a map that has 2 fields in "lenient" mode and 3 fields in "strict" mode: `set`, `selectors`, and `default`.

An example of a "lenient" Element Set:

```scss
$font-sizes: (
  'selectors': (
    'body.small': 14px,
    'body.normal': 16px,
    'body.large': 18px,
    'heading.small': 20px,
    'heading.normal': 24px,
  ),
  'default': 'body.small',
);
```

An example of a "strict" Element Set:

```scss
$font-sizes: (
  'set': (
    12px,
    14px,
    16px,
    18px,
    24px,
  ),
  'selectors': (
    'body.small': 2,
    'body.normal': 3,
    'body.large': 4,
    'heading.small': 4,
    'heading.normal': 5,
  ),
  'default': 3,
);
```

#### set

The **set** field is a list of "scalar" values that the getter functions will try to resolve value from.

#### selectors

The **selectors** field is a map. The key is a "selector" string and the value is an index pointing to a value inside **set**. The above example uses dot notation to add hierarchy, this is not restricted, you may use whatever naming convention you want. More than one selectors can point to the same value, notice that in the above example `body.large` and `heading.small` points to the same value, `18px`.

*In SCSS, the index starts with 1 and not 0.*

#### default

In "lenient" mode, the **default** field value is a selector key. In "strict" mode, the **default** field value is an index that points to a value inside **set**. This value is the default returned value if no selector or index is passed to the getter function.

For example:

```scss
@use 'elements/getters' as elements;

.body {
  // The getter function, font-size(),
  // will return 16px based on the Element Set example above.
  font-size: elements.font-size();
}
```

### SuperSet

A SuperSet is a map of Sets. They key is the "name" of the Set and the value is a Set.

The Element, colours, below is an example of a SuperSet:

```scss
$colors: (
  'paper': (
    'set': (
      #FFFFFF,
      #FAFAFA,
      #F5F5F5,
      #EFEFF0,
      #E4E5E7,
    ),
    'selectors': (
      'lighter': 1,
      'light': 2,
      'paper': 3,
      'dark': 4,
      'darker': 5,
    ),
    'default': 3,
  ),
  'ink': (
    'set': (
      #5C6370,
      #454A54,
      #2E3138,
      #121721,
    ),
    'selectors': (
      'lightest': 1,
      'lighter': 2,
      'light': 3,
      'ink': 4,
    ),
    'default': 4,
  ),
);
```

## Getters

Getters are functions that *selects* a value from a Set or SuperSet. Although getter functions can accept a set index, it is generally a good practice to use selectors when trying to resolve a value from a set.

## Elements

Elements are composed of, and restricted, to the following **16 Sets** and **1 SuperSet** (`colors`).
They are listed below, in canonical order, with their associated getter function names:

| Element          | Type     | Getter          |
| ---------------- | -------- | --------------- |
| colors           | SuperSet | color           |
| opacities        | Set      | opacity         |
| font-families    | Set      | font-family     |
| font-sizes       | Set      | font-size       |
| font-weights     | Set      | font-weight     |
| letter-spacings  | Set      | letter-spacing  |
| line-heights     | Set      | line-height     |
| border-styles    | Set      | border-style    |
| border-widths    | Set      | border-width    |
| radii            | Set      | radius          |
| spaces           | Set      | space           |
| heights          | Set      | height          |
| widths           | Set      | width           |
| breakpoints      | Set      | breakpoint      |
| z-indices        | Set      | z-index         |
| images           | Set      | image           |
| times            | Set      | time            |
| timing-functions | Set      | timing-function |

You don't have to define all of them in your project, you are free to pick and choose depending on your requirements.

## Compounds

"Compounds" is an optional level of abstraction above Elements.
Unlike Elements, Compounds are not restricted, which means you can define your own Sets and/or SuperSets.
In addition to this, you can use and interpolate Element Getters in your set values.
It is conventional to use *plural* to name your compounds and *singular* to name it's associated getter function.

An example of a Compound in "lenient" mode:

```scss
// compounds.scss

@use '../elements/getters' as elements;

$gradients: (
  'selectors': (
    'sunset': 'linear-gradient(90deg, #{elements.color('magenta', 'lighter')}, #{elements.color('indigo', 'light')})',
    'beach': 'linear-gradient(45deg, #{elememts.color('peach', 'light')}, #{elements.color('cyan', 'light')})',
  ),
  'default': 'sunset'
);
```

An example of a Compound in "strict" mode:

```scss
// compounds.scss

@use '../elements/getters' as elements;

$gradients: (
  'set': (
    'linear-gradient(90deg, #{elements.color('magenta', 'lighter')}, #{elements.color('indigo', 'light')})',
    'linear-gradient(45deg, #{elememts.color('peach', 'light')}, #{elements.color('cyan', 'light')})',
  ),
  'selectors': (
    'sunset': 1,
    'beach': 2,
  ),
  'default': 1,
);
```

## Mixtures

Mixtures is the final level of abstraction in Style Chemistry. Similar to Compounds, it is optional and you are free to define your own Sets and/or SuperSets, but now you can use Compound and Elements Getters in your set values. You can also define mixins to a specify a set of CSS rules instead of a getter function.

It is conventional to use *plural* to name your mixtures and *singular* to name it's associated getter function.

```scss
%normalize-fonts {
  font-size: elements.font-size('body.normal');
  line-height: elements.line-height('body.normal');
}
```
