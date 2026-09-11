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
| `plant(entity)` | 在腳下種植指定的植物。**會自動消耗背包內的成本資源**！ | `plant(Entities.Bush)`<br>`plant(Entities.Carrot)` |
| `till()` | **翻土開關**：草地變土壤；土壤變回草地。 | ⚠️ 請先檢查地貌，避免重複翻土切回草地！ |
| `clear()` | 清空農場所有物件，將無人機重置回 `(0, 0)`。 | `clear()` |

---

## 👁️ 3. 感知系統 (Senses)

| 函數 | 說明 | 常用比對範例 |
| :--- | :--- | :--- |
| `get_entity_type()` | 取得腳下的作物。若空地則回傳 `None`。 | `if get_entity_type() == None:`<br>`if get_entity_type() == Entities.Bush:` |
| `get_ground_type()` | 取得腳下的地面類型。 | `if get_ground_type() != Grounds.Soil:`<br>&nbsp;&nbsp;&nbsp;&nbsp;`till()` |
| `num_items(item)` | 查詢目前背包中某個物品的**庫存數量**。 | `if num_items(Items.Hay) >= 1000:` |
| `num_unlocked(unlock)` | 查詢某項目是否已解鎖或升級等級。 | `if num_unlocked(Unlocks.Carrots) > 0:` |

---

## 🏷️ 4. 正確的 Enum 常數對照表（嚴格區分大小寫與單複數）

### ⚠️ 重點口訣：
1. **分類名稱一律是「複數」**：`Entities`、`Items`、`Grounds`、`Unlocks`（千萬不要寫成 `Item.` 或 `Entity.`）
2. **植物名稱是「單數」**：`Entities.Carrot`（沒有加 s！）

| 分類 | 常數項目 | 說明 |
| :--- | :--- | :--- |
| **植物 (Entities)** | `Entities.Grass`<br>`Entities.Bush`<br>`Entities.Carrot` | 草（自動生長）<br>灌木（產木材，消耗木材種植）<br>胡蘿蔔（產胡蘿蔔，**種植直接消耗木材與乾草，不需買種子**） |
| **物品 (Items)** | `Items.Hay`<br>`Items.Wood`<br>`Items.Carrot` | 乾草（割草取得）<br>木材（收割灌木取得）<br>胡蘿蔔（收割胡蘿蔔取得） |
| **地面 (Grounds)** | `Grounds.Grassland`<br>`Grounds.Soil` | 天然草地（會自動長草）<br>耕作土壤（胡蘿蔔唯一能生長的地質） |

---

## 🥕 5. 官方官方說明：胡蘿蔔正確種植邏輯

> **官方原文節錄**：
> 「在用 `plant(Entities.Carrot)` 種植胡蘿蔔之前，你需要先耕地。這會將地塊變更為 `Grounds.Soil`，只要呼叫 `till()`。再次呼叫 `till()` 則會將地塊變回 `Grounds.Grassland`。  
> 種植胡蘿蔔需要木材和乾草。呼叫 `plant(Entities.Carrot)` 時會自動移除這些物品。」

### 實戰正確範例：

```python
# 1. 如果可以收割就收割
if can_harvest():
    harvest()

# 2. 如果要種胡蘿蔔，先確保地面是土壤（不是土壤才翻土）
if get_ground_type() != Grounds.Soil:
    till()

# 3. 只要地面是土壤且目前是空地，直接種植（遊戲會自動扣除乾草與木材）
plant(Entities.Carrot)
```
