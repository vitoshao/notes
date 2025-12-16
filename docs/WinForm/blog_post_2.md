---
title: ComboBox 控制項
layout: default
parent: WinForm
nav_order: 2
description: "ComboBox 控制項"
date: 2016-01-15
tags: [WinForm]
postid: "6253865321427628050"
---
[ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 是下拉式輸入選單，基本上它是一個 TextBox 加上一個下拉式清單，目的是為了讓使用者方便操作。  可以變更設定成不允許使用者輸入，以限制資料內容。  

對於 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 這個控制項，重點都在這個下拉式清單，它就同 [ListBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.listbox.aspx) 一樣，就是一個物件集合，可以透過 [ComboBox.Items](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.items.aspx) 屬性取得。  既然是集合物件，所以在下拉選單中可以放入任何型別的資料，例如：數值、字串或自訂物件。  同樣的，因為放入的是非固定型別，在讀取時就必須再轉型回來。  
```c#
// add 3 item
comboBox1.Items.Add(new Student("1", "StudendA", "123"));
comboBox1.Items.Add(new Student("2", "StudendB", "123"));
comboBox1.Items.Add(new Student("3", "StudendC", "123"));
// get 2nd item
Student item = (Student)comboBox1.Items[1];
```

## Using String Data

不管數字或字串，對 [ComboBox.Items](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.items.aspx) 而言，都是 Object 資料，只是型別較簡單，可以相互轉型。  底下是 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 中用來修改清單項目的幾個方法。  

- [ObjectCollection.Add](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.objectcollection.add.aspx) ：加入指定的項目至 Items
- [ObjectCollection.AddRange](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.objectcollection.addrange.aspx) ：將陣列中的項目加入至 Items
- [ObjectCollection.Remove](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.objectcollection.remove.aspx) ：移除特定項目
- [ObjectCollection.RemoveAt](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.objectcollection.removeat.aspx) ：移除特定索引項目
- [ObjectCollection.Clear](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.objectcollection.clear.aspx) ：清除所有項目
```c#
// Add and AddRange
comboBox1.Items.Add(6);
comboBox1.Items.Add(5);
comboBox1.Items.Add(4);
string[] data = new string[] { "3", "2", "1" };
comboBox1.Items.AddRange(data);
// Remove and RemoveAt
comboBox1.Items.Remove(comboBox1.Items[4]);
comboBox1.Items.RemoveAt(1);
```

## Using User Class Data

你也可以在清單項目中放入自訂的類別物件。  
```c#
class Student
{
public Student(string _num, string _name, string _tel)
{
Num = _num;
Name = _name;
Tel = _tel;
}
public string Num { get; set; }
public string Name { get; set; }
public string Tel { get; set; }
public override string ToString()
{
return this.Name;
}
}
// Add and AddRange
comboBox1.Items.Add(new Student("1", "StudendA", "123"));
comboBox1.Items.Add(new Student("2", "StudendB", "123"));
comboBox1.Items.Add(new Student("3", "StudendC", "123"));
Student[] data = new Student[] { new Student("4", "StudendD", "123"), new Student("5", "StudendE", "123") };
comboBox1.Items.AddRange(data);
// Remove and RemoveAt
comboBox1.Items.Remove(comboBox1.Items[3]);
comboBox1.Items.RemoveAt(1);
// Clear all items
comboBox1.Items.Clear();
```

上面例子，下拉選單之所以會出現 StudendA, StudendB, StudendC 是因為在我們的自訂類別中有覆寫了 ToString 方法。  對 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 而言，預設的顯示文字就是該物件的 ToString 所回傳的資料。

## Using DataSource

[ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 提供 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource.aspx) 屬性，可以用來指定清單項目，要特別注意的是，若指定了 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource.aspx) 屬性，清單項目就不可以更動。  

### Binding a List

你可以直接將 List 物件塞給 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource.aspx) 。預設的顯示資訊內容是該物件的 ToString 回傳值。
```c#
List<Student> datalist = new List<Student>();
datalist.Add(new Student("1", "StudendA", "1111-5678"));
datalist.Add(new Student("2", "StudendB", "2222-5678"));
datalist.Add(new Student("3", "StudendC", "3333-5678"));
comboBox1.DataSource = datalist;
```

### 指定 DisplayMember 、 ValueMember

當使用 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource.aspx) 來指定資料來源，你也就可以使用 DisplayMember 、 ValueMember 屬性來設定 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 要顯示的文字內容，以及 [ComboBox.SelectedValue](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.selectedvalue.aspx) 值。  
```c#
List<Student> datalist = new List<Student>();
datalist.Add(new Student("1", "StudendA", "1111-5678"));
datalist.Add(new Student("2", "StudendB", "2222-5678"));
datalist.Add(new Student("3", "StudendC", "3333-5678"));
comboBox1.DisplayMember = "Tel";　  // ComboBox 將會顯示 Student 物件的 Tel 資訊。
comboBox1.ValueMember = "Num";
comboBox1.DataSource = datalist;
```

這時候就可以透過 SelecttedValue 屬性來指定選取項目。但是要注意的是，指派資料的型別必須與類別欄位的型別相同。
```c#
comboBox1.SelecttedValue = "1";
comboBox1.SelecttedValue = 1;    //因為 Student.Num 為字串型別，若指定數字會沒有效果。
```

如果沒有指定 DisplayMember 會怎麼樣，例如：
```c#
comboBox1.DisplayMember = "";
comboBox1.ValueMember = "Num";
comboBox1.DataSource = datalist;
```

上面例子，下拉選單會改成顯示 StudendA, StudendB, StudendC ，這是因為若指定了 ValueMember ，卻沒有指定 DisplayMember 的話，預設的顯示文字就是該物件的 ToString 所回傳的資料。

## Using BindingSource

上面範例，當 datalist 指定給 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource.aspx) 之後， [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 與 datalist 之間就二造不相干了。  你如果對 datalist 做異動，也不會反應到 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 身上，除了重新指定 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource.aspx) 。  

而 [BindingSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.bindingsource.aspx) 是一種雙向繫結控制項，用來將表單上的控制項與資料建立連結關係。  它可以用來維護資料與UI上的一致性，尤其是若有多個控制項共用同一個資料來源時，你只要異動 [BindingSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.bindingsource.aspx) 就可以同步所有的控制項資料。  除此之外，它也會接收變更通知，也就是說當 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 被使用者變更了選取項目，也會反應給 [BindingSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.bindingsource.aspx) 物件。  
```c#
List<Student> datalist = new List<Student>();
datalist.Add(new Student("1", "StudendA", "123"));
datalist.Add(new Student("2", "StudendB", "123"));
datalist.Add(new Student("3", "StudendC", "123"));
BindingSource bs = new BindingSource();
bs.DataSource = datalist;
comboBox1.DisplayMember = "Name";
comboBox1.ValueMember = "Num";
comboBox1.DataSource = bs;
comboBox2.DisplayMember = "Name";
comboBox2.ValueMember = "Num";
comboBox2.DataSource = bs;
bs.RemoveAt(1);
bs.Add(new Student("4", "StudendD", "123"));
```

上面例子中，我們對 [BindingSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.bindingsource.aspx) 進行 [Remove](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.objectcollection.remove.aspx) 與 [Add](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.objectcollection.add.aspx) 操作，其結果會同時反應到二個 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 中，而 datalist 這個 List 也會跟著變更。  而且，若你對其中一個 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 變更選取項目，另一個 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 也會同時變更。

## 如何清除重設 ComboxBox 中的項目

當 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 沒有使用資料繫結時，你可以透過 Add, Remove, [Clear](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.objectcollection.clear.aspx) 等方法調整 [ComboBox.Items](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.items.aspx) 中的項目。
```c#
comboBox1.Items.Clear();
```

如果資料清單是透過資料繫結取得，那麼要變更清單中的內容，你只能重新調整資料來源的內容，再重新繫結給 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 。
```c#
//modify datalist
...
comboBox1.DataSource = null;
comboBox1.DataSource = datalist;
```

## 如何指定 ComboxBox 中的選取項目

要透過程式碼指定選取項目，可以用 [SelectedIndex](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.selectedindex.aspx) 屬性來指定。  不過一般狀況下我們大都是希望依下拉選單中的文字來指定，底下幾個屬性或方法都可以用來指定選取項目，但使用時機略不同。  

- [Text](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.text.aspx) ：取得或設定這個控制項的相關文字。
- [FindStringExact](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.findstringexact.aspx) ：尋找下拉式方塊中第一個完全符合指定字串的項目。
- [SelectedItem](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.selecteditem.aspx) ：取得或設定目前在 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 中選取的項目。
- [SelectedValue](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.selectedvalue.aspx) ：取得或設定 ValueMember 屬性指定的成員屬性值。
- [SelectedText](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.selectedtext.aspx) ：這個屬性的字面意思最容易被誤解，它的作用是指，取得或設定 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 編輯方塊中的選取文字，所以不管 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 裡頭的項目是何種型別，**這個屬性是完全無法用來設定選取項目的**。  而且，若你想利用某個 Button\_Click 事件來取得 [ComboBox.SelectedText](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.selectedtext.aspx) ，該值也會是空白，因為當使用者操作了 Button\_Click 通常這個 [ComboBox](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.combobox.aspx) 就 Lost\_Focus ，所以無法取得選取文字。
```c#
comboBox1.Items.Add("Student1");
comboBox1.Items.Add("Student2");
comboBox1.Items.Add("Student3");
comboBox1.Items.Add("Student4");

comboBox1.SelectedIndex = 0;
comboBox1.Text = "Student2";
comboBox1.SelectedItem = "Student3";
comboBox1.SelectedIndex = comboBox1.FindStringExact("Student4");
```
```c#
Student Student1 = new Student("AAA", "Student1", "1");
Student Student2 = new Student("BBB", "Student2", "2");
Student Student3 = new Student("CCC", "Student3", "3");
Student Student4 = new Student("DDD", "Student4", "4");

comboBox1.Items.Add(Student1);
comboBox1.Items.Add(Student2);
comboBox1.Items.Add(Student3);
comboBox1.Items.Add(Student4);

comboBox1.SelectedIndex = 0;
comboBox1.Text = "Student2";
comboBox1.SelectedItem = Student3;   //必須指定原本的物件才行。
comboBox1.SelectedIndex = comboBox1.FindStringExact("Student4");
```
```c#
List<Student> list = new List<Student>();
list.Add(Student1);
list.Add(Student2);
list.Add(Student3);
list.Add(Student4);

comboBox1.DataSource = list;
comboBox1.DisplayMember = "Name";
comboBox1.ValueMember = "Num";

comboBox1.SelectedIndex = 0;
comboBox1.Text = "Student2";
comboBox1.SelectedItem = Student3;
comboBox1.SelectedIndex = comboBox1.FindStringExact("Student4");
comboBox1.SelectedValue = "BBB";　// 若使用資料繫結，才可以使用 SelectedValue 指定選取項目。
```

## SelectionChangeCommitted V.S. SelectedIndexChanged

SelectionChangeCommitted 和 SelectedIndexChanged 都是下拉選單選項變動時會引發的事件，主要差別在於 SelectionChangeCommitted 事件只有在使用者變更選項時才會發生，若是透過程式變更選取項目則不會。   而 SelectedIndexChanged 事件則二個方式都會引發。  

我們常會在 SelectionChangeCommitted 事件中讀取 ComboBox.Text 屬性值，  不過，如果你的 ComboBox.DropDownStyle 屬性是設定成 ComBoxStyle.DropDown 的話，那麼此時讀到的 Text 值會是舊的，這是因為 Text 值還沒有變更。  你可以將  ComboBox.DropDownStyle 樣式變更成 ComBoxStyle.DropDownList 可以解決。  或者在該事件中透過 SelectedItem 屬性去讀取選取項目也可以，而不要透過 ComboBox.Text 屬性。