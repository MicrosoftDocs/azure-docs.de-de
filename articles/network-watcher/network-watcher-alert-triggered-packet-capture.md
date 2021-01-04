---
title: Verwenden der Paketerfassung für die proaktive Netzwerküberwachung mit Warnungen – Azure Functions
titleSuffix: Azure Network Watcher
description: In diesem Artikel wird beschrieben, wie Sie eine per Warnung ausgelöste Paketerfassung mit Azure Network Watcher erstellen.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 3b6cb195f44bf6c868402481480d9b10802c4d59
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965669"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Verwenden der Paketerfassung für die proaktive Netzwerküberwachung mit Warnungen und Azure Functions

Mit der Network Watcher-Paketerfassung werden Erfassungssitzungen zum Nachverfolgen des ein- und ausgehenden Datenverkehrs von virtuellen Computern erstellt. Die Erfassungsdatei kann über einen Filter verfügen, der so definiert ist, dass nur der Datenverkehr nachverfolgt wird, den Sie überwachen möchten. Diese Daten werden dann in einem Speicherblob oder lokal auf dem Gastcomputer gespeichert.

Diese Funktion kann per Remotezugriff über Automatisierungsszenarien wie Azure Functions gestartet werden. Mit der Paketerfassung können Sie proaktive Erfassungen basierend auf definierten Netzwerkanomalien durchführen. Weitere Verwendungszwecke sind das Erfassen von Netzwerkstatistiken, das Gewinnen von Informationen zu Netzwerkangriffen, das Debuggen der Kommunikation zwischen Client und Server und andere.

In Azure bereitgestellte Ressourcen werden rund um die Uhr ausgeführt. Für Sie und Ihre Mitarbeiter ist es nicht möglich, den Status aller Ressourcen rund um die Uhr lückenlos aktiv zu überwachen. Was passiert beispielsweise, wenn nachts um 2 Uhr ein Problem auftritt?

Durch den Einsatz von Network Watcher, Warnungen und Funktionen im Azure-Ökosystem können Sie mit den entsprechenden Daten und Tools proaktiv reagieren, um Probleme im Netzwerk zu lösen.

![Diagramm der Network Watcher-Erweiterung auf einem virtuellen Computer, die an TCP-Segmente überträgt und mehr als 100 Fehler sendet, die dann an Azure Functions, von dort an Network Watcher und schließlich zurück zur Network Watcher-Erweiterung übertragen werden][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Die neueste Version von [Azure PowerShell](/powershell/azure/install-Az-ps)
* Eine vorhandene Instanz von Network Watcher. Wenn Sie noch keine haben, [erstellen Sie eine Instanz von Network Watcher](network-watcher-create.md).
* Ein virtueller Computer in der gleichen Region, in der sich auch die Network Watcher-Instanz befindet – mit der [Windows-Erweiterung](../virtual-machines/extensions/network-watcher-windows.md) oder mit der [Erweiterung für virtuelle Linux-Computer](../virtual-machines/extensions/network-watcher-linux.md)

## <a name="scenario"></a>Szenario

In diesem Beispiel werden von Ihrer VM ungewöhnlich viele TCP-Segmente gesendet, und Sie möchten dazu eine Warnung erhalten. Hier werden als Beispiel TCP-Segmente verwendet, Sie können jedoch eine beliebige Warnungsbedingung nutzen.

Bei einer Warnung möchten Sie Daten auf Paketebene erhalten, um die Erhöhung des Kommunikationsvolumens nachvollziehen zu können. Sie können dann Maßnahmen ergreifen, damit der Computer zur regulären Kommunikation zurückkehrt.

Bei diesem Szenario wird angenommen, dass Sie über eine vorhandene Instanz von Network Watcher und eine Ressourcengruppe mit einem gültigen virtuellen Computer verfügen.

Die folgende Liste enthält eine Übersicht über den Workflow:

1. Auf Ihrer VM wird eine Warnung ausgelöst.
1. Die Warnung ruft über einen Webhook Ihre Azure-Funktion auf.
1. Ihre Azure-Funktion verarbeitet die Warnung und startet eine Network Watcher-Paketerfassungssitzung.
1. Die Paketerfassung wird auf dem virtuellen Computer ausgeführt, und der Datenverkehr wird erfasst.
1. Die Paketerfassungsdatei wird zur Überprüfung und Diagnose in ein Speicherkonto hochgeladen.

Zur Automatisierung dieses Prozesses erstellen und verbinden wir eine Warnung auf unserem virtuellen Computer, die ausgelöst wird, wenn der Vorfall eintritt. Außerdem erstellen wir eine Funktion, die Network Watcher aufruft.

In diesem Szenario passiert Folgendes:

* Es wird eine Azure-Funktion erstellt, die eine Paketerfassung startet.
* Es wird eine Warnungsregel auf einem virtuellen Computer erstellt, und die Warnungsregel wird zum Aufrufen der Azure-Funktion konfiguriert.

## <a name="create-an-azure-function"></a>Erstellen einer Azure-Funktion

Im ersten Schritt wird eine Azure-Funktion zum Verarbeiten der Warnung und Erstellen einer Paketerfassung erstellt.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com)**Ressource erstellen** > **Compute** > **Funktions-App** aus.

    ![Erstellen einer Funktions-App][1-1]

2. Geben Sie auf dem Blatt **Funktionen-App** die folgenden Werte ein, und wählen Sie dann **OK** aus, um die App zu erstellen:

    |**Einstellung** | **Wert** | **Details** |
    |---|---|---|
    |**App-Name**|PacketCaptureExample|Der Name der Funktions-App|
    |**Abonnement**|[Ihr Abonnement] – das Abonnement, für das die Funktions-App erstellt werden soll||
    |**Ressourcengruppe**|PacketCaptureRG|Die Ressourcengruppe, in der sich die Funktions-App befinden soll|
    |**Hostingplan**|Verbrauchstarif| Die Art des Tarifs, der für Ihre Funktions-App verwendet wird. Zur Auswahl stehen „Verbrauchstarif“ und „Azure App Service-Tarif“. |
    |**Location**|USA (Mitte)| Die Region, in der Sie die Funktions-App erstellen möchten|
    |**Speicherkonto**|{automatisch generiert}| Das Speicherkonto, das von Azure Functions für allgemeine Speicherzwecke benötigt wird.|

3. Wählen Sie auf dem Blatt **PacketCaptureExample** der Funktionen-App die Option **Funktionen** > **Benutzerdefinierte Funktion** > **+** aus.

4. Wählen Sie **HttpTrigger Powershell** aus, und geben Sie dann die übrigen Informationen ein. Wählen Sie abschließend **Erstellen** aus, um die Funktion zu erstellen.

    |**Einstellung** | **Wert** | **Details** |
    |---|---|---|
    |**Szenario**|Experimentell|Art des Szenarios|
    |**Name Ihrer Funktion**|AlertPacketCapturePowerShell|Name der Funktion|
    |**Autorisierungsstufe**|Funktion|Autorisierungsebene für die Funktion|

![Funktionsbeispiel][functions1]

> [!NOTE]
> Die PowerShell-Vorlage ist experimentell und nicht in vollem Umfang unterstützt.

Für dieses Beispiel sind Anpassungen erforderlich, die in den folgenden Schritten erläutert werden.

### <a name="add-modules"></a>Hinzufügen von Modulen

Um die Network Watcher-PowerShell-Cmdlets zu verwenden, laden Sie das neueste PowerShell-Modul in die Funktions-App hoch.

1. Führen Sie auf dem lokalen Computer mit installierten aktuellsten Azure PowerShell-Modulen den folgenden PowerShell-Befehl aus:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    In diesem Beispiel wird der lokale Pfad Ihrer Azure PowerShell-Module angegeben. Diese Ordner werden in einem späteren Schritt verwendet. Die folgenden Module werden in diesem Szenario verwendet:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![PowerShell-Ordner][functions5]

1. Wählen Sie **Funktionen-App-Einstellungen** > **Zum App Service-Editor wechseln** aus.

    ![Einstellungen für Funktions-Apps][functions2]

1. Klicken Sie mit der rechten Maustaste auf den Ordner **AlertPacketCapturePowershell**, und erstellen Sie einen Ordner mit dem Namen **azuremodules**. 

4. Erstellen Sie einen Unterordner für jedes benötigte Modul.

    ![Ordner und Unterordner][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Klicken Sie mit der rechten Maustaste auf den Unterordner **Az.Network**, und wählen Sie **Dateien hochladen** aus. 

6. Wechseln Sie zu Ihren Azure-Modulen. Wählen Sie im lokalen Ordner **Az.Network** alle Dateien aus. Klicken Sie anschließend auf **OK**. 

7. Wiederholen Sie diese Schritte für **Az.Accounts** und **Az.Resources**.

    ![Hochladen von Dateien][functions6]

1. Nach Abschluss dieses Vorgangs sollte jeder Ordner die PowerShell-Moduldateien von Ihrem lokalen Computer enthalten.

    ![PowerShell-Dateien][functions7]

### <a name="authentication"></a>Authentication

Um die PowerShell-Cmdlets zu verwenden, müssen Sie sich authentifizieren. Sie konfigurieren die Authentifizierung in der Funktions-App. Zum Konfigurieren der Authentifizierung müssen Sie Umgebungsvariablen konfigurieren und eine verschlüsselte Schlüsseldatei in die Funktionen-App hochladen.

> [!NOTE]
> Dieses Szenario stellt lediglich ein Beispiel für das Implementieren einer Authentifizierung für Azure Functions dar. Es gibt auch andere Möglichkeiten, dieses Ziel zu erreichen.

#### <a name="encrypted-credentials"></a>Verschlüsselte Anmeldeinformationen

Das folgende PowerShell-Skript erstellt die Schlüsseldatei **PassEncryptKey.key**. Außerdem wird eine verschlüsselte Version des bereitgestellten Kennworts zur Verfügung gestellt. Dieses Kennwort ist das gleiche, das für die zum Authentifizieren verwendete Azure Active Directory-Anwendung definiert ist.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

Erstellen Sie im App Service-Editor der Funktions-App einen Ordner namens **keys** unter **AlertPacketCapturePowerShell**. Laden Sie dann die Datei **PassEncryptKey.key** hoch, die Sie im vorherigen PowerShell-Beispiel erstellt haben.

![Funktionsschlüssel][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Abrufen von Werten für Umgebungsvariablen

Die letzte Voraussetzung ist das Festlegen der Umgebungsvariablen, die für den Zugriff auf die Werte für die Authentifizierung erforderlich sind. In der folgenden Liste werden die erstellten Umgebungsvariablen aufgeführt:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Die Client-ID ist die Anwendungs-ID einer Anwendung in Azure Active Directory.

1. Wenn Sie noch nicht über eine zu verwendende Anwendung verfügen, führen Sie das folgende Beispiel aus, um eine Anwendung zu erstellen.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Das beim Erstellen der Anwendung verwendete Kennwort sollte das gleiche sein, das Sie zuvor beim Speichern der Schlüsseldatei erstellt haben.

1. Wählen Sie im Azure-Portal **Abonnements** aus. Wählen Sie das zu verwendende Abonnement und dann **Zugriffssteuerung (IAM)** aus.

    ![IAM für Funktionen][functions9]

1. Wählen Sie das zu verwendende Konto und dann **Eigenschaften** aus. Kopieren Sie die Anwendungs-ID.

    ![Funktionsanwendungs-ID][functions10]

#### <a name="azuretenant"></a>AzureTenant

Rufen Sie die Mandaten-ID durch Ausführen des folgenden PowerShell-Beispiels ab:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Der Wert der Umgebungsvariable AzureCredPassword ist der Wert, den Sie durch Ausführen des folgenden PowerShell-Beispiels erhalten. Dies ist das gleiche Beispiel, das im vorhergehenden Abschnitt **Verschlüsselte Anmeldeinformationen** vorgestellt wurde. Der erforderliche Wert ist die Ausgabe der Variable `$Encryptedpassword`.  Dies ist das Dienstprinzipalkennwort, das mithilfe des PowerShell-Skripts verschlüsselt wurde.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Speichern der Umgebungsvariablen

1. Navigieren Sie zur Funktions-App. Wählen Sie dann **Funktionen-App-Einstellungen** > **App-Einstellungen konfigurieren** aus.

    ![Konfigurieren von App-Einstellungen][functions11]

1. Fügen Sie den App-Einstellungen die Umgebungsvariablen und ihre Werte hinzu, und wählen Sie dann **Speichern** aus.

    ![App-Einstellungen][functions12]

### <a name="add-powershell-to-the-function"></a>Hinzufügen von PowerShell zur Funktion

Nun können Sie über die Azure-Funktion Aufrufe an Network Watcher senden. Die Implementierung dieser Funktion kann je nach den geltenden Anforderungen abweichen. Für den allgemeinen Code gilt aber folgender Ablauf:

1. Verarbeiten von Eingabeparametern
2. Abfragen von vorhandenen Paketerfassungen, um Grenzwerte zu überprüfen und Namenskonflikte zu lösen
3. Erstellen einer Paketerfassung mit den geeigneten Parametern
4. Regelmäßiges Abfragen der Paketerfassung bis zum Abschluss
5. Benachrichtigen des Benutzers über den Abschluss der Paketerfassungssitzung

Bei dem folgenden Beispiel handelt es sich um PowerShell-Code, der in der Funktion verwendet werden kann. Für **subscriptionId**, **resourceGroupName** und **storageAccountName** müssen einige Werte eingefügt werden.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Abrufen der Funktions-URL 
1. Konfigurieren Sie nach dem Erstellen der Funktion Ihre Warnung, um die der Funktion zugeordnete URL aufzurufen. Diesen Wert erhalten Sie, indem Sie die Funktions-URL aus Ihrer Funktionen-App kopieren.

    ![Ermitteln der Funktions-URL][functions13]

2. Kopieren Sie die Funktions-URL für Ihre Funktions-App.

    ![Kopieren der Funktions-URL][2]

Wenn Sie in der Nutzlast der Webhook-POST-Anforderung benutzerdefinierte Eigenschaften benötigen, helfen Ihnen die Informationen unter [Konfigurieren eines Webhooks für eine Azure-Metrikwarnung](../azure-monitor/platform/alerts-webhooks.md) weiter.

## <a name="configure-an-alert-on-a-vm"></a>Konfigurieren einer Warnung auf einem virtuellen Computer

Warnungen können konfiguriert werden, um Personen zu benachrichtigen, wenn eine bestimmte Metrik einen zugewiesenen Schwellenwert überschreitet. In diesem Beispiel wird die Warnung für die gesendeten TCP-Segmente ausgelöst, sie kann jedoch auch für viele andere Metriken ausgelöst werden. Hier wird eine Warnung konfiguriert, um einen Webhook zum Aufrufen der Funktion aufzurufen.

### <a name="create-the-alert-rule"></a>Erstellen der Warnungsregel

Navigieren Sie zu einem vorhandenen virtuellen Computer, und fügen Sie eine Warnungsregel hinzu. Ausführlichere Informationen zum Konfigurieren von Warnungen finden Sie unter [Erstellen von Metrikwarnungen in Azure Monitor für Azure-Dienste – Azure-Portal](../azure-monitor/platform/alerts-classic-portal.md). Geben Sie auf dem Blatt **Warnungsregel** die folgenden Werte ein, und wählen Sie dann **OK** aus.

  |**Einstellung** | **Wert** | **Details** |
  |---|---|---|
  |**Name**|TCP_Segments_Sent_Exceeded|Der Name der Warnungsregel.|
  |**Beschreibung**|Schwellenwertüberschreitung durch gesendete TCP-Segmente|Die Beschreibung für die Warnungsregel.|
  |**Metrik**|Gesendete TCP-Segmente| Die Metrik zur Auslösung der Warnung. |
  |**Condition**|Größer als| Die Bedingung, die bei der Auswertung der Metrik verwendet werden soll.|
  |**Schwellenwert**|100| Der Wert der Metrik, die die Warnung auslöst. Dieser Wert sollte auf einen gültigen Wert für Ihre Umgebung festgelegt werden.|
  |**Zeitraum**|In den letzten fünf Minuten| Bestimmt den Zeitraum, in dem der Schwellenwert für die Metrik geprüft werden soll.|
  |**Webhook**|[Webhook-URL aus der Funktions-App]| Die Webhook-URL aus der Funktions-App, die in den vorherigen Schritten erstellt wurde.|

> [!NOTE]
> Die Metrik der TCP-Segmente ist in der Standardeinstellung nicht aktiviert. Weitere Informationen dazu, wie Sie zusätzliche Metriken aktivieren, finden Sie unter [Aktivieren von Überwachung und Diagnose](../azure-monitor/overview.md).

## <a name="review-the-results"></a>Überprüfen der Ergebnisse

Wenn die Kriterien für die Warnung erfüllt sind, wird eine Paketerfassung erstellt. Wechseln Sie zu Network Watcher, und wählen Sie **Paketerfassung** aus. Auf dieser Seite können Sie den Link für die Paketerfassungsdatei auswählen, um die Paketerfassung herunterzuladen.

![Anzeigen der Paketerfassung][functions14]

Wenn die Erfassungsdatei lokal gespeichert ist, können Sie sie abrufen, indem Sie sich auf dem virtuellen Computer anmelden.

Eine Anleitung zum Herunterladen von Dateien aus Azure Storage-Konten finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Sie können auch den [Storage-Explorer](https://storageexplorer.com/) verwenden.

Nach dem Herunterladen Ihrer Erfassung können Sie diese mit einem beliebigen Tool anzeigen, das für Dateien vom Typ **.cap** geeignet ist. Hier finden Sie zwei Links zu geeigneten Tools:

- [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Anzeigen der Paketerfassungen finden Sie unter [Paketuntersuchung mit Azure Network Watcher](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png