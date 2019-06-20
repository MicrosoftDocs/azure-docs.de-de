---
title: Selbsttestclient zur Vorabüberprüfung eines virtuellen Computers | Azure Marketplace
description: Erfahren Sie, wie Sie einen Selbsttestclient zur Vorabüberprüfung eines VM-Images für den Azure Marketplace erstellen.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pabutler
ms.openlocfilehash: 117249feea04381b34f8fc1d95f77c2c1a567dba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938717"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Erstellen eines Selbsttestclients zur Vorabüberprüfung eines Azure-VM-Images

Verwenden Sie diesen Artikel als Anleitung zum Erstellen eines Clientdiensts, der die Selbsttest-API verwendet. Sie können die Selbsttest-API zur Vorabüberprüfung eines virtuellen Computers (VM) verwenden, um sicherzustellen, dass er den aktuellen Veröffentlichungsvoraussetzungen von Azure Marketplace entspricht. Dieser Clientdienst ermöglicht Ihnen, einen virtuellen Computer zu testen, bevor Sie Ihr Angebot zur Zertifizierung an Microsoft senden.

## <a name="development-and-testing-overview"></a>Entwicklungs- und Testübersicht

Im Rahmen des Selbsttests erstellen Sie einen lokalen Client, der eine Verbindung mit Azure Marketplace herstellt, um einen virtuellen Computer zu überprüfen, der in Ihrem Azure-Abonnement ausgeführt wird. Der virtuelle Computer kann unter dem Betriebssystem Windows oder Linux ausgeführt werden.

Der lokale Client führt ein Skript aus, das sich mit der Selbsttest-API authentifiziert, Verbindungsinformationen sendet und die Testergebnisse empfängt.

Die grundlegenden Schritte zum Erstellen eines Selbsttestclients sind:

1. Auswählen des Azure Active Directory-Mandanten (AD) für Ihre Anwendung.
2. Registrieren der Client-App.
3. Erstellen eines Tokens für die Client-Azure AD-App.
4. Übergeben des Tokens an die Selbsttest-API.

Nachdem Sie den Client erstellt haben, können Sie ihn auf Ihrem virtuellen Computer testen.

### <a name="self-test-client-authorization"></a>Autorisierung des Selbsttestclients

Das folgende Diagramm veranschaulicht die Funktionsweise der Autorisierung für die Dienst-zu-Dienst-Aufrufe mit Clientanmeldeinformationen (freigegebenes Geheimnis oder Zertifikat).

![Prozess der Clientautorisierung](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Die Selbsttestclient-API

Die Selbsttest-API enthält einen einzelnen Endpunkt, der nur die POST-Methode unterstützt.  Sie hat die folgende Struktur.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

In der folgenden Tabelle werden die API-Felder beschrieben.


|      Feld         |    BESCHREIBUNG    |
|  ---------------   |  ---------------  |
|  Autorisierung     |  Die Zeichenfolge „Bearer xxxx-xxxx-xxxx-xxxxx“ enthält das Clienttoken von Azure Active Directory (AD), das mithilfe von PowerShell erstellt werden kann.          |
|  DNSName           |  DNS-Name des zu testenden virtuellen Computers    |
|  Benutzer              |  Benutzername für die Anmeldung bei dem virtuellen Computer         |
|  Kennwort          |  Kennwort für die Anmeldung bei dem virtuellen Computer          |
|  Betriebssystem                |  Betriebssystem des virtuellen Computers: entweder `Linux` oder `Windows`          |
|  PortNo            |  Offene Portnummer für das Herstellen der Verbindung mit dem virtuellen Computer. Die Portnummer ist in der Regel `22` für Linux und `5986` für Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Nutzung der API

Sie können die Selbsttest-API mithilfe von PowerShell oder cURL nutzen.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Verwenden von PowerShell zum Nutzen der API auf dem Linux-Betriebssystem

Führen Sie zum Aufrufen der API in PowerShell die folgenden Schritte aus:

1. Verwenden Sie den `Invoke-WebRequest`-Befehl, um die API aufzurufen.
2. Die Methode ist Post und der Inhaltstyp JSON, wie im folgenden Codebeispiel und der Bildschirmaufnahme dargestellt.
3. Geben Sie die Textparameter im JSON-Format an.

Das folgende Codebeispiel zeigt einen PowerShell-Aufruf an die API.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```
Die folgende Bildschirmaufnahme zeigt ein Beispiel für das Aufrufen der API in PowerShell.

![Aufrufen der API mit PowerShell für das Linux-Betriebssystem](./media/stclient-call-api-ps-linuxvm.png)

Bei Verwendung des vorherigen Beispiels können Sie den JSON-Code abrufen und analysieren, um die folgenden Details zu erhalten:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

Die folgende Bildschirmaufnahme, die `$res.Content` zeigt, bietet Ihnen die Details Ihrer Testergebnisse im JSON-Format.

![JSON-Ergebnisse des PowerShell-Aufrufs von Linux](./media/stclient-pslinux-rescontent-json.png)

Die folgende Bildschirmaufnahme zeigt ein Beispiel für JSON-Testergebnisse, die in einem Online-JSON-Viewer angezeigt werden (z.B. [Code Beautify](https://codebeautify.org/jsonviewer) oder [JSON-Viewer](https://jsonformatter.org/json-viewer)).

![JSON-Ergebnisse des PowerShell-Aufrufs der Linux-VM](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Verwenden von PowerShell zum Nutzen der API auf dem Windows-Betriebssystem

Führen Sie zum Aufrufen der API in PowerShell die folgenden Schritte aus:

1. Verwenden Sie den `Invoke-WebRequest`-Befehl, um die API aufzurufen.
2. Die Methode ist Post und der Inhaltstyp JSON, wie im folgenden Codebeispiel und der Bildschirmaufnahme dargestellt.
3. Erstellen Sie die Body-Parameter im JSON-Format.

Das folgende Codebeispiel zeigt einen PowerShell-Aufruf an die API.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Die folgende Bildschirmaufnahme zeigt ein Beispiel für das Aufrufen der API in PowerShell.

![Aufrufen der API mit PowerShell für das Windows-Betriebssystem](./media/stclient-call-api-ps-windowsvm.png)

Bei Verwendung des vorherigen Beispiels können Sie den JSON-Code abrufen und analysieren, um die folgenden Details zu erhalten:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

Die folgende Bildschirmaufnahme, die `$res.Content` zeigt, bietet Ihnen die Details Ihrer Testergebnisse im JSON-Format.

![JSON-Ergebnisse des PowerShell-Aufrufs von Windows](./media/stclient-pswindows-rescontent-json.png)

Die folgende Bildschirmaufnahme zeigt Testergebnisse, die in einem Online-JSON-Viewer angezeigt werden.
(z.B. [Code Beautify](https://codebeautify.org/jsonviewer), [JSON-Viewer](https://jsonformatter.org/json-viewer))

![JSON-Ergebnisse des PowerShell-Aufrufs der Windows-VM](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Verwenden von cURL zum Nutzen der API auf dem Linux-Betriebssystem

Führen Sie zum Aufrufen der API mit cURL die folgenden Schritte aus:

1. Verwenden Sie den curl-Befehl, um die API aufzurufen.
2. Die Methode ist Post und der Inhaltstyp JSON, wie im folgenden Codeausschnitt dargestellt.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

Der folgende Bildschirm zeigt ein Beispiel der Verwendung von Curl zum Aufrufen der API.

![Aufrufen der API mithilfe des curl-Befehls](./media/stclient-consume-api-curl.png)

Die folgende Bildschirmaufnahme zeigt die JSON-Ergebnisse des curl-Aufrufs.

![JSON-Ergebnisse des curl-Aufrufs](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Auswählen des Azure AD-Mandanten für die App

Wählen Sie in den folgenden Schritten den Azure AD-Mandanten aus, bei dem Sie Ihre Anwendung erstellen möchten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie in der oberen Menüleiste Ihr Konto aus, und wählen Sie in der Liste „Verzeichnis“ den Active Directory-Mandanten aus, bei dem Sie Ihre Anwendung registrieren möchten. Wählen Sie alternativ das Symbol **Verzeichnis + Abonnement** aus, um den globalen Abonnementfilter anzuzeigen. Die folgende Bildschirmaufnahme zeigt ein Beispiel dieses Filters.

   ![Auswählen des Abonnementfilters](./media/stclient-subscription-filter.png)

3. Wählen Sie in der linken Navigationsleiste **Alle Dienste** aus, und wählen Sie anschließend **Azure Active Directory** aus.

   In den folgenden Schritten benötigen Sie möglicherweise den Mandantennamen (bzw. den Namen des Verzeichnisses) oder die Mandanten-ID (bzw. Verzeichnis-ID).

   **So erhalten Sie die Informationen über den Mandanten:**

   Suchen Sie in **Azure Active Directory-Überblick** nach „Eigenschaften“, und wählen Sie dann **Eigenschaften** aus. Verwenden Sie die folgende Bildschirmaufnahme als Beispiel:

   - **Name**: Der Name des Mandanten oder Verzeichnisname
   - **Verzeichnis-ID**: Die Mandanten- oder Verzeichnis-ID, oder suchen Sie „Eigenschaften“ mit der Bildlaufleiste.

   ![Eigenschaftenseite von Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registrieren der Client-App

Führen Sie zum Registrieren der Client-App die folgenden Schritte aus.

1. Wählen Sie in der linken Navigationsleiste **Alle Dienste** aus, und wählen Sie anschließend **App-Registrierungen** aus.
2. Wählen Sie unter **App-Registrierungen** die Option **+ Registrierung einer neuen Anwendung** aus.
3. Geben Sie unter **Erstellen** die erforderlichen Informationen für die folgenden Felder ein:

   - **Name**: Geben Sie einen Anzeigenamen für die App ein. Beispiel: „SelfTestClient“.
   - **Anwendungstyp**: Wählen Sie **Web-App/API** aus.
   - **Anmelde-URL**: Geben Sie „https:\//isvapp.azurewebsites.net/selftest-vm“ ein.

4. Klicken Sie auf **Erstellen**.
5. Kopieren Sie unter **App-Registrierungen** oder **Registrierte App** die **Anwendungs-ID**.

   ![Abrufen der Anwendungs-ID](./media/stclient-app-id.png)

6. Wählen Sie in der Symbolleiste der registrierten App **Einstellungen** aus.
7. Wählen Sie **Erforderliche Berechtigungen** aus, um Berechtigungen für die App zu konfigurieren.
8. Wählen Sie unter **Erforderliche Berechtigungen** die Option **+ Hinzufügen** aus.
9. Wählen Sie unter **API-Zugriff hinzufügen** die Option **API auswählen** aus.
10. Geben Sie unter **API auswählen** „Klassisches Windows Azure-Bereitstellungsmodell“ ein, um die API zu suchen.
11. Wählen Sie in den Suchergebnissen **Klassisches Windows Azure-Bereitstellungsmodell** aus, und klicken Sie dann auf **Auswählen**.

    ![Konfigurieren der Mehrinstanzenfähigkeit für die App](./media/stclient-select-api.png)

12. Wählen Sie unter **API-Zugriff hinzufügen** die Option **Berechtigungen auswählen** aus.
13. Wählen Sie **Auf „Windows Azure-Dienstverwaltungs-API“ zugreifen** aus.

    ![Aktivieren des API-Zugriffs für die App](./media/stclient-enable-api-access.png)

14. Klicken Sie auf **Auswählen**.
15. Wählen Sie **Fertig**aus.
16. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.
17. Scrollen Sie unter **Eigenschaften** zu **Mehrinstanzenfähig**. Wählen Sie **Ja** aus.

    ![Konfigurieren der Mehrinstanzenfähigkeit für die App](./media/stclient-yes-multitenant.png)

18. Wählen Sie **Speichern** aus.
19. Wählen Sie unter **Einstellungen** die Option **Schlüssel** aus.
20. Erstellen Sie einen geheimen Schlüssel durch Auswahl des Textfelds **BESSCHREIBUNG**. Konfigurieren Sie die folgenden Felder:

    - Geben Sie einen Schlüsselnamen ein. Beispiel: „selftestclient“.
    - Wählen Sie in der Dropdownliste **LÄUFT AB** „In 1 Jahr“ aus.
    - Wählen Sie **Speichern** aus, um den Schlüssel zu generieren.
    - Kopieren Sie den Schlüssel unter **WERT**.

      >[!Important]
      >Der Schlüsselwert wird nicht mehr angezeigt, nachdem Sie das Formular **Schlüssel** verlassen haben.

    ![Schlüsselwertformular](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Erstellen des Tokens für die Client-App

Sie können eines der folgenden Programme verwenden, um ein Token zu erstellen und über die OAuth-REST-API abzurufen:

- postman
- cURL in Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Erstellen und Abrufen eines Tokens mit Postman

 Um Auth0 für alle Ihre autorisierten Anwendungen nach Token abzufragen, führen Sie einen POST-Vorgang zum [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token)-Endpunkt mit einer Nutzlast im folgenden Format durch:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Übergeben Sie die folgenden Parameter im Anforderungstext:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Übergeben Sie die folgenden Parameter im Anforderungsheader:

```
Content-Type: application/x-www-form-urlencoded
```

Die folgende Bildschirmaufnahme zeigt ein Beispiel der Verwendung von Postman zum Abrufen eines Tokens.

![Abrufen eines Tokens mit Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Erstellen und Abrufen eines Tokens mit cURL in Linux

Um Auth0 für beliebige Ihrer autorisierten Anwendungen nach Token abzufragen, führen Sie einen POST-Vorgang zum [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token)-Endpunkt mit einer Nutzlast im folgenden Format durch:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Die folgende Bildschirmaufnahme zeigt ein Beispiel der Verwendung des curl-Befehls zum Abrufen eines Tokens.

![Abrufen eines Tokens mit dem curl-Befehl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Erstellen und Abrufen eines Tokens mit C&#35;

Um Auth0 für beliebige Ihrer autorisierten Anwendungen nach Token abzufragen, führen Sie einen POST-Vorgang zum „https:\//soamtenant.auth0.com/oauth/token“-Endpunkt mit einer Nutzlast im folgenden Format durch:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Erstellen und Abrufen eines Tokens mit PowerShell

Um Auth0 für beliebige Ihrer autorisierten Anwendungen nach Token abzufragen, führen Sie einen POST-Vorgang zum „https:\//soamtenant.auth0.com/oauth/token“-Endpunkt mit einer Nutzlast im folgenden Format durch:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>Übergeben des Client-App-Tokens an die API

Übergeben Sie das Token mit dem folgenden Code im Autorisierungsheader an die Selbsttest-API:

```powershell
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Testen Ihres Selbsttestclients

Um den Client zu testen, gehen Sie folgendermaßen vor:

1. Stellen Sie den virtuellen Computer bereit, den Sie testen möchten.
2. Rufen Sie die Selbsttest-API mit Ihrem Client-App-Token zur Autorisierung auf.
3. Rufen Sie die Testergebnisse im JSON-Format ab.

### <a name="test-result-examples"></a>Beispiele für Testergebnisse

Die folgenden Codeausschnitte zeigen die Testergebnisse im JSON-Format an.

**Testergebnisse für eine Windows-VM:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Testergebnisse für eine Linux-VM:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Azure-VM erfolgreich getestet haben, können Sie [das Angebot veröffentlichen](./cpp-publish-offer.md).
