# 🌾 The Farmer Was Replaced 常用函數與語法速查表 (Cheat Sheet)

這份文件整理了目前已解鎖（與常用）的遊戲內建函數、常數與語法，供你隨時查閱。

---

## 🧭 1. 移動與坐標 (Movement & Position)

| 函數 / 常數 | 說明 | 範例 |
| :--- | :--- | :--- |
| `move(direction)` | 讓無人機往指定方向移動 1 格。地圖邊界具備環形連通 (Wrap around) 特性。 | `move(North)` |
| `can_move(direction)` | 檢查該方向是否能移動，回傳 `True` 或 `False`。 | `if can_move(East): move(East)` |
| `get_pos_x()` | 取得目前無人機的 **X 座標**（水平橫向，最左為 `0`，向東遞增）。 | `x = get_pos_x()` |
| `get_pos_y()` | 取得目前無人機的 **Y 座標**（垂直縱向，最底為 `0`，向北遞增）。 | `y = get_pos_y()` |
| `get_world_size()` | 取得農場目前邊長（例如 3×3 時回傳 `3`）。 | `for i in range(get_world_size()):` |

> **方向常數**：`North`（北/上）、`South`（南/下）、`East`（東/右）、`West`（西/左）

---

## 🚜 2. 農作與地形 (Crops & Grounds)

| 函數 | 說明 | 注意事項與範例 |
| :--- | :--- | :--- |
| `can_harvest()` | 檢查腳下作物是否**成熟可採收**。回傳 `True` 或 `False`。 | `if can_harvest(): harvest()` |
| `harvest()` | 採收腳下的作物。若未成熟採收會將其清除。 | `harvest()` |
| `plant(entity)` | 在腳下種植指定的植物。 | `plant(Entities.Bush)`<br>`plant(Entities.Carrots)` |
| `till()` | **翻土切換**：草地變成土壤；土壤變回草地。 | ⚠️ **具開關特性**，若非必要勿重複執行！ |
| `clear()` | 清空農場所有物件，將無人機重置回 `(0, 0)`。 | `clear()` |

---

## 👁️ 3. 感知系統 (Senses)

| 函數 | 說明 | 常用比對範例 |
| :--- | :--- | :--- |
| `get_entity_type()` | 取得腳下的作物或物件類型。若沒有東西回傳 `None`。 | `if get_entity_type() == Entities.Bush:`<br>`if get_entity_type() == None:` |
| `get_ground_type()` | 取得腳下的地面類型。 | `if get_ground_type() != Grounds.Soil:`<br>&nbsp;&nbsp;&nbsp;&nbsp;`till()`（確保翻成土壤） |
| `num_items(item)` | 查詢目前背包中某個物品的**庫存數量**。 | `if num_items(Items.Hay) >= 1000:` |

---

## 💰 4. 交易系統 (Trade & Store)

| 函數 | 說明 | 範例 |
| :--- | :--- | :--- |
| `trade(item)` | 購買指定物品（例如種子）。成功回傳 `True`。 | `trade(Items.Carrot_Seed)` |
| `can_trade(item)` | 檢查目前的資源是否足夠購買指定物品。 | `if can_trade(Items.Carrot_Seed):` |

---

## 🏷️ 5. 常用枚舉常數清單 (Enums)

### 🌱 植物 (Entities)
- `Entities.Grass`（草）
- `Entities.Bush`（灌木，產木頭）
- `Entities.Carrots`（胡蘿蔔）

### 🪵 物品與種子 (Items)
- `Items.Hay`（乾草）
- `Items.Wood`（木頭）
- `Items.Carrot`（胡蘿蔔）
- `Items.Carrot_Seed`（胡蘿蔔種子）

### 🏞️ 地面類型 (Grounds)
- `Grounds.Grassland`（天然草地）
- `Grounds.Soil`（耕作土壤）

---

## 🛠️ 6. 偵錯與日常 (Debug & Utilities)

| 函數 | 說明 | 範例 |
| :--- | :--- | :--- |
| `print(...)` | 在遊戲左下角除錯視窗印出訊息或數值。 | `print("X坐標:", get_pos_x())` |
| `do_a_flip()` | 讓無人機轉一圈空翻。 | `do_a_flip()` |
| `pet_the_piggy()` | 摸摸農場小豬。 | `pet_the_piggy()` |

---

## 💡 常見邏輯組合小技巧

### 1. 安全翻土（只在不是土壤時才翻土）
```python
if get_ground_type() != Grounds.Soil:
    till()
```

### 2. 左側整列判斷（3×3 農田的最左欄）
```python
if get_pos_x() == 0:
    # 這裡是左側那一列
    pass
else:
    # 這裡是右側兩列
    pass
```

### 3. 種胡蘿蔔前自動補種子
```python
if num_items(Items.Carrot_Seed) == 0:
    trade(Items.Carrot_Seed)
plant(Entities.Carrots)
```
