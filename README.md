# remotes_test
使用Vue和ElementUI實做國家目錄
API來源: https://restcountries.com/

## 需求
- 目錄必須顯示欄位,()内為Api資料物件的屬性
  - 國旗(圖片資源請使用 flags裡面的PNG)
  - 國家名稱（name.official）
  - 2位國家代碼(cca2)
  - 3位國家代碼(cca3)
  - 母語名稱(name.nativeName.zho.official)
  - 替代國家名稱(altSpellings)
  - 國際電話區號(idd)
- 以國名搜尋功能(模糊搜尋)
- 以國名排序功能（正序、倒序）
- 分頁(每頁25筆)
- 點擊國家名稱后顯示一個Modal，裏面顯示該國家的其他資訊

## 備註
- 備註1: 母語國家顯示 nativeName 底下所有 object 的 official 變數
- 備註2: 搜尋欄位為空時進行搜尋會帶出所有資料

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).