Sub InHinh()
Dim rS As Range
Dim rD As Range
On Error Resume Next


Set rS = Range("I1:I100")
'Set rS = Application.InputBox("Vung chua link anh", Type:=8)

If Err.Number <> 0 Then Exit Sub

Set rD = Range("B1:B100")
'Set rD = Application.InputBox("Vung chua anh", Type:=8)

If Err.Number <> 0 Then Exit Sub
InsertPicture rS, rD, True

End Sub

Private Function AutoPicture(rPath As Range)
Dim ca  As Range
Application.Volatile
Set ca = Application.Caller

AutoPicture = InsertPicture(rPath, Application.Caller, False)
End Function


Private Sub ClearPicture(rrg As Range, isSubCall As Boolean)
Dim Ws As Worksheet
Dim pPics As Pictures
Dim pPic As Picture

On Error Resume Next
Set Ws = rrg.Worksheet


If isSubCall = True Then
    'xoa anh nam tren cell
    Set pPics = Ws.Pictures
    For Each pPic In pPics
        If Not (Application.Intersect(rrg, pPic.TopLeftCell) Is Nothing) Then
            If Not (Application.Intersect(rrg, pPic.BottomRightCell) Is Nothing) Then
                pPic.Delete
        
            End If
        End If
    Next
Else
    Dim rIndex As Range
    For Each rIndex In rrg
        Set pPic = Ws.Shapes(rIndex)
        pPic.Delete
    Next
    

End If

End Sub

 Private Function InsertPicture(rS As Range, rD As Range, Optional isSubCall As Boolean = True)

Dim lRows As Long
Dim lCols As Long
Dim lRow As Long
Dim lCol As Long
Dim rrg As Range
Dim Pic As Shape
Dim Ws As Worksheet
Set Ws = rD.Worksheet



lRows = rS.Rows.Count
lCols = rD.Columns.Count

If rS.Rows.Count <> rD.Rows.Count Or rS.Columns.Count <> rD.Columns.Count Then InsertPicture = CVErr(xlErrNA): Exit Function

On Error Resume Next
If isSubCall = True Then
    'If MsgBox("Xoa anh cu", vbYesNo) = vbYes Then
        ClearPicture rD, True 'xoa cac anh cu
    'End If

Else
    ClearPicture rD, False 'xoa anh voi tu cach ham
End If

Dim vKQ() As Variant
ReDim vKQ(1 To lRows, 1 To lCols) As Variant

For lRow = 1 To lRows
    For lCol = 1 To lCols
        Set rrg = rD(lRow, lCol)
        Err.Clear
        
       Set Pic = Ws.Shapes.AddPicture(rS(lRow, lCol), msoFalse, msoTrue, 1, 1, -1, -1)
      
      
        If Err.Number <> 0 Then
            vKQ(lRow, lCol) = CVErr(xlErrNA)
        Else
            vKQ(lRow, lCol) = Pic.NAME
            Pic.Placement = xlMoveAndSize
            ReSizeShape Pic, rrg
        End If
        
        
    Next
Next lRow

InsertPicture = vKQ

End Function

Private Sub ReSizeShape(a As Shape, rrg As Range)

Dim shr As Single
Dim swr As Single
Dim sha As Single
Dim swa As Single
Dim sTyLe As Single

a.LockAspectRatio = msoFalse
a.ScaleHeight 1, msoTrue, msoScaleFromMiddle
a.ScaleWidth 1, msoTrue, msoScaleFromMiddle

'shr = rrg.Height
'swr = rrg.Width

shr = rrg.MergeArea.Height
swr = rrg.MergeArea.Width



sha = a.Height
swa = a.Width
sTyLe = 10
If (shr / swr) >= (sha / swa) Then
    'a.Width = rrg.Width * (100 - sTyLe) / 100
    a.Width = swr * (100 - sTyLe) / 100
    
    a.Height = (a.Width * sha) / swa
   
Else
    'a.Height = rrg.Height * (100 - sTyLe) / 100
    a.Height = shr * (100 - sTyLe) / 100
    a.Width = (a.Height * swa) / sha
   
     
End If
'a.Left = rrg.Left + (rrg.Width - a.Width) / 2
'a.Top = rrg.Top + (rrg.Height - a.Height) / 2

a.Left = rrg.Left + (swr - a.Width) / 2
a.Top = rrg.Top + (shr - a.Height) / 2

a.LockAspectRatio = msoTrue
End Sub


