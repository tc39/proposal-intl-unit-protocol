---
marp: true
theme: default
paginate: true
footer: 'Intl Unit Protocol - Stage 2 Update'
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

A number ought to be annotated with the quantity it is measuring. The unit is a core part of the data model to be formatted, unlocking i18n features such as locale unit preferences and automatic unit conversion:

```javascript
const message = "You are {$distance :unit usage=road} from your destination";
let formatter = new MessageFormat("en", message);
formatter.format({
  distance: /* what goes here? */
});
```

- A number with a unit is the only data type required by MessageFormat 2.0 without a JavaScript analog.
- Defines how `Amount` and third-party classes interface with `Intl`.

---

## Proposal Recap

Add a protocol to `Intl.NumberFormat.prototype.format` (and `Intl.PluralRules.prototype.select`) accepting an options bag with `value` and `unit`:

```javascript
// Current:
let formatter = new Intl.NumberFormat(locale, {
  style: "unit",
  unit,
});
let result = formatter.format(value);

// Proposed:
let formatter = new Intl.NumberFormat(locale, {
  style: "unit",
});
let result = formatter.format({
  value,
  unit,
});
```

---

## Issue #5: Protocol sniffing behavior

`Intl.NumberFormat.prototype.format` has always called `ToPrimitive` on Object arguments. Unconditionally reading `.value` on all Objects would break existing code relying on `Symbol.toPrimitive` or `valueOf`.

To determine whether an Object implements the protocol, perform `Get` on both `"value"` and `"unit"` and check that neither is `undefined`:

```
1. Let value be input.
2. Let inputUnit be undefined.
3. If input is an Object, then
   a. Let candidateValue be ? Get(input, "value").
   b. Let candidateInputUnit be ? Get(input, "unit").
   c. If candidateValue is not undefined and candidateInputUnit is not undefined, then
      i. Set value to candidateValue.
      ii. Set inputUnit to candidateInputUnit.
4. Let intlMV be ? ToIntlMathematicalValue(value).
```

---

## Issue #5: `unit: null` vs `unit: undefined`

Dimensionless objects in the protocol use `unit: null`:

```javascript
let formatter = new Intl.NumberFormat(locale, { style: "unit" });
formatter.format({
  value: 333,
  unit: null,
}); // "333"
```

If either `value` or `unit` is absent or `undefined`, revert to calling `Symbol.toPrimitive` on the input object:

```javascript
formatter.format({
  value: 333,
  unit: undefined,
  [Symbol.toPrimitive](hint) {
    return hint === "number" ? 111 : 222;
  },
}); // "111"
```

---

## Open Question: Non-unit formatters (#7)

`Intl.NumberFormat` `style` can be `"decimal"`, `"percent"`, `"currency"`, or `"unit"`. What should happen if a protocol object is passed to a non-unit, non-currency formatter?

```javascript
const nf = new Intl.NumberFormat("en"); // default: style "decimal"
nf.format({ value: 333, unit: null });
```

1. Throw a `TypeError` because the formatter was not configured with `style: "unit"` or `"currency"` (current spec text).
2. Ignore the `null` unit and format as `"333"`.
3. Only check for the protocol when `style` is `"unit"` or `"currency"`, falling back to `Symbol.toPrimitive` otherwise (also resolves [#8](https://github.com/tc39/proposal-intl-unit-protocol/issues/8) on when to type-check `inputUnit`).

---

<!-- 
_class: lead 
_backgroundColor: #2c3e50
_color: #ffffff
-->

# Discussion
