# sass-error

Sass utility functions for building error message paths, value outputs, and options strings — following the [Error Message Authoring Specification](https://github.com/nicholasgillespie/sass-error-spec).

---

## The Convention

Every error message is a single structured string composed of a machine-readable code and a human-readable message.

```scss
[CODE] <EntityType> "<EntityName>" [@ <Path>]: <Issue> → <Action> [context: ...]
```

### Example in Practice

```scss
[TOKEN_TIER_VALUE] Token "font-weight" @ settings > tier: Invalid value "random" → Allowed: primitive | semantic | component
```

> **Note:** See the **[Error Message Authoring Specification](https://github.com/nicholasgillespie/sass-error-spec)** for a step-by-step breakdown of how each message is constructed.

---

## Installation

```sh
npm install sass-error sass-funcs
```

> Requires [`sass`](https://www.npmjs.com/package/sass) `>= 1.33.0` or [`sass-embedded`](https://www.npmjs.com/package/sass-embedded) `>= 1.33.0` — install one, not both. `sass-embedded` is recommended: it wraps a native Dart binary and is typically 20–30% faster to compile than the pure-JS `sass` package. Also requires [`sass-funcs`](https://github.com/nicholasgillespie/sass-funcs), used internally.

---

## Configuration

All constants are configurable via `@use ... with (...)`:

```scss
@use 'sass-error' as e with (
  $PATH_SEPARATOR: '/',
  $OPTIONS_LIMIT: 3,
  $OPTIONS_MORE_LABEL: 'plus'
);
```

| Variable               | Default              | Description                                |
| ---------------------- | -------------------- | ------------------------------------------ |
| `$ERROR_LABEL_ENTRY`   | `'ENTRY'`            | Default entry label in error codes         |
| `$ERROR_LABEL_FIELD`   | `'FIELD'`            | Alternative entry label                    |
| `$ERROR_ENTRY_LABELS`  | `('ENTRY', 'FIELD')` | All recognised entry labels                |
| `$PATH_PREFIX`         | `' @ '`              | Prefix before the path string              |
| `$PATH_SEPARATOR`      | `' > '`              | Separator between path segments            |
| `$PATH_ID_PLACEHOLDER` | `'<id>'`             | Placeholder when the key itself is invalid |
| `$OPTIONS_LIMIT`       | `5`                  | Max options shown before truncating        |
| `$OPTIONS_SEPARATOR`   | `' \| '`             | Separator between option values            |
| `$OPTIONS_MORE_LABEL`  | `'more'`             | Label for the remaining count              |

---

## [Module: Error](index.scss)

```scss
@use 'sass-error' as e;
```

| Function                                          | Description                                   |
| ------------------------------------------------- | --------------------------------------------- |
| [`path`](#epathpath-is-id-error-separator)        | Builds a formatted path string from segments  |
| [`print`](#eprintvalue-quote-empty)               | Renders a value as a printable string         |
| [`options`](#eoptionsitems-extra-limit-separator) | Formats allowed values into an options string |

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
e.path(('settings', 'tier'))       // → ' @ settings > tier'
e.path(('values', 'core'), true)   // → ' @ values > <id>'
e.path(())                         // → ''
```

---

### `e.print($value, $quote-empty)`

Renders a value as a printable string for use in error messages.

| Parameter      | Type   | Default | Description                                                       |
| -------------- | ------ | ------- | ----------------------------------------------------------------- |
| `$value`       | `*`    |         | Value to render                                                   |
| `$quote-empty` | `Bool` | `false` | Wraps empty or whitespace-only strings in double quotes when true |

**Returns** `String` — strings returned as-is, non-empty lists wrapped in parens, all others inspected.

```scss
e.print('hello')      // → 'hello'
e.print(42)           // → '42'
e.print((a, b, c))    // → '(a, b, c)'
e.print('', true)     // → '""'
```

---

### `e.options($items, $extra, $limit, $separator)`

Formats a list of allowed values into a readable options string for error messages.

| Parameter    | Type                                                | Default              | Description                                              |
| ------------ | --------------------------------------------------- | -------------------- | -------------------------------------------------------- |
| `$items`     | `List \| String`                                    |                      | Allowed values; normalised to a list if a single value   |
| `$extra`     | `String \| Number \| Bool \| Color \| List \| Null` | `null`               | Additional value(s) appended after the main list         |
| `$limit`     | `Number \| Null`                                    | `$OPTIONS_LIMIT`     | Max items shown before truncating with a remaining count |
| `$separator` | `String`                                            | `$OPTIONS_SEPARATOR` | Separator placed between options                         |

**Returns** `String` — joined options string, truncated to `$limit` items if the count is exceeded.

```scss
e.options(('foo', 'bar', 'baz'))                       // → 'foo | bar | baz'
e.options(('a', 'b', 'c', 'd', 'e', 'f'), $limit: 3)   // → 'a | b | c | ... 3 more'
e.options(('foo', 'bar'), $extra: 'baz')               // → 'foo | bar | baz'
```

[Back to top](#sass-error)
