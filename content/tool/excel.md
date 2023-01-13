---
date: 2020-01-01
lastmod: ["lastmod"]
title: Excel
LinkTitle: Excel
description: Excel
tags: ["Excel"]
---

# Excel

## 便利

### 重複

重複する値のチェック
```text
=IF(COUNTIF($A$1:$B$10,$A2)>=2,"Duplicate","-")
```
重複なしカウント(セルの範囲内に空白がある場合エラーあり)
```text
=SUMPRODUCT(1/COUNTIF($A$1:$B$10,$A$1:$B$10))
```
重複なしカウント(セルの範囲内に空白がある場合もエラーなし)
```text
=SUMPRODUCT(($A$1:$B$10<>"")/COUNTIF($A$1:$B$10,$A$1:$B$10&""))
```

### 表示中のセルの合計(非表示のセルを除く)

`109` は固定値
```text
=SUBTOTAL(109,$A$1:$B$10)
```

## 検索

### VLOOKUP(検索値,範囲,列位置,FALSE)
e.g.) ```A1``` の値が ```sample``` シートの範囲(```A1:A10```) にある場合 ```B``` 列の値を出力
```text
=VLOOKUP(A1,sample!$A$1:$B$10,2,FALSE)
```

### 特定の値があるか判定
```text
=IF(COUNTIF(A1:B10,"aaa")>0,"Yes","No")
```

## 日付・時刻
### 曜日
e.g.) ```A1``` の日時を日本語の曜日の短縮形で表示。
```text
=TEXT(A1,"aaa")
```
|第2引数|description|
|---|---|
|```aaaa```|日本語の曜日|土曜日|
|```aaa```|日本語の曜日の短縮形|土|
|```dddd```|英語の曜日|Saturday|
|```ddd```|英語の曜日の曜日の短縮形|Sat|

### 時間の差分

#### 時間(差分が24時間未満の場合のみ)
```text
=TEXT(B1-A1,"hh:mm:ss")
```

#### 日数
```text
=DATEDIF(A1,B1,"d")
```
#### 週数
```text
=DATEDIF(A1,B1,"d")/7
```
#### 月数
```text
=DATEDIF(A1,B1,"m")
```
#### 年数
年齢計算 etc.
```text
=DATEDIF(A1,B1,"y")
```

### n年後, nヶ月後, n日後
うるう年にも自動で対応  
e.g. 3年6ヶ月後
```text
=DATE(YEAR(A1)+3, MONTH(A1)+6, DAY(A1))
```


## ハイパーリンク

### シートへのリンク
```B1``` に記載されたシート内の ```A1``` へ移動
```text
=HYPERLINK("#"&B1&"!A1",B1)
```

### Webへのリンク
```text
=HYPERLINK("https://sample.com/","Sample")
```

## シート

### シート名
```text
=RIGHT(CELL("filename",A1),LEN(CELL("filename",A1))-FIND("]",CELL("filename",A1)))
```

### シート名の一覧

**1) VBA を使用**

1. ```Alt```+ ```F11``` で ```VBE``` 画面を表示
2. ```Alt``` を押下したまま ```I``` → ```M``` の順に押下し標準モジュールを挿入
3. 白画面に以下を貼り付け
```text
Sub test()
Dim i As Long
For i = 1 To Sheets.Count
Range("A" & i).Value = Worksheets(i).Name
Next i
End Sub
```
4. ```Alt```+ ```F11``` で通常画面を表示
5. シート名の一覧を作成したいシートで```Alt```+ ```FB```から ```マクロ``` を表示させ ```test``` を実行

**2) 4.0マクロ関数を使用**

6. 上部メニューの ```挿入``` > ```名前``` > ```定義``` から名前欄に任意の名前（仮に「シート一覧」）を記載
7. 参照範囲欄に以下を記載し ```OK```押下
```text
=MID(GET.WORKBOOK(1),FIND("]",GET.WORKBOOK(1))+1,31)&T(NOW())
```
8. シート名を出力したいセルに以下を記載し下へコピー
```text
=IF(COLUMNS(シート一覧)<ROW(A1),"",INDEX(シート一覧,ROW(A1)))
```

## 隣接したセルの値に加算

### そのセルの1つ上のセルの数字に 1 を加算
```text
=MAX(INDIRECT(ADDRESS(1,COLUMN())&":"&ADDRESS(ROW()-1,COLUMN())))+1
```

### そのセルの左隣のセルの数字に 1 を加算
```$A$1``` の箇所に「基準になる左端のセル」を指定
```text
=MAX(INDIRECT(ADDRESS(ROW(),$A$1)&":"&ADDRESS(ROW(),COLUMN()-1)))+1
```


## その他

### 数値を漢数字に変換

```text
=NUMBERSTRING(A2,1)
```

### 桁数に満たない場合 任意の文字で埋める
e.g.) 10桁ゼロ詰め  
```A1``` の値が ```1234``` の場合, ```1234aaaaaa``` と出力
```text
=A1&LEFT(REPT("a","10"), 10-LEN(A1))
```



## 参照

- [Excel 空白を含むセル範囲から重複を除いたデータ数をカウントする方法](https://my-terrace.com/excel_count/)
