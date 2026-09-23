# Produkcijska revizija — moja spletna stran (portfolio)

Datum: 2026-09-22
Obseg: statični pregled kode 6 HTML datotek v tej mapi (brez zagona strežnika, brez avtomatiziranega testiranja v brskalniku, brez pravnega mnenja).
Metoda: ročni pregled + iskanje po vzorcih (grep) čez celotno kodo — HTML, CSS, JS, meta podatki.

**Pomembno, preden začneš popravljati:** Nisem odvetnik in to ni pravni nasvet. Kjer je potrebna pravna presoja (npr. točna vsebina politike zasebnosti, poslovni podatki), je to jasno označeno kot `LASTNIK/OWNER INPUT REQUIRED` ali `PRAVNI NASVET PRIPOROČEN`. Nič od spodnjega ne naredi strani "100% varne pred tožbami" — zmanjšuje tveganja, ne odpravlja jih v celoti.

---

## PRIORITETNI SEZNAM (popravi najprej)

| # | Težava | Datoteka | Resnost |
|---|---|---|---|
| 1 | Glavna stran nima Politike zasebnosti, LEPrav ima resnični kontaktni obrazec | moja spletna stran.html | **Visoka** |
| 2 | Manjkajo poslovni/pravni podatki (ime, naslov, morebitna registracija) | moja spletna stran.html | **Visoka** |
| 3 | "#1 demo mizarstvo.html" nima VIDNE oznake "to je fiktiven demo" — edina od 5 demo strani brez tega | #1 demo mizarstvo.html | **Visoka** |
| 4 | Google Fonts naložen neposredno z Googlovega CDN-ja (pošlje IP obiskovalca Googlu pred privolitvijo) v 2 datotekah | demo restavracija #2.html, #1 demo mizarstvo.html | Srednja |
| 5 | 4 od 5 demo strani nimajo `noindex` — iskalniki jih lahko indeksirajo kot resnična podjetja (cisto.html ima celo polno LocalBusiness strukturirano shemo s fiktivnim naslovom) | demo #5.html, vilina-hotel_1.html, cisto.html, #1 demo mizarstvo.html | Srednja |
| 6 | Imena datotek s presledki in `#` (npr. "demo #5.html") — nezanesljivo pri nekaterih gostovanjih/URL-jih; glavna stran nima imena `index.html` | vse | Srednja |
| 7 | Statični `mailto:YOUR_EMAIL_HERE` placeholder v HTML kot fallback, če JS ne požene | moja spletna stran.html:666 | Nizka (JS ga prepiše, a brez JS/noscript ostane pokvarjeno) |

---

## 1. GDPR IN ZASEBNOST

### Kaj stran dejansko zbira
- **moja spletna stran.html** (resnična stran): kontaktni obrazec zbira ime, e-pošto, podjetje (neobvezno), sporočilo. Obrazec **ne pošilja ničesar na strežnik** — gradi samo `mailto:` povezavo (vrstica ~1091), ki jo mora obiskovalec sam odpreti in poslati iz svojega e-poštnega programa. To pomeni: **stran sama ne shranjuje in ne prenaša teh podatkov nikamor** — obdelava se zgodi izključno v brskalniku obiskovalca, dokler se sam ne odloči poslati e-pošto. To je zelo nizko tvegan vzorec, ker ti (lastnik) nisi upravljavec podatkov, dokler ti obiskovalec dejansko ne pošlje e-pošte — takrat pa velja običajna GDPR obveznost za e-poštno komunikacijo.
- **Demo strani** (cisto.html, demo #5.html, demo restavracija #2.html, vilina-hotel_1.html): obrazci **eksplicitno in vidno** povedo "podatki se ne pošiljajo nikamor" / "V predstavitveni različici obrazec ne pošilja podatkov." Preverjeno v kodi — res ne kličejo nobenega `fetch()` ali zunanjega endpointa. ✅
- **#1 demo mizarstvo.html**: obrazec je tehnično enako neaktiven (glej razdelek 4), a **brez vidnega opozorila** — problem je torej v komunikaciji do obiskovalca, ne v dejanskem pošiljanju podatkov.
- **IP naslovi**: nobena datoteka jih eksplicitno ne zbira v kodi, a strežnik/gostovanje, kjer boš stran gostil, bo IP naslove beležil v dnevniških datotekah po privzeti nastavitvi gostovanja — to ni v tvoji kodi, ampak v konfiguraciji gostitelja. `LASTNIK/OWNER INPUT REQUIRED`: preveri, kakšne dnevnike vodi tvoj ponudnik gostovanja in za koliko časa.
- **Piškotki**: nobena datoteka ne nastavlja `document.cookie`. ✅
- **localStorage**: uporabljen izključno za shranjevanje **izbire jezika** (npr. `lang`, `cisto2-lang`, `lipa-lang`, `ognjena-lang`) — nobenih osebnih podatkov, nobenega sledenja. To je funkcionalno shranjevanje, ki po splošni razlagi ePrivacy direktive praviloma **ne potrebuje soglasja** (šteje kot "nujno potrebno" za funkcijo, ki jo je uporabnik sam izbral), a je vseeno priporočljivo na kratko omeniti v politiki zasebnosti/piškotkih zaradi transparentnosti.

### Kaj manjka
- **Politika zasebnosti**: ne obstaja nikjer, niti na glavni strani, ki dejansko zbira ime/e-pošto/sporočilo prek obrazca. Tudi če se podatki ne prenašajo na strežnik, GDPR 13. člen zahteva transparentnost ob zbiranju osebnih podatkov prek obrazca na spletni strani — priporočam kratko, jasno obvestilo ob obrazcu ali povezavo do ločene strani s Politiko zasebnosti. `PRAVNI NASVET PRIPOROČEN` za točno besedilo, glede na tvoj status (s.p., d.o.o., zasebnik).
- **Pravice posameznika** (dostop, izbris, ugovor) niso nikjer omenjene.
- **Kontaktna oseba za zasebnost** ni navedena — lahko je ista e-pošta kot za posel, a mora biti eksplicitno navedena v politiki.

---

## 2. PIŠKOTKI IN SLEDENJE

**Dobra novica:** Po celotnem pregledu kode (vseh 6 datotek) nisem našel:
- Google Analytics / Google Tag Manager
- Meta/Facebook Pixel
- Google Ads / remarketing kod
- Hotjar, Microsoft Clarity ali podobnih snemalnikov seje
- reCAPTCHA
- Chat gradnikov (Intercom, Crisp ipd.)
- Zunanjih rezervacijskih orodij
- Kakršnegakoli `document.cookie` klica
- Fingerprinting tehnik

Edina "sledljiva" tehnologija je `localStorage` za izbiro jezika (glej razdelek 1) in — pri 2 datotekah — nalaganje pisav z Googlovega CDN-ja (glej razdelek 5, Google Fonts).

**Pasica za soglasje (cookie banner)**: ne obstaja na nobeni strani. Glede na to, da ni nobenega neobveznega sledenja, ki bi zahtevalo soglasje, pasica morda **ni pravno nujna** — a to je odvisno od končne presoje, ali štejeta jezikovni `localStorage` in nalaganje pisav pri Googlu kot "strogo potrebna" ali ne. Ker piškotkov v klasičnem smislu ni, je tveganje nizko, a `PRAVNI NASVET PRIPOROČEN`, če želiš biti povsem prepričan.

---

## 3. VARNOST

### Skrivnosti / API ključi
Preiskal sem celotno kodo za API ključe, gesla, tokene, AWS/GCP poverilnice ipd. **Ni najdenih pravih skrivnosti.** Edina dva zadetka pri iskanju vzorca "AKIA" sta se izkazala za naključne znake znotraj vgrajenih (`base64`) podatkov pisav (`@font-face`) — to niso poverilnice, ampak legitimni podatki pisave, vgrajeni neposredno v CSS. ✅

### XSS / vbrizgavanje kode
- Vsa mesta, kjer se uporabniško vneseni podatki (ime, sporočilo ipd.) izpisujejo nazaj na stran (npr. povzetek obrazca pred pošiljanjem), uporabljajo **varne metode**: `textContent` ali eksplicitno funkcijo `esc()`, ki pravilno pobegne `&`, `<`, `>`, `"` (in pri restavraciji tudi `'`). ✅
- `cisto.html` ima funkcijo `esc()`, ki NE pobegne enojnega narekovaja (`'`) — v trenutni uporabi (znotraj `<dt>`/`<dd>` besedilnih vozlišč, ne znotraj HTML atributov) to ni izkoristljivo, a priporočam dodati `'` → `&#39;` za doslednost in obrambo v globino, če se koda kdaj ponovno uporabi drugje.
- Nobena datoteka ne uporablja `eval()`, `document.write()` ali vstavlja neposredno neobdelane uporabniške vnose v `innerHTML`. ✅

### Zunanje knjižnice / odvisnosti
- **Ni naloženih zunanjih JavaScript knjižnic** (brez jQuery, brez ogrodij, brez analitičnih skriptov) — vsa koda je "vanilla" JS, napisana v datoteki sami. To pomeni **ničelno tveganje dobavne verige** (supply-chain) prek tretjih JS paketov. ✅
- Edine zunanje zahteve so: Google Fonts (2 datoteki), slike z Unsplash/Pexels (hotlinking, vse datoteke).

### Obrazci in endpointi
- `moja spletna stran.html`: obrazec ne pošilja nikamor (glej razdelek 1).
- 4 demo strani: obrazci eksplicitno ne pošiljajo nikamor, preverjeno v kodi.
- `#1 demo mizarstvo.html`: obrazec ima pripravljeno `fetch()` logiko, ki bi se sprožila **samo**, če bi bil na `<form>` element dodan atribut `data-endpoint="..."`. Trenutno ta atribut **ni nastavljen** (v komentarju je le primer s Formspree URL-jem kot navodilo, ne dejanska povezava) — torej obrazec trenutno resnično ne pošilja ničesar. ✅ A ker to ni vidno uporabniku (glej razdelek 7), priporočam dodati enako vidno opozorilo kot pri ostalih demo straneh.

### Varnostne glave (HTTP headers)
Statične HTML datoteke ne morejo same nastaviti `Content-Security-Policy`, `X-Frame-Options`, `Referrer-Policy` ali HSTS — to se nastavi na nivoju **gostovanja** (Netlify, Vercel, GitHub Pages, cPanel ipd.). `LASTNIK/OWNER INPUT REQUIRED`: glede na to, kje boš stran gostil, priporočam nastaviti vsaj:
- `X-Frame-Options: DENY` ali `Content-Security-Policy: frame-ancestors 'none'` (zaščita pred clickjackingom)
- `Referrer-Policy: strict-origin-when-cross-origin`
- HTTPS/HSTS (večina modernih gostovanj to nastavi samodejno)

Ker ni odvisnosti od zunanjih skriptov, bi lahko celo nastavil precej strogo CSP (npr. dovoli samo `fonts.googleapis.com`, `fonts.gstatic.com`, `images.unsplash.com`, `images.pexels.com`) — a to je odvisno od zmožnosti tvojega gostovanja.

### target="_blank"
Vse povezave z `target="_blank"` imajo pravilno dodano `rel="noopener noreferrer"` — ni tveganja za "reverse tabnabbing". ✅

---

## 4. KONTAKTNI OBRAZCI

| Stran | Kam gredo podatki | Vidno opozorilo | Nepotrebna polja |
|---|---|---|---|
| moja spletna stran.html | Nikamor — samo `mailto:` predloga, obiskovalec sam pošlje | Da ("Sporočilo napišeš tukaj, pošlješ pa ga iz svojega e-poštnega programa.") | Ne — ime, e-pošta, podjetje (neobvezno), sporočilo — sorazmerno |
| cisto.html | Nikamor (preverjeno v kodi) | Da, dvakrat (nad gumbom in v potrditvi) | Ne — telefon je obvezen, kar je smiselno za ponudbo čiščenja |
| demo #5.html | Nikamor (preverjeno v kodi) | Da | Ne |
| demo restavracija #2.html | Nikamor (preverjeno v kodi, ni fetch klica) | Da (`demo_tag` v nogi) | — |
| vilina-hotel_1.html | Nikamor — rezervacijski obrazec samo izračuna prikaz, ne pošlje | Da ("To je konceptni prikaz, rezervacija ni bila oddana.") | — |
| #1 demo mizarstvo.html | Nikamor (endpoint ni nastavljen) | **Ne — manjka** | Ne |

**Spam zaščita**: Ker noben obrazec dejansko ne pošilja podatkov na strežnik, honeypot/CAPTCHA trenutno ni potreben. **Če boš kdaj priklopil resnično pošiljanje** (npr. Formspree na glavni strani ali demo mizarstvo), takrat priporočam:
- Honeypot polje (skrito polje, ki ga boti izpolnijo, ljudje pa ne)
- Formspree ima vgrajeno osnovno zaščito pred spamom
- V politiki zasebnosti jasno navesti, da se podatki obrazca pošljejo tretji strani (Formspree) — glej razdelek 5

---

## 5. SEZNAM ZUNANJIH STORITEV

| Storitev | Namen | Kateri podatki gredo ven | Piškotki/sledenje | Vpliv na zasebnost | Potrebno ukrepanje |
|---|---|---|---|---|---|
| **Google Fonts** (fonts.googleapis.com, fonts.gstatic.com) | Nalaganje pisav | IP naslov obiskovalca, User-Agent | Google fonts CDN teoretično lahko beleži zahteve | Srednji — IP gre Googlu pred kakršnokoli privolitvijo | Uporabljeno samo v `demo restavracija #2.html` in `#1 demo mizarstvo.html`. Priporočam **samo-gostovanje pisav** (kot že narejeno v ostalih 4 datotekah prek `base64` v CSS) za doslednost in boljšo zasebnost |
| **Unsplash** (images.unsplash.com) | Slike (hotlinking) | IP naslov, referer | Ne (a zahteva gre neposredno na njihov CDN) | Nizek–srednji | Slike so na voljo pod Unsplash licenco (prosta komercialna uporaba), a hotlinking pomeni odvisnost od njihove razpoložljivosti in beleženje zahtev na njihovi strani. Za produkcijo priporočam prenesti slike lokalno |
| **Pexels** (images.pexels.com) | Slike (hotlinking), samo v mizarstvo demo | IP naslov, referer | Ne | Nizek–srednji | Enako kot zgoraj — Pexels licenca dovoljuje komercialno uporabo, priporočam lokalno gostovanje slik |
| **Formspree** | Omenjen samo v komentarju kot primer, **ni aktivno povezan** nikjer | — trenutno nič | — | — | Če ga v prihodnje priklopiš (na glavni strani ali demo mizarstvo), moraš to jasno navesti v politiki zasebnosti kot obdelovalca podatkov (processor) in preveriti njihove GDPR pogoje (Formspree ima DPA na voljo) |

Ni najdenih: Google Analytics, GTM, Meta Pixel, Google Ads, YouTube vdelave, Google Maps vdelave (`demo restavracija #2.html` ima samo **ilustrativen/lažen zemljevid**, ne prave Google Maps vdelave — glej vrstico 605), reCAPTCHA, Hotjar, chat gradniki.

---

## 6. AVTORSKE PRAVICE / GRADIVA

- **Slike**: vse fotografije so hotlinkane z Unsplash ali Pexels — obe platformi ponujata brezplačne licence za komercialno uporabo brez obveznega navajanja avtorja. To je nizko tveganje, **a jaz nisem preverjal vsake posamezne fotografije** (npr. ali morda vsebuje prepoznaven zaščiten znak, logotip ali osebo v kontekstu, ki bi lahko bil sporen). Priporočam hiter vizualni pregled slik, ki se dejansko uporabljajo, preden jih objaviš javno.
- **Pisave**: `Crimson`, `Italiana` in druge so vgrajene kot `base64` v `@font-face` — preveri, da imaš za vsako pisavo licenco, ki dovoljuje vdelavo v spletno stran (večina odprtokodnih Google pisav to dovoljuje, a če je katera komercialna, `LASTNIK/OWNER INPUT REQUIRED` za potrditev licence).
- **Koda/predloge**: Koda je videti kot izvirna (roka pisana, ne kopirana iz znane predloge/teme) — brez zaznanih licenčnih glav tretjih ogrodij.
- **Besedilo**: izvirno, v slovenščini in angleščini.

---

## 7. PORTFOLIO / DEMO STRANI — JASNOST OZNAČEVANJA

Zelo dobro izvedeno v **4 od 5** demo datotek — vsaka ima vidno, dvojezično opozorilo "to je fiktivno/konceptno, podatki se ne pošiljajo":

- ✅ `cisto.html` — "Demo projekt. ČISTO je izmišljeno podjetje, podatki niso resnični." (v nogi IN pri obrazcu)
- ✅ `demo #5.html` — "Fiktivni demo projekt za portfolio. Vsi podatki so izmišljeni." + "Predstavitvena stran: podatki se ne pošiljajo nikamor."
- ✅ `demo restavracija #2.html` — "Fiktivni demo za portfolio" v nogi
- ✅ `vilina-hotel_1.html` — "Izmišljen hotel — konceptni dizajn, Demo #2." + jasno v potrditvi rezervacije

- ❌ **`#1 demo mizarstvo.html` — NIMA nobenega vidnega opozorila.** Noga preprosto pravi "© 2026 LEP MIZARSTVO. Vse pravice pridržane." — brez besede "demo", "fiktivno" ali "koncept" kjerkoli na strani, ki bi jo videl obiskovalec. Kontaktni podatki v nogi so sicer očitni placeholderji (`info@vasa-domena.si`, `+386 00 000 000`, `Ulica 00, 0000 Kraj`), a to ni dovolj jasno za povprečnega obiskovalca in ni v skladu s standardom, ki si ga postavil pri ostalih štirih straneh.

  **Priporočilo:** dodaj enako vidno opozorilo kot pri ostalih demo straneh, PREDEN to stran kamorkoli objaviš ali povežeš z glavnim portfoliem. Trenutno ta datoteka sploh ni povezana z `moja spletna stran.html` (preverjeno — noben `PROJECTS` vnos je ne omenja), kar je lahko namerno (star osnutek, nadomeščen z drugimi demoji) — če je tako, jo preprosto izloči iz objave.

- **Lažne ocene/mnenja**: najdene v `cisto.html` in `demo restavracija #2.html`, obe jasno označeni kot izmišljene ("Izmišljena mnenja za demo stran." / "Guest reviews below are fictional demo content"). ✅

---

## 8. DOSTOPNOST (ACCESSIBILITY)

### Splošno stanje: dobro
- `alt` atribut je prisoten na **vseh 55 `<img>` elementih** v vseh datotekah — bodisi opisen, bodisi namerno prazen (`alt=""`) za okrasne slike. Priporočam še ročni pregled, da so slike z `alt=""` res okrasne, ne vsebinske (tega ni mogoče preveriti zgolj s pregledom kode).
- `<html lang="...">` je nastavljen v vseh datotekah, in pri vseh z jezikovnim preklopnikom (SL/EN) se `document.documentElement.lang` **pravilno posodobi ob preklopu** — bralniki zaslona bodo pravilno izgovarjali vsebino v obeh jezikih. ✅
- "Skip to content" povezava je prisotna na vseh straneh. ✅
- `:focus-visible` je definiran (viden fokus obroč) — preverjeno vsaj na glavni strani. ✅
- Obrazci uporabljajo prava `<label for="...">`, `aria-describedby` za sporočila o napakah, `role="alert"` za napake. ✅

### Animacije — posebna pozornost (kot si prosil)
Vseh **6 datotek** ima `@media (prefers-reduced-motion: reduce)` blok, ki:
1. V CSS prisili vse `transition`/`animation` trajanja na skoraj nič (`.001ms !important`)
2. V JS izklopi "reveal on scroll" animacije in takoj prikaže vsebino (namesto čakanja na IntersectionObserver)

To je pravilno in dosledno izvedeno na vseh straneh — obiskovalci, ki imajo v operacijskem sistemu vklopljeno "zmanjšaj gibanje", ne bodo prisiljeni gledati animacij. ✅ To je nadpovprečno dobro izvedeno v primerjavi s tipičnimi portfolio stranmi.

### Kontrast barv
Preveril sem glavne barvne žetone (`--ink` #191917 na `--bg` #F2EFE8) — kontrastno razmerje je zelo visoko (>15:1). Sekundarno besedilo (`--ink-2` #5B5851 na istem ozadju) doseže razmerje **~6.2:1**, kar presega zahtevani prag WCAG AA (4.5:1) za navadno besedilo. ✅ Nisem preveril vsake posamezne barvne kombinacije (npr. gumbi na barvnih poljih, značke) — priporočam hiter test z orodjem, kot je [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/), za preostale kombinacije, posebej na demo straneh z bolj nasičenimi barvami (restavracija, hotel).

### Manjše opažene vrzeli
- Slike prek `background-image: url()` (namesto `<img>`) se pojavijo enkrat (`vilina-hotel_1.html`) — taka slika je nevidna bralnikom zaslona, če nosi pomembno vsebino. Preveri, ali gre za okrasno sliko (v redu) ali vsebinsko (potrebuje `role="img"` + `aria-label` na starševskem elementu).
- Priporočam preizkusiti dejansko navigacijo s tipkovnico (Tab/Shift+Tab/Enter/Esc) skozi vse modalne elemente, meni na mobilnih napravah in akordeone — statični pregled kode kaže pravilno strukturo (`aria-expanded`, `aria-controls`), a dejansko obnašanje priporočam preizkusiti v brskalniku.

---

## 9. POSLOVNI / PRAVNI PODATKI — `LASTNIK/OWNER INPUT REQUIRED`

Glavna stran (`moja spletna stran.html`) trenutno prikazuje samo ime **"Žan Cep"** in ime studia **"znc studios"** ter kontaktni e-poštni naslov `studios.znc@gmail.com` in telefon `+386 51 369 659` (oba že vnesena v kodi, ne placeholderja — dobro).

Glede na to, da stran ponuja poslovne storitve (izdelava spletnih strani) in ima kontaktni obrazec, slovenska zakonodaja (npr. Zakon o elektronskem poslovanju na trgu — ZEPT, ki prenaša EU Direktivo o e-trgovini) praviloma zahteva, da so na spletni strani, ki opravlja gospodarsko dejavnost, jasno vidni:

- [ ] Polno ime/firma (osebno ime, če si samostojni podjetnik, ali ime družbe)
- [ ] Pravna oblika (s.p., d.o.o., ali fizična oseba brez registracije — če opravljaš dejavnost priložnostno/postransko, preveri, ali sploh potrebuješ registracijo)
- [ ] Matična številka / številka vpisa v poslovni register (če si registriran)
- [ ] Davčna številka in oznaka davčnega zavezanca, če si zavezanec za DDV
- [ ] Naslov sedeža/poslovni naslov
- [ ] Kontaktni e-mail (že prisoten ✅)
- [ ] Morebitna dodatna dolžnost razkritja, če opravljaš dejavnost kot s.p. ali si zavezan po drugih predpisih

**Nič od tega si nisem izmislil niti ne bom — to moraš vnesti sam, glede na svoj dejanski status.** Če trenutno nisi registriran kot s.p./d.o.o. in stran vodiš kot fizična oseba, `PRAVNI NASVET PRIPOROČEN`, da preveriš, ali tvoja dejavnost (izdelava spletnih strani za plačilo) zahteva registracijo v Sloveniji, preden javno oglašuješ storitve.

---

## 10. PRAVILNIKI (Privacy / Cookie / Terms)

Glede na ugotovitve zgoraj, priporočam za `moja spletna stran.html`:

1. **Politika zasebnosti** — kratka, jasna stran ali razdelek, ki pojasni: katere podatke obrazec zbira (ime, e-pošta, podjetje, sporočilo), da se NE pošiljajo na tvoj strežnik ampak samo pripravijo `mailto:` povezavo, kdo je upravljavec (ti / tvoje podjetje — potrebni podatki iz razdelka 9), in osnovne pravice posameznika.
2. **Cookie/piškotki** — glede na to, da ni pravih piškotkov, zadostuje kratka omemba `localStorage` uporabe za izbiro jezika znotraj politike zasebnosti; posebna pasica verjetno ni potrebna (glej razdelek 2), a `PRAVNI NASVET PRIPOROČEN` za dokončno potrditev.
3. **Pogoji uporabe** — za preprosto portfolio/kontaktno stran praviloma niso nujni, a lahko dodaš kratek odstavek o tem, da so demo/portfolio projekti fiktivni in namenjeni samo predstavitvi (kar že imaš na posameznih demo straneh — lahko strneš na enem mestu na glavni strani).

Demo strani (`cisto.html`) že imajo pripravljene gumbe "Zasebnost"/"Pogoji" — trenutno označeni kot `data-ph` (placeholder, verjetno prikažejo samo obvestilo "še ni na voljo"). To je v redu za fiktiven demo projekt, ni pa v redu za glavno stran, ki dejansko zbira podatke.

**Nisem generiral besedila pravilnikov** — to zahteva tvoje resnične podatke (razdelek 9) in ga lahko pripravim, ko jih imaš, ali pa priporočam kratek pregled pri pravniku/računovodji, če boš posel vodil registrirano.

---

## 11. MARKETING

Ni najdenega: glasila (newsletter), e-poštnega trženja, oglaševalskih skriptov, remarketinga, Meta Pixela, Google Ads ali promocijskih pojavnih oken. Stran trenutno ne izvaja nobenega trženjskega sledenja. ✅ Če boš to v prihodnje dodal (npr. Mailchimp prijava na novice), bo to zahtevalo eksplicitno soglasje (opt-in) in dodatek v politiko zasebnosti.

---

## DODATNE TEHNIČNE OPOMBE

- **Imena datotek**: `#1 demo mizarstvo.html`, `demo #5.html`, `demo restavracija #2.html` vsebujejo presledke in `#` — pri nekaterih gostovanjih/CDN-jih lahko to povzroči težave z URL-ji (znak `#` je rezerviran za "fragment" v URL-ju). Priporočam preimenovati v npr. `mizarstvo-demo.html`, `demo-5.html`, `demo-restavracija-2.html` — in ujemajoče posodobiti povezave v `moja spletna stran.html` (`PROJECTS` objekt, vrstice ~738–742).
- **Glavna datoteka**: `moja spletna stran.html` priporočam preimenovati v `index.html` (ali kar zahteva tvoje gostovanje za privzeto stran), da se stran servira na čistem korenskem URL-ju (npr. `tvojadomena.si` namesto `tvojadomena.si/moja%20spletna%20stran.html`).
- **Relativne povezave med stranmi**: `moja spletna stran.html` se sklicuje na `demo #5.html`, `vilina-hotel_1.html`, `cisto.html` kot **relativne poti** — vse 4 (oz. zdaj še vedno neuporabljena mizarstvo) datoteke morajo biti naložene v isti mapi na strežniku, sicer bodo povezave "Odpri live demo" pokvarjene.
- **`no-JS` scenarij**: Vsaj `demo restavracija #2.html` ima `<noscript>` opozorilo ("This demo needs JavaScript"). Ostale datoteke naj bi prav tako preverile, kaj se zgodi, če JavaScript ne požene (npr. `mailto:YOUR_EMAIL_HERE` placeholder na glavni strani bi ostal viden in nedelujoč).

---

## POVZETEK

Koda je pisana skrbno in nadpovprečno dobro za samostojen (brez ogrodja) projekt: ni sledenja, ni izpostavljenih skrivnosti, XSS zaščita je dosledna, `prefers-reduced-motion` je povsod pravilno obravnavan, dostopnostni temelji (alt, label, lang, focus) so na mestu, in 4 od 5 demo strani imajo vzorno jasno označevanje fiktivnih podatkov. Glavne stvari, ki manjkajo pred javno objavo, niso tehnične napake, ampak **manjkajoča pravna/poslovna transparentnost** (politika zasebnosti, poslovni podatki) in **ena nedokončana oznaka demo strani** (mizarstvo). To je povsem obvladljivo, a zahteva tvoj vnos (pravni status, naslov, itd.), ne le kodo.
