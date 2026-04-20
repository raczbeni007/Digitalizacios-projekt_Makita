# Műtő zajszint szimulátor

Interaktív webes szimulátor fogászati műtők hallható és ultrahangos zajszintjének modellezésére. Minden futtatáskor egy új, véletlenszerű elrendezést generál — random szobaméret, random eszközkészlet, random üzemmódok — és kiszámolja az összegzett zajszintet a hallgató pozíciójában, valós irodalmi mérések alapján.

---

## Áttekintés

A szimulátor egyetlen önálló HTML fájl, ami minden generáláskor:

- **Random szobaméretet** sorsol (3–10 m × 3–10 m között — tipikus fogászati műtő ~4×5 m)
- **4–8 eszközt** választ a 8 támogatott közül, mindegyiket legfeljebb egyszer
- **Random üzemmódot** rendel minden eszközhöz (több támogatott módból)
- **Garantálja**, hogy legalább egy ultrahangos forrás legyen aktív
- **Random pozíciókat** generál a szobában úgy, hogy minden pont legalább 0,5 m-re legyen a többitől és a hallgatótól

Az eredményt három fő metrika, egy felülnézeti térkép (zajszint-heatmap-pal) és egy forrás-bontó táblázat mutatja.

---

## Funkciók

| Funkció | Leírás |
|---|---|
| **Random szcenárió** | Új műtő generálása gombnyomásra vagy `SPACE` billentyűvel |
| **Hallható zaj összegzés** | Az összes audible forrás energikus kombinációja a hallgatónál |
| **Ultrahang csatorna** | Külön kezelt ultrahangos szint (25 kHz sáv és 46,5 kHz csúcs) |
| **NIOSH expozíció** | Max. napi biztonságos expozíciós idő a 3 dB cseretörvény alapján |
| **Zajszint-heatmap** | Színgradiens-térkép mutatja a dB szintet a szoba minden pontján |
| **Forrás-bontás** | Táblázat az L_ref → L_pred átmenetekkel és Δ dB különbséggel |
| **Távolság-vonalak** | Szaggatott segédvonalak a hallgató és minden forrás között |
| **Dinamikus skálázás** | A szoba aránya automatikusan igazodik a generált méretekhez |

---

## Zajszint zónák

A hallható zaj összegzett szintjét a szimulátor a NIOSH (National Institute for Occupational Safety and Health) határértékéhez viszonyítja:

| Tartomány | Besorolás | Jelzés |
|---|---|---|
| `< 70 dB` | Biztonságos zóna — normál beszélgetés szintje | zöld |
| `70 – 85 dB` | Figyelmeztetés — hallásvédelem ajánlott | narancs |
| `≥ 85 dB` | **NIOSH limit fölött** — kötelező hallásvédelem | piros |

### Referencia skála

Összehasonlítási alapként mindennapi zajforrások:

| dB (SPL) | Forrás |
|---|---|
| 30 | suttogás, csendes könyvtár |
| 50 | csendes iroda, hűtőgép |
| 60 | normál beszélgetés |
| 70 | porszívó, zuhany |
| 80 | forgalmas utca, ébresztőóra |
| **85** | **NIOSH expozíciós határ — 8 óra napi maximum** |
| 90 | fűnyíró — biztonságos 2 óra |
| 100 | láncfűrész, kézi fúró — biztonságos 15 perc |
| 107 | ultrahangos depurátor 1/3 oktáv sáv @ 25 kHz |
| 110 | rock koncert — biztonságos 5 perc |
| 115 | **fogászati turbina csúcs @ 46,5 kHz (ultrahang)** |
| 120 | sugárhajtású motor, mennydörgés |
| 130+ | fájdalomküszöb |

> **Fontos:** Az ultrahangos dB értékek *nem hasonlíthatók össze közvetlenül* a hallhatókkal — különböző frekvenciasávban vannak, és az emberi fül ezeket normál módon nem érzékeli. Ezért kezeli a szimulátor külön csatornaként.

---

## Képletek

### Távolság-korrekció (szabadtéri közelítés)

```
L_target = L_ref + 20·log₁₀(d_ref / d_target)
```

Minden megduplázott távolság ~6 dB csökkenést jelent (inverz négyzetes törvény).

### Független források energikus összegzése

```
L_total = 10·log₁₀(Σ 10^(Lᵢ/10))
```

dB értékek nem adhatók össze aritmetikailag — logaritmikus skála. Két 80 dB-es forrás együtt ~83 dB, nem 160.

### NIOSH 3 dB csere — max. napi expozíció

```
T_óra = 8 · 2^((85 − L)/3)
```

| L (dB) | T (óra) |
|---|---|
| 82 | 16 |
| 85 | 8 |
| 88 | 4 |
| 91 | 2 |
| 100 | 0,25 (15 perc) |

Minden 3 dB növekedés felezi a biztonságos expozíciós időt.

---

## Támogatott eszközök

A Source_Data alapján 8 eszköz, összesen 22 (eszköz, mód) kombinációval:

| Eszköz | Módok | Tartomány |
|---|---|---|
| Ultrahangos depurátor | 5 (köztük 1/3 oktáv @ 25 kHz ultrahang) | 51,7 – 107 dB |
| Nagysebességű kézidarab | 4 (bekapcsolva, fogat vág, +szívás variációk) | 63,4 – 85,5 dB |
| Nagysebességű turbina | 1 (average clinical use) | 75,4 dB(A) |
| Kissebességű kézidarab | 2 (bekapcsolva, fogat vág) | 69,2 – 83,8 dB |
| Kissebességű könyökdarab | 1 (average clinical use) | 68,3 dB(A) |
| Mikromotoros fúró | 3 (bekapcsolva, akril vágás) | 62,6 – 92,2 dB |
| Sebészeti szívó | 2 (szabadon / nyálkahártyán) | 70,8 – 78,0 dB |
| Fogászati turbina (ultrahang) | 1 (spectral peak @ 46,5 kHz) | 115 dB SPL |

---

## Irodalmi források

- **Qsaibati & Ibrahim (2014)** — dBA mérések 0,15 m-en, klinikai környezet · [PMC4275629](https://pmc.ncbi.nlm.nih.gov/articles/PMC4275629/)
- **Otoum et al. (2021)** — dB mérések 0,3 m-en, különböző üzemmódok · [RMS Journal](https://rmsjournal.org/Articles/637597959727034783.pdf)
- **Sorainen & Rytkönen (2002)** — 1/3 oktáv sáv ultrahang (25 kHz) · [PubMed 11975661](https://pubmed.ncbi.nlm.nih.gov/11975661/)
- **Barek et al. (1999)** — fogászati turbina spektrális analízis · [PubMed 12271344](https://pubmed.ncbi.nlm.nih.gov/12271344/)

---

## Futtatás

### Helyben

```bash
git clone https://github.com/<MatraBendi>/<Digitalizacios-projekt_Makit>.git
cd <repo>
open index.html
```

Nem igényel backendet, build stepet vagy függőség-telepítést. Egyetlen statikus HTML fájl.

### GitHub Pages

1. Settings → Pages
2. Source: `main` branch, `/` (root)
3. Elérés: `(https://matrabendi.github.io/Digitalizacios-projekt_Makita/)`

---

## Vezérlés

| Billentyű / gomb | Művelet |
|---|---|
| `Új szimuláció` gomb | Új random szcenárió generálása |
| `SPACE` | Ugyanaz, demo közben kényelmesebb |

---

## Technológia

- Vanilla HTML / CSS / JavaScript — nincs framework, nincs build
- Google Fonts: Instrument Serif (display), Geist (body), JetBrains Mono (data)
- SVG alapú vizualizáció, saját heatmap renderer
- ~30 KB egyetlen önálló fájl

---

## A modell korlátai

A szimulátor **első rendű közelítést** ad — egy élő műtő akusztikája ennél lényegesen bonyolultabb. A nem modellezett tényezők:

- **Szobareflexiók** — kemény felületek (csempe, fém) visszaverik a hangot, a valós szint magasabb lehet. Lágy anyagok (függöny) csillapítanak.
- **Árnyékolás** — az eszközök teste, a fogorvos, a páciens blokkolja a közvetlen terjedést.
- **Irányfüggőség** — a legtöbb eszköz nem izotropikus forrás.
- **Légköri elnyelés** (α) — a modell első közelítésben 0-nak veszi. Ultrahangra ez nem elhanyagolható, ISO 9613-1 szerint 20 kHz fölött gyorsan nő.
- **Időbeli dinamika** — pillanatnyi szintet ad, nem időátlagot (Leq).
- **dB / dB(A) keverés** — a szimulátor egyszerűsítetten közös "hallható" kategóriaként kezeli a kettőt.

Éles kockázatbecsléshez valódi mérés és akusztikai szimuláció (sugárkövetés, véges elem módszer) szükséges.

---

## Fájlstruktúra

```
.
├── index.html          # a teljes alkalmazás
├── dental_ultrasonic_noise_model_template.xlsx   # forrás-adatok, képletek
└── README.md                     # ez a fájl
```

---

## Licenc

MIT — szabadon használható oktatási és kutatási célra.

## Credits

Készítette: Makita csapat - Semsei,Rácz,Hímes,Sztaniszláv,Mátravölgyi
Év: 2026
