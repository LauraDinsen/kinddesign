# Teknisk dokumentation – KindDesign CPH

## Om projektet

Som en del af Tema 9 har vi udviklet et designsystem og et responsivt website til KindDesign CPH. KindDesign er et kreativt fællesskab, der arrangerer meetups, talks og vidensdeling i København og internationalt. Før projektet skulle man finde oplysninger om arrangementerne på flere platforme. Vores mål var at samle dem ét sted og gøre vejen til tilmelding kort.

På sitet skal brugeren kunne:

- Finde kommende events og se dato, tidspunkt og sted.
- Åbne et event, læse beskrivelsen og gå videre til tilmelding på Luma.
- Lære fællesskabet at kende gennem dets historie og billeder fra tidligere events.

Ved rapportens aflevering var designet fastlagt i en klikbar prototype i Figma Design. Vi arbejdede efterfølgende videre på det kodede site. På prototypen havde vi designet fem sider, men på det kodede site nåede vi kun at kode fire af dem.

## Links

- [GitHub-repository](https://github.com/LauraDinsen/kinddesign)
- [Website på Netlify](https://kinddesign.netlify.app/)
- [Research og proces i FigJam](https://www.figma.com/board/d20CiKrX4QBVgAprp3PqxG/Tema-9?node-id=308-3986)
- [Wireframes, prototyper og designsystem i Figma](https://www.figma.com/design/F7dLLOtjtrQIwZiLBjzMUj/Wireframes-og-Prototyper--Kind-Design?node-id=0-1)
- [Projektboard i Trello](https://trello.com/b/mCeVMZwl/t9-kinddesign)

---

## Projektstruktur

Projektet er opdelt i to hovedmapper. I `public/` ligger billeder, logoer og ikoner. I `src/` ligger kildekoden til sitets sider, layouts og genbrugelige komponenter.

```text
kinddesign/
├── public/
│   ├── events-img/
│   ├── billeder og svg
└── src/
    ├── assets/
    ├── components/
    │   ├── BrunKnap.astro
    │   ├── Eventcard.astro
    │   ├── Footer.astro
    │   ├── GulKnap.astro
    │   ├── Header-brun.astro
    │   └── Header-gul.astro
    ├── layouts/
    │   └── Layout.astro
    └── pages/
        ├── details/
        │   └── [id].astro
        ├── events.astro
        └── index.astro
```

### Mappe- og filbeskrivelser

- **`public/`** indeholder de filer, som sitet kan bruge direkte, fx favicons, KindDesigns logoer og ikoner til Instagram og LinkedIn. I `events-img/` ligger billeder til events.astro-siden.
- **`src/assets/`** er mappen til ressourcer, der importeres i kildekoden. Vi har ikke rigtigt benyttet denne, da vi brugte mappen public i stedet.
- **`src/components/`** indeholder genbrugelige dele af sitet. `Eventcard.astro` viser et event som et kort. Her ligger også footeren samt de brune og gule varianter af knapper og header.
- **`src/layouts/`** indeholder `Layout.astro`, som definerer den fælles grundstruktur for siderne.
- **`src/pages/`** indeholder sitets sider.
- **`src/pages/details/`** indeholder `[id].astro`, som er skabelonen til eventdetaljer. `[id]` er den del af URL'en, der varierer fra event til event, så den samme skabelon kan bruges til flere arrangementer.

## Hvordan koden fungerer

### Fra Supabase til detaljesiden

Vi har selv indtastet vores events i en tabel i Supabase. Hver række svarer til ét event, og kolonnerne indeholder oplysninger som navn, dato, adresse og beskrivelse.

På detaljesiden bruger vi `fetch()` til at hente oplysningerne. Vi sender forespørgslen til et _endpoint_, som er webadressen til vores eventtabel i Supabase. Sammen med forespørgslen sender vi en API-nøgle i en _header_. Nøglen giver adgang til de data, som projektet tillader at læse. Med `response.json()` får vi svaret som en liste af eventobjekter, som vi kan bruge i koden.

### Én skabelon til flere events

Vi har én skabelon til alle detaljesiderne, `src/pages/details/[id].astro`. De firkantede parenteser betyder, at `id` er en variabel del af webadressen. Et event med id `3` får fx adressen `/details/3`.

En _static path_ er i denne sammenhæng en konkret webadresse, som Astro bygger en side til på forhånd. Funktionen `getStaticPaths()` fortæller Astro, hvilke adresser der skal oprettes. I vores kode gennemgår funktionen listen fra Supabase og giver Astro to ting for hvert event:

- `params` indeholder eventets id og bestemmer dermed sidens adresse.
- `props` indeholder selve eventobjektet og giver siden adgang til de oplysninger, den skal vise.

På den måde kan Astro bygge en separat side for hvert event ud fra den samme skabelon.

### Sådan viser vi data med `{event.eventnavn}`

I skabelonen henter vi eventobjektet fra `Astro.props`. Derefter kan vi indsætte oplysningerne direkte i sidens HTML:

```astro
---
const { event } = Astro.props;
---

<h1>{event.eventnavn}</h1>
<p>{event.dato}</p>
<p>{event.beskrivelse}</p>
```

Det, vi skriver inde i `{ }`, kaldes et _JavaScript-udtryk_. Krølleparenteserne fortæller Astro, at værdien af udtrykket skal indsættes på den plads i HTML'en. Skrivemåden `event.eventnavn` kaldes _punktnotation_. Her er `event` objektet for det valgte arrangement, og `eventnavn` er den egenskab, vi vil læse. Hvis eventet hedder _Real Talk_, bliver `{event.eventnavn}` derfor til teksten _Real Talk_ i sidens overskrift.

### Fælles layouts og komponenter

Vi genbruger header og footer på tværs af siderne, så navigationen er genkendelig. Knapper til centrale handlinger følger også en fælles komponent. Vi har arbejdet mobile-first og tilpasset layoutet til større skærme.

---

## Navngivning

Vi har givet komponenterne navne efter deres funktion og udseende. Fx er `Eventcard.astro` et eventkort, mens `Header-brun.astro` er den brune variant af headeren. På detaljesiden importerer vi den som `HeaderBrun` og bruger den som `<HeaderBrun />`.

### Eksempler på variabler

- `endpoint` indeholder adressen til Supabases API.
- `apikey` indeholder den API-nøgle, vi sender med forespørgslen.
- `options` indeholder forespørgslens indstillinger, herunder `headers`.
- `data` indeholder listen af events fra Supabase.
- `event` er det enkelte arrangement, vi arbejder med.

### Eksempler på funktioner og metoder

- `getStaticPaths()` definerer, hvilke eventadresser Astro skal bygge.
- `fetch()` henter data fra vores endpoint.
- `map()` gennemgår listen af events og opretter `params` og `props` for hvert event.
- `toString()` laver eventets numeriske id om til tekst.

---

## Kommentarer i koden

Vi har sat kommentarer ind i koden undervejs. Blandt andet hjælper det os, når vi samarbejder om at kode det endelige site, og skal forestå hinandens kode. For det andet hjælper det os selv med at skabe overblik over koden - hvis man for eksempel leder efter noget særligt.
Fordi vi stadig finder js udfordrende, har vi også nogle mere forklarende kommentarer, som vi kan kigge tilbage på senere og forstå hvordan vi har gjort.

På detaljesiden har vi fx skrevet en kort kommentar om `toString()`:

```javascript
//  toString laver tal om til tekst
```

Kommentaren knytter sig til `event.id.toString()`. I Supabase er id'et et tal, mens Astro bruger det som tekst i sidens adresse.

---

## Data og JSON-struktur

Supabase returnerer vores tabeldata som JSON. Her er et udsnit af de felter, der hører til eventet _Real Talk_:

```json
{
  "id": 3,
  "eventnavn": "Real Talk",
  "dato": "2026-07-01",
  "tid": "18:00:00",
  "lokation": "Copenhagen",
  "kategori": "talk"
}
```

### Felter vi bruger

| Felt                    | Hvad det indeholder eller bruges til                                                         |
| ----------------------- | -------------------------------------------------------------------------------------------- |
| `id`                    | Eventets unikke nummer. Vi bruger det i detaljesidens adresse og til at finde eventbilledet. |
| `eventnavn`             | Eventets navn, som vises i sidens hovedoverskrift.                                           |
| `dato` og `tid`         | Dato og starttidspunkt for arrangementet.                                                    |
| `lokation` og `adresse` | Byen og den konkrete adresse, hvor eventet foregår.                                          |
| `beskrivelse`           | Introduktionen til arrangementet.                                                            |
| `kategori`              | Typen af event, fx `talk`, `meetup` eller `skillshare`.                                      |
| `details`               | Uddybende oplysninger under _Event Details_.                                                 |
| `Agenda`                | Programmet, der vises under _Agenda_. Feltnavnet starter med stort `A`, også i koden.        |
| `know`                  | Praktiske oplysninger under _A few things to know_.                                          |
| `billeder`              | Billedoplysninger i tabellen. På detaljesiden sammensætter vi billedets adresse ud fra `id`. |
| `created_at`            | Tidspunktet, hvor rækken blev oprettet i Supabase.                                           |

---

## Git og branches

Vi har brugt Git til versionsstyring og GitHub til at samarbejde om koden. Vi har to faste branches, `main` og `deploy`. Derudover har vi oprettet en ny branch fra `main`, hver gang vi er gået i gang med en ny arbejdsopgave.

### Vores branches

- **`main`** er vores fælles branch, hvor vi samler de færdige opgaver.
- **Opgavebranches** har vi navngivet efter den enkelte arbejdsopgave, så vi nemt kunne se, hvilken del af sitet vi arbejdede på i hver branch.
- **`deploy`** er den branch, som Netlify bruger til at udgive sitet.

### Workflow

1. Når vi starter på en ny opgave, opretter vi en branch fra `main` og giver den et navn, der beskriver opgaven.
2. Vi laver ændringerne i opgavens branch og gemmer dem i commits.
3. Når opgaven er færdig, merger vi opgavens branch ind i `main` på GitHub.
4. Når vi vil udgive en ny version af sitet, merger vi `main` ind i `deploy`. Netlify bygger og udgiver derefter sitet fra `deploy`.

---

## Bæredygtighed

- Vi har brugt WebP til billeder for at begrænse filstørrelsen og mængden af data, der skal hentes.
- Astro genererer HTML på forhånd og begrænser mængden af JavaScript, som sendes til browseren.
- Vi har samlet kommende events i én oversigt. Brugeren kan se, hvornår og hvor et event foregår, og dermed undgå at åbne flere sider for at finde et relevant arrangement.

---

## Udfordringer undervejs

**Sortering og filtrering med JavaScript.** Vi synes stadig, at JavaScript er svært. På eventlisten ville vi gerne gøre det muligt at sortere og filtrere arrangementerne. Vi havde en idé om, hvordan det skulle fungere for brugeren, men det var svært at omsætte den til kode. Når vi går i stå, søger vi vejledning hos underviserne. Det hjælper os med at forstå koden og komme videre med opgaven.

**Samarbejde med Git.** Det krævede tilvænning at arbejde i hver vores branch. Hvis vi undervejs fik øje på noget, vi gerne ville ændre på en anden side, måtte vi først afklare, om en anden allerede arbejdede på den. Vi skulle derfor øve os i at holde ændringerne i hver branch til den konkrete opgave. Det krævede mere koordinering, men gjorde det også lettere at arbejde parallelt og mindskede risikoen for konflikter, når vi mergede vores ændringer.

**Tid til kodning.** Da tiden til kodning var begrænset, valgte vi at bruge den på at gøre fire sider færdige. Derfor findes siden om KindDesigns samarbejdspartnere i vores Figma-prototype, men den indgår ikke i det kodede website.

---

## Mulige forbedringer

Hvis vi skulle arbejde videre med projektet, ville vi især fokusere på følgende:

- **Brugertest af det kodede site.** Vi vil gerne gennemføre brugertest af det kodede site og foretage iterationer i koden.

- **Øget tilgængelighed.** Vi vil gerne undersøge hvor tilgængelig vores kodede site er, og optimere.

- **En side om samarbejdspartnerne.** Vi vil gerne kode partnersiden fra Figma-prototypen, så besøgende også på det færdige site kan se, hvem KindDesign samarbejder med, og hvilken rolle hver partner har i fællesskabet.

---

## Gruppemedlemmer

- Laura Dinsen
- Line Bøgh
- Maja Holgersen
