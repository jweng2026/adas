# 選型決策（目標職缺：整車／系統整合、Robotaxi 與自駕新創）

> 依據：docs/01-architecture-ecosystem-survey.md 第 3、5、7、8 節。日期：2026-11-22。

## 1. 目標職缺要看到的能力

| 職缺 | 面試官想看到的 | 專案要產出的證據 |
|---|---|---|
| 整車／系統整合（鴻華、中華、富智捷） | 系統工程能力：需求、介面、整合、模擬驗證、問題追蹤 | ODD 與需求文件、架構圖與介面表（topic/msg、延遲預算）、場景回歸 CI、整合問題紀錄 |
| Robotaxi（鴻海 + NVIDIA 生態） | L4 全堆疊理解、感知與規劃、安全論證、E2E 趨勢 | 在 Autoware 上替換或強化一個模組並量化改善、SOTIF 場景庫、E2E 模型的對照實驗 |
| 自駕新創 | 能獨立打通閉環、工程速度、ISO 26262/SOTIF 意識 | 一鍵啟動的可重現 demo（Docker）、README 與影片、指標儀表板 |

## 2. 選型結論

| 層 | 選擇 | 理由 | 備案／注意 |
|---|---|---|---|
| OS／中介軟體 | Ubuntu 22.04 + ROS 2 Humble | 目前 Autoware、AWSIM、scenario_simulator_v2、CARLA 介面的文件最完整，都以 Humble 為目標 [1][2] | Autoware 正從 Humble 遷移到 Jazzy [3]。Humble 支援到 2027-05（**未驗證**）。開工前先確認 Autoware 2.0 預設的 distro |
| 功能堆疊 | **Autoware（Core + Universe）** | 模組化 L4 架構、Apache-2.0，最貼近 Robotaxi 與系統整合職缺 | 固定使用一個 release tag，不追 main |
| 主模擬器 | **AWSIM**（閉環）+ **scenario_simulator_v2**（OpenSCENARIO 場景回歸） | 官方為 Autoware 設計，不需要額外橋接 [2] | — |
| 次模擬器 | CARLA 0.9.15 + autoware_carla_interface（可選） | 感測器與天候多樣性、ML 資料生成；官方介面只支援 Humble + 0.9.15 [1] | 不用 0.10 (UE5)：硬體需求高，官方介面也不支援 |
| 自有貢獻模組 | 在以下二選一：**(a) 感知**：攝影機與 LiDAR 融合或 BEV 偵測替換；**(b) 規劃與控制**：自製行為規劃或 MPC | (a) 對應 Robotaxi 與 ML；(b) 對應系統整合與 PnC | 依興趣二選一，另一個只需要做到能讀懂 |
| 系統工程層（必做） | ODD、需求追溯、介面表、延遲預算、HARA/SOTIF、場景 CI | 整車與系統整合職缺的核心差異化 | 用 Markdown + GitHub Actions 就能做，不需要商用工具 |
| E2E 延伸（後期） | Autoware AutoE2E 或 NVIDIA Alpamayo 小模型，與模組化管線做 A/B 對照 | 符合 Robotaxi 的 E2E 趨勢 | 模型大小與 VRAM 需求：**未驗證**，到時再評估 |
| 硬體 | 桌機：NVIDIA RTX GPU（建議 12GB VRAM 以上）、32GB 以上 RAM | AWSIM 與感知模型需要 GPU | 建議值，**未驗證**，以 Autoware 官方需求為準。不需要 Jetson |

## 3. 不選的項目

| 項目 | 原因 |
|---|---|
| Apollo | 重心轉向低速場景；在台灣的生態連結較弱 |
| openpilot 主軸 | L2 端到端，與 L4 系統整合職缺的關聯度較低；可作為 E2E 參考閱讀 |
| AUTOSAR 實作 | 目標職缺不以 AUTOSAR 為主；只要在介面文件中說明與 SOME/IP 的對應即可 |
| NVIDIA DRIVE 實機 | 成本高；改以 TensorRT 部署與 Alpamayo 模型展示相同技能 |

## 4. 里程碑（建議）

| 階段 | 內容 | 完成定義 |
|---|---|---|
| M0 | 環境：Docker 化 Autoware + AWSIM，跑通官方 planning simulation | 一個指令就能重現 demo |
| M1 | 系統工程：定義 ODD（例如「園區或市區低速 Robotaxi」）、需求、架構與介面表 | docs/ 下有可追溯的需求與介面文件 |
| M2 | 場景 CI：10 個以上 OpenSCENARIO 場景 + GitHub Actions 回歸測試 | PR 會自動跑場景並產出報告 |
| M3 | 自有模組：替換或強化 (a) 或 (b)，量化指標（例如 mAP、碰撞率、舒適度） | 前後對照表 |
| M4 | 安全與 E2E：HARA/SOTIF 文件；E2E 模型對照實驗 | 安全案例文件 + 對照報告 |

## 待決定
1. 自有模組選 (a) 感知 或 (b) 規劃與控制
2. ODD 情境（建議：低速園區或市區 Robotaxi，與 Robotaxi 生態一致）
3. 目前的硬體規格（GPU、VRAM、RAM）

## 來源
1. autoware_carla_interface：https://autowarefoundation.github.io/autoware_universe/main/simulator/autoware_carla_interface/
2. AWSIM + scenario_simulator_v2：https://tier4.github.io/AWSIM/GettingStarted/Advanced/UsingOpenScenario
3. Autoware 文件（Humble 到 Jazzy 的遷移）：https://docs.autoware.org/main/
