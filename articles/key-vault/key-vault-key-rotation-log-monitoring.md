---
title: Einrichten von Azure Key Vault mit End-to-End-Schlüsselrotation und Überwachung | Microsoft Docs
description: Diese Anleitung bietet Informationen zum Einrichten der Schlüsselrotation und Überwachen von Schlüsseltresorprotokollen.
services: key-vault
author: barclayn
manager: barbkess
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 20a170963ff4a8ff9cb69d3397e66e12c1047d16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65561184"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Einrichten von Azure Key Vault mit Schlüsselrotation und Überwachung

## <a name="introduction"></a>Einführung

Nach dem Erstellen eines Schlüsseltresors können Sie Schlüssel und Geheimnisse in diesem Tresor speichern. Ihre Schlüssel und Geheimnisse müssen in Ihren Anwendungen nicht mehr dauerhaft gespeichert werden, sondern können bei Bedarf aus dem Tresor angefordert werden. Mit einem Schlüsseltresor können Sie Schlüssel und Geheimnisse ohne Auswirkungen auf das Verhalten Ihrer Anwendung aktualisieren, was eine Vielzahl von Möglichkeiten für die Verwaltung von Schlüsseln und Geheimnissen eröffnet.

>[!IMPORTANT]
> Die Beispiele in diesem Artikel werden lediglich zur Veranschaulichung bereitgestellt. Sie sind nicht für Produktionszwecke vorgesehen. 

In diesem Artikel wird Folgendes beschrieben:

- Ein Beispiel für die Verwendung von Azure Key Vault zum Speichern eines Geheimnisses. In diesem Artikel handelt es sich bei dem gespeicherten Geheimnis um den Azure Storage-Kontoschlüssel, auf den eine Anwendung zugreift. 
- Sie lernen die Implementierung einer geplanten Rotation dieses Speicherkontoschlüssels kennen.
- Sie lernen die Überwachung der Überwachungsprotokolle des Schlüsseltresors kennen, und wie bei unerwarteten Anforderungen Warnungen ausgelöst werden.

> [!NOTE]
> In diesem Artikel werden nicht die Einzelheiten der erstmaligen Einrichtung Ihres Schlüsseltresors behandelt. Die entsprechenden Informationen finden Sie unter [Was ist Azure Key Vault?](key-vault-overview.md). Anleitungen für die plattformübergreifende Befehlszeilenschnittstelle finden Sie unter [Verwalten von Key Vault mit der Azure CLI](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Einrichten von Key Vault

Damit eine Anwendung einen geheimen Schlüssel aus Key Vault abrufen kann, müssen Sie zuerst den geheimen Schlüssel erstellen und ihn in den Tresor hochladen.

Starten Sie eine Azure PowerShell-Sitzung, und melden Sie sich mit dem folgenden Befehl bei Ihrem Azure-Konto an:

```powershell
Connect-AzAccount
```

Geben Sie im Popup-Browserfenster den Benutzernamen und das Kennwort für Ihr Azure-Konto ein. PowerShell ruft alle Abonnements ab, die diesem Konto zugeordnet sind. PowerShell verwendet standardmäßig das erste Abonnement.

Wenn Sie über mehrere Abonnements verfügen, müssen Sie unter Umständen das Abonnement angeben, das zum Erstellen des Schlüsseltresors verwendet wurde. Geben Sie Folgendes ein, um die Abonnements für Ihr Konto anzuzeigen:

```powershell
Get-AzSubscription
```

Geben Sie Folgendes ein, um das Abonnement anzugeben, das dem zu protokollierenden Schlüsseltresor zugeordnet ist:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Da in diesem Artikel das Speichern eines Speicherkontoschlüssels als geheimer Schlüssel demonstriert wird, müssen Sie diesen Speicherkontoschlüssel abrufen.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Nachdem Sie Ihr Geheimnis (in diesem Fall Ihren Speicherkontoschlüssel) abgerufen haben, müssen Sie diesen Schlüssel in eine sichere Zeichenfolge konvertieren und anschließend ein Geheimnis mit diesem Wert in Ihrem Schlüsseltresor erstellen.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Rufen Sie als Nächstes den URI für den erstellten geheimen Schlüssel ab. Sie benötigen diesen URI in einem späteren Schritt zum Aufrufen des Schlüsseltresors und Abrufen Ihres Geheimnisses. Führen Sie den folgenden PowerShell-Befehl aus, und notieren Sie den Wert „ID“, bei dem es sich um den URI des Geheimnisses handelt:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Einrichten der Anwendung

Da Sie nun über ein gespeichertes Geheimnis verfügen, können Sie dieses mithilfe von Code abrufen und verwenden, nachdem Sie ein paar weitere Schritte ausgeführt haben.

Zunächst müssen Sie Ihre Anwendung bei Azure Active Directory registrieren. Teilen Sie Key Vault dann Informationen zu Ihrer Anwendung mit, damit Anforderungen Ihrer Anwendung zugelassen werden können.

> [!NOTE]
> Ihre Anwendung muss mit demselben Azure Active Directory-Mandanten wie Ihr Schlüsseltresor erstellt werden.

1. Öffnen Sie **Azure Active Directory**.
2. Wählen Sie **App-Registrierungen** aus. 
3. Wählen Sie **Registrierung einer neuen Anwendung** aus, um Ihrer Azure Active Directory-Instanz eine Anwendung hinzuzufügen.

    ![„Anwendungen“ in Azure Active Directory öffnen](./media/keyvault-keyrotation/azure-ad-application.png)

4. Belassen Sie unter **Erstellen** den Anwendungstyp als **Web-App/API**, und versehen Sie Ihre Anwendung mit einem Namen. Geben Sie für die Anwendung eine **Anmelde-URL** ein. Diese URL ist für diese Demo beliebig.

    ![Erstellen der Anwendungsregistrierung](./media/keyvault-keyrotation/create-app.png)

5. Nachdem Azure Active Directory die Anwendung hinzugefügt wurde, wird die Anwendungsseite geöffnet. Wählen Sie **Einstellungen** und dann **Eigenschaften** aus. Kopieren Sie den Wert für **Anwendungs-ID**. Sie benötigen sie in einem späteren Schritt.

Generieren Sie dann einen Schlüssel für die Anwendung, damit diese mit Azure Active Directory interagieren kann. Wählen Sie zum Erstellen eines Schlüssels **Schlüssel** unter **Einstellungen** aus. Notieren Sie den neu generierten Schlüssel für Ihre Azure Active Directory-Anwendung. Sie benötigen sie in einem späteren Schritt. Der Schlüssel wird nicht verfügbar sein, wenn Sie diesen Abschnitt verlassen haben. 

![Azure Active Directory-App-Schlüssel](./media/keyvault-keyrotation/create-key.png)

Bevor Sie Aufrufe von Ihrer Anwendung an den Schlüsseltresor durchführen können, müssen Sie den Schlüsseltresor über Ihre Anwendung und deren Berechtigungen informieren. Der folgende Befehl verwendet den Namen des Tresors und die Anwendungs-ID aus Ihrer Azure Active Directory-App, um der Anwendung den Zugriff zum **Abrufen** auf Ihren Schlüsseltresor zu gewähren.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Sie können nun Ihre Anwendungsaufrufe erstellen. In Ihrer Anwendung müssen Sie die NuGet-Pakete installieren, die für die Interaktion mit Azure Key Vault und Azure Active Directory benötigt werden. Geben Sie in der Paket-Manager-Konsole von Visual Studio die folgenden Befehle ein. Bei der Erstellung dieses Artikels lautete die aktuelle Version des Azure Active Directory-Pakets 3.10.305231913. Prüfen Sie, ob es eine neuere Version gibt, und führen Sie ggf. eine entsprechende Aktualisierung durch.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Erstellen Sie in Ihrem Anwendungscode eine Klasse, die die Methode für Ihre Azure Active Directory-Authentifizierung enthalten soll. In diesem Beispiel heißt diese Klasse **Utils**. Fügen Sie die folgende `using` -Anweisung hinzu:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Fügen Sie als Nächstes die folgende Methode hinzu, um das JWT-Token aus Azure Active Directory abzurufen. Zur besseren Verwaltbarkeit könnten Sie die hartcodierten Zeichenfolgenwerte in Ihre Web- oder Anwendungskonfiguration verschieben.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Fügen Sie den erforderlichen Code zum Aufrufen von Key Vault und Abrufen des Werts Ihres geheimen Schlüssels hinzu. Zuerst müssen Sie die folgende `using`-Anweisung hinzufügen:

```csharp
using Microsoft.Azure.KeyVault;
```

Fügen Sie die Methodenaufrufe zum Aufrufen von Key Vault und Abrufen Ihres geheimen Schlüssels hinzu. In dieser Methode geben Sie den URI Ihres geheimen Schlüssels an, den Sie in einem vorherigen Schritt gespeichert haben. Beachten Sie die Verwendung der **GetToken**-Methode in der zuvor erstellten **Utils**-Klasse.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Wenn Sie Ihre Anwendung ausführen, müssen Sie sich jetzt bei Azure Active Directory authentifizieren und dann den Wert Ihres geheimen Schlüssels aus Azure Key Vault abrufen.

## <a name="key-rotation-using-azure-automation"></a>Schlüsselrotation mithilfe von Azure Automation

> [!IMPORTANT]
> Für Azure Automation-Runbooks ist das `AzureRM`-Modul weiterhin erforderlich.

Sie können nun eine Rotationstrategie für die Werte einrichten, die Sie als Key Vault-Geheimnisse speichern. Geheimnisse können auf verschiedene Weise gedreht werden:

- Im Rahmen eines manuellen Prozesses
- Programmgesteuert mit API-Aufrufen
- Über ein Azure Automation-Skript

Für diesen Artikel verwenden Sie PowerShell zusammen mit Azure Automation zum Ändern eines Zugriffsschlüssels für das Azure Storage-Konto. Anschließend aktualisieren Sie ein Geheimnis im Schlüsseltresor mit diesem neuen Schlüssel.

Damit Azure Automation Geheimniswerte in Ihrem Schlüsseltresor festlegen kann, müssen Sie die Client-ID der Verbindung namens **AzureRunAsConnection** abrufen. Diese Verbindung wurde hergestellt, als Sie Ihre Azure Automation-Instanz einrichteten. Um diese ID zu suchen, wählen Sie in Ihrer Azure Automation-Instanz **Assets** aus. Dort wählen Sie **Verbindungen** und dann den Dienstprinzipal **AzureRunAsConnection** aus. Notieren Sie sich den **ApplicationId**-Wert.

![Azure Automation-Client-ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Wählen Sie in **Assets** die Option **Module** aus. Wählen Sie **Katalog** aus, und suchen Sie nach aktualisierten Versionen der einzelnen folgenden Module, die Sie importieren:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Zum Zeitpunkt der Erstellung dieses Artikels mussten nur die zuvor genannten Module für das folgende Skript aktualisiert werden. Wenn bei Ihrem Automatisierungsauftrag ein Fehler auftritt, überprüfen Sie, ob Sie alle erforderlichen Module und ihre Abhängigkeiten importiert haben.

Nachdem Sie die Anwendungs-ID für Ihre Azure Automation-Verbindung abgerufen haben, müssen Sie Ihren Schlüsseltresor darüber informieren, dass diese Anwendung die Berechtigung zum Aktualisieren von Geheimnissen in Ihrem Tresor hat. Verwenden Sie den folgenden PowerShell-Befehl:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Als Nächstes wählen Sie die Ressource **Runbooks** unter Ihrer Azure Automation-Instanz und dann **Runbook hinzufügen** aus. Wählen Sie **Schnellerfassung**. Benennen Sie Ihr Runbook, und wählen Sie als Runbooktyp **PowerShell** aus. Sie können eine Beschreibung hinzufügen. Wählen Sie abschließend **Erstellen**.

![Runbook erstellen](./media/keyvault-keyrotation/Create_Runbook.png)

Im Editorbereich Ihres neuen Runbooks fügen Sie das folgende PowerShell-Skript ein:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Wählen Sie im Editorbereich **Testbereich** aus, um Ihr Skript zu testen. Sobald das Skript ohne Fehler ausgeführt wird, können Sie die Option **Veröffentlichen** auswählen. Im Konfigurationsbereich für das Runbook können Sie dann einen Zeitplan dafür auswählen.

## <a name="key-vault-auditing-pipeline"></a>Überwachungspipeline für den Schlüsseltresor

Bei der Einrichtung eines Schlüsseltresors können Sie die Überwachung aktivieren, um Protokolle zu Zugriffsanforderungen zu sammeln, die an Ihren Schlüsseltresor gerichtet wurden. Diese Protokolle werden in einem festgelegten Azure Storage-Konto gespeichert und können dann abgerufen, überwacht und analysiert werden. Im folgenden Szenario werden Azure-Funktionen, Azure-Logik-Apps und Schlüsseltresor-Überwachungsprotokolle zum Erstellen einer Pipeline verwendet, die eine E-Mail sendet, sobald Geheimnisse aus dem Tresor von einer App abgerufen werden, die nicht der App-ID der Web-App entspricht.

Zunächst müssen Sie die Protokollierung für Ihren Schlüsseltresor aktivieren. Verwenden Sie die folgenden PowerShell-Befehle. (Bitte entnehmen Sie die vollständigen Details [diesem Artikel zur Schlüsseltresorprotokollierung](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Wenn die Protokollierung aktiviert ist, werden Überwachungsprotokolle im vorgesehenen Speicherkonto gespeichert. Diese Protokolle enthalten Ereignisse dazu, wie, wann und von wem auf Ihre Schlüsseltresore zugegriffen wurde.

> [!NOTE]
> Sie können auf Ihre Protokollinformationen zehn Minuten nach dem Schlüsseltresorvorgang zugreifen. Sie sind oft schneller verfügbar.

Der nächste Schritt besteht im [Erstellen einer Azure Service Bus-Warteschlange](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). In diese Warteschlange werden die Überwachungsprotokolle für den Schlüsseltresor übertragen. Wenn sich die Überwachungsprotokollmeldungen in der Warteschlange befinden, ruft die Logik-App sie ab und reagiert entsprechend. Führen Sie die folgenden Schritte aus, um einen Service Bus-Instanz zu erstellen:

1. Erstellen Sie einen Service Bus-Namespace (falls bereits ein Service Bus-Namespace vorhanden ist, den Sie verwenden möchten, fahren Sie mit Schritt 2 fort).
2. Wechseln Sie im Azure-Portal zur Service Bus-Instanz, und wählen Sie den Namespace aus, in dem die Warteschlange erstellt werden soll.
3. Wählen Sie **Ressource erstellen** > **Unternehmensintegration** > **Service Bus** aus, und geben Sie dann die erforderlichen Details ein.
4. Suchen Sie die Service Bus-Verbindungsinformationen, indem Sie zuerst den Namespace und dann **Verbindungsinformationen** auswählen. Sie benötigen diese Informationen für den nächsten Abschnitt.

Als Nächstes [erstellen Sie eine Azure-Funktion](../azure-functions/functions-create-first-azure-function.md) zum Abfragen der Schlüsseltresorprotokolle im Speicherkonto und Auswählen neuer Ereignisse. Diese Funktion wird nach einem Zeitplan ausgelöst.

Um eine Azure-Funktionen-App zu erstellen, wählen Sie **Ressource erstellen** aus, suchen im Marketplace nach **Funktions-App** und wählen dann **Erstellen** aus. Während der Erstellung können Sie einen vorhandenen Hostingplan verwenden oder einen neuen erstellen. Sie können sich auch für dynamisches Hosting entscheiden. Weitere Informationen zu den Hostingoptionen für Azure Functions finden Sie unter [Skalieren von Azure Functions](../azure-functions/functions-scale.md).

Nachdem die Azure-Funktions-App erstellt wurde, navigieren Sie dorthin und wählen das **Timer**-Szenario und **C\#** für die Sprache aus. Wählen Sie dann **Diese Funktion erstellen** aus.

![Blatt „Start“ von Azure Functions](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Ersetzen Sie auf der Registerkarte **Entwickeln** den Code von „run.csx“ durch Folgendes:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Ändern Sie die Variablen im vorangegangenen Code so, dass sie auf Ihr Speicherkonto zeigen, in das die Schlüsseltresorprotokolle geschrieben werden, auf die zuvor erstellte Service Bus-Instanz und auf den spezifischen Pfad zu den Speicherprotokollen für den Schlüsseltresor.

Die Funktion verwendet die neueste Protokolldatei im Speicherkonto, in das Schlüsseltresorprotokolle geschrieben werden, ruft die neuesten Ereignisse aus dieser Datei ab und überträgt sie in eine Service Bus-Warteschlange. 

Da eine einzelne Datei mehrere Ereignisse enthalten kann, sollten Sie die Datei „sync.txt“ erstellen, die die Funktion ebenfalls untersucht, um den Zeitstempel des letzten ausgewählten Ereignisses zu bestimmen. Mit dieser Datei wird sichergestellt, dass Sie dasselbe Ereignis nicht mehrmals in die Warteschlange übertragen. 

Diese Datei „sync.txt“ enthält einen Zeitstempel des letzten eingetretenen Ereignisses. Wenn die Protokolle geladen werden, müssen sie auf der Basis des Zeitstempels sortiert werden, um sicherzustellen, dass sie ordnungsgemäß sortiert sind.

Hierfür verweisen wir auf verschiedene zusätzliche Bibliotheken, die in Azure Functions nicht standardmäßig verfügbar sind. Um diese Bibliotheken einzubeziehen, muss Azure Functions sie über NuGet abrufen. Wählen Sie unter dem Feld **Code** die Option **Dateien anzeigen** aus.

![Option „Dateien anzeigen“](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Fügen Sie eine Datei namens „project.json“ mit folgendem Inhalt hinzu:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Nachdem Sie **Speichern** ausgewählt haben, lädt Azure Functions die erforderlichen Binärdateien herunter.

Wechseln Sie zur Registerkarte **Integrieren** , und versehen Sie den Parameter „Timer“ mit einem aussagekräftigen Namen, der in der Funktion verwendet werden soll. Im obigen Code erwartet die Funktion, dass der Timer *myTimer*heißt. Geben Sie einen [CRON-Ausdruck](../app-service/webjobs-create.md#CreateScheduledCRON) für den Timer wie folgt an: `0 * * * * *`. Dieser Ausdruck bewirkt, dass die Funktion einmal pro Minute ausgeführt wird.

Fügen Sie auf derselben Registerkarte **Integrieren** eine Eingabe des Typs **Azure Blob Storage** hinzu. Diese Eingabe zeigt auf die Datei „sync.txt“, die den Zeitstempel des letzten Ereignisses enthält, das von der Funktion untersucht wurde. Der Zugriff auf diese Eingabe in der Funktion erfolgt mit dem Parameternamen. Im obigen Code erwartet die Azure Blob Storage-Eingabe, dass der Parametername *inputBlob* ist. Wählen Sie das Speicherkonto aus, das die Datei „sync.txt“ enthalten soll (dabei kann es sich um dasselbe oder ein anderes Speicherkonto handeln). Geben Sie im Feld für den Pfad den Pfad zur Datei im Format `{container-name}/path/to/sync.txt` ein.

Fügen Sie eine Ausgabe des Typs **Azure Blob Storage** hinzu. Diese Ausgabe zeigt auf die Datei „sync.txt“, die Sie in der Eingabe definiert haben. Diese Ausgabe wird von der Funktion zum Schreiben des Zeitstempels des letzten untersuchten Ereignisses verwendet. Der obige Code erwartet, dass dieser Parameter *outputBlob*heißt.

Die Funktion ist jetzt bereit. Wechseln Sie zurück zur Registerkarte **Entwickeln**, und speichern Sie den Code. Überprüfen Sie das Ausgabefenster auf Kompilierungsfehler, und korrigieren Sie diese entsprechend. Falls die Kompilierung erfolgt, sollte der Code nun die Schlüsseltresorprotokolle minütlich überprüfen und alle neuen Ereignisse in die definierte Service Bus-Warteschlange übertragen. Immer wenn die Funktion ausgelöst wird, sollten Protokollinformationen im Protokollfenster ausgegeben werden.

### <a name="azure-logic-app"></a>Azure-Logik-App

Als Nächstes müssen Sie eine Azure-Logik-App erstellen, die die Ereignisse verarbeitet, die die Funktion in die Service Bus-Warteschlange überträgt, den Inhalt analysiert und basierend auf einer erfüllten Bedingung eine E-Mail sendet.

[Erstellen Sie eine Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) durch Auswahl von **Ressource erstellen** > **Integration** > **Logik-App**.

Nachdem die Logik-App erstellt wurde, navigieren Sie zu ihr, und wählen Sie **Bearbeiten** aus. Wählen Sie im Logik-App-Editor **Service Bus-Warteschlange** aus, und geben Sie Ihre Service Bus-Anmeldeinformationen ein, um sie mit der Warteschlange zu verbinden.

![Service Bus für Azure-Logik-App](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Wählen Sie **Bedingung hinzufügen**aus. Wechseln Sie in der Bedingung zum erweiterten Editor, und geben Sie den folgenden Code ein. Ersetzen Sie *APP_ID* mit der eigentlichen App-ID der Web-App:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Dieser Ausdruck gibt **false** zurück, wenn die *appid* im eingehenden Ereignis (dem Text der Service Bus-Nachricht) nicht die *appid* der App ist.

Erstellen Sie jetzt unter **FALLS NEIN, NICHTS TUN** eine Aktion.

![Azure Logic Apps – Aktion auswählen](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Wählen Sie als Aktion **Office 365 – E-Mail senden** aus. Füllen Sie die Felder aus, um eine E-Mail zu erstellen, wenn die definierte Bedingung **false** zurückgibt. Wenn Sie Office 365 nicht haben, wählen Sie eine Alternative aus, um dieselben Ergebnisse zu erzielen.

Sie haben nun eine lückenlose Pipeline, die einmal pro Minute eine Überprüfung auf neue Schlüsseltresor-Überwachungsprotokolle vornimmt. Die gefundenen neuen Protokolle werden in eine Service Bus-Warteschlange verschoben. Die Logik-App wird ausgelöst, wenn eine neue Nachricht in der Warteschlange eintrifft. Wenn die *appid* innerhalb des Ereignisses nicht der App-ID der aufrufenden Anwendung entspricht, wird eine E-Mail gesendet.
