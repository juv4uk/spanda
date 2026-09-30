# Архітектурний напрямок: FPGA-керована матриця електронних ключів

Дата: 2026-09-30
Статус: `CONFIRMED DIRECTION / COMPONENTS PENDING`

## Рішення

На відміну від [THE ANALOG THING](https://the-analog-thing.org/) (`anabrid`)
— відкритого аналогового комп'ютера, де з'єднання між обчислювальними
елементами (інтегратори, суматори, компаратори, множники) роблять
вручну, фізичними патч-кабелями — `spanda` конфігурує з'єднання
**електронно**: матрицею електронних ключів, керованою FPGA.

Межа лишається чистою: **конфігурація** (яка точка з якою з'єднана) —
цифрова, керована FPGA; **обчислення** (сама напруга, що тече через
з'єднані елементи) — справді аналогове, безперервне. FPGA не бере участі
в обчисленні, лише в маршрутизації.

## Чому це не просто копія THAT

THAT вже має механізм масштабування через фізичне з'єднання кількох
пристроїв (Master/Minion порти), але саму топологію всередині одного
пристрою користувач патчить руками щоразу. Матриця ключів під FPGA
прибирає цей ручний крок — конфігурація стає відтворюваною,
версійованою, програмованою, а не одноразовим фізичним станом кабелів.

## Компоненти

**Підтверджено (2026-09-30, з розмови 2026-06-12 — див.
[`2026-06-12-e-p-dance-origin.md`](2026-06-12-e-p-dance-origin.md)):
MT8816AE** (Zarlink/Microsemi), 10 штук куплено. Аналоговий crosspoint
8×16, 128 точок перетину, послідовне керування, ±12В аналоговий сигнал,
~100Ω опір ключа (відомий параметр моделі, не проблема). Живлення: +5В
логіка + ±12В аналог — потрібне двополярне живлення. Керується через
SPI від Tang Primer 25K.

Розглянуті й відхилені альтернативи: CD4067, ADG1606 (простіші,
доступніші, але MT8816 обрано свідомо). Раніше в цьому документі
згадані AD75019/ADV3200/ADV3201/LMH6583 були лише прикладами категорії
до підтвердження — фактичний вибір інший.

## Що далі

- [x] Уточнити точні куплені мікросхеми — MT8816AE, підтверджено
- [ ] Задокументувати SPI-протокол MT8816AE і як FPGA (Tang Primer 25K /
      `zlc_core`) з ним говоритиме
- [ ] Вирішити двополярне живлення (+5В/±12В) для аналогової частини
- [ ] Визначити розмір матриці (скільки входів/виходів потрібно для
      перших обчислювальних елементів — RC-ядро e/π)

---

## English

Unlike [THE ANALOG THING](https://the-analog-thing.org/) (`anabrid`) —
an open analog computer where connections between computing elements
(integrators, summers, comparators, multipliers) are made manually with
patch cables — `spanda` configures connections **electronically**: a
matrix of electronic switches, controlled by an FPGA.

The boundary stays clean: **configuration** (which point connects to
which) is digital, FPGA-controlled; **computation** (the actual voltage
flowing through connected elements) stays genuinely analog and
continuous. The FPGA never participates in the computation itself, only
in routing.

### Why this isn't just a copy of THAT

THAT already scales by physically chaining multiple devices (Master/
Minion ports), but the topology inside one device is hand-patched every
time. An FPGA-driven switch matrix removes that manual step — the
configuration becomes reproducible, versionable, and programmable rather
than a one-off physical cable state.

### Components

**Confirmed (2026-09-30, from the 2026-06-12 conversation — see
[`2026-06-12-e-p-dance-origin.md`](2026-06-12-e-p-dance-origin.md)):
MT8816AE** (Zarlink/Microsemi), 10 units purchased. 8×16 analog
crosspoint, 128 crosspoints, serial control, ±12V analog signal, ~100Ω
switch resistance (a known model parameter, not a problem). Power: +5V
logic + ±12V analog — needs a bipolar supply. Controlled via SPI from
the Tang Primer 25K.

Considered and rejected alternatives: CD4067, ADG1606 (simpler, more
available, but MT8816 chosen deliberately). AD75019/ADV3200/ADV3201/
LMH6583, mentioned earlier in this document, were only category
examples pending confirmation — the actual choice differs.

### Next

- [x] Confirm the exact purchased ICs — MT8816AE, confirmed
- [ ] Document MT8816AE's SPI protocol and how the FPGA (Tang Primer
      25K / `zlc_core`) will talk to it
- [ ] Resolve the bipolar (+5V/±12V) supply for the analog side
- [ ] Determine matrix size (how many inputs/outputs the first computing
      elements need — the e/π RC core)
