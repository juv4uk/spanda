# spanda — Е-П танець (E-P Dance)

`spanda` (स्पन्द) — назва репозиторію для проєкту, який власник назвав
**"Е-П танець"** ("E-P Dance"): гібридний комп'ютер для симуляції
всесвіту, задуманий і частково зібраний з 2026-06. Це не новий проєкт —
`spanda` є домом для вже наявної роботи, ідеї, назви й закупленого
заліза.

Повна історія задуму — у [`docs/2026-06-12-e-p-dance-origin.md`](docs/2026-06-12-e-p-dance-origin.md).
Цей README — стиснутий поточний стан.

## Рівноправний трикутник

Три вершини, жодна не головна — кожна говорить своєю природною мовою:

```
                    sens (PC)
                   /         \
          p/q → маска    маска → p/q (АЦП, неперервні дроби)
                 /             \
      Tang Primer 25K FPGA ─── аналогова частина (АОМ)
              маска → напруга (матриця ключів)
```

**Оновлення 2026-09-30:** оригінальний задум (2026-06) називав вершину
PC — **Clojure** (`clojure.lang.Ratio` для точних дробів, immutable
структури для стану всесвіту). Тепер, коли є власна мова **`sens`**
(СЕНС) з точною раціональною арифметикою за конструкцією, вершина PC
стає **sens**, не Clojure. Це свідома заміна власника, зроблена сьогодні
— не те, що обговорювалось у першій розмові 2026-06.

- **sens ↔ FPGA:** дріб `p/q` (точний, довільної ширини — див.
  координацію в `sens#1857`/`cml#387`/`fpga-lisp#38`) → бітова маска
  конфігурації матриці ключів
- **FPGA ↔ аналог:** фізичне керування ключами матриці (MT8816AE, SPI
  від Tang Primer 25K)
- **аналог → sens:** напруга через АЦП → апроксимація найближчим дробом
  (алгоритм Штерна–Броко / неперервні дроби) → назад у `sens`

## Ядро: e і π танцюють в одному колі

Аналогове ядро обчислює `e` і `π` **одночасно** через спільне RC-коло:
інтегратор зі зворотним зв'язком дає `e^(t/RC)`; два інтегратори зі
схресним зв'язком (sin/cos-осцилятор) дають `π` через момент переходу
sin через нуль (`t = π·RC`). У ту саму мить блок `e` фізично показує
`e^π` — не обчислено, а *є* фізикою кола. Це матеріалізація формули
Ейлера `e^(iπ) + 1 = 0`.

RC — не фіксована стала, а число, яке FPGA задає динамічно через
матрицю ключів. Одна зміна `p/q` одночасно масштабує і `e`, і `π`.

## Самокалібрування (три рівні)

Еталон — відоме з нескінченною точністю значення `e^π`, не зовнішній
фізичний зразок:

1. **Аналог** бачить відхилення напруги від еталону — швидко, фізично
2. **FPGA** коригує RC через матрицю ключів — дискретно, точно, як `p/q`
3. **sens** верифікує результат точними раціональними наближеннями —
   повільно, але абсолютно

## Матриця ключів

**MT8816AE** (Zarlink/Microsemi) — аналоговий комутатор 8×16, 128 точок
перетину, незалежне керування через послідовний інтерфейс, працює з
аналоговими сигналами до ±12В, опір ключа ~100Ω (береться як відомий
параметр компонента, не як проблема для усунення). Куплено — 10 штук.
Живлення: +5В логіка, ±12В аналоговий сигнал (потрібне двополярне
живлення).

Розглядались і відхилені: CD4067, ADG1606 (простіші, доступніші, але
MT8816 обрано свідомо).

## Дорожня карта ядра (не ратифіковано, напрямок)

Після `e`/`π` — інші константи як синхронізовані осцилятори на тому ж
спільному RC:

- **φ** (золотий перетин, через Фібоначчі) — і вже живе всередині блоку
  `π`: `φ = 2·cos(π/5)`
- **√2** (метод Ньютона)
- **γ** (Ейлера-Маскероні) — міст між дискретним і неперервним
- **α** (стала тонкої структури, ≈1/137)
- **G** (гравітаційна стала) — **не фіксована константа, а змінний
  масштаб**, `p/q` через FPGA-матрицю; у планківських одиницях `G=c=ħ=1`.
  Дозволяє експериментально перевіряти теорії змінної гравітації
  (Дірак, Бранс-Дікке) — крутити параметр і спостерігати, а не лише
  теоретизувати.

## Філософія

> "Е-п танець — це не симулятор всесвіту. Це мова, якою можна описати
> всесвіт."

Симуляція починається з малого (планета Земля) і розширюється поступово
— не намагається одразу охопити космічний масштаб. Відмова від
floating-point на всіх рівнях — не інженерна зручність, а вибір: для
довготривалої стабільності орбіт накопичення похибки має бути
принципово іншим.

## Статус

Реальне, куплене залізо: MT8816AE, Mini360 DC-DC, TL074, резистори 1%,
ESP32-S3, ADS1115. FPGA-бік (`zlc_core` — стек-процесор з власним ISA:
PUSH, ADD, DUP, DROP, JMP...) — у розробці, дебажиться на Tang Primer
25K. Архітектура матриці ключів під FPGA-контролем — див.
[`docs/2026-09-30-fpga-controlled-switch-matrix.md`](docs/2026-09-30-fpga-controlled-switch-matrix.md).

Перший практичний крок за оригінальною розмовою — простий RC+TL074
експеримент без матриць, перш ніж збирати повну матрицю ключів.

---

## spanda — E-P Dance (English)

`spanda` (स्पन्द) is the repository name for the project the owner calls
**"Е-П танець"** ("E-P Dance"): a hybrid computer for universe
simulation, conceived and partly built since 2026-06. This is not a new
project — `spanda` is the home for already-existing work, an existing
name, and already-purchased hardware.

Full origin story: [`docs/2026-06-12-e-p-dance-origin.md`](docs/2026-06-12-e-p-dance-origin.md).
This README is the condensed current state.

### The equal triangle

Three vertices, none central — each speaks its own natural language.

**2026-09-30 update:** the original 2026-06 design named the PC vertex
**Clojure** (`clojure.lang.Ratio` for exact fractions, immutable
structures for universe state). Now that the owner's own language
**`sens`** exists, with exact rational arithmetic by construction, the PC
vertex becomes **sens**, not Clojure. This is a deliberate substitution
made today — not something discussed in the original 2026-06
conversation.

- **sens ↔ FPGA:** exact fraction `p/q` (arbitrary width — see
  coordination in `sens#1857`/`cml#387`/`fpga-lisp#38`) → switch-matrix
  configuration bitmask
- **FPGA ↔ analog:** physical switch control (MT8816AE, SPI from Tang
  Primer 25K)
- **analog → sens:** voltage through ADC → nearest-fraction
  approximation (Stern-Brocot / continued fractions) → back into `sens`

### Core: e and π dancing in one circuit

The analog nucleus computes `e` and `π` **simultaneously** through a
shared RC circuit: a feedback integrator yields `e^(t/RC)`; two
cross-coupled integrators (sin/cos oscillator) yield `π` via the moment
sin crosses zero (`t = π·RC`). At that same instant the `e` block
physically shows `e^π` — not computed, but *is* the circuit's physics. A
physical realization of Euler's identity `e^(iπ) + 1 = 0`.

RC is not fixed — it's a number the FPGA sets dynamically through the
switch matrix. One change to `p/q` scales both `e` and `π` at once.

### Self-calibration (three levels)

The reference is the infinitely-precise known value of `e^π`, not an
external physical standard:

1. **Analog** sees voltage deviation from the reference — fast, physical
2. **FPGA** corrects RC via the switch matrix — discrete, exact, as `p/q`
3. **sens** verifies the result with exact rational approximations —
   slow, but absolute

### Switch matrix

**MT8816AE** (Zarlink/Microsemi) — 8×16 analog crosspoint switch, 128
crosspoints, independently controlled via serial interface, handles
analog signals up to ±12V, ~100Ω switch resistance (taken as a known
component parameter, not a problem to eliminate). Purchased — 10 units.
Power: +5V logic, ±12V analog signal (needs a bipolar supply).

Considered and rejected: CD4067, ADG1606 (simpler, more available, but
MT8816 chosen deliberately).

### Core roadmap (not ratified, a direction)

After `e`/`π` — other constants as synchronized oscillators on the same
shared RC:

- **φ** (golden ratio, via Fibonacci) — already lives inside the `π`
  block: `φ = 2·cos(π/5)`
- **√2** (Newton's method)
- **γ** (Euler-Mascheroni) — bridge between discrete and continuous
- **α** (fine-structure constant, ≈1/137)
- **G** (gravitational constant) — **not a fixed constant but a variable
  scale**, `p/q` via the FPGA matrix; in Planck units `G=c=ħ=1`. Allows
  experimentally testing variable-gravity theories (Dirac, Brans-Dicke)
  — turn the parameter and observe, not just theorize.

### Philosophy

> "E-P dance is not a universe simulator. It's a language to describe
> the universe."

The simulation starts small (planet Earth) and expands gradually — not
an attempt at cosmic scope from day one. Rejecting floating-point at
every layer isn't engineering convenience — it's a choice: for
long-term orbital stability, error accumulation must be a fundamentally
different kind.

### Status

Real, purchased hardware: MT8816AE, Mini360 DC-DC, TL074, 1% resistors,
ESP32-S3, ADS1115. FPGA side (`zlc_core` — a stack processor with its own
ISA: PUSH, ADD, DUP, DROP, JMP...) is in development, being debugged on
the Tang Primer 25K. FPGA-controlled switch-matrix architecture — see
[`docs/2026-09-30-fpga-controlled-switch-matrix.md`](docs/2026-09-30-fpga-controlled-switch-matrix.md).

First practical step per the original conversation: a simple RC+TL074
experiment without matrices, before assembling the full switch matrix.
