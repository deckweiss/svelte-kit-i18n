# Getting started
This repository contains internationalization for SvelteKit.

## Installation
### 1. Install package
```sh
pnpm i @deckweiss/internationalization
```

### 2. Initialize i18n
```typescript
// src/i18n.ts
import { initialize as initializeI18n } from '@deckweiss/internationalization';
import de from '$lib/translations/de.json';
import en from '$lib/translations/en.json';

initializeI18n({
    defaultLocale: 'en',
    locales: {
        de,
        en
    },
    useCookie: true
});
```

```typescript
// src/hooks.server.ts
import { handle } from '@deckweiss/internationalization';
import './i18n.js';

export { handle };
```

```typescript
// hooks.client.ts
import './i18n.js';
```

```html
<!-- app.html -->
<html lang="%lang">
    ...
</html
```

```typescript
// +layout.server.ts
import { get } from 'svelte/store';
import { locale} from '@deckweiss/internationalization';

export function load(event) {
    return { userLocale: get(locale) }
}
```

```typescript
// +layout.ts
import { setLocale } from '@deckweiss/internationalization';

export function load(event) {
    setLocale(event.data.userLocale)
}
```

### 3. Use translations
```svelte
// +page.svelte
<script>
    import { t } from '@deckweiss/internationalization';
</script>


<p>{$t('app.day', { date: Date.now() })}</p>
```

### 4. (optional) Update selected locale
```svelte
// src/lib/components/language-picker.svelte
<script>
    import { locale, locales, setLocale } from '@deckweiss/internationalization';
</script>


<select value={$locale} on:change={(event) => setLocale(event.target.value)}>
    <option disabled>Choose language</option>
    {#each locales as l}
        <option value={l} selected={l === $locale}>{l}</option>
    {/each}
</select>
```


## JSON Format
This package is compatible with [i18next JSON v4](https://www.i18next.com/misc/json-format#i18next-json-v4). Although, it currently does only support nested keys and interpolation with formatting.

### Examples
```json
// src/lib/translations/en.json
{
    "app": {
        "name": "Example app",
        "day": "Today is {{date, date(format: dddd)}}"
    }
}
```

| Usage | Output |
| ----- | ------ |
| $t('app.name') | "Example app" |
| $t('app.day', { date: new Date(1997, 4, 22) })} | "Today is Thursday" |

Date format symbols can be found [here](https://github.com/felixge/node-dateformat?tab=readme-ov-file#mask-options).
**Bundled date languages**  
- German
- English
- Italian
- Spanish


## Supported Interpolations
### Date and Time
```json
// src/lib/translations/en.json
{
    "day": "Today is {{date, date(format: dddd, dd.mm.yyyy)}}" // Output: "Today is Friday, 23.05.1997"
}
```

### Numbers and Currency
The `options` argument gets directly passed into the `Intl.NumberFormat` constructor. Hence, you can configure any option in JSON syntax.
```json
// src/lib/translations/en.json
{
    "number": "{{count, number(options: {\"style\": \"decimal\", \"minimumFractionDigits\": 5})}}", // Output: "5,132.95000"
    "currency": "{{amount, number(options: {\"style\": \"currency\", \"currency\": \"EUR\"})}}" // Output: "5,132.95 €"
}
```


## Contribution
### Create a new release
1. Update package version either by hand or with `pnpm version`.
2. The commit message onto main must begin with `chore: release`
