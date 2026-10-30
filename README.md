# Custom Survey Components for Qualtrics

Custom JavaScript components for collecting structured information about interactions with public institutions and service providers in multilingual social research.

The repository combines institution selection, conditional follow-up questions, autocomplete suggestions, and demographic fields. It includes Turkish, English, Arabic, and Persian interface text, with right-to-left layout for Arabic and Persian.

## Research use

These components were tested and used in a research survey designed to collect data for network modeling of relationships between service providers and public institutions.

The survey captures which institutions respondents interact with, how frequently they communicate, whether they personally know someone at those institutions, and how long beneficiaries' cases take to resolve. These measures support an investigation of whether personal connections are associated with the time required to access services or complete administrative processes.

Data collection is complete; analysis and manuscript preparation are pending.

## Screenshots

### Institution selection and autocomplete

Selecting an institution reveals follow-up questions about interaction duration, location, and personal connections. The Turkish example below shows province/district autocomplete suggestions.

![Institution selection with conditional follow-up questions and location autocomplete](docs/images/institution-autocomplete.png)

### Interaction frequency

The survey also captures how frequently respondents communicate with selected institutions, including a free-text option.

![Institution selection with conditional interaction-frequency questions](docs/images/interaction-frequency.png)

### Demographic fields

The demographic component collects gender, nationality, and age group. The example below shows the Turkish interface.

![Turkish demographic fields for gender, nationality, and age group](docs/images/demographics.png)

## Features

- **Conditional questions:** Selecting an institution reveals follow-up fields for interaction duration, location or institution name, and whether the respondent knew someone at that institution.
- **Autocomplete suggestions:** Hospital, school, and province/district lists are loaded from a separate JavaScript file using native HTML `datalist` elements.
- **Multilingual interface:** Custom text is selected using Qualtrics' `Q_Language` value.
- **Response persistence:** The scripts write to Qualtrics Embedded Data and include logic to restore previously stored answers.
- **Demographic component:** A separate question collects gender, nationality, and age group.

These are survey-specific components to adapt to your own instrument, rather than a standalone application or a complete Qualtrics survey export.

## Files

| File | Purpose |
| --- | --- |
| [Qualtrics_Custom](Qualtrics_Custom) | Institution selection, conditional follow-ups, autocomplete loading, and response storage. |
| [qualtrics_custom-demographics](qualtrics_custom-demographics) | Multilingual demographic fields and response storage. |
| [autocomplete_data.js](autocomplete_data.js) | Suggestion lists consumed by the institution component. |

The two extensionless component files contain JavaScript intended for the Qualtrics question JavaScript editor.

## Setup and adaptation

You need a Qualtrics survey with access to question-level custom JavaScript.

1. Create a question to host the institution component and paste the contents of `Qualtrics_Custom` into its JavaScript editor. Use a separate question for `qualtrics_custom-demographics` if needed.
2. Review the institution categories, translations, demographic options, and follow-up wording for your study.
3. Configure the relevant Embedded Data fields in Survey Flow before the block containing these questions; see the field reference below.
4. Review the `script.src` URL in `Qualtrics_Custom`. It currently points to:
   ```text
   https://nihankg.github.io/qualtrics-autocomplete/autocomplete_data.js
   ```
   For an independent deployment, host your own copy over HTTPS and update this URL. The file must expose `window.hastaneListesi`, `window.okulListesi`, and `window.ilceListesi` as arrays of suggestion strings.
5. Align the scripts with your survey's language settings. They recognize `TR`, `EN`, `AR`, and `FA` from `Q_Language`. The institution component defaults to Turkish; the demographic component defaults to English.
6. Preview the survey and submit a test response. Inspect the exported Embedded Data before using the components in a study.

## Embedded Data reference

Both scripts currently use the prefix `icerde1_`. Change the prefix consistently in the code and Survey Flow if adapting the components for another case or repeated block.

### Institution component

| Field | Contents |
| --- | --- |
| `icerde1_INSTID` | Selected institution IDs, separated by `|||`. |
| `icerde1_INSTNAME` | Selected translated institution labels, separated by `|||`. |
| `icerde1_<id>_secildi` | Selection flag: `1` or `0`. |
| `icerde1_<id>_sureMiktar` | Duration quantity. |
| `icerde1_<id>_sureTipi` | Duration unit or the translated “I don't remember” option. |
| `icerde1_<id>_ilce` | Location text, where that field is present. |
| `icerde1_<id>_kurumadi` | Hospital or school name, where present. |
| `icerde1_<id>_tanidik` | Translated yes/no response about knowing someone at the institution. |
| `icerde1_otherentry` | Free-text name for the “Other” institution category. |

Replace `<id>` with an ID from the `ngos` array in `Qualtrics_Custom`, such as `hospital`, `school`, or `courthouse`. Declare fields for the categories and inputs your survey uses.

### Demographic component

| Field | Contents |
| --- | --- |
| `icerde1_cinsiyeti` | Selected gender label. |
| `icerde1_uyrugu` | Selected nationality label. |
| `icerde1_yasi` | Selected age-group label. |

## Behavior and limitations

- Autocomplete supplies suggestions; it does **not** restrict responses to the list or validate institution names.
- Institution responses are written through a question-click handler; demographic responses are written on question unload. Verify saving after keyboard input, navigation, and returning to earlier pages.
- Deselected institutions retain previously stored follow-up values. Use the selection flags or `INSTID` when interpreting exports.
- Several stored values are translated labels. For multilingual analysis, recode these consistently; stable institution IDs are available in `INSTID`.
- The institution interface is built after the external suggestion script loads. If that request fails, the interface will not be built; the current component has no load-error fallback.
- The current DOM IDs assume one institution component per page. Repeated components on the same page need unique IDs.
- Suggestion-list provenance, coverage, and update dates are not documented here yet.

## Checks before deployment

Test all four languages, right-to-left display, mobile layout, autocomplete loading, conditional fields, deselection, response restoration, and the final data export in your own Qualtrics environment.

Review study-specific wording, including any confidentiality statements, before adapting it. This README documents the current code; it does not establish browser compatibility or replace end-to-end survey validation.

<img width="394" height="754" alt="interaction-frequency" src="https://github.com/user-attachments/assets/067c99e9-34d7-4cbb-80c3-4fd0a650f3c3" />
<img width="395" height="723" alt="institution-autocomplete" src="https://github.com/user-attachments/assets/cb534d17-5e26-4e3f-906a-68a5d078e0f7" />
<img width="405" height="122" alt="demographics" src="https://github.com/user-attachments/assets/fc757361-fff5-49eb-8836-6de07cea0579" />
