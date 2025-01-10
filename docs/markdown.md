---
date: 2024-09-18
title: Markdown �y�k
nav_order: 10
tags:
  - markdown
---
### �ѦҤ��G
> [Markdown ���](https://markdown.tw/ "Markdown ������")
> [https://commonmark.org/help/](https://commonmark.org/help/ "�x���y�k²��")

## �϶�

### �϶��Y��

�ϥ� 4 �ӪťթάO 1 �� tab�A�i�H����Y�Ƥ�r

	�϶���ܥΦb���Ʊ椺�e�H�@��q����󪺤覡�h�ƪ��A�ӬO�ӭ�Ӫ��ˤl��ܡC

### �ި� (Blockquote)

>�b��r�e���[�J�u`>`�v�A�i�H�إߤި��ĪG�C
>�Ϊ̿����r��A�s�ο��\��G\>Paragraph>Quote

### Callout

> [!Note] Callout�ĪG
> Callout �M�ި��ĪG�����A���󬰬���B���[�C
> �s�ο��\��G\>Insert>Callout
[Callout �]�w�Ѧ�](https://help.obsidian.md/Editing+and+formatting/Callouts)

#### �M��
##### ���زM��
- �M��1
- �M��2
##### �Ʀr�M��
1. �M��1
2. �M��2


### ���{���X�϶�

```
`inline text`
``tom`s code``
```

�ϥΤϤ޸�(Backtick) �е���r�A�i�H��ܦ椺�϶��ĪG�G

**��**�G�ϥΤϤ޸�(Backtick) `inline text` �i�H��ܦ椺�϶��ĪG�C

�Y�n�b�椺�϶����J�Ϥ޸��A�i�H�Φh�ӤϤ޸��Ӷ}�ҩM�����{���X�Ϭq�G

**��**�G``tom`s code``�C

Please don't use any `<blink>` tags.

### �h��{���X�϶�

�ϥΤT�ӤϤ޸�(Backtick) \`\`\`your code\`\`\` �i�H��ܦ椺�϶��ĪG�C

�ҡG
``` C#
//User���
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
## �s��

#### �椺�Φ�

```
�~���s��: [link text](http://url "link title") 
�����s��: [[Hot Key]] 
```
[Obsidian Help](https://help.obsidian.md/Editing+and+formatting/Basic+formatting+syntax "Obsidian Help") 
[[Hot Key]]

#### �ѦҧΦ�1

I get 10 times more traffic from [Google] [1] than from
[Yahoo] [2] or [MSN] [3].

  [1]: http://google.com/        "Google"
  [2]: http://search.yahoo.com/  "Yahoo Search"
  [3]: http://search.msn.com/    "MSN Search"

##### �ѦҧΦ�2

I get 10 times more traffic from [Google][] than from
[Yahoo][] or [MSN][].

  [google]: http://google.com/        "Google"
  [yahoo]:  http://search.yahoo.com/  "Yahoo Search"
  [msn]:    http://search.msn.com/    "MSN Search"

---
## �Ϥ�

#### �����Ϥ�
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

#### �~���Ϥ�
```
![Sample|180](https://t4.ftcdn.net/jpg/01/43/42/83/240_F_143428338_gcxw3Jcd0tJpkvvb53pfEztwtU9sxsgT.jpg)
```

![Sample|180](https://t4.ftcdn.net/jpg/01/43/42/83/240_F_143428338_gcxw3Jcd0tJpkvvb53pfEztwtU9sxsgT.jpg)
