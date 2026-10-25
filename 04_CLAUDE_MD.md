# CLAUDE.md – pravidla projektu

# Ekonomické hry antigravity

## TVRDÉ PRAVIDLO PRO SCÉNÁŘE (VOICEOVER) — Ondřej 23. 10. 2026

Scénáře píše **vždy a jen Gemini 4 Argon** (`gemini-4-argon-high`) přes **Antigravity CLI** (příkaz `agy`);
Claude je datový analytik, přípravář podkladů a přísný kontrolor faktů. Jiný model nemá smysl (Ondřej 23. 10. 2026)
a skript ho odmítne. Gemini CLI s předplatným Ultra od 18. 6. 2026 nefunguje (Google ho nahradil Antigravity CLI).

- **Claude má zakázáno psát finální texty scénářů (voiceoverů) sám.**
- Kdykoliv Ondřej požádá o napsání, vygenerování nebo přepsání scénáře či voiceoveru, Claude automaticky:
  1. **sestaví balík podkladů:** instrukce z `PROMPT_GEMINI_SCENARISTA.md` (jdou jako závazná instrukce v čele zprávy), kompletní
     tabulky regionu z `KOLA_TABULKA.md` (včetně tabulky dohánění a tabulky podílu mezd na výkonu, bez čekaného místa),
     výsledek v cíli z `data/vysledek.json`, štítky z hodnocení a plné texty `vyklad/{KOD}.md` všech zemí regionu;
  2. **odešle ho Gemini 4 Argonu** přes Antigravity CLI (Argon běží v prázdné složce bez povolení nástrojů);
  3. **převezme hotový scénář a udělá audit faktické přesnosti** proti `data/vysledek.json` a `data/kola.json`
     (automatická kontrola čísel a ruční kontrola čísel psaných slovy, pořadí a příčin proti výkladům zemí). Číselnou
     chybu Claude opraví bodově a v auditu ji označí, nebo scénář vrátí Geminimu k přepsání; styl Geminiho nepřepisuje.
- Nástroj: `py scripts/gemini_scenarista.py vse --region "Střední Evropa"` (kroky `sestav`, `posli`, `audit`
  i zvlášť; výstupy v `05_epizode_packs/ep_001_prumerna_mzda_pps_v18/output/scenar_regiony_2026_10/gemini/{REGION}/`).
  Antigravity CLI je přihlášené Ondřejovým účtem Google (předplatné Google AI Ultra). **Ondřej terminál nepoužívá
  a komunikuje jen přes chat (25. 10. 2026):** všechny příkazy spouští Claude sám na pozadí. Když přihlášení vyprší,
  Claude ho sám spustí tak, aby se Ondřejovi otevřelo okno Googlu v prohlížeči (nebo mu do chatu dá jen klikací
  odkaz), řekne mu jen, co má odkliknout, a po jeho „Hotovo“ přihlášení sám ověří. Když `agy models` Argon nenabídne,
  scénář se nepíše a Claude to Ondřejovi řekne; náhradní model se nepoužívá.
- **Přímá konzultace s Argonem (Ondřej 25. 10. 2026: „chci to přímo konzultovat s Gemini 4 Argon“):** když Ondřej
  píše Argonovi, Claude jeho zprávu pošle **doslova** do Argonova rozhovoru regionu (krok `rozhovor --zprava "…"`;
  rozhovor obsahuje kuchařku, všechny podklady i platnou verzi scénáře) a Argonovu odpověď vloží do chatu **beze
  změny**, bez vlastního komentáře a bez úprav. Claude do konzultace nevstupuje; ozve se až tehdy, když Argon
  vrátí nový text scénáře: ten uloží a znovu zkontroluje čísla. Záznam rozhovoru je v `{REGION}/ROZHOVOR.md`.
- Platí dál: sloh v souvislých větách, žádné „my“ o Česku, žádné čekané místo, žádné kódy (vše je i v kuchařce, oddíl 7).

## KONTEXT PRO WEBOVÉ GEMINI: VEŘEJNÝ REPOZITÁŘ A DISK GOOGLE — Ondřej 25. 10. 2026

Hlavní repozitář `economic-games` je a **zůstává soukromý** (Ondřej 25. 10. 2026), takže ho webové Gemini
(gemini.google.com) neotevře (chyba 13), a Ondřej nechce nic nahrávat ručně. Souhrnný kontext proto vzniká
**automaticky** a nahrává se do malého **veřejného** repozitáře
[economic-games-kontext](https://github.com/ondrejpilarik-coder/economic-games-kontext), jehož odkaz Ondřej dává
webovému Gemini (místní klon `%USERPROFILE%\economic-games-kontext`, commity s anonymní adresou GitHubu, ne
Ondřejovým e-mailem). Obsah: poslední scénář s kontrolou čísel a konzultací, kuchařka, podklady scénáře,
`CLAUDE.md` a aktivní skilly jako **čisté Markdowny bez PDF** (`00_CTI_NEJDRIV.md` až `05_AKTIVNI_SKILLY.md`)
a vše spojené v **`KOMPLETNI_KONTEXT.md`** (`scripts/kontext_pro_gemini_web.py`; u každého souboru nejnovější verze
z mainu a všech pracovních kopií). PDF do veřejného repozitáře nepatří: s nimi ho webové Gemini načetlo jako ZIP
a text nepřečetlo (Ondřej 25. 10. 2026); PDF vzniká jen na požádání (`--pdf`) do místní složky. Do veřejného
repozitáře nepatří nic jiného (žádná data, zvuk ani klíče). `KOMPLETNI_KONTEXT.md` leží i v kořeni hlavní pracovní
kopie projektu (`C:\Users\ondre\Desktop\Ekonomické hry Codex\KOMPLETNI_KONTEXT.md`, git ho ignoruje přes
`.git/info/exclude`) a všechny soubory i ve složce Disku Google `Můj disk/Ekonomické hry – kontext pro Gemini`
(dokud Disk Google na PC není, ve stejnojmenné složce v profilu uživatele).
- **TRVALÉ PRAVIDLO (Ondřej 25. 10. 2026):** „Pokaždé, když se změní jakýkoliv skill, kuchařka nebo se vygeneruje
  nový scénář, automaticky přegeneruj `KOMPLETNI_KONTEXT.md` a pushni změny do veřejného repozitáře
  `economic-games-kontext`, aniž by ti to Ondřej musel připomínat.“ Dělají to hooky a nástroj níže; Claude po
  každé takové změně ověří, že veřejný repozitář má novou verzi, a když ne, spustí
  `py scripts/kontext_pro_gemini_web.py` sám.
- Obnovuje se sám: git hooky `post-commit` a `post-merge` po změně skillů, `CLAUDE.md`, kuchařky nebo scénáře
  (instalace `py scripts/instaluj_hooky_gemini_web.py`, platí pro všechny pracovní kopie na Ondřejově PC) a
  `scripts/gemini_scenarista.py` po každém novém scénáři, kontrole i odpovědi Argona.
- Nový aktivní skill, který má Gemini vidět, se zapíše do přehledu skillů v tomto souboru (odrážka „- **`název.md`**“)
  nebo do seznamu `NAVIC` ve skriptu. Souborů je nejvýš 10, aby se vešly do znalostí jednoho Gemu.

> **Hlas a model voiceoverů — 1. 10. 2026 (Ondřej):** každý voiceover se tvoří jen klonem Ondřejova hlasu (`Cak3fRkQmNTeK1EmSkdA`) a jen modelem Eleven v4 (`eleven_v4`). Hlas a model jsou pevně ve `scripts/tts_hlas.py` a skripty, které volají ElevenLabs, jiný hlas ani model nepustí. Pravidlo je v TTS skillu `05_epizode_packs/_skills/section-14-1-elevenlabs-tts.md` (oddíl „Závazný hlas a model“). Kontrola na PC: `py scripts/tts_hlas.py --env-file <.env>`.

> **Writer v10.10, doplňky z Ondřejových připomínek — 30. 9. 2026:** Ondřej ke všem 27 zemím FINAL mezd napsal připomínky a na návrh doplňků odpověděl „určitě ano“. Writer skill §3.10 z nich dělá dvanáct mechanismů s podkladem z builderu (`live.json`) a kontrolou v detektoru (`--live`, `--measured-year`):
> - emoce podle situace na konci kola;
> - tempo u zemí s očekáváním v tempu;
> - čekaný výsledek podle kol jen proti vlastní hodnotě země („stejné se stejným“);
> - konce kol v souladu s grafikou, kroky s rokem;
> - reakce na souboje proti očekávání, napětí a návrat ke klíčovému kolu;
> - motiv osudu, vedlejší účinky a návrat k rychlým pákám;
> - srozumitelný první výklad odvodů.
>
> Kola se dál nesčítají (§3.6). FINAL mezd je podle v10.10 přepsaný; Ondřej 1. 10. potvrdil délku asi 101 s na zemi a vyhodnocení tipů 12 z 27. Podrobnosti jsou v PREKLADOVY_MUSTEK.md.

> **Writer v10.9, zlatý vzor a postup — 24. 9. 2026 večer:** Ondřej o FINAL mezd z 24. 9. napsal „A to se mi opravdu moc líbí“ a chce, aby se scénáře tvořily přesně tak. Forma tohoto FINAL je proto závazný [zlatý vzor](05_epizode_packs/_skills/references/voiceover-komentar-zlaty-vzor-2026-09-24.md): anotované ukázky, obměny formulací se stropy opakování a anti-vzory z kontrol. Writer skill §3.9 zapisuje postup, kterým FINAL vznikl:
> 1. váhy kol (`scripts/calculate_round_weights.py`);
> 2. komentátorské listy z builderu (`scripts/build_commentary_sheets.py`);
> 3. psaní po dávkách;
> 4. detektor po každé dávce;
> 5. tři kontroly faktů, slepá kontrola stylu podle šablon v `eval/prompts/` a slepé divácké čtení;
> 6. revize a regresní kontrola.
>
> Rozpočet je přepočítaný na změřené tempo hlasu: asi 230 slov na zemi, výjimky nejvýš asi 280. Vzor žádné pravidlo neuvolňuje: odchylky FINAL jsou v něm zapsané a čtyři z nich čekají na Ondřejovo rozhodnutí. Podrobnosti jsou v PREKLADOVY_MUSTEK.md.

> **Writer v10.8, komentátorská vrstva — 24. 9. 2026 (Ondřej schválil pilot Belgie):** voiceover se píše jako komentář přenosu, ne přednáška: nejvýš 90 sekund (asi 250 slov) na zemi, showcase s tipem v sedmistupňové škále bez zmínky o datech, na konci každého kola pořadí v kole a vztah k lídrovi, stupnice verdiktů, konkrétní kroky vlád a firem v dlouhodobém kole, Results jako dojezd do cíle s čekaným výsledkem podle všech tří kol (writer skill §3.8). Analýza mezd má nový oddíl „Váhy kol a čekaný výsledek podle všech tří kol“. Kontrola: `scripts/check_voiceover_commentary.py`. Stav přepisu určuje AKTUALNI_EPIZODY.md.

> **Nová metoda indexů a nová finální analýza mezd — 23. 9. 2026:** Ondřej schválil metodu po slepé kritice (výsledek = hrubá mzda, minimální mzda „čím vyšší vůči mediánu, tím lépe“). Je zapsaná bodově v hlavním analytickém skillu a výpočetním doplňku §2.2a–2.3d. Mzdová analýza je podle ní nově postavená pro všech 27 zemí (revize výběru `2026-09-23.1`) a krátkodobý index je v ní znovu bodovaný; otevřená otázka z odstavce níže je tím uzavřená. Voiceover a jeho posudky jsou proti ní zastaralé. Stav určuje AKTUALNI_EPIZODY.md.

> **Writer v10.7 — 23. 9. 2026 (zadání Ondřeje):** kola se nesčítají. V blocích kol se mluví o směru a síle podle karty kola (pomáhá, brzdí), ne o umazané ztrátě, stavu po kolech ani bodech za kolo; kola se nespojují do společného odhadu a Results smí podle nálepky výbavy říct, zda výsledek odpovídá tomu, co výbava slibovala (writer skill §3.6). Příčiny se vybírají podle mapy příčin napříč ligou a testu záměny; sdílený mechanismus se vysvětlí naplno jednou a jinde se na něj odkazuje (§3.7). Validátor pěti bloků nově odmítá aritmetiku kol a běžné užití názvu kola „potenciál“. Analýza ani voiceover se tím nepřepisují; podrobnosti v PREKLADOVY_MUSTEK.md.

> **Oprava rozsahu a autorský writer — 22. 9. 2026:** Ondřej výslovně upřesnil, že krátkodobý index nechtěl zrušit. Jeho vyřazení v mzdové revizi bylo neautorizované rozhodnutí asistenta; numerické obnovení s opravenými vstupy zůstává otevřené. Tato změna aktivuje pouze autorské doplnění hlavního writer skillu (§2–4, §10–12), sladění kontrol a uvolnění délky. Tři indexy zůstávají požadavkem. Finální analýza ani voiceover se tímto krokem nepřepisují a nezískávají nové schválení. Stav určuje AKTUALNI_EPIZODY.md.

> **Aktivní oprava relevance a výběru — 22. 9. 2026:** hlavní analytický skill §2.1a a §5.2a vyžaduje vztah faktoru k přesnému hlavnímu indikátoru, doložený přenos, období, rozsah a porovnání kandidátů. Nezpůsobilý katalog se nesmí vynuceně bodovat; jeho vada není oprávnění zrušit uživatelem požadovaný index. Daňový klín je v této epizodě vyřazen z aktivního výkladu. Historické poznámky o 196 kartách níže nejsou novou autoritou.

> **Aktivní komentátorské doplnění 21. 9. 2026:** Ondřej schválil `05_epizode_packs/_skills/analysis-voiceover-commentary-ADDENDUM-2026-09-21.md`. Společně s doplněnými hlavními skilly řídí oddělené předstartovní tipy, showcase, úsudek v každém kole, odstupňované emoce, doložené vedlejší účinky a interpretaci potenciálu vůči výsledku. Staré odvozování očekávání z hotové analýzy ustupuje tomuto kontraktu; ostatní funkční pravidla zůstávají.

> **Aktivní analytická aktualizace 2026-09-19 — všechny epizody:** Ondřej schválil všech 14 požadavků a všech pět náhrad rozporných pravidel v `05_epizode_packs/_skills/european-economy-analysis-v23-zlaty-standard.md` a jejich publikaci do mainu. Nové analýzy a revize vycházejí z vlastních tabulek, hodnotí význam nosných faktorů, rozlišují kvalitu důkazů od jejich počtu, připouštějí ohraničené nerozlišení příčin a doložené kumulativní strategické procesy. Povinný čtecí výstup má čtyři oddíly, kartu pro writera, sousední souboje a úplné interaktivní tabulky se sedmi barvami podle `05_epizode_packs/_skills/analysis-review/FORMAT_A_REPRODUKCE.md`. Mzdové konstanty nejsou univerzální; nové indikátory vyžadují adaptér přehledu. Bodování, ústava, archiv a nesouvisející kontroly se nemění. Předchozí zákaz publikace této integrace a pět čekajících otázek jsou výslovným pokynem uživatele uzavřeny. Přesný rozsah: `10_documents/active/integrace_analytickeho_skillu_2026_09_19/AKTIVACE.md`.

> **Zachovaná univerzální metoda indexů a katalogu 2026-09-18:** hlavní analytický skill `05_epizode_packs/_skills/european-economy-analysis-v23-zlaty-standard.md` a schválený doplněk `05_epizode_packs/_skills/factor-research-three-groups-ADDENDUM-2026-09-18.md` požadují obsáhlou počáteční rešerši agentů, otevřený katalog s možností uživatelských doplnění a tři skupiny: potenciál, krátkodobá strategie, dlouhodobá strategie. Žádný konkrétní faktor není povinný všude; stejný relevantní faktor se může opakovat v libovolném počtu indikátorů. Úplné společné tabulky doplňují označené odhady se zdroji a nejistotou. Výpočet řídí `05_epizode_packs/_skills/strategy-potential-capacity-ADDENDUM-v1.0.md` (průměr 100, rovné váhy, tři samostatné souhrny). Není to již návrh ani výjimka pouze pro mzdy. Historické analýzy se samotnou aktivací nepřepisují.

YouTube video serie analyzující ekonomiky evropských zemí ve stylu "tycoon game" miniature diorama.

## Společné předání mezi modely a epizodami (2026-09-18)

Na začátku každé práce přečti [AKTUALNI_EPIZODY.md](AKTUALNI_EPIZODY.md), ověř větev
a rozpracované změny a obnov přehled `origin`. Na staré větvi přečti aktuální rozcestník
přes `git show origin/main:AKTUALNI_EPIZODY.md`. Platný soubor se vybírá podle tohoto
rozcestníku a předání, nikoli podle nejvyššího čísla v názvu. Nové zadání jiné epizody
se provádí v oddělené pracovní kopii; cizí změny ani chráněný turistický běh se nepřepisují.
Předání musí být v repozitáři a obsahovat stav, kontroly, otevřené vady a další krok.
Před publikací porovnej novější main; worktree sám neřeší souběžné úpravy téhož souboru.

## Git: automatická synchronizace do mainu (trvalé svolení Ondřeje, 2026-07-12)

Práce probíhá na pracovní/session větvi, ale po **každém commitu**, který mění některou
z těchto kategorií, se změny **okamžitě a bez ptaní** dostanou do `main`:

- `05_epizode_packs/_skills/**` — nové skilly i každá předělávka/verze existujícího skillu
- finální dokumenty epizod a jejich předělávky: `FINAL_voiceover*`, `FINAL_analysis*`,
  `broll_prompts.json` (prompty pro tvorbu obrázků), `remotion_edit.json`,
  `karta_indikatoru.yaml`
- `.claude/agents/**` a `scripts/**`, pokud jsou součástí pipeline

**Postup:** `git push origin HEAD:main` (fast-forward). Když fast-forward nejde (main mezitím
uhnul), `git fetch origin main` → merge → push, případně PR + okamžitý merge. Konflikt,
který nejde vyřešit triviálně, se eskaluje Ondřejovi — nikdy neřešit force-pushem do mainu.

Toto pravidlo **nahrazuje** dřívější režim „merge do mainu jen na výslovný pokyn".
Důvod: Ondřej pracuje na PC přes `git pull` z mainu a Codex čte skilly z mainu — na ruční
pokyny k merge se zapomíná. Rozpracované mezivýstupy (chunky, drafty) smí do mainu přijet
společně s finály; vadí jen chybějící finály, ne pracovní stopa navíc.

**Výjimka (Ondřej 2026-09-08):** větev `goal/turismus-velke-kolo-prepisu-2026-09` se do `main`
NEsynchronizuje — push jen na tuto větev + draft PR, merge až po Ondřejově review. Zadání a
podmínky běhu: `10_documents/active/2026-09-08_zadani_velke_kolo_prepisu_turismus_goal_beh.md`.

**Výslovná výjimka 2026-09-18:** Ondřej schválil okamžité publikování a aktivaci
nové univerzální metodiky indexů do mainu. Toto svolení se týká metodiky a jejího
zapojení; nepřenáší celý rozpracovaný turistický běh ani jeho finály. Publikace
se připravuje nad aktuálním mainem, bez force-pushe a přepsání novějších změn.

## Univerzálnost metodiky a správa schválení

Každý indikátor má vlastní doložený katalog faktorů z obsáhlé rešerše. Stejný
faktor se smí opakovat napříč indikátory, pokud je pro každý relevantní. Nepřebírej
automaticky celý katalog, počet, data či směry hodnocení bez věcného posouzení.
Úplnost znamená pokrýt relevantní mechanismy; uživatel může katalog následně doplnit.
Konkrétní případy jsou jen podmíněné příklady. Novou změnu otestuj i na jiném
indikátoru a jiné velikosti katalogu. Brainstorming není schválené pravidlo.
Schválený stav a jeho supersesi zapiš v aktivních instrukcích, manifestu a můstku.

Chráněná ústava zůstává beze změny; rozsah operativní přednosti přímého
uživatelského rozhodnutí eviduje
`05_epizode_packs/_skills/adversarial_loop/NOVELA-indexy-prumer100-2026-09-18.md`.
Její pozdější konsolidace nesmí blokovat již schválenou aktivaci skillu.

## Komunikační pravidlo „ŽÁDNÉ KÓDY BEZ PŘEKLADU" (ZÁVAZNÉ, Ondřej 2026-08-18)

Ondřej a model si musí rozumět bez interního žargonu. Proto:

1. **Interní kódy (v23, O7, E-001, P30, S_A2…) se v komunikaci s Ondřejem NIKDY nepoužívají
   samostatně** — vždy s vysvětlením v obyčejné češtině, nebo se místo kódu rovnou řekne věc
   („nový potenciál — jen neměnné faktory"). Kód smí zůstat v závorce kvůli dohledatelnosti. Platí to i pro NOVĚ vznikající
   pojmenování: pracovní balíky, vrstvy a nástroje dostávají srozumitelné české jméno
   („velké kolo přepisu finální analýzy"), interní kód smí existovat jen v závorce —
   session nikdy nezavádí novou zkratku jako hlavní jméno věci (poučení „balík D1",
   2026-09-01).
2. **Rozhodnutí se Ondřejovi předkládají jako obyčejné otázky** s kontextem a doporučením,
   po malých dávkách — nikdy jako seznam kódů.
3. **`PREKLADOVY_MUSTEK.md` (kořen repa) je živý překladový můstek:** co znamenají verze
   metodik, co je platné / na zkoušku / nahrazené, a co čeká na Ondřeje (jako otázky).
   Každá session, která změní stav verze, návrhu nebo otevřených rozhodnutí, MUSÍ můstek
   aktualizovat ve stejném commitu. Zastaralý můstek = stejná závažnost jako rozbitá brána.
4. Důvod: 2026-08-18 se Ondřej a model míjeli v tom, která verze potenciálu se používá
   („šest faktorů vs třicet") a co znamená „potvrdit E-001" — stálo to důvěru i tokeny.
5. **Otázky vyžadující Ondřejovo rozhodnutí se pokládají PŘÍMO V CHATU** — v textu
   odpovědi dané session, ne (jen) na konci vygenerovaného dokumentu či artefaktu,
   protože ke koncům dokumentů se Ondřej většinou nedostane. Dokumenty
   a `PREKLADOVY_MUSTEK.md` slouží jako dohledatelný ZÁZNAM otázek, nikdy jako
   jediný doručovací kanál. (Ondřej 2026-08-31.)

## BRÁNA ESKALACE — kdy rozhoduje Ondřej a kdy stroj (ZÁVAZNÉ, Ondřej 2026-09-01)

Cíl projektu je autonomní analýza: Ondřej NEMŮŽE rozhodovat dílčí faktory
(„Já opravdu nemohu vše detailně rozhodovat"). Před položením jakékoli otázky
Ondřejovi projde rozhodnutí touto branou:

**Ondřej rozhoduje POUZE:**
- změny ústavy a trvalých definic (co je potenciál/strategie/průběh hry, brány, škály);
- rozhodnutí s **hmotným dopadem** podle citlivostního testu: překlopení verdiktu,
  pásma bilance, mluvené věty, nebo posun pořadí o víc než ~2 příčky;
- vkus a dramaturgii (co a jak se vypráví divákovi, vizuální styl);
- to, co si výslovně vyhradil.

**Vše ostatní se rozhoduje autonomně a jen se LOGUJE do můstku:**
1. Kde existuje deterministické pravidlo, rozhoduje pravidlo — session ho NESMÍ
   obcházet nabídkou „doporučuji A, ale vyber si" (přesně tento anti-vzor bránu zrodil:
   pravidlo „max přes epizody" bylo napsané, a stejně se položila otázka Karel vs Rudolf).
2. Kde pravidlo nestačí, běží adversariální protokol: návrh s prameny → slepá kritika
   s nezávislým researchem → citlivostní test; co přežije, platí.
3. Nehmotná rozhodnutí (citlivostní test nic nepřeklápí) se uzavírají konzervativním
   defaultem hned; u nejistých číselných odhadů platí nově **zdůvodněný centrální odhad s citlivostí**,
   nikoli automatická dolní kotva, podle schváleného univerzálního doplňku 18. 9. 2026.

Otázka, která branou neprojde, se Ondřejovi NEpokládá — do můstku se zapíše rozhodnutí
s odůvodněním („rozhodnuto branou eskalace"). Když session váhá, jestli je dopad hmotný,
spočítá citlivostní test místo ptaní.

## Pravidlo „OBOHACUJ, NENAHRAZUJ" (ZÁVAZNÉ, Ondřej 2026-08-03)

Při každé předělávce epizodního dokumentu (FINAL_analysis, FINAL_voiceover, karta…)
nebo skillu se **vychází z poslední funkční verze**: její struktura a slovní rozbory
se zachovávají a nové požadavky se do ní **vkládají jako označená vrstva nebo bodové
úpravy**. Kompletní přepis od nuly je povolen JEN na výslovný pokyn Ondřeje
(„napiš to celé znovu"). Nová verze musí v hlavičce říct, co se změnilo a co zůstalo.
Důvod: model opakovaně „upgrade" pojal jako nový koncept a zahodil měsíce slovních
rozborů (naposledy FINAL_analysis_v22 ep_002, která vyhodila v20 rozbory potenciálu,
strategie a results). Porušení = zahodit a udělat znovu z původního dokumentu.

**Vynucení:** každá epizoda má registr povinných sekcí
(`input/_final_analysis_required_sections.yaml`) a před commitem finální analýzy
MUSÍ projít `tools/check_final_analysis_sections.py <analysis> <registr>` s exit 0.
Nové dobré sekce se do registru PŘIDÁVAJÍ (tabulky faktorů, substance sondy);
mazat z něj smí jen Ondřej.

## Sloh voiceoverů (ZÁVAZNÉ, Ondřej 2026-07-14)

- **Scripty se píšou v souvislých, plynulých větách** — tak, jak člověk skutečně mluví a píše.
- **ZÁKAZ staccato stylu:** řetězení úderných krátkých vět a fragmentů („Pět záhad. Jedna listina.
  Začínáme.") je zakázaný default, do kterého model opakovaně sklouzává. Krátká věta je vzácné
  koření na vrchol pointy — ne stavební kámen textu.
- Platí pro všechny části scriptu (intro, segmenty, vyhlášení) i všechny budoucí epizody a ligy.
  Každý writer prompt a každý eval musí toto pravidlo vynucovat.

## Python

- Na Windows vždy používej `py` (ne `python` ani `python3`)

## Image Generation

- **Co se generuje (a jedině to):** country stage panoramata + factor-card diorama kartičky pro bloky 2/3 (skill `voiceover-to-factor-cards-stage-SKILL-v1.0` + v18 tails addendum). „B-roll" pro bloky 1/4 = **stock video** (Pixabay/Pexels, fáze `6_7_stock_video_selection`) — **fotorealistické B-roll obrázkové prompty se NETVOŘÍ** (Ondřej 2026-07-17; skill `broll-prompt-generator` archivován v `05_epizode_packs/_skills/_legacy/`, nepoužívat, dokud ho Ondřej výslovně nevrátí).
- **Výběr kartiček (aktualizováno přímým pokynem 19. 9. 2026):** kanonický výběr ve `FINAL_analysis.md` a `output/production_factor_selection.yaml` má 3 potenciálové + 1 krátkodobý (2 jen s konkrétním odůvodněním) + 3 dlouhodobé faktory. Writer, grafika, animace a střih přebírají stejná stabilní ID, revizi a významový důraz; všechny vybrané faktory se zmíní. Výběr podle hlavního analytického skillu §5.2 nemění úplný katalog ani váhy indexů. `tools/build_selected_cards_from_tails.py` dává tomuto výběru přednost; pouze historické packy bez něj používají taily a staré počty 2–4/1–3. Grimasy vycházejí z doloženého hodnocení analytika, nikdy mechanicky z bodů.
- **Aktivní render** (od 2026-07-17, po zrušení Codex aplikace): **`scripts/render_images_gpt2.py`** — volá OpenAI API s modelem **gpt-image-2** přímo nad zmrazenými prompty (`country_stage_prompts.json` + `factor_card_image_prompts.json` přes resumovatelnou render queue). Vyžaduje `OPENAI_API_KEY` (API kredit z platform.openai.com — ChatGPT subscription API nepokrývá). Spouští Ondřej na PC přes `py`. Aplikace Work (nástupce Codexu) je nespolehlivá — nepoužívat jako primární cestu.
- **Nano Banana je MIMO PROVOZ — nepoužívat.** `google-genai` SDK (Nano Banana **Pro** `gemini-3-pro-image-preview` i Nano Banana 2 `gemini-3.1-flash-image-preview`) má **nefunkční API**. Nano Banana Pro (panoramata) je odstraněná.
- **Legacy skripty NESPOUŠTĚT**: `imagen3_pro_generator.py` a `scripts/generate_factor_card_images.py` / `scripts/generate_country_stage_images.py` volají to mrtvé Nano Banana API. Render dělá agent přes gpt-image 2.0, ne tyto skripty.
- **NIKDY nepoužívej Imagen 4.**

## Agentní tým

8 agentních rolí definovaných v `.claude/agents/`:

| Agent | Soubor | Fáze | Skill |
|-------|--------|------|-------|
| Researcher | `researcher.md` | 1a, 1b | `data-collection-SKILL-v1.0` + `national-data-collector-SKILL-v2.0` |
| Research Agent | `research-agent.md` | 1.5 | `research-agent-SKILL-v2.0` |
| Analyst | `analyst.md` | 2 | `european-economy-analysis-v23-zlaty-standard` (vrstva) + `european-economy-analysis-v22-dva-kameny` (základ) |
| Writer | `writer.md` | 3 | `voiceover-skill-v10-live-race` + aktuální společná vrstva kvality |
| Reviewer | `reviewer.md` | 3 | `voiceover-review-skill-v2_4` + `voiceover-creative-control-v2_3` |
| Visual Designer | `visual-designer.md` | 4 | `script-to-broll-prompts-SKILL-v2.1` |
| Tech Executor | `tech-executor.md` | 3.5, 5 | `section-14-1-elevenlabs-tts` + Nano Banana 2 API |
| Remotion Architect | `remotion-architect.md` | 6 | `remotion-skill-v7_4` |

Team Lead = hlavní Claude Code session (koordinátor, nemá vlastní definici).

**Model podle úkolu (Ondřej 2026-09-13, univerzální):** koordinuje vždy nejsilnější dostupný model (Codex nebo Claude Code) a přiděluje každému úkolu nejlevnější model, který ho prokazatelně zvládne (levné patro = mechanika, střední = bodové úpravy a testy, drahé = kontrola a úsudek, nejsilnější = slepá kritika a metodika); když levnější model nestačí, povolá se silnější. Znění: skill `european-economy-analysis-v23-zlaty-standard.md` §11; do ústavy jako novela (edituje jen Ondřej).

## Claude Code Runtime

- `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` je v uživatelském prostředí zapnuto, ale pro tento projekt **nepoužívej plné TeamCreate/Agent Teams orchestrace** pro celou pipeline.
- Pipeline je převážně sekvenční. V Claude Code preferuj samostatné sub-agenty/Agent tool s `mode: auto` a explicitním promptem pro každou fázi.
- Pokud krok potřebuje Bash nebo Web nástroje, preferuj `general-purpose` sub-agenta s plným promptem místo custom agent type. V minulých bězích se custom agent types zasekávaly na permission handoff.

## Skills (AI instrukce)

## Pipeline Source-of-Truth Rule (závazné od 2026-05-07)

**Karta indikátoru (`input/karta_indikatoru.yaml`) je single source of truth pro strategický obsah epizody.**

- Skilly (analyst, voiceover writer) nikdy nedoplňují strategická fakta za běhu. Pokud chybí rozhodující fakt, analytik zadá konkrétní mechanism_research_target a vymezí známé části. Writer nesmí vymýšlet příčinu ani přeskočením strategie předstírat kompletní analýzu; před psaním musí vzniknout diagnostická karta podle společné vrstvy kvality.
- Žádný build skript ani Python helper nesmí generovat `dominant_narratives` z `factor_matrices_all.json` percentilů. Factor matrix je screening tool pro Potential, ne náhrada za Strategy.
- `validate_indicator_card.py` musí projít s exit code 0 dřív, než kterákoliv pozdější fáze (research, analysis, voiceover) může běžet.
- Pokud `validate_indicator_card.py` zjistí prázdné `dominant_narratives` u featured zemí nebo template fill, pipeline se zastaví a vyžaduje manuální/research-based doplnění karty. Žádný automated workaround není povolen.

Tato pravidla nelze obejít přejmenováním proxy faktorů, přidáním audit skriptu, nebo `coverage_tier: missing_strategy`. Strategie buď je v kartě s evidence_url, nebo se v dané epizodě prostě nepodá.

## Adversariální self-improvement loop (od 2026-08-07)

Analytická fáze se ladí adversariálním loopem (generátor × slepý kritik
s nezávislým web researchem): `05_epizode_packs/_skills/adversarial_loop/`
(README = mapa). Klíčové: `USTAVA-v1.0.md` je READ-ONLY, chráněná hashem
(`scripts/adversarial/constitution_guard.py verify`) — mění ji jen Ondřej;
metodickou vrstvu (`METODIKA-v1.0.md`) edituje loop po třídách chyb
s povinnou regresní sadou (`scripts/adversarial/regression_suite.py`).
Kola: `scripts/adversarial/run_round.py`, logy v `evaluation/adversarial_rounds/`.
Kritik před ostrým provozem musí projít kalibrací na seed incidentech
(Mietpreisbremse/DE — viz `adversarial_loop/regressions/`).

Instrukční dokumenty pro generování obsahu jsou v `05_epizode_packs/_skills/`:

### Pipeline skills (hlavní workflow)

> **Aktuální ep_001 handoff (2026-07-29):** Před prací na průměrné mzdě
> povinně přečti
> `05_epizode_packs/ep_001_prumerna_mzda_pps_v18/HANDOFF_BACK_TO_CLAUDE_analysis_v19_rebuild.md`
> a `output/eval_reports/CURRENT_V19_EVAL_STATUS.md`. Staré eval reporty bez
> aktuálních artifact hashů jsou STALE a nesmějí schválit TTS.

- **`pipeline-orchestration-SKILL-v3.md`** — Aktivní master orchestrace; závazný registr fází a fresh-context politiky je `active_pipeline_stack.json`.
- **`data-collection-SKILL-v1.0.md`** — Autonomní stahování dat z Eurostat, OECD, IMF, World Bank pro 27 zemí.
- **`national-data-collector-SKILL-v2.0.md`** — Sběr čerstvých dat z 27 národních statistických úřadů. Eligibility Gate, PPP harmonizace, dvouosá důvěryhodnost (M0-M3 × T0-T3), přepočtový protokol pro cross-check.
- **`research-agent-SKILL-v2.0.md`** — Strukturovaný sběr dat per faktor s percentilovým scoringem (20-100), cross-country tabulky, persistentní Eurostat DB.
- **`european-economy-analysis-v23-zlaty-standard.md`** — HLAVNÍ AKTIVNÍ analytický skill (aktualizace 2026-09-18): vlastní katalog každého indikátoru, uzavřené verdikty A/B/C, analytický úsudek o významu potenciálu a volitelné S_f minus CELKOVÝ P. P, krátkodobá a dlouhodobá strategie se počítají samostatně metodou průměr 100. Zachovány profily, důkazní kontrola, modelová orchestrace a upřesnění definice potenciálu; žádné turistické kvóty, LOO kapacitní reference nebo index posunu.
- **`european-economy-analysis-v22-dva-kameny.md`** — AKTUALIZOVANÝ ZÁKLAD pod hlavním skillem: exogenní potenciál, současné strategické faktory, §3 odkazuje na aktivní mean100 a rovné váhy, nikoli min-max či historický posun. Interpretaci řídí hlavní skill §2.
- **`potencial-index-produktove-svazky-ADDENDUM-v1.0.md`** — věcná konstrukce produktů, kontrola souběhu a dvojího započtení, audit významu a přínosů. Aktuálně jeden finální faktor = jedna váha; dědění počtu původních sloupců, jiný katalog podle země a starý min-max jsou nahrazené metodou 2026-09-18.
- **`european-economy-analysis-v20-capacity-first.md`** — Podklad v22: platí z něj §1.2, §4, §5, §7.1, §8, §9 (v22 na ně odkazuje); konstrukce indexu z v20 §2–3 je nahrazená.
- **`european-economy-analysis-v19-causal-first.md`** — Předchozí causal-first rámec (nahrazen v20→v22); kauzální kontrakt žije dál ve v22 §5 odkazem.
- **`voiceover-skill-v10-live-race.md` (v10.3) + `voiceover-styl-ondrej-SKILL-v1.0.md`** — AKTIVNÍ writer kontrakt (Ondřej, laděno 2026-07-24→28; závazné i pro ep_001). Formát „živý závod": abecední startovní listina, PAPER FORM/OČEKÁVÁNÍ před výsledkem, GAME PLAN + TARGET BAND, živá tabulka se zásuny, checkpointy po 10/20, vyhlášení §7. Stylová vrstva styl-ondrej v1.0 při konfliktu vyhrává. Kauzalita se vynucuje NAVRCH přes `causal-claim-repair-ADDENDUM-v1.0.md` (bodové opravy à la cr/cr2 vzorky — styl se při nich zachovává). Kanonický vzor: `output/v14/FINAL_voiceover_v14.md` + cr2 segmenty.
- **`voiceover-uvod-SKILL-v1.0.md`** — AKTIVNÍ skill mluveného úvodu (Ondřej 8. 10. 2026), nahrazuje §9 writer skillu: trademark „Vítejte u {první} epizody {Evropských/Amerických/Globálních} ekonomických her.“, hned disciplína, bez otázky a výčtu zemí; část „Pojďme se podívat na to, co v dnešní disciplíně uvidíme.“ (favoriti, černí koně a outsideři vždy od Ondřeje — bez nich se FINAL nepíše, trikoty, pořadí startu, tři kola s faktory a vahami, nesčítání kol se smyšleným příkladem, zdroje); nakonec co přesně měříme a rok dat ověřený u zdroje.
- **`voiceover-cestina-SKILL-v1.0.md`** — ČESKÁ JAZYKOVÁ VRSTVA, NA ZKOUŠKU (Ondřej rozhoduje vkus; 2026-09-15, z rešerší R1/R2 a ze slepého porovnání dvou verzí voiceoveru ep_003). Není to writer ani přepis: **omezený druhý průchod** nad hotovou skillovou verzí — mapa bodových oprav jazyka (`scripts/voiceover_apply_replacements.py`), zámek číslovek a hedgí (`scripts/voiceover_cestina_detektory.py --pred`, exit 0), INFO rozdíly kvalifikátorů/první osoby/zesilovačů, čerstvá kontrola, pak standardní slepé čtení + viewer review. §15.1 = pravidla, která obstála (nominalizace → sloveso, aktér místo pasiva, réma na konec, dvojtečka → spojka ne tečka, „kam“, genitiv po „dost“ a u kvantity, statistická jednotka není fyzický objekt); §15.2 = tvrdé zákazy (hodnotící přívlastek u čísla, první osoba u verdiktu, škrtnutý kvalifikátor, změněná kauzální spojka, přidaný fakt, přejmenovaný pojem série, dvojtečka → tečka); §15.4 = otázky vkusu pro Ondřeje (vrstva postoje, první osoba, průvodcovské vazby, nocleh/startér).
- **`voiceover_skill_v_9_2.md`** — LEGACY craft base (v9.x formát bez živého závodu). ⚠ v19/v20 voiceovery napsané podle v9.2 (FINAL_voiceover_v19, první FINAL_voiceover_v20) NEJSOU stylový kanon — Ondřej 2026-07-31: „absolutně nepoužitelný styl"; slouží jen jako zdroj kauzálních formulací.
- **`voiceover-review-skill-v2_4.md`** — Recenzuje voiceover z osy analytické hloubky, kreativní kvality, UI synchronizace a týmové validace.
- **`voiceover-creative-control-v2_3.md`** — Kontroluje a opravuje kreativní kvalitu (pomlčky, vzorce, dynamika, neutralita, VO=✓ ochrana, týmové úvody).
- **`voiceover-to-factor-cards-stage-SKILL-v1.0.md`** — Vizuální handoff bloků 2/3: country stage panorama + factor-card kartičky (locked diorama styl, assembler gate). Pro v18 reason-first packy platí tails addendum (canonical výběr z `output/tails`, počty dle analýzy, ne 3+3).
- **`section-14-1-elevenlabs-tts.md`** — TTS generování + word-level timestamps (ElevenLabs API). Od 1. 10. 2026 jediný hlas `Cak3fRkQmNTeK1EmSkdA` (klon Ondřejova hlasu) a jediný model `eleven_v4` (Eleven v4), oddíl „Závazný hlas a model“.
- **`remotion-skutecny-obraz-sport-SKILL-v1.0.md`** — AKTIVNÍ edit zemí ve fotorealistickém stylu (Ondřej 23. 9. – 8. 10. 2026, prototyp Belgie `be6-real`): skutečné fotky a autentická videa přes celý obraz s pohybem, tempo do 2 s, co nejméně textu (ikony, vlajky, čísla), sportovní grafika s pevnými zónami (karta kola, graf míst proti průběžnému lídrovi, faktory 0–100 v barvě pásma pod sebou, velké průhledné panely ve výsledcích), mapy s vlajkami v hranicích států, moderní hudba po blocích. Pro bloky zemí nahrazuje grafiku z v7_4; diorama z kódu odloženo.
- **`remotion-skill-v7_4.md`** — Remotion editační instrukce v7.4 (frame-accurate, percentile bars, TeamBanner, TeamLeaderboard, component registry, timestamps sync, týmová episode struktura).
- **`intro-part1-trademark-startlist-SKILL-v1.0.md`** — LOCKED pixel-exact spec pro Part 1 úvodu (`INTRO_trademark`): keyovaný „blueprint" Economic Games trademark reveal přes B-roll + 27-country START LIST (15+12). Série-level, indikátor-agnostické; mění se jen B-roll. Reference: `ekonomicke-hry-remotion/src/IntroPrototype.tsx` (frames 0–452). Episode-name end card do Part 1 NEPATŘÍ. ⚠ Logo pod právní revizí (podobnost s olympijskými kruhy) — viz LEGAL CAUTION.
- **`intro-part2-teaser-storywindow-SKILL-v1.0.md`** — LOCKED spec pro Part 2 úvodu (`INTRO_teaser`, upoutávka): B-roll + velké navy-glass okno „FIVE MYSTERIES" s featured Codex character-diorama obrázkem přepínaným per VO beat + thumbnail board. Struktura locked, obsah (záhady/obrázky/hooky/VO/B-roll) per-epizoda. Finál = všech 5 obrázků; „?" karta jen fallback. Brand mark = nové NE-olympijské logo (ne rings badge). Reference: `src/IntroPart2Teaser.tsx` (composition `ep1-intro-part2`).
- **`thumbnail-figurine-row-SKILL-v1.0.md`** — Univerzální YouTube thumbnail (gpt-image-2, 16:9): zamčený handmade figurine-row styl (vítězný v3 směr z ep_001 dle Ondřejovy reference) + rozhodovací postup per epizoda (4 role zemí: šampion/dno/paradox/twist, grimasy z kartičkové škály, rekvizity po kolena vázané na indikátor, 2slovné ALL-CAPS labely). Létající ostrůvky a terasovité krajiny = zamítnuté slop směry, neopakovat.
- **`intro-part3-explainer-SKILL-v1.0.md`** — GENERATIVE BRIEF pro Part 3 úvodu (`INTRO_explainer`): vysvětlení zkoumaného indikátoru. Zamčený rámec (navy-glass olympijské „discipline-briefing" okno + identita + sync na VO + 100% Remotion / žádné generované panely + žádné rings logo) a zamčená **3-aktová kostra (01 THE RULE / 02 THE METRIC / 03 THE STAKES)**; ale grafika UVNITŘ každého aktu je **generativní per indikátor** (discovery procedure + paleta vzorů + laťka kvality + worked example). NEKopírovat ep1 wage grafiku — ODVODIT. Reference: `src/IntroPart3Explainer.tsx` (composition `ep1-intro-part3`).

### Legacy skills
- `CURSOR_MASTER_TEMPLATE_v2.md` — Starší master template pro batch generování
- `pipeline-orchestration-SKILL-v1.md` — Předchozí verze orchestrace (nahrazena v2)
- `european-economy-analysis-v10.0.md` — Předchozí verze analýzy (nahrazena v14.0)
- `european-economy-analysis-v12.0.md` — Předchozí verze analýzy (nahrazena v14.0)
- `european-economy-analysis-v13.0.md` — Předchozí verze analýzy (nahrazena v14.1)
- `european-economy-analysis-v14.0.md` — Předchozí verze analýzy (nahrazena v14.1)
- **Staré voiceover verze** → přesunuty do `05_epizode_packs/_skills/_legacy/` (voiceover skill v7.2–v9.0 + obě v9.1 addenda, review v2.1–v2.3, creative-control v2.1–v2.2, voiceover-to-cursor v8.0). Aktuální závazný writer určuje active_pipeline_stack.json: živý závod v10 plus společná vrstva kvality z 2026-09-06. Starší v9.x jsou historický craft base.
- `remotion-skill-v7_2.md` — Předchozí verze Remotion (nahrazena v7.4)
- `script-to-broll-prompts-SKILL-v2.0.md` — Předchozí verze B-roll (nahrazena v2.1)
- `research-agent-SKILL-v1.0.md` — Předchozí verze research agenta (nahrazena v2.0)
- `script-to-broll-prompts-SKILL-v1.3.md` — Předchozí verze B-roll (nahrazena v2.0)
- `script-to-broll-prompts-SKILL-v2.0.md` / `v2.1` — Fotorealistické B-roll obrázkové prompty — **CELÁ LINIE VYŘAZENA** (broll = stock video; obrázky = stage + kartičky). Netvořit podle nich.
- `_legacy/broll-prompt-generator/` — bývalý Claude Code skill na fotorealistické prompty (Nano Banana 2) — archivován 2026-07-17, nepoužívat.

## Struktura složek

```
05_epizode_packs/
├── _skills/              ← AI instrukce (skills pro generování)
├── _template/            ← Šablona pro nové epizody
└── ep_XXX_<název>/       ← Konkrétní epizoda
    ├── input/
    │   ├── data_spec.md          ← Vstup od uživatele (zdroje dat, indexy)
    │   ├── raw_data.md           ← Stažená data (27 zemí)
    │   ├── metadata.json         ← Zdrojové informace o datech
    │   ├── FINAL_analysis.md     ← Analytický brief
    │   └── data.json             ← UI data pro Remotion (auto-generováno Analyst)
    ├── output/
    │   ├── voiceover_draft_v1.md        ← První draft scriptu
    │   ├── review_report.md             ← Analytická recenze
    │   ├── voiceover_cc_rewrite.md      ← CC přepis
    │   ├── FINAL_voiceover.md           ← Finální script
    │   ├── broll_prompts.json           ← B-roll prompty (JSON)
    │   └── remotion_edit.json           ← Remotion edit instrukce
    └── audio/
        ├── {ISO2}_{block}.mp3           ← TTS audio per blok
        └── {ISO2}_{block}_timestamps.json ← Word-level timestamps

06_generated_images/
└── ep_XXX_<název>/
    ├── {ISO2}_{SEQ}_{location}.png  ← B-roll obrázky (Nano Banana 2)
    └── manifest.json                ← Status generování

.claude/agents/                      ← Agentní definice (8 rolí)

scripts/
├── imagen3_pro_generator.py  ← API wrapper (Nano Banana 2 + Pro)
├── generate_cz_images.py     ← Legacy: base layers
└── generate_cz_overlays.py   ← Legacy: overlays s transparentním bg
```

## Workflow: Nová epizoda (agentní tým)

Viz `pipeline-orchestration-SKILL-v3.md` a `active_pipeline_stack.json` pro kompletní workflow.

Stručně:
1. **Data Collection** (Researcher: Fáze 1a centrální DB + Fáze 1b národní úřady) → Checkpoint 1
2. **Analytical Brief** (Analyst) → Checkpoint 2
3. **Voiceover Script** (Writer → Reviewer → Writer rewrite) → Checkpoint 3
4. **TTS Generation** (Tech Executor) → automaticky
5. **B-Roll Prompts** (Visual Designer) → Checkpoint 4
6. **Image Generation** (Tech Executor) → Checkpoint 5
7. **Remotion Edit** (Remotion Architect) → Checkpoint 6
