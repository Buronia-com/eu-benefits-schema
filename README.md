# eu-benefits-schema

Open structural dataset of **EU government benefits** — what exists in which country, who administers it, what it pays, what's the official URL, and how to model it with [schema.org](https://schema.org/).

Maintained by [Buronia](https://buronia.com) and released under the [MIT License](LICENSE).

## What's in here

```
data/
├── countries.json                 # 27 EU member states with metadata
├── benefits/
│   ├── at.json                    # Austria — every benefit Buronia helps with
│   ├── be.json                    # Belgium
│   ├── bg.json                    # Bulgaria
│   …
│   └── sk.json                    # Slovakia
templates/
├── organization.jsonld            # Schema.org Organization template
├── government-service.jsonld      # Schema.org GovernmentService template
└── website-search.jsonld          # WebSite + SearchAction template
```

- **27 countries**: at, be, bg, cy, cz, de, dk, ee, es, fi, fr, gr, hr, hu, ie, it, lt, lu, lv, mt, nl, pl, pt, ro, se, si, sk
- **106 benefits** across the 27 countries, each with: native name, English name, administering authority, official URL, average annual value (EUR), complexity score, processing-time estimate, eligibility key, related benefits.
- **3 schema.org JSON-LD templates** for the page types you need on a benefits-help site: `Organization` (with `subOrganization` for multi-country sites), `GovernmentService` (per-benefit landing page), and `WebSite` + `SearchAction` (sitelinks search box).

## Why this exists

If you're building anything that touches EU social-security data — a benefits-help service, a migrant-support tool, a gov-tech research project, a comparison engine — you'll need this metadata. It took us three months of country-by-country research to compile, in collaboration with native speakers of each country's administrative vocabulary.

We're publishing it so you don't have to redo the work, and so corrections from the community make it better than any one team could maintain.

## Quick examples

### Look up every benefit Germany helps with

```bash
$ jq '.[] | {slug, title_native, authority, avg_value_eur_per_year}' data/benefits/de.json
```

```json
{ "slug": "wohngeld",
  "title_native": "Wohngeld",
  "authority": "Wohngeldstelle (kommunale Behörde)",
  "avg_value_eur_per_year": 2400 }
{ "slug": "kindergeld",
  "title_native": "Kindergeld",
  "authority": "Familienkasse (Bundesagentur für Arbeit)",
  "avg_value_eur_per_year": 3000 }
…
```

### Find every benefit administered nationally vs. regionally

```bash
$ jq -r '.[] | "\(.country) \(.slug) \(.level)"' data/benefits/*.json | sort -k3
```

### Generate schema.org JSON-LD for a country page

Copy `templates/government-service.jsonld`, swap the `name`, `provider`, `inLanguage`, and `areaServed` for the country/benefit pair you need. Live example: <https://germany.buronia.com/wohngeld> — view source and search for `application/ld+json`.

## Schema

### `data/countries.json`

```json
[
  {
    "code": "de",
    "subdomain": "germany",
    "english_name": "Germany",
    "native_name": "Deutschland",
    "default_lang": "de",
    "langs": ["de", "en", "tr", "ru", "uk", "ar", "pl"],
    "currency": "EUR",
    "flag_emoji": "🇩🇪"
  },
  …
]
```

### `data/benefits/<country>.json`

Each entry:

```json
{
  "key": "de:wohngeld",
  "country": "de",
  "slug": "wohngeld",
  "title_native": "Wohngeld",
  "title_english": "Housing allowance (Wohngeld)",
  "authority": "Wohngeldstelle (kommunale Behörde)",
  "official_url": "https://www.bmwsb.bund.de/Webs/BMWSB/DE/themen/stadt-wohnen/wohnraumfoerderung/wohngeld/wohngeld-node.html",
  "level": "country",
  "regions": [],
  "complexity": 4,
  "complexity_hours_estimated": 2.0,
  "avg_value_eur_per_year": 2400,
  "description_key": "benefit.de.wohngeld.description",
  "eligibility_key": "benefit.de.wohngeld.eligibility",
  "one_liner_key": "benefit.de.wohngeld.one_liner",
  "seo_keywords": ["wohngeld antrag", "wohngeld berechnen", "wohngeld 2026", "miete zuschuss", "wohngeldrechner", "wohngeld online beantragen"],
  "related_keys": [],
  "form_fields": [
    {"key": "monthly_rent_warm", "type": "money", "label_key": "wohngeld.field.rent_warm",
     "help_key": "wohngeld.help.rent_warm", "required": true},
    …
  ]
}
```

The translated content for the `*_key` fields lives in the **[eu-benefits-i18n](https://github.com/Buronia-com/eu-benefits-i18n)** companion repository — 27 languages, 243 translation files.

## Companion datasets

- **[eu-benefits-i18n](https://github.com/Buronia-com/eu-benefits-i18n)** — multilingual translations for every benefit in this dataset. Same release cadence.

## Live examples

Buronia ships these patterns in production. View source on any of the following to see the structured data in action:

- <https://buronia.com> — apex with `Organization` + `WebSite` JSON-LD
- <https://germany.buronia.com> — country home with `Organization` referenced from the country subdomain
- <https://germany.buronia.com/wohngeld> — single benefit page with `GovernmentService` schema

## Contributing

PRs welcome. Most-needed:

- **Corrections.** If you administer or work with one of these benefits and a number is out of date or an authority name is wrong, fix it.
- **New benefits.** Each country has more benefits than we currently list — open a PR with a new entry following the schema above.
- **Schema improvements.** If you spot a richer schema.org type that fits a particular benefit better than `GovernmentService` (e.g. `Service` subtypes), propose it.

By contributing you agree your contribution is licensed under MIT.

## License

[MIT](LICENSE). Reuse freely, including commercially. Attribution appreciated but not required.
