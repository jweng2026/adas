# ADAS 系統架構與生態系選型調查

> 調查日期：2026-11-22。標「未驗證」的項目是這次搜尋沒能查證的內容，實作前要再確認。

## 1. 先看分層：一套 ADAS 由哪些層組成

| 層 | 內容 | 代表選項 |
|---|---|---|
| L1 參考架構／標準 | 軟體分層、介面、通訊規範 | AUTOSAR CP/AP、SOAFEE、Eclipse SDV (S-CORE) |
| L2 OS／中介軟體 | 即時 OS、通訊（DDS、SOME/IP）、排程 | ROS 2、Apollo CyberRT、DriveOS、QNX、Linux |
| L3 功能堆疊 | 感知 → 定位 → 預測 → 規劃 → 控制 | Autoware、Apollo、openpilot |
| L4 運算平台 | SoC 與 BSP | NVIDIA DRIVE、Qualcomm Snapdragon Ride、Mobileye、Jetson（學習用） |
| L5 驗證工具鏈 | 模擬、場景、資料集、CI | CARLA、AWSIM、OpenSCENARIO/esmini、nuScenes、Waymo Open |
| L6 安全／流程標準 | 功能安全、SOTIF、AI 安全、資安、流程 | ISO 26262、ISO 21448、ISO/PAS 8800、ISO/SAE 21434、ASPICE |

## 2. 參考架構與產業標準（量產端使用）

| 選項 | 定位 | 已驗證的證據 | 取得門檻 | 個人專案可行性 |
|---|---|---|---|---|
| AUTOSAR Classic (CP) | MCU 上的 ECU 靜態架構（BSW/RTE/SWC） | 量產業界標準；最新版 R25-11 (2025-11) 已發布 [1] | 規格免費下載；商用 stack（Vector、ETAS 等）要收費 | 中：可用開源 OSEK/學習型 stack 模擬分層，不過拿不到完整工具鏈 |
| AUTOSAR Adaptive (AP) | 高算力 SoC 上的 POSIX、SOA 架構（ara::com、SOME/IP） | R25-11 包含 AP；發表會重點是 CAPI（共通 AP 實作）[1] | 規格免費；商用實作要收費 | 中：可以用開源 SOME/IP（vsomeip）實作同樣概念，**不等於** AUTOSAR 合規 |
| SOAFEE | Arm 主導的雲原生 SDV 參考架構（容器、編排、混合關鍵性） | 已演進到 SOAFEE.next，對齊 Armv9 與 RD-1 AE [2]；Open AD Kit 以它為編排層 [3] | 開放 | 中：有 x86/Arm 機器就可以跑容器化 Open AD Kit |
| Eclipse SDV / S-CORE | 開源 SDV 中介軟體（C++ 與 Rust），以 ISO 26262 為目標 | 0.5 (2025-11)、0.7 (2026-05)、0.9 milestone (2026-09) [4]；1.0 是否已發布：**未驗證** | 開源 | 低～中：還在成熟中，適合關注，不建議當主軸 |

## 3. 開源 ADAS／自駕功能堆疊

| 選項 | 架構特徵 | 成熟度／部署證據 | 授權 | 個人可行性與求職價值 |
|---|---|---|---|---|
| **Autoware**（Core/Universe） | ROS 2 模組化管線：Sensing→Perception→Localization→Planning→Control；Core 穩定、Universe 擴充 [5] | 2026-11-02 發布 Autoware 2.0，新增 AI／E2E 標籤與 AutoE2E 模型 [5]；官方說法是已用於多個園區與公路 robotaxi、接駁車與巴士案例 [6]（具體量產車型：**未驗證**） | Apache-2.0 | **高**：可在 PC 上配合 AWSIM／CARLA 跑完整閉環；模組化架構最貼近 Tier-1 與 L4 公司的職缺描述 |
| **Apollo**（百度） | CyberRT 自研中介軟體 + 模組化管線；10.0 加入 ADFM 大模型 | 10.0 在 2024-12 發布；11.0 在 2026-01-30 發布，主打低速無人車（物流、清掃、園區接駁）[7]；GitHub tag 只到 v9.0.0，要固定 commit（**未驗證**） | Apache-2.0 | 中：體量大、需要 Docker 與 GPU；學 CyberRT 和中國生態有價值，目前重心偏低速場景 |
| **openpilot**（comma.ai） | 端到端駕駛模型 + 精簡 C++/Python 服務（cereal msg）；屬於 L2 ADAS | 累計 1 億英里以上 [8]；支援 325 款以上車型 [9]；最新版 0.11.x (2026) [10] | MIT [10] | **高（L2 主題）**：程式碼精簡易讀，可用公開資料集回放；求職價值偏 E2E／ML，上車要另外買硬體，也要自負法規風險 |

## 4. 中介軟體與運算平台

| 選項 | 定位 | 證據 | 門檻 | 個人可行性 |
|---|---|---|---|---|
| **ROS 2**（DDS） | 研究與原型的主流中介軟體；Autoware 的基礎 | Lyrical Luth (2026-05) 是新 LTS，支援到 2031-05 [11]；Autoware 目前對應哪個 distro：**未驗證**（過去是 Humble） | 免費，Apache-2.0 | 高，必學 |
| NVIDIA DRIVE（AGX Thor、DriveOS 7、Hyperion、Alpamayo） | 量產級 AV 運算平台與軟體堆疊 | Thor DevKit 2025 年出貨 [12]；BYD、Geely、Nissan 等採用 Hyperion [13]；Alpamayo 開放 VLA 模型 [13] | DevKit 價格高，SDK 要申請 | 低（硬體）；Alpamayo 模型、TensorRT 技能可以在一般 GPU 上練 |
| NVIDIA Jetson Orin | 嵌入式 AI 平台（非車規） | 機器人與教學領域普遍使用（**未驗證**） | 硬體數百美元等級；JetPack 免費 | **高**：可以當成「車用 SoC 部署」的替代練習（TensorRT、INT8、延遲量測） |
| Qualcomm Snapdragon Ride、Mobileye EyeQ/SuperVision | 量產 ADAS SoC 與（Mobileye）封閉整套方案 | 量產車大量採用（具體車廠與車型：**未驗證**） | 只對 OEM／Tier-1 開放，屬商業授權 | 很低：只需要了解定位，作為面試知識 |

## 5. 驗證工具鏈（模擬、場景、資料集）

| 選項 | 用途 | 證據 | 授權／門檻 | 個人可行性 |
|---|---|---|---|---|
| **CARLA** | 開源高擬真模擬器；0.10.0 改用 UE5.5，並原生支援 ROS 2 [14] | 學界業界廣泛使用；UE5 版不支援 Ubuntu 20.04 [14] | 程式碼 MIT、素材 CC-BY（**未驗證**）；免費 | 高，但需要中高階 GPU；舊版 0.9.x (UE4) 的需求較低 |
| AWSIM | TIER IV 為 Autoware 做的 Unity 模擬器 | Autoware 官方教學採用（**未驗證**） | 開源（授權：**未驗證**）；免費 | 高（如果選 Autoware 路線） |
| ASAM OpenSCENARIO + esmini | 標準化場景描述；SOTIF 場景測試的業界格式 | ASAM 標準（**未驗證**） | 標準文件可免費取得（**未驗證**）；esmini 開源 MPL-2.0（**未驗證**） | 高：輕量，可以用 CI 跑回歸測試 |
| 資料集：nuScenes、Waymo Open、KITTI、comma2k19 | 感知與預測模型訓練、回放評估 | 學術基準（**未驗證**） | 多數限非商業使用，要註冊同意條款（**未驗證**） | 高：不需要實車就能做感知 |

## 6. 安全與流程標準（履歷關鍵字）

| 標準 | 內容 | 個人專案的落地方式 |
|---|---|---|
| （共通） | 授權／門檻：ISO 標準要付費購買（每份數百瑞士法郎，**未驗證**）；ASPICE 由 VDA QMC 發布 | 可先讀公開摘要與教科書 |
| ISO 26262 | 功能安全（HARA、ASIL、V-model） | 為功能（例如 AEB/LKA）寫 HARA、安全目標、需求追溯表 |
| ISO 21448 (SOTIF) | 預期功能的安全：感知不足、誤用情境 | 用場景庫列出觸發條件並在模擬中驗證 |
| ISO/PAS 8800:2024 | 車用 AI 安全 [15] | 為 ML 模型寫資料需求、性能 KPI 與監控 |
| ISO/SAE 21434、ASPICE | 資安與開發流程 | 簡化的 TARA；Git 流程對應需求、設計、測試的追溯 |

## 7. 推薦路線（個人、求職導向、預算有限）

| 路線 | 技術組合 | 對應職缺 | 優點 | 取捨 |
|---|---|---|---|---|
| **A. 模組化 L2+/L4 堆疊**（首推） | ROS 2 + Autoware + AWSIM/CARLA + OpenSCENARIO CI；自己實作或替換一個模組（例如感知融合或 MPC 控制） | 自駕軟體工程師、Planning/Control、感知整合、Tier-1 | 架構最貼近業界描述；有完整閉環 demo；Apache-2.0 | 學習曲線陡；需要 16GB 以上 RAM 與 GPU |
| **B. 量產 ADAS 功能 + 嵌入式**（偏車廠與 Tier-1 傳統職缺） | 自訂分層架構（模仿 AUTOSAR AP 概念：SOME/IP 用 vsomeip）+ C++17 + 單一功能（ACC/AEB/LKA）+ ISO 26262/SOTIF 文件 + Jetson 部署 | ADAS 功能開發、嵌入式、功能安全、系統工程 | 能展示「需求→架構→實作→驗證→安全分析」完整 V-model，面試說服力強 | 功能範圍小；沒有真正的 AUTOSAR 工具鏈 |
| C. E2E／ML 導向 | openpilot 架構研讀 + nuScenes/comma2k19 訓練 + TensorRT 部署 + CARLA 閉環評估；可延伸到 Alpamayo | ADAS 感知與 E2E 模型、ML 部署工程師 | 符合 2025–2026 業界 E2E／VLA 趨勢 | 需要 GPU 算力；安全論證較難 |

**建議**：主軸選 **A**（可展示性與架構完整度最高），再疊上 **B 的安全文件與單一功能深挖**（例如在 Autoware 上實作 AEB，並附 HARA、SOTIF 場景）。這樣同時覆蓋 L4 軟體和量產 ADAS 兩類職缺。C 留作後續延伸。

接下來要先決定：
1. 主路線（A／B／C）
2. 主要模擬器（CARLA 或 AWSIM）
3. 硬體預算（只用 PC／PC 加 Jetson）

## 來源
1. AUTOSAR R25-11：https://www.autosar.org/news-events/detail/release-r25-11-is-now-available ；https://www.autosar.org/fileadmin/standards/R25-11/CP/AUTOSAR_CP_TR_ReleaseOverview.pdf
2. SOAFEE.next：https://newsroom.arm.com/news/soafee-three-year-milestone
3. Open AD Kit Blueprint：https://embeddedcomputing.com/application/automotive/the-open-ad-kit-blueprint-is-accelerating-sdv-development-across-the-industry
4. Eclipse S-CORE：https://eclipse.dev/score/articles.html ；https://autotechinsight.spglobal.com/news/5282384/eclipse-foundation-announces-05-release-of-s-core-for-software-defined-vehicles
5. Autoware 2.0：https://github.com/autowarefoundation/autoware/releases/tag/2.0.0 ；https://autoware.org/autoware-foundation-2-0-growing-together-toward-scalable-autonomy/
6. Autoware 部署概述：https://autoware.org/autoware-overview/
7. Apollo 10.0／11.0：https://www.marklines.com/en/news/318844 ；https://www.stdaily.com/web/gdxw/2026-01/31/content_468680.html ；https://github.com/ApolloAuto/apollo
8. openpilot 里程：https://comma.ai/openpilot
9. openpilot 車型：https://www.comma.ai/cars
10. openpilot 版本與授權：https://github.com/commaai/openpilot/releases ；https://en.wikipedia.org/wiki/Openpilot
11. ROS 2 Lyrical Luth：https://docs.ros.org/en/lyrical/Releases/Release-Lyrical-Luth.html
12. DRIVE AGX Thor DevKit：https://developer.nvidia.com/blog/accelerate-autonomous-vehicle-development-with-the-nvidia-drive-agx-thor-developer-kit
13. NVIDIA DRIVE／Hyperion／Alpamayo：https://developer.nvidia.com/drive ；https://www.nvidia.com/en-sg/solutions/autonomous-vehicles/in-vehicle-computing/
14. CARLA 0.10.0：https://carla.org/2024/12/19/release-0.10.0
15. ISO/PAS 8800:2024：https://www.iso.org/standard/83303.html
