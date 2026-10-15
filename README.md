# TV regularizacija u tomografskoj rekonstrukciji

Projekt iz kolegija **Inverzni problemi i strojni vid** — Prirodoslovno-matematički fakultet, Matematički odsjek, Sveučilište u Zagrebu.

Rekonstruira se sintetički fantom veličine **32 × 32 piksela** iz šumnog paralelnog sinograma (**45 kutova, 32 zrake po kutu**). Uspoređuju se filtrirana povratna projekcija (**FBP**), **Tikhonovljeva regularizacija** i **anizotropna totalna varijacija (TV)** s nenegativnim rješenjem. Za TV minimizaciju koristi se **Chambolle–Pockov primalno-dualni algoritam**.

Regularizacijski parametar odabire se **Morozovljevim pravilom diskrepancije**: rezidual rekonstrukcije približno se izjednačuje s poznatom razinom šuma, bez korištenja originalne slike.

## Matematički model

Rješava se optimizacijski problem

$$
\min_{x \ge 0} \; \frac12\|Ax-y^\delta\|_2^2 + \lambda\|Dx\|_1,
$$

gdje je $A$ diskretizirana Radonova/Roentgenova transformacija, $y^\delta$ šumni sinogram, a $D$ diskretni gradijent s periodičkim rubnim uvjetima. Pritom je $\|Dx\|_1$ anizotropna totalna varijacija.

Koraci algoritma biraju se kao $\tau=\sigma=0.99/\|[A;D]\|$.

Za sintetička mjerenja poznata je razina šuma $\delta=\|y^\delta-y^0\|_2$. Morozovljev parametar zadovoljava

$$
\|Ax_\lambda-y^\delta\|_2 \approx \delta.
$$

## Rezultati

Rezultati su iz spremljenog izvođenja priložene bilježnice na **izvornom skupu podataka**. Brojevi se ne odnose na neki zamjenski generirani skup.

| Metoda | Parametar | Relativna pogreška prema originalnoj slici |
|:--|:--|--:|
| FBP | — | 0.184 |
| Tikhonov | $\alpha=0.1$ | 0.487 |
| TV, početni pokušaj (preslaba regularizacija) | $\lambda=10^{-3}$ | 0.180 |
| **TV, Morozovljevo pravilo** | **$\lambda\approx0.321$** | **0.102** |

Za taj je eksperiment $\delta\approx3.234$, a rezidual Morozovljeve rekonstrukcije približno je $3.234$. Početni $\lambda=10^{-3}$ daje rezidual $2.244$ (manji od razine šuma), odnosno djelomično prati šum.

![Usporedba originala i rekonstrukcija](figures/reconstruction-comparison.png)

![Rezidual i relativna pogreška za različite vrijednosti parametra lambda](figures/morozov-diagnostics.png)

**Važna ograda:** najmanja relativna pogreška na dodatno ispitanoj mreži iznosi $0.075$ pri $\lambda=0.1$, ali taj se odabir oslanja na *poznatu originalnu sliku*. Morozovljevo pravilo daje dobar, podatkovno opravdan parametar **bez poznavanja originala** i ne jamči globalni minimum relativne pogreške. Tikhonovljev $\alpha=0.1$ nije dodatno optimiran, pa usporedba ne dokazuje opću nadmoć jedne metode.

## Organizacija repozitorija

```text
TV_tomografija_Morozov.ipynb     # Matematička teorija, implementacija i rezultati
README.md                       # Opis projekta i upute
requirements.txt                # Python biblioteke
.gitignore                      # Zanemaruje lokalne podatke i privremene datoteke
data/                          # Mjesto za originalne podatke (prazno na GitHubu)
figures/                       # Sačuvane ilustracije iz izvođenja bilježnice
```

## Pokretanje

Potrebna je instalacija Pythona **3.11+**.

1. Klonirajte repozitorij i uđite u njegovu mapu.

   ```bash
   git clone https://github.com/VAŠE-KORISNIČKO-IME/TV-tomografija.git
   cd TV-tomografija
   ```

   Zamijenite `VAŠE-KORISNIČKO-IME` svojim GitHub korisničkim imenom ili otvorite lokalnu mapu ako projekt još nije na GitHubu.

2. Preporučeno je izraditi virtualno okruženje i instalirati ovisnosti:

   ```bash
   python -m venv .venv
   source .venv/bin/activate       # Windows: .venv\Scripts\activate
   python -m pip install -r requirements.txt
   ```

3. Nabavite originalnu arhivu `VjezbeTomografija_sinteticki.zip` iz materijala projekta i smjestite je u `data/`. Alternativno, raspakirajte je tako da datoteka bude na lokaciji `data/VjezbeTomografija/sinogram.mat`.

   **Arhiva nije priložena repozitoriju** niti priloženom paketu; bez nje kod za rekonstrukciju namjerno prijavljuje jasnu poruku o nedostajućim podacima. Podatke nemojte javno objavljivati bez provjere dopuštenja.

4. Iz korijenske mape projekta pokrenite:

   ```bash
   jupyter notebook TV_tomografija_Morozov.ipynb
   ```

   Zatim odaberite **Run All**. Izračun je iterativan i može potrajati, osobito pretraživanje parametra $\lambda$ (više TV rekonstrukcija s po 3000 iteracija).

## Ograničenja i moguća proširenja

- Razina šuma $\delta$ u ovom se sintetičkom primjeru određuje uz poznati čisti sinogram; kod stvarnih mjerenja trebalo bi je procijeniti.
- Anizotropna TV koristi periodičke rubne uvjete.
- Moguće je ispitati manje brojeve projekcijskih kutova, različite razine šuma i robusnost izbora $\lambda$.

## Autorstvo i kontekst

**Lucija Kristić** — projekt za kolegij Inverzni problemi i strojni vid, Sveučilište u Zagrebu, PMF – Matematički odsjek.
