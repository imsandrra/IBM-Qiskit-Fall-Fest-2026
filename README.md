# Kwantowa Kostka — Qiskit Fall Fest

Dwie interaktywne aplikacje webowe pokazujące mechanikę kwantową przez analogię do kostki do gry. Działają w pełni **offline**, bez żadnych zależności — wystarczy otworzyć plik HTML w przeglądarce.

Projekt stworzony na **Qiskit Fall Fest**.

---

## Pliki

| Plik | Opis |
|---|---|
| [`quantum_dice.html`](#quantum_dice_v2html--kwantowa-kostka-v2) | Zaawansowana wersja z sferami Blocha 3D i ekranem splątania |
| [`app.html`](#app2html--splątane-kostki) | Prostsza wersja skupiona na efekcie splątania i kolapsie stanu |

---

## `quantum_dice_v2.html` — Kwantowa Kostka 

### Idea

Kostka zbudowana z **3 kubitów** daje 8 możliwych wyników (000–111). Wynik odczytuje się binarnie i dodaje 1 → ścianki 1–6. Wyniki `110` i `111` oznaczają, że kostka **spadła ze stołu** i rzut jest powtarzany.

### Ekran 1 — Ustaw kostkę

- **Trzy sfery Blocha 3D** dla kubitów A, B, C — punkt na sferze reprezentuje stan kwantowy kubitu
- Przeciąganie punktu zmienia θ (prawdopodobieństwo) i φ (fazę)
- **Biegun północny** = pewne `|0⟩`, **biegun południowy** = pewne `|1⟩`, **równik** = superpozycja 50/50
- **Przycisk H** — nakłada bramkę Hadamarda z animacją przejścia punktu na sferze
- **Ściąga** (tabelka wynik → kubity A B C) z podświetleniem najbardziej prawdopodobnej ścianki
- **Histogram teoretyczny** — aktualizuje się na żywo
- **Histogram rzeczywisty** — 120 rzutów z animacją i licznikiem powtórek

### Ekran 2 — Splącz z kostką kolegi

- **Dwie kostki** (X i Y), każda z trzema sferami Blocha
- **Kable CNOT** — splątują odpowiadające sobie kubity kostek X i Y
- Gdy kubit zostaje splątany, jego punkt **zapada się do wnętrza kuli** — kubit nie ma już własnego stanu
- **„Rzuć tylko X"** → histogram Y natychmiast zapada się do jednego słupka (kolaps splątanego stanu)
- **Siatka 6×6** z rozkładem wspólnych wyników
- **Tryb zaawansowany** — H przed pomiarem, pokazuje różnicę między splątaniem a klasyczną korelacją

### Sterowanie sferą Blocha

| Akcja | Efekt |
|---|---|
| Lewy przycisk + drag | Przesuwa punkt kwantowy (zmienia stan kubitu) |
| Prawy przycisk + drag lub Ctrl+drag | Obraca kamerę |
| Przycisk **H** | Bramka Hadamarda z animacją |

---

## `app2.html` — Splątane kostki

### Idea

Uproszczona demonstracja skupiona na jednym efekcie: **jak pomiar jednej kostki natychmiast zmienia rozkład prawdopodobieństwa drugiej** (kolaps stanu kwantowego).

### Funkcje

- **Dwie kostki** (A i B) z przełącznikiem splątania
- **Tryb niezależny** — obie kostki mają własne pokrętła i niezależne rozkłady; rzut A nie wpływa na B
- **Tryb splątany** — kostki tworzą jeden wspólny stan kwantowy; po rzucie A histogram B natychmiast się zmienia
- **Pokrętła** — 3 parametry na kostkę, kontrolujące rotacje Ry na poszczególnych kubitach
- **Histogramy** prawdopodobieństw dla A i B osobno
- **Siatka 6×6** wspólnych wyników

---

## Silnik kwantowy (wspólny)

Obie aplikacje implementują pełny silnik kwantowy w czystym JavaScript — bez żadnych bibliotek.

```
Stan = wektor amplitud zespolonych [re, im] × 2ⁿ
```

| Operacja | Opis |
|---|---|
| `Ry(θ)` | Obrót wokół osi Y sfery Blocha — ustawia P(\|0⟩) i P(\|1⟩) |
| `Rz(φ)` | Obrót wokół osi Z — ustawia fazę |
| `H` | Bramka Hadamarda: `(a,b) → ((a+b)/√2, (a−b)/√2)` |
| `CNOT` | Zamiana amplitud kubitu docelowego gdy kubit sterujący = \|1⟩ |
| `collapse(k)` | Zerowanie niezgodnych amplitud + normalizacja — kolaps stanu |
| `blochVector(k)` | Macierz gęstości zredukowana → wektor `[x,y,z]`; \|r\|<1 dla splątanego kubitu |

### Splątanie przez CNOT

```
|ψ_A⟩ ⊗ |0⟩  →CNOT→  stan splątany

Przykład (A w superpozycji):
(α|0⟩ + β|1⟩) ⊗ |0⟩  →  α|00⟩ + β|11⟩
```

Po pomiarze A = 0: stan natychmiast zapada się do `|00⟩` → B na pewno = 0.  
Po pomiarze A = 1: stan natychmiast zapada się do `|11⟩` → B na pewno = 1.

---
### Bramki

- **H (Hadamard)** — tworzy superpozycję stanów kubitu, rozdzielając amplitudę pomiędzy `|0⟩` i `|1⟩`.
- **Ry (obrót)** — modyfikuje amplitudy kubitu w zależności od zadanego kąta obrotu.
- **CNOT** — odwraca kubit docelowy, gdy kubit sterujący ma wartość `1`. W projekcie łączy odpowiadające sobie kubity obu kostek.
- **Collapse (redukcja stanu)** — zeruje amplitudy stanów niezgodnych z wynikiem pomiaru i normalizuje pozostały stan.
- **Normalizacja** — zapewnia, że suma prawdopodobieństw wszystkich stanów wynosi `1`.

### Splątanie i pomiar

W trybie niezależnym obie kostki przygotowywane są osobno, a pomiar jednej nie wpływa na rozkład prawdopodobieństwa drugiej.

W trybie splątanym trzy bramki CNOT łączą odpowiadające sobie kubity kostek A i B. Pomiar jednej kostki powoduje redukcję wspólnego stanu kwantowego, co może zmienić rozkład prawdopodobieństwa drugiej kostki. Aktualizacja prezentowana jest na wykresach marginalnych oraz w macierzy prawdopodobieństw wspólnych.

## Wymagania

- Nowoczesna przeglądarka (Chrome, Firefox, Safari, Edge)
- Brak połączenia z internetem — pliki działają w pełni lokalnie
- Brak instalacji, brak node.js, brak zależności

---

## Uruchomienie

```
# Sklonuj repozytorium
git clone <url>

# Otwórz wybrany plik w przeglądarce
start quantum_dice_v2.html   # Windows
open quantum_dice_v2.html    # macOS
xdg-open quantum_dice_v2.html  # Linux
```

---

*Projekt stworzony na Qiskit Fall Fest.*
