# Kastrekenmachine

Eén HTML-bestand, geen build, geen dependencies. Je vinkt aan wat er in je kast
hangt; de tool telt hoeveel draagbare combinaties dat oplevert en rekent uit welk
volgend stuk de meeste combinaties per euro toevoegt.

Live: https://rubenkraan-droid.github.io/kast/

## Wat het doet

- **Stuks** — de hele lijst, per categorie. Klik een regel om aan of uit te vinken.
  Buiten het seizoen staat een stuk gedimd.
- **Beste volgende aankoop** — voor elk stuk dat je niet hebt: hoeveel combinaties
  komen erbij als je het koopt, gedeeld door de prijs. Gesorteerd op rendement.
- **Wat je hiermee kunt dragen** — de eerste veertien geldige combinaties van
  pantalon + bovenstuk + schoen.
- **Betaalde prijs** — per stuk een veld rechts. Vul je er iets in, dan rekent de
  ranking, de teller "nog te kopen" en de prijs per keer met dát bedrag. Laat je
  het leeg, dan geldt de adviesprijs uit `ITEMS` (die staat als grijze hint in het
  veld).
- **Prijs per keer** — onder de prijs, uitgaande van **40 draagbeurten per jaar**.
- **Eigen stuk toevoegen** — knop bovenaan: naam, categorie, kleur, formaliteit
  1–4, seizoen en betaalde prijs. Eigen stukken krijgen een id `x1`, `x2`, … en
  een kruisje om ze weer te verwijderen.

Alles blijft in de browser. Er gaat niets naar een server.

## Opslag

Drie sleutels in `localStorage`:

| sleutel | inhoud |
| --- | --- |
| `kast.owned.v1` | array met ids van aangevinkte stuks |
| `kast.prices.v1` | object `{id: bedrag}` met betaalde prijzen |
| `kast.custom.v1` | array met eigen stukken |

De export-knop schrijft alle drie naar één `kast.json`. De import-knop leest dat
formaat én het oudere formaat (een kale array met ids), zodat een export van vóór
deze versie blijft werken.

## ITEMS

`ITEMS` staat onderaan `index.html` en is de complete garderobe. Eén object per
stuk:

```js
{id:'p1', k:'pantalon', n:'Grijze flanellen pantalon', m:'VBC S120 flanel',
 tex:'flanel', hex:'#6e7074', p:169, f:3, s:'w', c:'grijs', u:'https://…'}
```

| veld | betekenis |
| --- | --- |
| `id` | uniek, wordt in `localStorage` bewaard — nooit hergebruiken voor iets anders |
| `k` | categorie: `pantalon`, `bovenstuk`, `blazer`, `schoen` |
| `n` | naam zoals hij in de lijst staat |
| `m` | materiaal, de kleine regel eronder |
| `tex` | weefsel voor het patroontje op het staal: `flanel`, `wol`, `brei`, `katoen`, `leer`, `suede` |
| `hex` | kleur van het staal |
| `p` | adviesprijs in euro's; wordt overschreven door een ingevulde betaalde prijs |
| `f` | formaliteit 1–4 — 1 is jeans en sneakers, 4 is een zwarte oxford |
| `s` | seizoen: `w` winter, `z` zomer, `j` jaarrond |
| `c` | kleurfamilie: `grijs`, `navy`, `bruin`, `beige`, `wit`, `zwart` |
| `u` | link naar het product, mag leeg |

Een stuk toevoegen is een regel erbij met een nieuw `id`. Blazers doen niet mee in
de combinatietelling; die zijn een optionele laag.

## RULES

```js
const RULES = {
  maxFormalityGap: 1,
  shoeColour: {
    zwart: ['grijs','navy'],
    bruin: ['grijs','navy','bruin','beige'],
    beige: ['bruin','beige','grijs'],
  },
  inSeason: (item, season) => season==='alles' || item.s==='j' || item.s===season[0],
};
```

- `maxFormalityGap` — twee stuks mogen hooguit zoveel stappen in `f` uit elkaar
  liggen. Op `1` combineert een 2 met een 3, maar niet met een 4. Zet je hem op
  `2`, dan wordt de lijst een stuk losser.
- `shoeColour` — per schoenkleur de toegestane pantalonkleuren. Zwarte schoenen
  dus niet bij bruin of beige. Een schoenkleur die hier niet in staat, combineert
  met alles.
- `inSeason` — `alles` laat alles door; verder telt een stuk mee als het jaarrond
  is (`j`) of als de eerste letter van het seizoen klopt (`winter` → `w`).

Een combinatie is geldig als de drie stuks onderling binnen `maxFormalityGap`
liggen én de schoenkleur bij de pantalonkleur past. Alle drie de regels zitten in
`countFits()` en `listFits()`, direct onder `RULES`.

## Lokaal draaien

`index.html` openen in een browser is genoeg. Let op: via `file://` bewaart de
browser soms niets — dan zegt de tool dat er onderaan bij. Gehost werkt het wel.
