
Bu kod **BFS (Breadth-First Search)** və **DFS (Depth-First Search)** alqoritmlərinin **vizualizasiyasını (görsəl nümayişini)** göstərir.
Gəlin addım-addım, sadə və aydın şəkildə izah edək 👇

---

## 🧩 Ümumi Məlumat

Bu HTML sənədi bir **10x10-luq labirint (maze)** yaradır və iki axtarış alqoritmini göstərir:

* **BFS (Genişinə axtarış)** → ən qısa yolu tapır
* **DFS (Dərinliyinə axtarış)** → daha sürətli ola bilər, amma ən qısa yolu tapmaya bilər

İstifadəçi ekrandakı düymələrlə bu alqoritmləri işə sala bilir və xanaların necə ziyarət olunduğunu vizual olaraq görür.

---

## 🧱 1. HTML Quruluşu

Əsas hissələr:

```html
<div class="controls">
    <button class="btn-bfs" onclick="startBFS()">▶ BFS Başlat</button>
    <button class="btn-dfs" onclick="startDFS()">▶ DFS Başlat</button>
</div>
```

➡️ Bu hissədə 2 düymə var — biri **BFS**, biri **DFS** alqoritmini işə salır.
`onclick="startBFS()"` və `onclick="startDFS()"` — bu düyməyə klik ediləndə həmin JS funksiyalar çağırılır.

---

```html
<div class="grid" id="grid"></div>
```

➡️ Burada 10x10-luq **xəritə (labirint)** yaradılacaq. Hər bir xananın (`cell`) divar, başlanğıc (S), son (H) və ya ziyarət edilmiş vəziyyəti olacaq.

---

```html
<div class="info">
  <h3>📊 Statistika</h3>
  ...
</div>
```

➡️ Burada alqoritm haqqında canlı məlumat göstərilir:

* hansı alqoritm işləyir;
* neçə xana ziyarət olunub;
* tapılmış yolun uzunluğu;
* status (tapıldı / tapılmadı).

---

```html
<div class="explanation">
  <h3>💡 Alqoritmlər haqqında</h3>
  <ul>
    <li>BFS...</li>
    <li>DFS...</li>
  </ul>
</div>
```

➡️ Bu hissə tədris məqsədi ilə nəzərdə tutulub – istifadəçiyə alqoritmlərin fərqini izah edir.

---

## 🎨 2. CSS Dizaynı

CSS hissəsi səhifənin görünüşünü formalaşdırır:

* Arxa fon gradient effekti ilə mavi-bənövşəyi tonlardadır (`background: linear-gradient(...)`).
* Xanalar (`.cell`) 50x50 piksellik kvadratlardır.
* Rənglər:

  * 🟩 yaşıl — başlanğıc (S)
  * 🟥 qırmızı — hədəf (H)
  * ⬛ qara — divar
  * 🟨 sarı — ziyarət olunub
  * 🟦 mavi — tapılmış yol
  * 🟧 narıncı — cari yoxlanılan xana

Həmçinin `@keyframes` animasiyaları ilə xanaların rəngi dəyişəndə yumşaq keçid effekti olur.

---

## ⚙️ 3. JavaScript Məntiqi

İndi keçək əsas hissəyə — **alqoritmlərin işləmə prinsipi**.

---

### 🧱 Labirintin yaradılması

```js
const ROWS = 10;
const COLS = 10;
const maze = [ ... ];
```

➡️ `maze` 10x10 ölçüdə 2D massivdir.
`1` — divar
`0` — keçilə bilən yol

---

### 🔹 `initGrid()` funksiyası

Bu funksiya səhifə açıldıqda çağırılır və xəritəni yaradır:

* hər xanaya `div` yaradır;
* əgər `maze[i][j] === 1` → divar rəngi verilir;
* başlanğıc `(0,0)` → yaşıl, Hədəf `(9,9)` → qırmızı xanadır.

---

### 🔹 `getNeighbors(row, col)`

Bu funksiya verilmiş xananın **dörd qonşusunu** (sağ, sol, yuxarı, aşağı) qaytarır — amma yalnız o halda ki:

* xəritə daxilində olsun,
* divar olmasın (`maze[newRow][newCol] === 0`),
* və hələ ziyarət edilməmiş olsun.

---

## 🔹 BFS (Genişinə Axtarış)

```js
async function startBFS() {
    const queue = [{row: start.row, col: start.col}]; // növbə (queue)
    grid[start.row][start.col].visited = true;

    while (queue.length > 0) {
        const current = queue.shift(); // əvvəldən götür
        ...
        if (current.row === end.row && current.col === end.col) {
            await drawPath(current);
            return;
        }

        const neighbors = getNeighbors(current.row, current.col);
        for (const neighbor of neighbors) {
            grid[neighbor.row][neighbor.col].visited = true;
            grid[neighbor.row][neighbor.col].parent = current;
            queue.push(neighbor);
        }
    }
}
```

📘 **İzah:**

* BFS növbə (`queue`) istifadə edir.
* İlk öncə başlanğıc nöqtəni növbəyə əlavə edir.
* Hər dəfə növbənin əvvəlindən bir xana götürüb, onun qonşularını növbəyə əlavə edir.
* Hədəf (end) tapıldıqda `drawPath()` ilə tapılmış yolu rəngləyir.
* BFS **ən qısa yolu** tapır.

---

## 🔹 DFS (Dərinliyinə Axtarış)

```js
async function startDFS() {
    const stack = [{row: start.row, col: start.col}]; // stack (yığın)
    grid[start.row][start.col].visited = true;

    while (stack.length > 0) {
        const current = stack.pop(); // sondan götür
        ...
        if (current.row === end.row && current.col === end.col) {
            await drawPath(current);
            return;
        }

        const neighbors = getNeighbors(current.row, current.col);
        for (const neighbor of neighbors) {
            grid[neighbor.row][neighbor.col].visited = true;
            grid[neighbor.row][neighbor.col].parent = current;
            stack.push(neighbor);
        }
    }
}
```

📙 **İzah:**

* DFS **stack (yığın)** istifadə edir.
* Yəni ən son daxil edilən xananı birinci yoxlayır.
* Daha “dərin” istiqamətdə irəliləyir, sonra geri dönür.
* Hər zaman ən qısa yolu tapmaya bilər.

---

## 🔹 `drawPath()` funksiyası

```js
async function drawPath(endNode) {
    const path = [];
    let current = endNode;

    while (current !== null) {
        path.unshift(current);
        current = grid[current.row][current.col].parent;
    }

    for (let i = 1; i < path.length - 1; i++) {
        const cell = grid[path[i].row][path[i].col];
        cell.element.classList.add('path');
        await sleep(50);
    }

    document.getElementById('pathLength').textContent = path.length;
}
```

➡️ Tapılmış yolun hüceyrələrini ardıcıl mavi rənglə göstərir.

---

## 🕐 `sleep(ms)` funksiyası

```js
function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}
```

➡️ Bu animasiya üçün kiçik gecikmə yaradır (məsələn, 100 ms).

---

## 💡 Qısa Nəticə

| Hissə    | Funksiya                                             |
| -------- | ---------------------------------------------------- |
| **HTML** | Strukturu (düymələr, grid, məlumatlar) yaradır       |
| **CSS**  | Vizual görünüş və animasiyalar                       |
| **JS**   | Alqoritmlərin işləmə prinsipi və vizualizasiyası     |
| **BFS**  | Növbə ilə səviyyə-səviyyə axtarır, ən qısa yol tapır |
| **DFS**  | Stack ilə dərinliyinə axtarır, amma yol uzana bilər  |

---

JS_derinden izah

Əla 👍
Gəlin indi **yalnız JavaScript hissəsini** addım-addım, sadə və başa düşülən şəkildə izah edək.
Bu JS kodu 3 əsas hissədən ibarətdir:

1. 🧱 **Grid (labirint) yaradılması**
2. 🔁 **BFS və DFS alqoritmlərinin işləməsi**
3. 🎨 **Yolun (path) göstərilməsi və animasiya**

---

## 🧱 1️⃣ Əsas dəyişənlər və gridin hazırlanması

```js
const ROWS = 10;
const COLS = 10;
let grid = [];
let isRunning = false;
```

* **ROWS**, **COLS** → grid 10x10 ölçüsündə olacaq.
* **grid** → hər xananın məlumatını (div elementi, ziyarət olub-olmaması, valideynini) saxlayır.
* **isRunning** → birdən çox alqoritm eyni anda işləməsin deyə qoruma üçün istifadə olunur.

---

### 🧩 Labirintin xəritəsi

```js
const maze = [
  [0,0,0,1,0,0,0,0,0,0],
  [0,1,0,1,0,1,1,1,1,0],
  ...
];
```

* Bu 2D massivdir.

  * `0` → keçilə bilən yol
  * `1` → divar
* Yəni, alqoritm yalnız `0` olan xanalarla hərəkət edə bilər.

---

### 🚩 Başlanğıc və son nöqtə

```js
const start = {row: 0, col: 0};
const end = {row: 9, col: 9};
```

* `start` → yuxarı sol künc (S)
* `end` → aşağı sağ künc (H)

---

### 🧠 `initGrid()` funksiyası

```js
function initGrid() {
  const gridEl = document.getElementById('grid');
  gridEl.innerHTML = '';
  grid = [];

  for (let i = 0; i < ROWS; i++) {
    grid[i] = [];
    for (let j = 0; j < COLS; j++) {
      const cell = document.createElement('div');
      cell.className = 'cell';
      cell.id = `cell-${i}-${j}`;

      if (maze[i][j] === 1) {
        cell.classList.add('wall');
      } else if (i === start.row && j === start.col) {
        cell.classList.add('start');
        cell.textContent = 'S';
      } else if (i === end.row && j === end.col) {
        cell.classList.add('end');
        cell.textContent = 'H';
      }

      gridEl.appendChild(cell);
      grid[i][j] = {
        element: cell,
        visited: false,
        parent: null
      };
    }
  }
}
```

🟢 **Nə edir:**

1. Əvvəlcə HTML-də grid-i (boş konteyneri) təmizləyir.
2. Sonra hər bir xana üçün `div` elementi yaradır.
3. Əgər `maze[i][j]` → `1` → divar olur.
4. Əgər başlanğıcdır → yaşıl (S).
5. Əgər sondur → qırmızı (H).
6. Hər bir xananın məlumatı `grid` massivinə saxlanılır:

   * `element`: DOM elementi
   * `visited`: ziyarət olunub ya yox
   * `parent`: hansı xananın içindən gəlib (yolu tapmaq üçün)

---

## 🔁 2️⃣ Qonşu xanaları tapmaq

```js
function getNeighbors(row, col) {
  const neighbors = [];
  const directions = [[0,1], [1,0], [0,-1], [-1,0]]; // sağ, aşağı, sol, yuxarı

  for (const [dr, dc] of directions) {
    const newRow = row + dr;
    const newCol = col + dc;

    if (
      newRow >= 0 && newRow < ROWS &&
      newCol >= 0 && newCol < COLS &&
      maze[newRow][newCol] === 0 &&
      !grid[newRow][newCol].visited
    ) {
      neighbors.push({row: newRow, col: newCol});
    }
  }

  return neighbors;
}
```

🟢 **İzah:**

* Verilmiş `row, col` üçün 4 istiqamət yoxlanılır (sağ, aşağı, sol, yuxarı).
* Hər biri:

  * grid-in içində olmalıdır,
  * divar (`1`) olmamalıdır,
  * ziyarət olunmamış olmalıdır.
* Uyğun xanalar **neighbors** (qonşular) kimi qaytarılır.

---

## 🔄 3️⃣ BFS (Genişinə Axtarış)

```js
async function startBFS() {
  if (isRunning) return;
  isRunning = true;

  document.getElementById('algorithm').textContent = 'BFS';
  document.getElementById('status').textContent = 'İşləyir...';

  const queue = [{row: start.row, col: start.col}];
  grid[start.row][start.col].visited = true;
  let visitedCount = 1;

  while (queue.length > 0) {
    const current = queue.shift(); // Queue - əvvəldən götür
    const cell = grid[current.row][current.col];

    if (!(current.row === start.row && current.col === start.col)) {
      cell.element.classList.add('current');
      await sleep(100);
      cell.element.classList.remove('current');
      if (!(current.row === end.row && current.col === end.col)) {
        cell.element.classList.add('visited');
      }
    }

    if (current.row === end.row && current.col === end.col) {
      await drawPath(current);
      document.getElementById('status').textContent = '✓ Tapıldı!';
      isRunning = false;
      return;
    }

    const neighbors = getNeighbors(current.row, current.col);
    for (const neighbor of neighbors) {
      grid[neighbor.row][neighbor.col].visited = true;
      grid[neighbor.row][neighbor.col].parent = current;
      queue.push(neighbor);
      visitedCount++;
      document.getElementById('visited').textContent = visitedCount;
    }
  }

  document.getElementById('status').textContent = '✗ Yol tapılmadı';
  isRunning = false;
}
```

🟢 **İzah:**

1. Əgər artıq alqoritm işləyirsə (`isRunning`), təkrar başlamasın.
2. Başlanğıc xananı növbəyə əlavə edir (`queue`).
3. `while` dövrü: növbədə element qaldıqca işləyir.
4. `queue.shift()` → növbənin **əvvəlindən** götürür (FIFO).
5. Cari xananı göstərmək üçün animasiya olunur (`await sleep(100)`).
6. Əgər hədəf xanadırsa → `drawPath()` çağırılır və alqoritm bitir.
7. Əks halda qonşular tapılır → hər bir qonşu:

   * **ziyaret** = true
   * **parent** = cari xana
   * növbəyə əlavə olunur (`queue.push`).

BFS **səviyyə-səviyyə** hərəkət etdiyi üçün ən qısa yolu tapır.

---

## 🌀 4️⃣ DFS (Dərinliyinə Axtarış)

```js
async function startDFS() {
  if (isRunning) return;
  isRunning = true;

  document.getElementById('algorithm').textContent = 'DFS';
  document.getElementById('status').textContent = 'İşləyir...';

  const stack = [{row: start.row, col: start.col}];
  grid[start.row][start.col].visited = true;
  let visitedCount = 1;

  while (stack.length > 0) {
    const current = stack.pop(); // Stack - sondan götür
    const cell = grid[current.row][current.col];

    if (!(current.row === start.row && current.col === start.col)) {
      cell.element.classList.add('current');
      await sleep(100);
      cell.element.classList.remove('current');
      if (!(current.row === end.row && current.col === end.col)) {
        cell.element.classList.add('visited');
      }
    }

    if (current.row === end.row && current.col === end.col) {
      await drawPath(current);
      document.getElementById('status').textContent = '✓ Tapıldı!';
      isRunning = false;
      return;
    }

    const neighbors = getNeighbors(current.row, current.col);
    for (const neighbor of neighbors) {
      grid[neighbor.row][neighbor.col].visited = true;
      grid[neighbor.row][neighbor.col].parent = current;
      stack.push(neighbor);
      visitedCount++;
      document.getElementById('visited').textContent = visitedCount;
    }
  }

  document.getElementById('status').textContent = '✗ Yol tapılmadı';
  isRunning = false;
}
```

🟢 **İzah:**

* BFS-ə çox bənzəyir, amma fərq budur:

  * **queue.shift()** əvəzinə **stack.pop()** istifadə olunur.
  * Yəni, **ən son daxil edilən xana** ilk çıxarılır → bu DFS-in “dərin” axtarış davranışını yaradır.
* DFS bəzən daha tez olur, amma qısa yolu tapmaya bilər.

---

## 🎨 5️⃣ Yolun göstərilməsi

```js
async function drawPath(endNode) {
  const path = [];
  let current = endNode;

  while (current !== null) {
    path.unshift(current);
    current = grid[current.row][current.col].parent;
  }

  for (let i = 1; i < path.length - 1; i++) {
    const cell = grid[path[i].row][path[i].col];
    cell.element.classList.remove('visited');
    cell.element.classList.add('path');
    await sleep(50);
  }

  document.getElementById('pathLength').textContent = path.length;
}
```

🟢 **İzah:**

* `endNode`-dan başlayır və **parent** vasitəsilə geriyə (başlanğıca) qədər gedir.
* Hər xananı `path` massivinə əlavə edir.
* Sonra ardıcıl şəkildə hər xananı mavi rənglə işarələyir (tapılmış yol kimi).

---

## 🕐 6️⃣ Gecikmə funksiyası

```js
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}
```

➡️ Bu sadəcə animasiya üçün `await sleep(100)` şəklində istifadə olunur, yəni 0.1 saniyəlik fasilə verir.

---

## 🧩 7️⃣ Grid-in başlanğıcda yaradılması

```js
initGrid();
```

Səhifə açılan kimi grid-in ilkin vəziyyəti (S, H və divarlar) göstərilir.

---

## 🔚 Nəticə

| Funksiya         | Nə edir                                                |
| ---------------- | ------------------------------------------------------ |
| `initGrid()`     | Grid yaradır, divarları və start/end təyin edir        |
| `getNeighbors()` | Qonşu xanaları tapır                                   |
| `startBFS()`     | BFS alqoritmini işlədir (növbə ilə, ən qısa yol tapır) |
| `startDFS()`     | DFS alqoritmini işlədir (stack ilə, dərin axtarır)     |
| `drawPath()`     | Tapılmış yolu rənglə göstərir                          |
| `sleep()`        | Animasiya üçün gecikmə yaradır                         |

---

İstəsən bu kodun içində **console.log** əlavə edərək sətir-sətir necə işlədiyini real vaxtda göstərən bir versiyasını da yaza bilərəm — yəni ekranda hansı xanaya keçdiyini izah edən.
İstəyirsən elə bir izahlı (addım-addım console log ilə) versiya hazırlayım?

