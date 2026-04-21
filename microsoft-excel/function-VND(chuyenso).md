Function VND(chuyenso) As String
'Private Function GetCodeModule(ByVal codeModuleName As String) As String
'Private Function VND(chuyenso) As String
s09 = Array("", " M" & ChrW(7897) & "t", " hai", " ba", " b" & ChrW(7889) & "n", " n" & ChrW(259) & "m", " s" & ChrW(225) & "u", " b" & ChrW(7843) & "y", " t" & ChrW(225) & "m", " ch" & ChrW(237) & "n")
lop3 = Array("", " tri" & ChrW(7879) & "u", " ngh" & ChrW(236) & "n", " t" & ChrW(7927))
'Stop
If Trim(chuyenso) = "" Then
  VNDUni = ""
ElseIf IsNumeric(chuyenso) = True Then
  If chuyenso < 0 Then dau = ChrW(226) & "m " Else dau = ""
  chuyenso = Application.WorksheetFunction.Round(Abs(chuyenso), 0)
  chuyenso = " " & chuyenso
  chuyenso = Replace(chuyenso, ",", "", 1)
  vt = InStr(1, chuyenso, "E")
  If vt > 0 Then
    sonhan = Val(Mid(chuyenso, vt + 1))
    chuyenso = Trim(Mid(chuyenso, 2, vt - 2))
    chuyenso = chuyenso & String(sonhan - Len(chuyenso) + 1, "0")
  End If
  chuyenso = Trim(chuyenso)
  sochuso = Len(chuyenso) Mod 9
  If sochuso > 0 Then chuyenso = String(9 - (sochuso Mod 12), "0") & chuyenso
  VND = ""
  i = 1
  lop = 1
  Do
    n1 = Mid(chuyenso, i, 1)
    n2 = Mid(chuyenso, i + 1, 1)
    n3 = Mid(chuyenso, i + 2, 1)
    baso = Mid(chuyenso, i, 3)
    i = i + 3
    If n1 & n2 & n3 = "000" Then
      If VND <> "" And lop = 3 And Len(chuyenso) - i > 2 Then s123 = " t" & ChrW(7927) Else s123 = ""
    Else
      If n1 = 0 Then
        If VND = "" Then s1 = "" Else s1 = " kh" & ChrW(244) & "ng tr" & ChrW(259) & "m"
      Else
        s1 = s09(n1) & " tr" & ChrW(259) & "m"
      End If
      If n2 = 0 Then
        If s1 = "" Or n3 = 0 Then
          s2 = ""
        Else
          s2 = " linh"
        End If
      Else
        If n2 = 1 Then s2 = " M" & ChrW(432) & ChrW(7901) & "i" Else s2 = s09(n2) & " m" & ChrW(432) & ChrW(417) & "i"
      End If
      If n3 = 1 Then
        If n2 = 1 Or n2 = 0 Then s3 = " M" & ChrW(7897) & "t" Else s3 = " m" & ChrW(7889) & "t"
      ElseIf n3 = 5 And n2 <> 0 Then
        s3 = " l" & ChrW(259) & "m"
      Else
        s3 = s09(n3)
      End If
      If i > Len(chuyenso) Then
        s123 = s1 & s2 & s3
      Else
        s123 = s1 & s2 & s3 & lop3(lop)
      End If
    End If
    lop = lop + 1
    If lop > 3 Then lop = 1
    VND = VND & s123
    If i > Len(chuyenso) Then Exit Do
  Loop
  If VND = "" Then VND = "kh" & ChrW(244) & "ng" Else VND = dau & Trim(VND)
Else
  VND = chuyenso
End If
End Function
