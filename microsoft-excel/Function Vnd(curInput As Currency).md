Option Explicit

'---
' @File name: F_Vnd
' Function: vnd(curInput)
' Muc dich: chuyen so thanh chu

' Text: = vnd(12345)
'   @param curInput As Currency -> khai bao gia tri lon 100 nghin ty

'---


Public Function Vnd(curInput As Currency) As String
    ' chuyen string de xu ly chuoi
    Dim strInput As String
    strInput = CStr(curInput)
    'MsgBox strInput
    
    'khai bao ket qua
    Dim strKQ As String
    'strKQ = ""
    'lap qua mang lay chuoi 3 so
    Dim strBlok3 As String
    'strBlok3 = ""
    ' kai bao cac ky tu don vi
    'MsgBox strInput
    Dim dv(0 To 5) As String
    
    dv(0) = ""
    ' nghìn
    dv(1) = ChrW(110) & ChrW(103) & ChrW(104) & ChrW(236) & ChrW(110)
    ' triêju
    dv(2) = ChrW(116) & ChrW(114) & ChrW(105) & ChrW(7879) & ChrW(117)
    ' tyr
    dv(3) = ChrW(116) & ChrW(7927)
    ' nghìn tyr
    dv(4) = ChrW(110) & ChrW(103) & ChrW(104) & ChrW(236) & ChrW(110) _
            & ChrW(32) _
            & ChrW(116) & ChrW(7927)
    
    ' ddoofng khoang trang ChrW(32)
    Dim dong As String
    dong = ChrW(32) & ChrW(273) & ChrW(7891) & ChrW(110) & ChrW(103)
    
    ' khai bao i de lay mang
    Dim i As Long
    i = 0
    
    ' xu ly dau ,
    Dim strDauphay As String
    'strDauPhay = ","
    Dim arrDauphay(0 To 9) As Boolean
    arrDauphay(i) = False
    
    ' lap het chuoi
    Do While Len(strInput) > 0
        ' lay 3 ky tu ben trai input
        strBlok3 = Right(strInput, 3)
        'strKQ = strBlok3 & dv(i) & strKQ
        
        ' stoop loop
        ' lay chuoi lap tu trai qua - 3
        strInput = Left(strInput, Len(strInput) - 3)
        ' lay chuoi cuoi cung neu khong du 3 ky tu
        If Len(strInput) > 0 And Len(strInput) < 3 Then
            'MsgBox strInput
            ' strInput < 3 - them 0 cho du len 3
            ' strInput > 0 - de khong lap vo han
            strInput = Right("000" & strInput, 3)
        End If
        
        ' don vi
        'MsgBox dv(i)
        'MsgBox strBlok3
        
        ' kq
        'strKQ = dv(i) & strKQ
        'MsgBox dv(i)
        
        
        
        ' chuoi ket qua
        'strKQ = strBlok3 & " " & dv(i) & strKQ
        'strKQ = strBlok3 & " " & dv(i) & Trim(strKQ)
        'strKQ = strBlok3 & " " & dv(i) & "," & Trim(strKQ)
        
        ' xu ly dau ,
        arrDauphay(i + 1) = True
        If arrDauphay(i) = False And strBlok3 = "000" Then
            arrDauphay(i + 1) = False
        End If
        
        If arrDauphay(i) Then
            strDauphay = ","
        End If
        
        'test KQ
        'strDauPhay = ","

        'xu lý strBlok3 = "000"
        If strBlok3 = "000" Then
            strBlok3 = ""
            dv(i) = ""
            strDauphay = ""
            
        End If
        
        strKQ = strBlok3 & " " & dv(i) & strDauphay & Trim(strKQ)
        'MsgBox strBlok3
        'them i neu lap
        i = i + 1
    Loop
    'vnd = Len(strKQ)
    Vnd = Len(Trim(strKQ))
    'MsgBox strKQ
    'vnd = "run"
End Function



'---
' @File name: F_Vnd
' Function: docbaso(lngInput)
' Muc dich: doc 3 so thanh chu
'---

Public Function docbaso(lngInput) As String
    ' xu ly dau vao
    Dim strInput As String
    strInput = Right("000" & CStr(lngInput), 3)
    
    ' khai bao bien doc chu
    Dim s(0 To 9) As String
    s(0) = ""
    'moojt
    s(1) = ChrW(109) & ChrW(7897) & ChrW(116)
    'hai
    s(2) = ChrW(104) & ChrW(97) & ChrW(105)
    'ba
    s(3) = ChrW(98) & ChrW(97)
    'boosn
    s(4) = ChrW(98) & ChrW(7889) & ChrW(110)
    'nawm
    s(5) = ChrW(110) & ChrW(259) & ChrW(109)
    'sasu
    s(6) = ChrW(115) & ChrW(225) & ChrW(117)
    'bary
    s(7) = ChrW(98) & ChrW(7843) & ChrW(121)
    'tasm
    s(8) = ChrW(116) & ChrW(225) & ChrW(109)
    'chisn
    s(9) = ChrW(99) & ChrW(104) & ChrW(237) & ChrW(110)
    
    
    Dim dv(0 To 3) As String
    ' hang tram - trawm
    dv(1) = ChrW(116) & ChrW(114) & ChrW(259) & ChrW(109)
    ' hang chuc - muowi
    dv(2) = ChrW(109) & ChrW(432) & ChrW(417) & ChrW(105)
    
    ' xu ly cach doc
    Dim db(0 To 9) As String
    ' khoong trawm
    db(0) = ChrW(107) & ChrW(104) & ChrW(244) & ChrW(110) & ChrW(103) & _
            ChrW(32) & _
            ChrW(116) & ChrW(114) & ChrW(259) & ChrW(109)
    ' hang chuc bang 1 - muowfi
    db(1) = ChrW(109) & ChrW(432) & ChrW(7901) & ChrW(105)
    ' hang chuc bang 0 - ler
    db(3) = ChrW(108) & ChrW(7867)
    ' hang don vi bang 1 - hang chuc > 1 - môst
    db(4) = ChrW(109) & ChrW(7889) & ChrW(116)
    ' hang don vi bang 5 - hang chuc > 0 - lawm
    db(5) = ChrW(108) & ChrW(259) & ChrW(109)
    
    ' xu ly
    'MsgBox strInput
    ' khai bao vong lap
    Dim sodoc As String
    Dim donvi As String
    Dim docso As String
    Dim strKQ As String
    ' vong lap
    Dim i As Long
    
    
    For i = 1 To 3
        sodoc = Mid(strInput, i, 1)
        donvi = dv(i)
        docso = s(CLng(sodoc)) & " " & donvi
        ' xu lý 0 tram
        If i = 1 And sodoc = "0" Then
            'MsgBox 1
            docso = db(0)
            'MsgBox db(0)
        End If
        
        ' xu lý hang chuc
        'hang chuc = 1 -> muowfi
        
        'docso = s(CLng(sodoc)) & " " & donvi
        
        strKQ = Trim(strKQ) & " " & Trim(docso)
    Next i
    docbaso = strKQ
    'docbaso = "run"
End Function
