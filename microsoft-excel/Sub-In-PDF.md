Sub In_PDF()

'Create and assign variables
Dim saveLocation As String

wb_name = ActiveSheet.NAME
saveLocation = Sheet2.Range("B5") & "-" & wb_name

'Save Active Sheet(s) as PDF

ActiveSheet.ExportAsFixedFormat Type:=xlTypePDF, _
    Filename:=saveLocation

End Sub

