---
title: VBA两列去重并提取不重复数据到新列
date: 2019-04-28 15:03:49
tags:
- vba
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

```vb
Sub CheckDiff()
    Dim r%, i%
    Dim arr, brr
    Dim d As Object
    Set d = CreateObject("scripting.dictionary")
    With Worksheets("sheetName")
        r = .Cells(.Rows.Count, 1).End(xlUp).Row
        arr = .Range("a2:a" & r)
        For i = 1 To UBound(arr)
          d(arr(i, 1)) = ""
        Next
        r = .Cells(.Rows.Count, 2).End(xlUp).Row
        arr = .Range("b2:b" & r)
        ReDim brr(1 To UBound(arr), 1 To 1)
        m = 0
        For i = 1 To UBound(arr)
          If Not d.exists(arr(i, 1)) Then
            m = m + 1
            brr(m, 1) = arr(i, 1)
          End If
        Next
        .Range("c2").Resize(m, 1) = brr
    End With
End Sub
```

参考：http://club.excelhome.net/thread-1383635-1-1.html
