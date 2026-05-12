# sass-error

Sass utility functions for building error messages — following the [Error Message Specification](https://github.com/nicholasgillespie/sass-error-spec).

---

## Message Format

As defined by the specification, every error message is a single structured string composed of a machine-readable code and a human-readable message.

```scss
[CODE] <EntityType> "<EntityName>" [@ <Path>]: <Issue> → <Action> [context: ...]
```

### Example

```scss
[TOKEN_TIER_VALUE] Token "font-weight" @ settings > tier: Invalid value "random" → Allowed: primitive | semantic | component
```

> **Note:** See the **[Error Message Specification](https://github.com/nicholasgillespie/sass-error-spec)** for a step-by-step guide on how to construct error messages.

---

## Installation

```sh
npm install sass-error sass-funcs
```

> Requires [`sass`](https://www.npmjs.com/package/sass) or [`sass-embedded`](https://www.npmjs.com/package/sass-embedded) `>= 1.33.0` — install one, not both (`sass-embedded` recommended). Also requires [`sass-funcs`](https://github.com/nicholasgillespie/sass-funcs), used internally.

---

## Configuration

All constants are configurable via `@use ... with (...)`:

```scss
@use 'sass-error' as e with (
  $PATH_SEPARATOR: '/',
  $EXPECTED_LIMIT: 3,
  $EXPECTED_MORE_LABEL: 'plus'
);
```

| Variable               | Default  | Description                                |
| ---------------------- | -------- | ------------------------------------------ |
| `$PATH_PREFIX`         | `' @ '`  | Prefix before the path string              |
| `$PATH_SEPARATOR`      | `' > '`  | Separator between path segments            |
| `$PATH_ID_PLACEHOLDER` | `'<id>'` | Placeholder when the key itself is invalid |
| `$EXPECTED_LIMIT`      | `5`      | Max values shown before truncating         |
| `$EXPECTED_SEPARATOR`  | `' \| '` | Separator between values                   |
| `$EXPECTED_MORE_LABEL` | `'more'` | Label for the remaining count              |

---

## [Module: Error](index.scss)

```scss
@use 'sass-error' as e;
```

| Function                                            | Description                                    |
| --------------------------------------------------- | ---------------------------------------------- |
| [`path`](#epathpath-is-id-error-separator)          | Builds a formatted path string from segments   |
| [`received`](#ereceivedvalue-quote-empty)           | Renders a received value as a string           |
| [`expected`](#eexpecteditems-extra-limit-separator) | Formats expected values into a readable string |

---

### `e.path($path, $is-id-error, $separator)`

Builds a formatted path string from path segments for use in error messages.

| Parameter      | Type             | Default           | Description                                           |
| -------------- | ---------------- | ----------------- | ----------------------------------------------------- |
| `$path`        | `List \| String` |                   | Path segments, or a single value normalised to a list |
| `$is-id-error` | `Bool`           | `false`           | Replaces the last segment with `<id>` when `true`     |
| `$separator`   | `String`         | `$PATH_SEPARATOR` | Separator placed between path segments                |

**Returns** `String` — path string prefixed with `$PATH_PREFIX`, or `''` if path is empty.

```scss
e.path(('settings', 'tier'))           // → ' @ settings > tier'
e.path(('values', 'core', ''), true)   // → ' @ values > core > <id>'
e.path(())                             // → ''
```

---

### `e.received($value, $quote-empty)`

Renders a received value as a string for use in error messages.

| Parameter      | Type   | Default | Description                                                       |
| -------------- | ------ | ------- | ----------------------------------------------------------------- |
| `$value`       | `*`    |         | Value to render                                                   |
| `$quote-empty` | `Bool` | `false` | Wraps empty or whitespace-only strings in double quotes when true |

**Returns** `String` — strings returned as-is, non-empty lists wrapped in parens, all others inspected.

```scss
e.received('hello')      // → 'hello'
e.received(42)           // → '42'
e.received((a, b, c))    // → '(a, b, c)'
e.received('', true)     // → '""'
```

---

### `e.expected($items, $extra, $limit, $separator)`

Formats a list of expected values into a readable string for error messages.

| Parameter    | Type                                                | Default               | Description                                              |
| ------------ | --------------------------------------------------- | --------------------- | -------------------------------------------------------- |
| `$items`     | `List \| String`                                    |                       | Expected values; normalised to a list if a single value  |
| `$extra`     | `String \| Number \| Bool \| Color \| List \| Null` | `null`                | Additional value(s) appended after the main list         |
| `$limit`     | `Number \| Null`                                    | `$EXPECTED_LIMIT`     | Max items shown before truncating with a remaining count |
| `$separator` | `String`                                            | `$EXPECTED_SEPARATOR` | Separator placed between values                          |

**Returns** `String` — joined expected string, truncated to `$limit` items if the count is exceeded.

```scss
e.expected(('foo', 'bar', 'baz'))                       // → 'foo | bar | baz'
e.expected(('a', 'b', 'c', 'd', 'e', 'f'), $limit: 3)   // → 'a | b | c | ... 3 more'
e.expected(('foo', 'bar'), $extra: 'baz')               // → 'foo | bar | baz'
```

---

## Migration

### v1 → v2

- `print()` renamed to `received()`
- `options()` renamed to `expected()`
- `$ERROR_LABEL_ENTRY`, `$ERROR_LABEL_FIELD`, `$ERROR_ENTRY_LABELS` removed — moved to [`sass-valid`](https://github.com/nicholasgillespie/sass-valid)

---

[Back to top](#sass-error)
