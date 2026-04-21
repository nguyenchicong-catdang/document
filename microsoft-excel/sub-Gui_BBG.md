Sub Gui_BBG()
'
' Macro2 Macro
'

' xóa dong

    Sheet2.Range("A16:A30").EntireRow.Delete
' copty bào don hang
    Selection.EntireRow.Copy
    Sheet2.Select
    Rows("16:16").Select
    ActiveSheet.Paste
    Sheet3.Rows("14:30").EntireRow.Hidden = False
    Sheet3.Rows("14:30").AutoFit
      
 ' an hang
  
  ' Set variables
    Dim c As Range
Application.ScreenUpdating = False
For Each c In Sheet3.Range("A14:A28")
    If c.Value = 0 Or c.Value = "" Then
        c.EntireRow.Hidden = True
    Else
        c.EntireRow.Hidden = False
    End If
Next c
Application.ScreenUpdating = True
    
 



    Sheet3.Select
End Sub
