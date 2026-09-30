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

Власник уже придбав конкретні мікросхеми для матриці ключів — які саме,
ще не зафіксовано тут. Загальна категорія компонентів (для контексту,
не як прийняте рішення): аналогові crosspoint-перемикачі з послідовним
цифровим інтерфейсом конфігурації (наприклад клас пристроїв: AD75019,
ADV3200/ADV3201, LMH6583, MT8816) — усі керуються послідовним
протоколом, що природно лягає на FPGA як контролер. **Це приклади
категорії, не підтверджений вибір** — точні куплені мікросхеми власник
уточнить окремо.

## Що далі

- [ ] Уточнити точні куплені мікросхеми (owner, pending)
- [ ] Задокументувати їхній інтерфейс керування й як FPGA з ним говоритиме
- [ ] Визначити розмір матриці (скільки входів/виходів потрібно для
      перших обчислювальних елементів)

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

The owner has already purchased specific ICs for the switch matrix —
which ones is not yet recorded here. The general component category (for
context, not as a confirmed choice): analog crosspoint switches with a
serial digital configuration interface (example device class: AD75019,
ADV3200/ADV3201, LMH6583, MT8816) — all controlled via a serial protocol
that maps naturally onto an FPGA as controller. **These are category
examples, not a confirmed selection** — the owner will specify the
actual purchased parts separately.

### Next

- [ ] Confirm the exact purchased ICs (owner, pending)
- [ ] Document their control interface and how the FPGA will talk to it
- [ ] Determine matrix size (how many inputs/outputs the first computing
      elements need)
