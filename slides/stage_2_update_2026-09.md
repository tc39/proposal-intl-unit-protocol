---
marp: true
theme: default
paginate: true
footer: 'Intl Unit Protocol - Stage 2 Update'
style: |
  .columns {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 1.5rem;
  }
---

<!-- 
_class: lead 
_backgroundColor: #2c3e50
_color: #ffffff
-->

# Intl Unit Protocol
## Stage 2 Update
### September 2026 TC39

---

## Background & Motivation

<div class="columns">
<div>

A number ought to be annotated with the quantity it is measuring.

- The unit is part of the data model, not just a formatting style.
- Only MessageFormat input without a native JS type.
- Unlocks locale unit preferences and automatic unit conversion.
- Defines how `Amount` and third-party classes interface with `Intl`.

</div>
<div>

```javascript
const message =
  "You are {$distance :unit " +
  "usage=road} from destination";

let formatter =
  new MessageFormat("en", message);

formatter.format({
  distance: /* what goes here? */
});
```

</div>
</div>

---

## Proposal Recap

<div class="columns">
<div>

### Current: Constructor Option

```javascript
let formatter =
  new Intl.NumberFormat(locale, {
    style: "unit",
    unit,
  });

let result =
  formatter.format(value);
```

</div>
<div>

### Proposed: Format Protocol

```javascript
let formatter =
  new Intl.NumberFormat(locale, {
    style: "unit",
  });

let result =
  formatter.format({
    value,
    unit,
  });
```

</div>
</div>

---

## Issue #5: Protocol sniffing behavior

- `format()` historically coerces Object arguments via `ToPrimitive`. Unconditionally reading `.value` breaks existing objects relying on `Symbol.toPrimitive` or `valueOf`.
- Solution: Perform `Get` on `"value"` and `"unit"`. Only activate the protocol if neither is `undefined`:

```
1. Let _value_ be _input_.
2. Let _unit_ be *undefined*.
3. If _input_ is an Object, then
  a. Let _protocolValue_ be ? Get(_input_, *"value"*).
  b. Let _protocolUnit_ be ? Get(_input_, *"unit"*).
  c. If _protocolValue_ is not *undefined* and _protocolUnit_ is not *undefined*, then
    i. Set _value_ to _protocolValue_.
    ii. Set _unit_ to _protocolUnit_.
```

---

## Issue #5: `unit: null` vs `unit: undefined`

<div class="columns">
<div>

### Dimensionless: `unit: null`

```javascript
let formatter =
  new Intl.NumberFormat(locale, {
    style: "unit",
  });

formatter.format({
  value: 333,
  unit: null,
});
// "333"
```

`null` is used for a number with explicitly no unit.

</div>
<div>

### Fallback: `unit: undefined`

```javascript
formatter.format({
  value: 333,
  unit: undefined,
  [Symbol.toPrimitive](hint) {
    return hint === "number"
      ? 111 : 222;
  },
});
// "111"
```

No difference between explicit `undefined` and a missing field.

</div>
</div>

---

## Open Question: Non-unit formatters (#7)

<div class="columns">
<div>

What should happen when passing a protocol object to a non-unit/non-currency formatter?

```javascript
const nf =
  new Intl.NumberFormat("en");

nf.format({
  value: 333,
  unit: null,
  [Symbol.toPrimitive](hint) {
    return hint === "number"
      ? 111 : 222;
  },
});
```

</div>
<div>

1. **Throw `TypeError`**: Formatter was not configured with `style: "unit"` or `"currency"` (current spec text).
2. **Format as `"333"`**: Ignore the `null` unit as dimensionless.
3. **Format as `"111"`**: Only check for protocol when `style` is `"unit"` or `"currency"` (also resolves [#8](https://github.com/tc39/proposal-intl-unit-protocol/issues/8)).

</div>
</div>

---

<!-- 
_class: lead 
_backgroundColor: #2c3e50
_color: #ffffff
-->

# Discussion
