

Sub Sua_thong_tin_khach()

ActiveCell.EntireRow.Copy
    Sheet2.Select
    Rows("16:16").Select
    ActiveSheet.Paste
Sheet3.Select

End Sub

