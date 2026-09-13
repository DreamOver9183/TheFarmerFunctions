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
| `plant(entity)` | 在腳下種植指定的植物。**會自動消耗背包內的成本資源**！ | `plant(Entities.Bush)`<br>`plant(Entities.Carrot)`<br>`plant(Entities.Pumpkin)` |
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
| `get_entity_type()` | 取得腳下的作物。若空地則回傳 `None`。 | `if get_entity_type() == None:`<br>`if get_entity_type() == Entities.Dead_Pumpkin:` |
| `get_ground_type()` | 取得腳下的地面類型。 | `if get_ground_type() != Grounds.Soil:`<br>&nbsp;&nbsp;&nbsp;&nbsp;`till()` |
| `num_items(item)` | 查詢目前背包中某個物品的**庫存數量**。 | `if num_items(Items.Hay) >= 1000:` |
| `num_unlocked(unlock)` | 查詢某項目是否已解鎖或升級等級。 | `if num_unlocked(Unlocks.Carrots) > 0:` |

---

## 🏷️ 5. 正確的 Enum 常數對照表（嚴格區分大小寫與單複數）

### ⚠️ 重點口訣：
1. **分類名稱一律是「複數」**：`Entities`、`Items`、`Grounds`、`Unlocks`（千萬不要寫成 `Item.` 或 `Entity.`）
2. **植物名稱是「單數」**：`Entities.Carrot`、`Entities.Pumpkin`

| 分類 | 常數項目 | 說明 |
| :--- | :--- | :--- |
| **植物 (Entities)** | `Entities.Grass`<br>`Entities.Bush`<br>`Entities.Carrot`<br>`Entities.Tree`<br>`Entities.Pumpkin`<br>`Entities.Dead_Pumpkin` | 草（自動生長）<br>灌木（產木材，消耗木材種植）<br>胡蘿蔔（產胡蘿蔔，消耗木材與乾草）<br>樹木（產 5 木材，**相鄰種植會生長減速**）<br>南瓜（**消耗胡蘿蔔種植**，需在土壤上）<br>枯萎南瓜（死亡的南瓜，需重新補種） |
| **物品 (Items)** | `Items.Hay`<br>`Items.Wood`<br>`Items.Carrot`<br>`Items.Pumpkin`<br>`Items.Water` | 乾草、木材、胡蘿蔔、南瓜、水 |
| **地面 (Grounds)** | `Grounds.Grassland`<br>`Grounds.Soil` | 天然草地<br>耕作土壤（胡蘿蔔與南瓜必須種在土壤上） |

---

## 🎃 6. 南瓜種植特性與規則

> **官方原文節錄**：
> 「南瓜在耕作的土壤上像胡蘿蔔一樣生長。**種植它們需要消耗胡蘿蔔**。  
> 當一個方形區域內的所有南瓜都完全成熟時，它們會合併為一個巨型南瓜。不幸的是，南瓜在完全成熟後有 **20% 的機率會死亡**。  
> 南瓜死亡時會留下枯萎南瓜 (`Entities.Dead_Pumpkin`)。在其位置種植新植物會自動移除枯死的南瓜，因此不需要特別採收。`can_harvest()` 在枯萎南瓜上永遠回傳 `False`。」

👉 **先求有的極簡策略**：
1. 和胡蘿蔔一樣，只能種在土壤上（`Grounds.Soil`）。
2. 種植呼叫 `plant(Entities.Pumpkin)`，會自動扣除胡蘿蔔。
3. 如果看到腳下是枯萎南瓜（`get_entity_type() == Entities.Dead_Pumpkin`），直接對它呼叫 `plant(Entities.Pumpkin)` 就能原地補種！
