# Raco 跟單包

這個資料夾裝了 Trader Skill 的十一個 skill（在 `.claude/skills/`），設定在 `trader/config.json`（從範本 `trader/config.example.json` 複製來的），每個 skill 一句話的說明在 `中文說明.md`。

## 回答方式

- 一律用繁體中文回答，數字整理成表格
- 用到 13F 或國會申報，都寫出資料日期：13F 寫持股截止日和申報日，國會申報寫交易日和申報日

## 什麼事用哪個 skill

- 國會申報用 trader-pelosi，金額照申報上的區間寫，附上交易日和申報日
- 跟大戶用 trader-copycat，從最新一季的 13F 算持股比例
- 分配金額用 trader-budget，照設定的預算、備用現金和單檔上限
- 持股新聞用 trader-news
- 財報用 trader-analyst、選股用 trader-screener、價格提醒用 trader-alerts、比例跑掉用 trader-rebalance、損益和稅用 trader-tax
- 下單一律先過 trader-risk，再用 trader-broker 一張一張給我看，我打 yes 才送出，預設用模擬帳戶

## 設定

- `trader/config.json` 是我自己的設定，只存在這台電腦，不要推上 GitHub（跟單包的 `.gitignore` 已經排除 `trader/`；這個資料夾如果是我自己的 git 倉庫，確認它的 `.gitignore` 也有一行 `trader/`）。這個檔案不存在的話，先把 `trader/config.example.json` 複製成 `trader/config.json` 再繼續，不要用 skill 資料夾裡的範本
- 範本已經填好：預算 5000 美元、留一成現金、單一檔最多 25%、單筆最多 2000 美元、一天最多 10 筆、模擬交易（`"paper": true`），沒有接任何券商
- `sec_user_agent` 要填我的英文名字和 Email，格式 `"英文名字 Email"`，例如 `"Alex Chen alex.chen@example.com"`。美國證交會（SEC）規定查資料要附上，這份只存在本機的 config，只在查 SEC 資料時附在連線裡給 SEC，不會上傳到 GitHub 或其他地方
- 安裝時先問我的英文名字和 Email，填進 `sec_user_agent` 再繼續
- 之後任何 skill 要連 SEC（trader-copycat、trader-analyst、trader-news 的 8-K 公告）時，`sec_user_agent` 是空的、或還是舊版的共用名稱 `Raco Trader Pack raco-trader-pack@example.com`，就先停下來問我，填好再跑；不要自己編一個，也不要用別人的
- 填好了 SEC 還是回 403，先確認格式是「名字 空格 Email」；格式對的話通常是查太快，等幾分鐘再跑
- 我要改預算或規則，直接改 `trader/config.json`，改完跟我說改了哪幾個數字
- 不要把 `paper` 改成 `false`，除非我明確說要用真錢
- 要接真的券商，照 trader-broker 的步驟帶我接 Alpaca，先接模擬帳戶；API 金鑰只放環境變數，不要在對話裡跟我要

## 執行

- skill 裡的指令寫 `python3`；Windows 沒有 `python3` 就用 `python` 或 `py`
- Windows 跑之前先設 `PYTHONIOENCODING=utf-8`（PowerShell：`$env:PYTHONIOENCODING="utf-8"`），不然輸出會亂碼或報錯
- 每個 skill 產生的檔案都放在 `trader/`（`targets.json`、`orders.json`、`state/`…），下一個 skill 會接著讀
