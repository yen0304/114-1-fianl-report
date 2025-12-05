# 統計分析增強計畫 | Statistical Analysis Enhancement Plan

## 問題分析 | Problem Analysis

### 1. NaN 值問題
**現象**: 在相關係數分析中出現 `Pearson r = nan`
**原因**: 某些縣市的土地面積數據缺失或為0,導致計算 `每平方公里據點數` 時出現除以零或無效值

### 2. 缺少統計表格與圖表
**需求**: 參考薪資政策研究範例,新增以下表格:
- 表格1: 皮爾森相關係數分析統計表
- 表格2-3: 迴歸統計表
- 表格4: ANOVA表
- 表格5: 線性回歸計算表
- 表格6: 各數值統計表
- 迴歸分析視覺化圖表(散佈圖+迴歸線)

---

## 解決方案 | Solution Plan

### Phase 1: 修正 NaN 問題

```python
def calculate_correlation_analysis(df):
    """
    計算相關係數分析 | Calculate correlation analysis
    
    修正: 過濾 NaN 和無限值以避免計算錯誤
    """
    print("\n" + "=" * 80)
    print("相關係數分析 | Correlation Analysis")
    print("=" * 80)
    
    results = {}
    correlation_table = []
    
    # 變數對1:老年人口比例 vs 每千位高齡人口據點數
    valid_mask1 = df[['老年人口比例(%)', '每千位高齡人口據點數']].notna().all(axis=1) & \
                  np.isfinite(df['老年人口比例(%)']).values & \
                  np.isfinite(df['每千位高齡人口據點數']).values
    if valid_mask1.sum() > 2:
        r1, p1 = pearsonr(df.loc[valid_mask1, '老年人口比例(%)'], 
                          df.loc[valid_mask1, '每千位高齡人口據點數'])
        results['aging_vs_service'] = {'r': r1, 'p': p1, 'n': valid_mask1.sum()}
        correlation_table.append({
            '變數對': '老年人口比例(%) vs 每千位高齡人口據點數',
            '樣本數': valid_mask1.sum(),
            'Pearson r': r1,
            'p-value': p1,
            '顯著性': '**' if p1 < 0.01 else '*' if p1 < 0.05 else 'ns'
        })
        print(f"\n【變數對1】老年人口比例 vs 每千位高齡人口據點數")
        print(f"  有效樣本數 n = {valid_mask1.sum()}")
        print(f"  Pearson r = {r1:.4f}, p-value = {p1:.4f}")
    else:
        results['aging_vs_service'] = {'r': np.nan, 'p': np.nan, 'n': valid_mask1.sum()}
        print(f"\n【變數對1】⚠ 有效樣本數不足 (n={valid_mask1.sum()})")
    
    # 變數對2:人口密度 vs 每平方公里據點數
    valid_mask2 = df[['人口密度(人/km²)', '每平方公里據點數']].notna().all(axis=1) & \
                  np.isfinite(df['人口密度(人/km²)']).values & \
                  np.isfinite(df['每平方公里據點數']).values
    if valid_mask2.sum() > 2:
        r2, p2 = pearsonr(df.loc[valid_mask2, '人口密度(人/km²)'], 
                          df.loc[valid_mask2, '每平方公里據點數'])
        results['density_vs_spatial'] = {'r': r2, 'p': p2, 'n': valid_mask2.sum()}
        correlation_table.append({
            '變數對': '人口密度(人/km²) vs 每平方公里據點數',
            '樣本數': valid_mask2.sum(),
            'Pearson r': r2,
            'p-value': p2,
            '顯著性': '**' if p2 < 0.01 else '*' if p2 < 0.05 else 'ns'
        })
        print(f"\n【變數對2】人口密度 vs 每平方公里據點數")
        print(f"  有效樣本數 n = {valid_mask2.sum()}")
        print(f"  Pearson r = {r2:.4f}, p-value = {p2:.4f}")
    else:
        results['density_vs_spatial'] = {'r': np.nan, 'p': np.nan, 'n': valid_mask2.sum()}
        print(f"\n【變數對2】⚠ 有效樣本數不足 (n={valid_mask2.sum()})")
    
    # 變數對3:65歲以上人口 vs 總據點數
    valid_mask3 = df[['65歲以上', '總據點數']].notna().all(axis=1) & \
                  np.isfinite(df['65歲以上']).values & \
                  np.isfinite(df['總據點數']).values
    if valid_mask3.sum() > 2:
        r3, p3 = pearsonr(df.loc[valid_mask3, '65歲以上'], 
                          df.loc[valid_mask3, '總據點數'])
        results['elderly_vs_facilities'] = {'r': r3, 'p': p3, 'n': valid_mask3.sum()}
        correlation_table.append({
            '變數對': '65歲以上人口 vs 總據點數',
            '樣本數': valid_mask3.sum(),
            'Pearson r': r3,
            'p-value': p3,
            '顯著性': '**' if p3 < 0.01 else '*' if p3 < 0.05 else 'ns'
        })
        print(f"\n【變數對3】65歲以上人口 vs 總據點數")
        print(f"  有效樣本數 n = {valid_mask3.sum()}")
        print(f"  Pearson r = {r3:.4f}, p-value = {p3:.4f}")
    else:
        results['elderly_vs_facilities'] = {'r': np.nan, 'p': np.nan, 'n': valid_mask3.sum()}
        print(f"\n【變數對3】⚠ 有效樣本數不足 (n={valid_mask3.sum()})")
    
    # 儲存相關係數表格
    results['correlation_table'] = pd.DataFrame(correlation_table)
    
    return results
```

**修正重點**:
- 使用 `notna()` 檢查缺失值
- 使用 `np.isfinite()` 檢查無限值
- 只對有效數據進行相關分析
- 記錄有效樣本數

---

### Phase 2: 新增統計表格輸出函數

#### 函數1: 皮爾森相關係數分析統計表

```python
def print_correlation_table(corr_results):
    """
    輸出表格:皮爾森相關係數分析統計表
    """
    print("\n" + "=" * 80)
    print("表一、皮爾森相關係數分析統計表")
    print("=" * 80)
    
    if 'correlation_table' in corr_results:
        df_corr = corr_results['correlation_table']
        print(df_corr.to_string(index=False))
        print("\n註:* p < 0.05, ** p < 0.01, ns = 不顯著")
    else:
        print("無相關係數數據")
```

#### 函數2: 迴歸統計表與ANOVA表

```python
def print_regression_table(reg_results, df):
    """
    輸出表格:迴歸統計表與ANOVA表
    """
    print("\n" + "=" * 80)
    print("表二、線性迴歸統計表 - 模型1")
    print("=" * 80)
    print("依變數:每千位高齡人口據點數")
    print("自變數:老年人口比例(%)")
    print("-" * 80)
    
    model1 = reg_results['model1']
    n = len(df)
    
    # 迴歸統計
    print(f"\nMultiple R:     {np.sqrt(model1['r2']):.4f}")
    print(f"R²:             {model1['r2']:.4f}")
    print(f"調整後 R²:       {1 - (1-model1['r2'])*(n-1)/(n-2):.4f}")
    print(f"標準誤:         {np.sqrt(mean_squared_error(df['每千位高齡人口據點數'], model1['predictions'])):.4f}")
    print(f"觀察值個數:     {n}")
    
    # ANOVA表
    print("\n" + "-" * 80)
    print("表三、ANOVA表 - 模型1")
    print("-" * 80)
    
    SSR = np.sum((model1['predictions'] - df['每千位高齡人口據點數'].mean())**2)
    SSE = np.sum((df['每千位高齡人口據點數'] - model1['predictions'])**2)
    SST = SSR + SSE
    df_reg = 1  # 自變數個數
    df_res = n - 2  # n - k - 1
    MSR = SSR / df_reg
    MSE = SSE / df_res
    F = MSR / MSE
    
    anova_data = {
        '來源': ['迴歸', '殘差', '總和'],
        'df': [df_reg, df_res, n-1],
        'SS': [SSR, SSE, SST],
        'MS': [MSR, MSE, ''],
        'F': [F, '', ''],
        '顯著性F': [1 - stats.f.cdf(F, df_reg, df_res), '', '']
    }
    df_anova = pd.DataFrame(anova_data)
    print(df_anova.to_string(index=False))
    
    # 係數表
    print("\n" + "-" * 80)
    print("表四、線性迴歸係數表 - 模型1")
    print("-" * 80)
    
    se_slope = np.sqrt(MSE / np.sum((df['老年人口比例(%)'] - df['老年人口比例(%)'].mean())**2))
    t_slope = model1['coef'] / se_slope
    p_slope = 2 * (1 - stats.t.cdf(abs(t_slope), df_res))
    
    coef_data = {
        '': ['截距', '老年人口比例(%)'],
        '係數': [model1['intercept'], model1['coef']],
        '標準誤': ['', se_slope],
        't統計': ['', t_slope],
        'p-value': ['', p_slope]
    }
    df_coef = pd.DataFrame(coef_data)
    print(df_coef.to_string(index=False))
    
    # 模型2
    print("\n\n" + "=" * 80)
    print("表五、線性迴歸統計表 - 模型2")
    print("=" * 80)
    print("依變數:總據點數")
    print("自變數:65歲以上人口")
    print("-" * 80)
    
    model2 = reg_results['model2']
    print(f"\nMultiple R:     {np.sqrt(model2['r2']):.4f}")
    print(f"R²:             {model2['r2']:.4f}")
    print(f"調整後 R²:       {1 - (1-model2['r2'])*(n-1)/(n-2):.4f}")
    print(f"標準誤:         {np.sqrt(mean_squared_error(df['總據點數'], model2['predictions'])):.4f}")
    print(f"觀察值個數:     {n}")
```

#### 函數3: 描述性統計表

```python
def print_descriptive_table(desc_results, df):
    """
    輸出表格:各數值統計表
    """
    print("\n" + "=" * 80)
    print("表六、各縣市主要指標統計表")
    print("=" * 80)
    
    summary_cols = ['縣市名稱', '總人口', '65歲以上', '老年人口比例(%)', 
                    '總據點數', '每千位高齡人口據點數', '人口密度(人/km²)']
    
    display_df = df[summary_cols].copy()
    display_df['總人口'] = display_df['總人口'].apply(lambda x: f'{x:,.0f}')
    display_df['65歲以上'] = display_df['65歲以上'].apply(lambda x: f'{x:,.0f}')
    display_df['老年人口比例(%)'] = display_df['老年人口比例(%)'].apply(lambda x: f'{x:.2f}')
    display_df['每千位高齡人口據點數'] = display_df['每千位高齡人口據點數'].apply(lambda x: f'{x:.2f}')
    display_df['人口密度(人/km²)'] = display_df['人口密度(人/km²)'].apply(lambda x: f'{x:.2f}')
    
    print(display_df.to_string(index=False))
```

---

### Phase 3: 新增迴歸分析視覺化

```python
def plot_regression_analysis(df, reg_results):
    """
    繪製迴歸分析圖表 | Plot regression analysis with scatter plot and regression line
    """
    fig, axes = plt.subplots(1, 2, figsize=(16, 6))
    
    # 圖1:模型1 - 老年人口比例 vs 每千位高齡人口據點數
    ax1 = axes[0]
    model1 = reg_results['model1']
    
    # 散佈圖
    ax1.scatter(df['老年人口比例(%)'], df['每千位高齡人口據點數'], 
                alpha=0.6, s=100, edgecolors='black', linewidth=0.5)
    
    # 迴歸線
    x_line = np.linspace(df['老年人口比例(%)'].min(), df['老年人口比例(%)'].max(), 100)
    y_line = model1['intercept'] + model1['coef'] * x_line
    ax1.plot(x_line, y_line, 'r-', linewidth=2, 
            label=f'迴歸線: y = {model1["intercept"]:.2f} + {model1["coef"]:.2f}x')
    
    # 添加縣市標籤
    for idx, row in df.iterrows():
        ax1.annotate(row['縣市名稱'], 
                    (row['老年人口比例(%)'], row['每千位高齡人口據點數']),
                    xytext=(3, 3), textcoords='offset points', fontsize=8, alpha=0.7)
    
    ax1.set_xlabel('老年人口比例 (%)', fontsize=11)
    ax1.set_ylabel('每千位高齡人口據點數', fontsize=11)
    ax1.set_title(f'模型1:線性迴歸分析\nR² = {model1["r2"]:.4f}', fontsize=12, fontweight='bold')
    ax1.legend(fontsize=9)
    ax1.grid(True, alpha=0.3)
    
    # 圖2:模型2 - 65歲以上人口 vs 總據點數
    ax2 = axes[1]
    model2 = reg_results['model2']
    
    # 散佈圖
    ax2.scatter(df['65歲以上']/10000, df['總據點數'], 
                alpha=0.6, s=100, edgecolors='black', linewidth=0.5, color='green')
    
    # 迴歸線
    x_line2 = np.linspace(df['65歲以上'].min(), df['65歲以上'].max(), 100)
    y_line2 = model2['intercept'] + model2['coef'] * x_line2
    ax2.plot(x_line2/10000, y_line2, 'r-', linewidth=2, 
            label=f'迴歸線: y = {model2["intercept"]:.2f} + {model2["coef"]:.4f}x')
    
    # 添加縣市標籤
    for idx, row in df.iterrows():
        ax2.annotate(row['縣市名稱'], 
                    (row['65歲以上']/10000, row['總據點數']),
                    xytext=(3, 3), textcoords='offset points', fontsize=8, alpha=0.7)
    
    ax2.set_xlabel('65歲以上人口 (萬人)', fontsize=11)
    ax2.set_ylabel('總據點數', fontsize=11)
    ax2.set_title(f'模型2:線性迴歸分析\nR² = {model2["r2"]:.4f}', fontsize=12, fontweight='bold')
    ax2.legend(fontsize=9)
    ax2.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()
```

---

## 實施步驟 | Implementation Steps

### Step 1: 替換相關分析函數
在 notebook 中找到 `def calculate_correlation_analysis(df):` 函數,替換為修正版本

### Step 2: 新增表格輸出函數
在統計分析函數區段後,新增三個表格輸出函數:
- `print_correlation_table()`
- `print_regression_table()`
- `print_descriptive_table()`

### Step 3: 新增視覺化函數
在視覺化函數區段,新增 `plot_regression_analysis()` 函數

### Step 4: 更新執行流程
在執行分析的儲存格後,新增以下呼叫:

```python
# 輸出統計表格
print_correlation_table(corr_results)
print_regression_table(reg_results, df)
print_descriptive_table(desc_results, df)

# 繪製迴歸分析圖表
plot_regression_analysis(df, reg_results)
```

---

## 預期成果 | Expected Outcomes

### 1. 修正後的輸出
```
【變數對2】人口密度 vs 每平方公里據點數
  有效樣本數 n = 20
  Pearson r = 0.8234, p-value = 0.0001
```

### 2. 新增的表格
- ✅ 表一: 皮爾森相關係數分析統計表 (含樣本數、r值、p值、顯著性)
- ✅ 表二: 線性迴歸統計表 - 模型1 (含 Multiple R, R², 調整後R², 標準誤)
- ✅ 表三: ANOVA表 - 模型1 (含 df, SS, MS, F, 顯著性F)
- ✅ 表四: 線性迴歸係數表 - 模型1 (含係數、標準誤、t統計、p-value)
- ✅ 表五: 線性迴歸統計表 - 模型2
- ✅ 表六: 各縣市主要指標統計表

### 3. 新增的視覺化
- ✅ 模型1迴歸分析圖 (散佈圖 + 迴歸線 + 縣市標籤)
- ✅ 模型2迴歸分析圖 (散佈圖 + 迴歸線 + 縣市標籤)

---

## 注意事項 | Notes

1. **數據清理**: 確保在計算衍生指標時避免除以零
2. **有效樣本**: 相關分析需要至少3個有效樣本點
3. **表格格式**: 使用 `to_string(index=False)` 輸出無索引的表格
4. **統計顯著性**: 使用 `*` (p<0.05) 和 `**` (p<0.01) 標註顯著性
5. **中文字型**: 確保圖表使用 Noto Sans TC 字型以正確顯示中文

---

## 參考資料 | References

- Pearson correlation coefficient calculation
- Linear regression with scikit-learn
- ANOVA table construction
- Matplotlib scatter plots with regression lines
