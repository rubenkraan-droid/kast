# Kastrekenmachine

Eén HTML-bestand, geen build, geen dependencies. Je vinkt aan wat er in je kast
hangt; de tool telt hoeveel draagbare combinaties (pantalon + bovenstuk + schoen)
dat oplevert en rekent uit welk volgend stuk de meeste combinaties per euro
toevoegt.

Live: https://rubenkraan-droid.github.io/kast/

## Wat het doet

- **Stuks** — de hele lijst per categorie; klik een regel om aan of uit te vinken.
- **Beste volgende aankoop** — voor elk stuk dat je niet hebt: hoeveel combinaties
  erbij komen als je het koopt, gedeeld door de prijs. Gesorteerd op rendement.
- **Wat je hiermee kunt dragen** — de eerste geldige combinaties, met notities
  als "toon op toon".
- **Seizoen** — winter, zomer of alles; stukken buiten het seizoen tellen niet mee.

Je vinkjes staan in `localStorage` van de browser; er gaat niets naar een server.
Gebruik **Kast exporteren** / **Kast importeren** (een `kast.json`-bestand) om je
kast tussen apparaten mee te nemen.

## ITEMS en RULES aanpassen

Alle data en logica staan in twee arrays onderaan `index.html`.

### `ITEMS`

Eén object per kledingstuk:

| Veld  | Betekenis |
|-------|-----------|
| `id`  | uniek id (`p1`, `t3`, `s2`, …) — hieraan hangen de opgeslagen vinkjes |
| `k`   | categorie: `pantalon`, `bovenstuk`, `blazer` of `schoen` |
| `n`   | naam zoals getoond |
| `m`   | materiaal/omschrijving (klein getoond onder de naam) |
| `tex` | textuur van de kleurstaal: `flanel`, `wol`, `brei`, `katoen`, `leer` of `suede` |
| `hex` | kleur van de staal |
| `p`   | prijs in euro's |
| `f`   | formaliteit 1 (casual) t/m 4 (zwarte oxford) |
| `s`   | seizoen: `w` winter, `z` zomer, `j` jaarrond |
| `c`   | kleurfamilie: `grijs`, `navy`, `bruin`, `beige`, `wit`, `zwart` of `groen` |
| `u`   | link naar het product (mag leeg) |

Combinaties worden geteld over pantalon + bovenstuk + schoen; blazers staan in de
lijst als optionele laag maar tellen niet mee in de rekensom.

### `RULES`

- `maxFormalityGap` — hoeveel stappen formaliteit twee stukken uit elkaar mogen
  liggen (standaard 1). Elke paarsgewijze combinatie in een outfit moet hieraan
  voldoen.
- `shoeColour` — per schoenkleur de toegestane broekkleuren, bijv.
  `zwart: ['grijs','navy']`. Staat een schoenkleur er niet in, dan mag alles.
- `inSeason(item, season)` — bepaalt of een stuk meetelt in het gekozen seizoen
  (`j`-stukken tellen altijd mee).

Nieuwe stukken toevoegen = een regel bij `ITEMS`; nieuwe kleurregels = `shoeColour`
uitbreiden. Verander je een `id`, dan raakt het bijbehorende vinkje kwijt.
