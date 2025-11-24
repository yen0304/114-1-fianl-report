# 字型資料夾

本資料夾包含專案使用的中文字型檔案。

## 字型資訊

- **字型名稱**: Noto Sans TC（思源黑體繁體中文版）
- **授權**: SIL Open Font License 1.1
- **來源**: Google Noto Fonts
- **官方網站**: https://fonts.google.com/noto/specimen/Noto+Sans+TC

## 檔案說明

本資料夾包含完整的 Noto Sans TC 字型家族：

### Noto_Sans_TC/ 目錄結構
- `static/` - 靜態字型檔案（推薦使用）
  - `NotoSansTC-Regular.ttf` - 常規字重（用於一般文字）
  - `NotoSansTC-Bold.ttf` - 粗體字重（用於標題和強調）
  - `NotoSansTC-Medium.ttf` - 中等字重
  - 以及其他字重（Thin, Light, SemiBold, ExtraBold, Black）
- `NotoSansTC-VariableFont_wght.ttf` - 可變字型（包含所有字重）
- `OFL.txt` - SIL Open Font License 授權條款
- `README.txt` - 字型官方說明

### 為什麼使用靜態字型？
本專案使用 `static/` 資料夾中的靜態字型檔案，因為：
- ✓ matplotlib 對靜態 TTF 格式支援最佳
- ✓ 載入速度快，穩定性高
- ✓ 只載入需要的字重（Regular, Bold, Medium）
- ✓ 相容性好，適合數據視覺化

## 授權說明

Noto Sans TC 採用 SIL Open Font License 1.1 授權，可以自由使用於個人和商業專案。

詳細授權條款請參考：https://scripts.sil.org/OFL

## 為什麼使用 Noto Sans TC？

1. **開源免費**: 完全免費，無需擔心授權問題
2. **跨平台支援**: 在 Windows、macOS、Linux 都能正常顯示
3. **字元完整**: 包含所有繁體中文常用字
4. **美觀易讀**: Google 設計，適合數據視覺化
5. **專案內建**: 無需依賴系統字型，確保在任何環境都能正常顯示

## 更新字型

如需更新字型到最新版本，可以從 Google Fonts 下載：

```bash
# 下載可變字型
curl -L "https://github.com/google/fonts/raw/main/ofl/notosanstc/NotoSansTC%5Bwght%5D.ttf" -o "NotoSansTC-Variable.ttf"
```

或訪問 Google Fonts 官網：https://fonts.google.com/noto/specimen/Noto+Sans+TC

