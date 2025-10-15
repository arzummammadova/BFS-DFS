
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

İstəsən sənin üçün bu kodu **addım-addım animasiyalı izahla** və ya **sadələşdirilmiş versiya ilə** də yaza bilərəm (məsələn, yalnız BFS ilə).
İstəyirsən elə birini tərtib edim?
