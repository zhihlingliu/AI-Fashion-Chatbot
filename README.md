# 👗 AI 穿搭顧問 (AI Fashion Advisor)

> 透過多輪對話了解你的需求，結合即時天氣資訊，為你生成個人化穿搭建議與穿搭圖像。

![Python](https://img.shields.io/badge/Python-3.8+-blue?logo=python)
![Gradio](https://img.shields.io/badge/Gradio-UI-orange?logo=gradio)
![Stable Diffusion](https://img.shields.io/badge/Stable%20Diffusion-v1.5-purple)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 專案簡介

**AI 穿搭顧問**是一個結合大型語言模型（LLM）與圖像生成（Image Generation）的 AI 應用，讓使用者透過自然對話方式描述穿搭需求，系統會：

1. 自動收集對話資訊（場合、心情、風格、顏色偏好等）
2. 整合即時天氣資訊，給出適合當天氣候的穿搭建議
3. 智慧判斷對話完整度，在時機成熟時自動提出詳細穿搭建議
4. 使用 Stable Diffusion 根據對話內容生成穿搭圖像

---

## 主要功能

| 功能 | 說明 |
|------|------|
| 多輪對話 | 模擬真實造型師的對話流程，自動追蹤對話歷史與資訊完整性 |
| 即時天氣整合 | 透過 `wttr.in` API 取得當地即時天氣，動態調整穿搭建議 |
| 需求分析 | 自動識別場合、心情、風格、顏色偏好、身材考量、預算等維度 |
| 穿搭圖像生成 | 使用 Stable Diffusion v1.5 根據對話內容生成穿搭視覺化圖片 |
| Web 介面 | 基於 Gradio 打造的友善使用者介面，支援聊天與圖片展示 |

---

## 技術架構

```
┌─────────────────────────────────────────────┐
│              Gradio Web Interface           │
├──────────────────┬──────────────────────────┤
│   Chat Module    │   Image Generation Module│
│ ┌──────────────┐ │ ┌───────────────────────┐│
│ │ Groq API     │ │ │ Stable Diffusion v1.5 ││
│ │ (Llama3-70B) │ │ │ (Diffusers)           ││
│ └──────────────┘ │ └───────────────────────┘│
├──────────────────┴──────────────────────────┤
│         Conversation State Manager          │
│         Weather API (wttr.in)               │
└─────────────────────────────────────────────┘
```

### 使用技術

- **語言模型**：[Groq](https://groq.com/) + Llama3-70B（快速推論）
- **圖像生成**：[Stable Diffusion v1.5](https://huggingface.co/runwayml/stable-diffusion-v1-5)（`diffusers` 套件）
- **Web UI**：[Gradio](https://gradio.app/)
- **天氣資料**：[wttr.in](https://wttr.in/) 免費天氣 API
- **執行環境**：Google Colab（支援 GPU 加速）

---

## 快速開始

### 前置需求

- Python 3.8+
- [Groq API Key](https://console.groq.com/)（免費申請）
- GPU 建議（可使用 Google Colab 免費 GPU）

### 安裝套件

```bash
pip install openai gradio requests pillow
pip install diffusers transformers accelerate safetensors huggingface_hub
```

### 設定 API Key

本專案在 Google Colab 環境中透過 `userdata` 管理 API Key：

```python
from google.colab import userdata
api_key = userdata.get('Groq')
```

> 請至 Colab 左側欄 → **Secrets** → 新增 `Groq` 並貼上你的 API Key。

### 執行

直接在 Google Colab 依序執行所有 Cell 即可啟動 Gradio Web App。

---

## 系統配置

主要參數集中在 `CONFIG` 字典，可依需求調整：

```python
CONFIG = {
    'MODEL_NAME': "runwayml/stable-diffusion-v1-5",  # 圖像生成模型
    'DEFAULT_CITY': "Taipei",                         # 預設城市
    'MAX_CHAT_HISTORY': 20,                           # 最大對話記錄
    'IMAGE_GENERATION': {
        'WIDTH': 512,
        'HEIGHT': 768,
        'INFERENCE_STEPS': 35,
        'GUIDANCE_SCALE': 8.5
    },
    'API_SETTINGS': {
        'MODEL': "llama3-70b-8192",                  # LLM 模型
        'BASE_URL': "https://api.groq.com/openai/v1",
        'MAX_TOKENS': 1200,
        'TIMEOUT': 30
    }
}
```

---

## 專案結構

```
GAI_Final_Report.ipynb
├── 1. 安裝必要套件
├── 2. 設定 API 和初始化
├── 3. 載入 Stable Diffusion 模型
├── 4. 獲取天氣資訊函數
├── 5. 對話狀態管理 (ConversationStateManager)
├── 6. 對話分析函數
├── 7. 對話回應生成函數
├── 8. 詳細穿搭建議函數
├── 9. 精確回應生成函數
├── 10. 圖片生成相關函數
├── 11. 主要應用邏輯
└── 12. Gradio Web App
```

---

## 對話流程

```
使用者輸入
    │
    ▼
對話狀態分析 (ConversationStateManager)
    │
    ├── 資訊不足 → 自然追問（場合/心情/風格）
    │
    └── 資訊充足 → 生成詳細穿搭建議
                        │
                        ▼
              使用者點擊「生成穿搭圖」
                        │
                        ▼
              Stable Diffusion 圖像生成
```

### 收集資訊維度

- **場合**：工作、約會、聚會、休閒、運動、正式場合
- **風格偏好**：甜美、帥氣、優雅、休閒、正式
- **顏色偏好**：黑、白、藍、紅、粉、灰、綠、黃等
- **心情**：緊張、興奮、放鬆、自信等
- **身材考量**：想修飾或突出的部位
- **預算考量**

---

## 介面預覽

Gradio Web App 包含：

- **左側**：對話聊天區（支援多輪對話）
- **右側**：穿搭圖像顯示區
- **頂部設定列**：性別選擇、城市輸入
- **快速範例按鈕**：幫助使用者快速開始對話

---

## 注意事項

- Stable Diffusion 首次載入需要較長時間，建議使用 Colab GPU 加速
- 若 GPU 記憶體不足，系統會自動啟用 CPU offload 模式
- 天氣 API 若請求失敗，會自動使用預設天氣（25°C，Partly cloudy）
- 對話需至少 3 輪且收集到場合與風格資訊後，才會觸發詳細穿搭建議

---

## License

This project is licensed under the MIT License.

---

## 致謝

- [Groq](https://groq.com/) — 高速 LLM 推論服務
- [Hugging Face](https://huggingface.co/) — Stable Diffusion 模型
- [Gradio](https://gradio.app/) — Web UI 框架
- [wttr.in](https://wttr.in/) — 免費天氣 API
