---
date: 2024-09-18
title: Markdown 語法
nav_order: 10
tags:
  - markdown
---
### 參考文件：
> [Markdown 文件](https://markdown.tw/ "Markdown 中文文件")
> [https://commonmark.org/help/](https://commonmark.org/help/ "官網語法簡表")

## 區塊

### 區塊縮排

使用 4 個空白或是 1 個 tab，可以顯示縮排文字

	區塊顯示用在不希望內容以一般段落文件的方式去排版，而是照原來的樣子顯示。

### 引言 (Blockquote)

>在文字前面加入「`>`」，可以建立引言效果。
>或者選取文字後，叫用選單功能：\>Paragraph>Quote

### Callout

> [!Note] Callout效果
> Callout 和引言效果類似，但更為突顯、美觀。
> 叫用選單功能：\>Insert>Callout
[Callout 設定參考](https://help.obsidian.md/Editing+and+formatting/Callouts)

#### 清單
##### 項目清單
- 清單1
- 清單2
##### 數字清單
1. 清單1
2. 清單2


### 單行程式碼區塊

```
`inline text`
``tom`s code``
```

使用反引號(Backtick) 標註文字，可以顯示行內區塊效果：

**例**：使用反引號(Backtick) `inline text` 可以顯示行內區塊效果。

若要在行內區塊插入反引號，可以用多個反引號來開啟和結束程式碼區段：

**例**：``tom`s code``。

Please don't use any `<blink>` tags.

### 多行程式碼區塊

使用三個反引號(Backtick) \`\`\`your code\`\`\` 可以顯示行內區塊效果。

例：
``` C#
//User資料
var model = new VmFormUsers
{
	Data = user
};
```
#### Highligh

```
==Highlighted text==
```
==Highlighted text==

---
## 連結

#### 行內形式

```
外部連結: [link text](http://url "link title") 
內部連結: [[Hot Key]] 
```
[Obsidian Help](https://help.obsidian.md/Editing+and+formatting/Basic+formatting+syntax "Obsidian Help") 
[[Hot Key]]

#### 參考形式1

I get 10 times more traffic from [Google] [1] than from
[Yahoo] [2] or [MSN] [3].

  [1]: http://google.com/        "Google"
  [2]: http://search.yahoo.com/  "Yahoo Search"
  [3]: http://search.msn.com/    "MSN Search"

##### 參考形式2

I get 10 times more traffic from [Google][] than from
[Yahoo][] or [MSN][].

  [google]: http://google.com/        "Google"
  [yahoo]:  http://search.yahoo.com/  "Yahoo Search"
  [msn]:    http://search.msn.com/    "MSN Search"

---
## 圖片

#### 內部圖片
```
![[sample.jpg]]
![[sample.jpg|180x120]]
![[sample.jpg|180]]
![sample.jpg|180](../_images/sample.jpg)
```  

![[sample.jpg|180x120]]

![sample.jpg](_images/sample.jpg){ width=180 height=80 }

![sample.jpg](_images/sample.jpg){ width=180}

![sample.jpg](_images/sample.jpg)

#### 外部圖片
```
![Sample|180](https://t4.ftcdn.net/jpg/01/43/42/83/240_F_143428338_gcxw3Jcd0tJpkvvb53pfEztwtU9sxsgT.jpg)
```

![Sample|180](https://t4.ftcdn.net/jpg/01/43/42/83/240_F_143428338_gcxw3Jcd0tJpkvvb53pfEztwtU9sxsgT.jpg)
