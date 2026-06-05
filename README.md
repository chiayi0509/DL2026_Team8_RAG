# DL2026_Team8_RAG
# 專案名稱：Retrieval-Augmented Generation (RAG)
## 1. 創作目標
在當前各個尖端科學與工程研究領域中，最頂尖的核心技術論文、協定白皮書與研究報告，絕大多數皆是以英文撰寫。這使得中文研究人員、研究生或現場工程師在即時查閱、快速理解文獻中的關鍵技術與高度專業數據時，往往面臨跨語言的語意與專業術語障礙。
傳統的大語言模型（LLM）直接回答特定領域的專業問題時，極易產生**「幻覺（Hallucination）」**或給出過於籠統、不具參考價值的回答；且普通的跨語言檢索也容易因為翻譯落差，導致系統無法在長文本中精準對齊、定位到文獻內的核心實驗數據（例如特定的演算法參數、精準度或關鍵效能指標）。
為了克服上述痛點，本專案的創作目標為：

1.打造即插即用知識庫： 建置一套無須重新訓練或微調基底大模型，即可動態動態匯入任意特定領域英文文獻的檢索增強生成（RAG）系統。

2.實現跨語言精準對答： 使用者可直接使用繁體中文提問，系統會自動在後台進行英語翻譯、雲端向量資料庫語意檢索，並嚴格根據文獻內容，回傳流暢且附帶專業術語對照的繁體中文解答。

3.實作多策略對比實驗： 本專案分別實作了RAG_Version_B與RAG_Version_A兩套系統。
## 2. 檔案說明
系統架構與演算法對比:

RAG_Version_A:改用本機端 all-distilroberta-v1 模型（維度 768），並透過 MultipleNegativesRankingLoss 對文獻文本進行 3 個 Epochs 的領域適應強化訓練。同時加入了「父子區塊邏輯 」、「大幅度重疊切片 」與「Metadata 實驗結果標籤化」，解決了傳統 RAG 容易在語意檢索中漏掉特定圖表與硬數據的缺陷。

RAG_Version_B:採用全新的 google-genai 官方 SDK，搭配萬用端點 models/gemini-embedding-2（輸出維度 3072）。核心特色在於實作了強健的異常捕獲機制，能自動攔截免費版 API 的 429 配額超限錯誤並自動掛起倒數，確保系統在現場 Demo 流程中具備極高的不中斷防禦力。
## 3.使用方法
模式一：運行 RAG_Version_A.ipynb

1.環境準備： 在 Colab 儲存格中安裝正確命名的新版套件與相容的 Torch

!pip install -qU pinecone sentence-transformers pypdf google-generativeai torch==2.11.0

2.開啟 程式碼/RAG_Version_A.ipynb.

3.前置檔案準備： 請先手動將欲測試的專業文獻 PDF 檔上傳到 Colab 的根目錄中，並確認程式碼內的 pdf_path 變數名稱與該檔名一致。

4.點選「全部執行」，程式將會全自動執行以下進階優化流程

5.文本切片與微調：以高重疊率切片後，使用本機 RoBERTa 模型進行對比學習領域強化訓練。

6.Metadata 標籤化與父子上下文：系統自動掃描包含關鍵字的區塊，打上 Experiment_Results 標籤，並自動綁定前後文作為父文本。

7.高召回檢索 Demo： 於最下方的提問對話框中輸入中文問題。

8.系統會自動擴大檢索範圍至 Top-K=20，優先過濾實驗標籤段落，由 LLM 產出高精確、不產生幻覺的學術級中文解答。


模式二:運行 RAG_Version_B.ipynb

1.環境準備： 在 Colab 儲存格中安裝正確命名的新版套件

!pip install -qU pypdf pinecone google-genai google-generativeai

2.開啟 程式碼/RAG_Version_B.ipynb。

3.點選「全部執行」

4.互動式上傳： 程式執行到上傳區塊時會彈出選取視窗，請從 範例文獻/ 中選擇並上傳你想提問的英文論文 PDF。

5.自動化處理： 系統會自動提取文本、切分區塊、轉換為向量，並在清理舊向量快取後完整灌入 Pinecone 資料庫。

6.對話 Demo： 在下方彈出的輸入框中直接輸入繁體中文問題，系統即會跨語言檢索並輸出流暢解答。輸入 exit 即可安全關閉問答迴圈。
##資料集提供範例連結

https://ieeexplore.ieee.org/document/11385136

https://ieeexplore.ieee.org/document/10942952

