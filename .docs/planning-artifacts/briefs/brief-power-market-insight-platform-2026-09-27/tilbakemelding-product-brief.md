# Tilbakemelding på product brief

| | |
|---|---|
| **Gruppe** | G98 – G98-fjellstad |
| **Product brief** | `.docs/planning-artifacts/briefs/brief-power-market-insight-platform-2026-09-27/brief.md` med `addendum.md` (commit e317933) |
| **Tilbakemelding fra** | Faglærer i IBE160 (utarbeidet med KI-støtte) |
| **Dato** | 2026-10-06 |

## Samlet vurdering

- **Bør revideres før dere går videre.** Rett punktene markert «Endre» før dere lager PRD og arkitektur.

Briefen er faglig svært sterk. Det som må endres, handler ikke om idéen, men om at sensor skal kunne kjøre og etterprøve løsningen, og om at omfanget passer for én person i ett semester.

**Det som er bra:**

1. Metoden er tydelig og ærlig formulert: Module 1 starter top-down fra en realisert, historisk budkurve fra Nord Pool og justerer den for endringer i fundamentale faktorer, i stedet for å bygge tilbud og etterspørsel nedenfra. Briefen sier eksplisitt at dette er et eksperiment, og at avvik fra markedet bare er diagnostisk til modellens treffsikkerhet er testet in-sample og out-of-sample.
2. Prioriteringen er klar: Module 1 er gulvet, Module 2 og 3 kommer etter i fast rekkefølge, og addendumet skiller godt mellom hva som er med, hva som er utenfor, og hva som er åpne forskningsspørsmål (valg av referansetilstand og hvor temperaturfølsomme bud ligger i kurven). Du har domenekunnskapen som trengs for å vurdere om modellen gir fornuftige svar.

**De viktigste endringene:**

1. Løs kjørbarheten for sensor. Module 1 bygger på Nord Pool API, Volue API og NUCS. Volue og Nord Pools data krever i praksis kommersielle avtaler, og lisensierte data kan trolig ikke legges i et offentlig repo. Sensor må kunne kjøre appen etter README uten dine nøkler og abonnementer. Planlegg et datasett sensor kan bruke, for eksempel syntetiske eller sterkt aggregerte data som ligner de ekte, eller offentlig tilgjengelige historiske data, med en tydelig «demo-modus».
2. Gjør suksesskriteriene sjekkbare. «Methodologically functioning», «calibrated in-sample» og «good enough to use professionally» er ikke målbare slik de står. Bestem konkrete mål, for eksempel hvilket feilmål (MAE eller CRPS for ensemble-fordelingen) som brukes, mot hvilken referanse (for eksempel naiv prognose eller forrige dags pris), og over hvilken testperiode.
3. Avgrens omfanget eksplisitt til Module 1 for dette semesteret. Module 1 alene har ti justeringsfaktorer på tilbudssiden, ensemble-prognose og sammenligning med finansielle kontrakter. Det er et stort prosjekt for én person. Flytt Module 2 og 3 til visjonen, og beskriv heller hvilke av justeringsfaktorene som er med i første versjon.

## Vanskelighetsgrad og gjennomførbarhet

### Vurdert vanskelighetsgrad

- **Vanskelig**

**Sammenlignbart med:** 4) KI-støttet MRP II og 4.1 Prognoser og Demand Management (vanskelig): omfattende domenelogikk som må stemme, prognoser med usikkerhet og flere moduler som henger sammen. Prosjektet ligger i øvre del av vanskelig, også om bare Module 1 gjennomføres.

**Begrunnelse:**

| Faktor | Nivå (lav / middels / høy) | Kommentar |
|---|---|---|
| Domenelogikk – hvor mange og hvor kompliserte regler og beregninger må stemme? | Høy | Justering av tilbuds- og etterspørselskurver for vind, sol, uregulert vann, balansekapasitet, revisjoner, kjernekraft, CHP, vannverdier og termiske kostnader, og prisklarering per ensemble-medlem. |
| Datamodell – antall entiteter og relasjoner mellom dem | Høy | Budkurver per time, fundamentale tidsserier, prognoser per ensemble-medlem, finansielle kontrakter, og senere nett- og anleggsdata. |
| Brukere, roller og innlogging | Lav | Én brukertype. Ingen innlogging er beskrevet, og det trengs neppe. |
| KI-funksjonalitet i appen, f.eks. kall til språkmodell, prompts i koden og håndtering av usikre svar | Lav i Module 1 | KI-assistenten ligger i Module 3. Module 1 og 2 er statistiske modeller, ikke KI i appen. |
| Integrasjoner og eksterne tjenester, f.eks. API-er, betaling og e-post | Høy | Nord Pool, Volue, NUCS, ECMWF-ensemble via leverandør og senere JAO. Flere av dem er betalte eller krever avtale. |
| Sanntid, samtidighet eller flere brukere som påvirker hverandre | Middels | Målet om å kjøre prognosen på nytt intradag når ny informasjon kommer, krever jevnlig henting. Ikke nødvendig for v1. |
| Filhåndtering, f.eks. opplasting, PDF-lesing og eksport | Lav | Ingen opplasting. Store mengder tidsseriedata må likevel lagres og håndteres. |
| Sikkerhet og personvern | Middels | Ingen personopplysninger, men lisensierte data og API-nøkler må holdes utenfor det offentlige repoet. |

**Hva vanskelighetsgraden betyr for dere:**

- _Vanskelig:_ Et vanskelig prosjekt gir større mulighet for toppkarakter, men også større risiko. Definer en minimal versjon som sikkert kan bli ferdig, og legg resten i tydelige trinn etterpå. For deg kan minimumsversjonen være: velg referansetilstand → juster kurven for et fåtall faktorer (for eksempel vind, sol og uregulert vann) → klarér pris → sammenlign med faktisk pris i en testperiode. Legg til flere faktorer, ensemble-fordeling og sammenligning med finansielle kontrakter i trinn etter det.

### Gjennomførbarhet med BMAD og Claude Code

Dere skal planlegge med BMAD (product brief → PRD → arkitektur → epics og stories) og implementere med Claude Code. Vurderingen under tar hensyn til at det må være tid til hele denne flyten, og til testing, retting og README til slutt.

| Spørsmål | Vurdering (OK / risiko / stor risiko) | Kommentar |
|---|---|---|
| **Tid og omfang** – kan v1 realistisk bli ferdig og stabil i løpet av semesteret, med tid til flere iterasjoner? | Stor risiko | Module 1 med alle faktorene er krevende alene. Tre moduler er ikke realistisk for én person med hele BMAD-flyten, testing og README. Repoet har foreløpig bare briefen. |
| **BMAD-flyten** – er briefen konkret nok til at PRD, arkitektur og stories kan lages uten store hull, og blir det overkommelig mange stories? | Risiko | Faglig svært konkret, men åpne forskningsspørsmål og uavklart teknologistakk gjør at PRD-en må skille mellom det som skal bygges og det som skal undersøkes. |
| **Egnet for Claude Code** – bruker løsningen en vanlig, godt dokumentert teknologistakk som Claude Code håndterer godt, eller krever den nisjeteknologi, spesialmaskinvare eller mye manuell konfigurasjon? | Risiko | Python med vanlige biblioteker for statistikk og grafer passer godt. Integrasjon mot nisje-API-er (Volue, NUCS) og domenespesifikk kurvejustering er mindre dokumentert, og Claude Code vil trenge presise instruksjoner. |
| **Kontroll på KI-ens arbeid** – kan gruppen selv avgjøre om koden gjør det riktige? Krever domenet kunnskap gruppen ikke har, f.eks. avanserte beregninger eller fagregler, så er det vanskelig å kvalitetssikre. | OK | Du har faglig bakgrunn for å vurdere om kurvejusteringene og prisene er rimelige. Det er en stor fordel. Dokumenter kontrollene, slik at sensor ser hvordan KI-generert kode ble sjekket. |
| **Testbarhet** – finnes det tydelige regler og forventede resultater som tester kan skrives mot? | Risiko | Prisklarering og enkeltjusteringer kan testes med små, konstruerte kurver med kjent svar. Treffsikkerheten krever et definert feilmål og testperiode. |
| **Kjørbar for sensor** – kan appen kjøres lokalt etter README, uten gruppens nøkler, betalte kontoer eller egen infrastruktur? | Stor risiko | Ikke slik briefen beskriver i dag. Uten demo-data kan ikke sensor kjøre noe. |
| **Avhengigheter og kostnader** – krever løsningen betalte API-er, f.eks. språkmodeller, og finnes det en plan for kostnad, testmodus eller mock-data? | Stor risiko | Avhengig av kommersielle data-API-er. Ingen plan for testmodus eller vilkår for videreformidling er beskrevet. |

**Konklusjon om gjennomførbarhet:**

- **Gjennomførbart med justert omfang.** Se forslagene under.

**Forslag til justering av omfang eller vanskelighetsgrad:**

1. Avgrens semesteret til Module 1, og del den i trinn: (a) én referansetilstand og 3–4 justeringsfaktorer med deterministisk værprognose, (b) flere faktorer, (c) ensemble-fordeling, (d) sammenligning med finansielle kontrakter. Module 2 og 3 flyttes til visjonen.
2. Lag et datalag med to kilder bak samme grensesnitt: ekte API-er for din egen bruk, og et lite, delbart demodatasett for sensor og tester. Avklar vilkårene for hver datakilde og skriv dem ned.

## Hvorfor product brief er viktig for mappen

Product brief er utgangspunktet for PRD, arkitektur, stories og til slutt koden. Del 1 av mappen vurderes blant annet på om sensor kan følge en sporbar vei fra plan til ferdig app. Den vurderes også på om appen gjør det dere har beskrevet, om den er testet, om den er godt designet, og om den kan kjøres etter README. Et uklart, for stort eller for lite brief gjør alt dette vanskeligere senere. Det er mye enklere å rette nå enn sent i semesteret.

## 1. Gjennomgang av briefens deler

| Del av brief | Status | Kommentar |
|---|---|---|
| Executive Summary – er det klart hva appen er, og hvilket problem den løser? | OK | Klart hva plattformen er, med tre moduler og en tydelig metodisk kjerne. |
| The Problem – er problemet konkret, med reelle situasjoner og brukere? | OK | Konkret og faglig presist: manuell sammenstilling av kilder, og at kommersielle prognoser er tunge, eksterne og vanskelige å kjøre på nytt intradag. |
| The Solution – beskriver løsningen brukeropplevelsen, ikke bare teknologi? | Juster | Svært god beskrivelse av metoden, men lite om hva brukeren gjør i dashboardet. Beskriv kjerneflyten i Module 1 i 3–5 steg fra brukerens side. |
| What Makes This Different – er vurderingen ærlig og realistisk? | OK | Ærlig: én navngitt metodisk satsing, ingen påstått datafordel, og eksplisitt usikkerhet om den er bedre enn eksisterende prognoser. |
| Who This Serves – er primærbrukerne tydelige, og vet vi hva de trenger? | OK | Deg selv og kolleger i kraftmarkedet er en tydelig primærbruker. Det er også bevisst at sensor er et sekundært publikum. |
| Success Criteria – kan kriteriene faktisk sjekkes eller testes? | Endre | Kriteriene er kvalitative. Legg til konkrete mål for treffsikkerhet (feilmål, referanse, testperiode) og funksjonelle kriterier for dashboardet. |
| Scope – er det klart hva som er med i første versjon, og hva som ikke er det? | Endre | Tydelig prioritering, men alle tre modulene står «in for this semester». Avgrens til Module 1, og si hvilke faktorer som er med i første trinn. |
| Vision – henger visjonen sammen med resten uten å blåse opp omfanget? | OK | Visjonen (områdepriser, CWE og Baltikum, skyggepriser) er tydelig holdt utenfor. Module 2 og 3 passer også best her. |

## 2. Utgangspunkt for del 1 av mappen

Punktene følger kriteriene i sensorveiledningen for del 1. Vektene i parentes viser hvor mye hvert kriterium teller i del 1.

| Kriterium i del 1 | Hva briefen bør legge til rette for | Status | Kommentar |
|---|---|---|---|
| **1. Prosess og KI-styring** (30 %) | Brief som er presis nok til at PRD og stories kan bygges direkte på den, slik at krav kan spores fra brief til kode. | OK | Briefen er laget på nytt etter full discovery og revidert etter gjennomgang. Det er god sporbar prosess. Fortsett slik, og lagre prompts og KI-økter. |
| **2. Funksjonalitet og omfang** (20 %) | Realistisk omfang for gruppen og semesteret: en tydelig kjerneflyt som kan bli ferdig og stabil, og nok innhold til å vise reell funksjonalitet. | Endre | For stort omfang for én person med tre moduler. Avgrens til Module 1 i trinn. |
| **3. Kvalitetssikring og testing** (15 %) | Suksesskriterier og funksjoner som er konkrete nok til å bli testtilfeller. | Juster | In-sample/out-of-sample-validering er en god plan. Gjør den målbar, og legg til enhetstester for kurvejustering og prisklarering med konstruerte eksempler. |
| **4. Design og brukeropplevelse** (10 %) | Tydelige brukere og brukssituasjoner som designet kan bygges rundt, gjerne med de viktigste skjermbildene eller flytene skissert. | Juster | Frontend er bevisst ikke detaljert ennå. Skisser de viktigste visningene: kurver, prisfordeling per tidssteg og sammenligning med markedet. |
| **5. Kodekvalitet og arkitektur** (10 %) | Teknologivalg som er begrunnet og ikke mer komplekse enn appen trenger. | Juster | Python er riktig valg. Kursets standardoppsett (Supabase, Vercel, Docker med mer) er ikke et krav. Velg det enkleste som virker for dashboardet, og begrunn valget. |
| **6. README og kjørbarhet** (10 %) | Løsning som andre kan kjøre lokalt uten betalte kontoer, og uten tilgang til gruppens egne tjenester og nøkler. | Endre | Avhengig av betalte data-API-er. Demo-datasett og demo-modus må planlegges før arkitekturen. |
| **7. Ryddighet i repoet** (5 %) | En plan for hvor hemmeligheter, testdata og dokumentasjon skal ligge. | Juster | Lisensierte rådata og nøkler må holdes utenfor repoet. Planlegg hvor demo-data ligger. Planleggingsdokumentene ligger i den skjulte mappen `.docs/`, så lenk til dem fra README. |

## 3. Neste steg for gruppen

1. Avgrens briefen til Module 1 i trinn, og flytt Module 2 og 3 til visjonen.
2. Skriv målbare suksesskriterier for treffsikkerhet (feilmål, referanseprognose, testperiode), og legg til funksjonelle kriterier for dashboardet.
3. Avklar vilkårene for datakildene, og planlegg et delbart demodatasett og en demo-modus før du lager PRD og arkitektur.

Oppdater product brief i repoet når dere har gjort endringene, slik at historikken viser hvordan planen utviklet seg. Det er en del av prosessen sensor ser etter.
