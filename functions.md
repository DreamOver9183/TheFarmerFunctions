# 🌾 The Farmer Was Replaced 官方精確速查手冊 (Cheat Sheet)

> 💡 本文件已核對遊戲本體繁體中文官方文本（`StreamingAssets/Languages/TW/docs`）與 `__builtins__.py`，所有名稱保證 100% 精準。

---

## 🧭 1. 移動與坐標 (Movement & Position)

| 函數 / 常數 | 說明 | 範例 |
| :--- | :--- | :--- |
| `move(direction)` | 讓無人機往指定方向移動 1 格。地圖邊界具備環形連通 (Wrap around) 特性。 | `move(North)` |
| `can_move(direction)` | 檢查該方向是否能移動，回傳 `True` 或 `False`。 | `if can_move(East): move(East)` |
| `get_pos_x()` | 取得目前無人機的 **X 座標**（最左為 `0`，向東遞增）。 | `if get_pos_x() == 0:` |
| `get_pos_y()` | 取得目前無人機的 **Y 座標**（最底為 `0`，向北遞增）。 | `if get_pos_y() == 0:` |
| `get_world_size()` | 取得農場目前邊長（例如 3×3 時回傳 `3`）。 | `for i in range(get_world_size()):` |

> **方向常數**：`North`（北）、`South`（南）、`East`（東）、`West`（西）

---

## 🚜 2. 農作與翻土 (Crops & Grounds)

| 函數 | 說明 | 注意事項與範例 |
| :--- | :--- | :--- |
| `can_harvest()` | 檢查腳下作物是否**成熟可採收**。回傳 `True` 或 `False`。 | `if can_harvest(): harvest()` |
| `harvest()` | 採收腳下的作物。若作物尚未成熟就採收會將其銷毀。 | `harvest()` |
| `plant(entity)` | 在腳下種植指定的植物。**會自動消耗背包內的成本資源**！ | `plant(Entities.Bush)`<br>`plant(Entities.Carrot)`<br>`plant(Entities.Tree)` |
| `till()` | **翻土開關**：草地變土壤；土壤變回草地。 | ⚠️ 請先檢查地貌，避免重複翻土切回草地！ |
| `clear()` | 清空農場所有物件，將無人機重置回 `(0, 0)`。 | `clear()` |

---

## 💧 3. 澆水系統 (Watering System)

| 函數 | 說明 | 範例 |
| :--- | :--- | :--- |
| `get_water()` | 取得腳下地塊的含水量（範圍 `0.0` ～ `1.0`）。含水量愈高生長愈快（最高 5 倍速）。 | `if get_water() < 0.5:` |
| `use_item(Items.Water)` | 對腳下地塊澆一桶水（增加 `0.25` 含水量）。每 10 秒自動補充一桶水。 | `if get_water() < 0.75: use_item(Items.Water)` |

---

## 👁️ 4. 感知系統 (Senses)

| 函數 | 說明 | 常用比對範例 |
| :--- | :--- | :--- |
| `get_entity_type()` | 取得腳下的作物。若空地則回傳 `None`。 | `if get_entity_type() == None:`<br>`if get_entity_type() == Entities.Bush:` |
| `get_ground_type()` | 取得腳下的地面類型。 | `if get_ground_type() != Grounds.Soil:`<br>&nbsp;&nbsp;&nbsp;&nbsp;`till()` |
| `num_items(item)` | 查詢目前背包中某個物品的**庫存數量**。 | `if num_items(Items.Hay) >= 1000:` |
| `num_unlocked(unlock)` | 查詢某項目是否已解鎖或升級等級。 | `if num_unlocked(Unlocks.Carrots) > 0:` |

---

## 🏷️ 5. 正確的 Enum 常數對照表（嚴格區分大小寫與單複數）

### ⚠️ 重點口訣：
1. **分類名稱一律是「複數」**：`Entities`、`Items`、`Grounds`、`Unlocks`（千萬不要寫成 `Item.` 或 `Entity.`）
2. **植物名稱是「單數」**：`Entities.Carrot`、`Entities.Tree`

| 分類 | 常數項目 | 說明 |
| :--- | :--- | :--- |
| **植物 (Entities)** | `Entities.Grass`<br>`Entities.Bush`<br>`Entities.Carrot`<br>`Entities.Tree` | 草（自動生長）<br>灌木（產木材，消耗木材種植）<br>胡蘿蔔（產胡蘿蔔，消耗木材與乾草）<br>樹木（產 5 木材，**相鄰種植會生長減速**） |
| **物品 (Items)** | `Items.Hay`<br>`Items.Wood`<br>`Items.Carrot`<br>`Items.Water` | 乾草（割草取得）<br>木材（收割灌木/樹木取得）<br>胡蘿蔔（收割胡蘿蔔取得）<br>水（系統每 10 秒自動補給） |
| **地面 (Grounds)** | `Grounds.Grassland`<br>`Grounds.Soil` | 天然草地（會自動長草）<br>耕作土壤（胡蘿蔔唯一能生長的地質） |

---

## 🌲 6. 樹木種植特性與棋盤格規則

> **官方原文節錄**：
> 「樹木比灌木更適合取得木材。每棵樹木會提供 5 個木材。  
> 樹木喜歡保留一些空間，如果將樹木相鄰種植會減慢它們的生長速度。位於其東、南、西或北方相鄰格子的每一棵樹木，生長時間都會加倍。」

👉 **最佳實踐**：利用座標奇偶數相加 `(get_pos_x() + get_pos_y()) % 2 == 0` 種植成**西洋棋盤格（Checkerboard）**，避免任何兩棵樹上下左右相鄰！
