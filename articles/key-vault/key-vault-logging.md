---
title: Azure Key Vault-Protokollierung | Microsoft-Dokumentation
description: Dieses Tutorial dient als Hilfe bei den ersten Schritten mit der Azure-Schlüsseltresor-Protokollierung.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 89f9ef37ed7c53817854442b3a32b32b7d11ae27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706027"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault-Protokollierung

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nachdem Sie einen oder mehrere Schlüsseltresore erstellt haben, möchten Sie vermutlich überwachen, wie, wann und von wem auf die Schlüsseltresore zugegriffen wird. Hierfür können Sie die Protokollierung für Azure Key Vault aktivieren, bei der Informationen im von Ihnen bereitgestellten Azure-Speicherkonto gespeichert werden. Für Ihr angegebenes Speicherkonto wird automatisch ein neuer Container namens **insights-logs-logs-auditevent** erstellt. Sie können dasselbe Speicherkonto verwenden, um Protokolle für mehrere Schlüsseltresore zu sammeln.

Sie können auf Ihre Protokollinformationen (spätestens) zehn Minuten nach dem Schlüsseltresorvorgang zugreifen. In den meisten Fällen geht es aber schneller.  Die Verwaltung der Protokolle im Speicherkonto ist Ihre Aufgabe:

* Verwenden Sie zum Schützen der Protokolle standardmäßige Azure-Zugriffssteuerungsmethoden, indem Sie einschränken, wer darauf zugreifen kann.
* Löschen Sie Protokolle, die im Speicherkonto nicht mehr aufbewahrt werden sollen.

Dieses Tutorial dient als Hilfe bei den ersten Schritten mit der Azure-Schlüsseltresor-Protokollierung. Sie erstellen ein Speicherkonto, aktivieren die Protokollierung und interpretieren die erfassten Protokollinformationen.  

> [!NOTE]
> Dieses Tutorial enthält keine Anleitung zur Erstellung von Schlüsseltresoren, Schlüsseln oder geheimen Schlüsseln. Die entsprechenden Informationen finden Sie unter [Was ist Azure Key Vault?](key-vault-overview.md). Anleitungen für die plattformübergreifende Azure CLI finden Sie in [diesem entsprechenden Tutorial](key-vault-manage-with-cli2.md).
>
> Dieser Artikel enthält Anweisungen für Azure PowerShell zum Aktualisieren der Diagnoseprotokollierung. Sie können die Diagnoseprotokollierung auch aktualisieren, indem Sie Azure Monitor im Abschnitt **Diagnoseprotokolle** des Azure-Portals verwenden. 
>

Eine Übersicht über Key Vault finden Sie unter [Was ist Azure Key Vault?](key-vault-whatis.md). Informationen dazu, wo Key Vault verfügbar ist, finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Vorhandenen Schlüsseltresor, der von Ihnen genutzt wird  
* Azure PowerShell, Mindestversion 1.0.0. Um Azure PowerShell zu installieren und Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview). Wenn Sie Azure PowerShell bereits installiert haben und die Version nicht kennen, geben Sie über die Azure PowerShell-Konsole `$PSVersionTable.PSVersion` ein.  
* Ausreichend Speicherplatz unter Azure für Ihre Schlüsseltresor-Protokolle

## <a id="connect"></a>Verbinden mit Ihrem Key Vault-Abonnement

Der erste Schritt bei der Einrichtung der Schlüsselprotokollierung besteht darin, Azure PowerShell auf den Schlüsseltresor zu verweisen, den Sie protokollieren möchten.

Starten Sie eine Azure PowerShell-Sitzung, und melden Sie sich mit dem folgenden Befehl bei Ihrem Azure-Konto an:  

```powershell
Connect-AzAccount
```

Geben Sie im Popup-Browserfenster den Benutzernamen und das Kennwort Ihres Azure-Kontos ein. Azure PowerShell ruft alle Abonnements ab, die diesem Konto zugeordnet sind. PowerShell verwendet standardmäßig das erste Abonnement.

Möglicherweise müssen Sie das Abonnement angeben, mit dem Sie Ihren Schlüsseltresor erstellt haben. Geben Sie den folgenden Befehl ein, um die Abonnements für Ihr Konto anzuzeigen:

```powershell
Get-AzSubscription
```

Geben Sie dann Folgendes ein, um das Abonnement anzugeben, das dem zu protokollierenden Schlüsseltresor zugeordnet ist:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

PowerShell auf das richtige Abonnement zu verweisen, ist ein wichtiger Schritt, insbesondere wenn mehrere Abonnements mit Ihrem Konto verknüpft sind. Weitere Informationen zum Konfigurieren von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Erstellen eines Speicherkontos für Ihre Protokolle

Sie können zwar ein vorhandenes Speicherkonto für Ihre Protokolle verwenden, aber wir erstellen ein Speicherkonto, das den Key Vault-Protokollen zugeordnet wird. Wir speichern die Details in einer Variablen mit dem Namen **sa**, damit wir sie später leicht angeben können.

Um die Verwaltung noch weiter zu vereinfachen, verwenden wir auch die gleiche Ressourcengruppe wie die Gruppe, die den Schlüsseltresor enthält. Im [Tutorial zu den ersten Schritten](key-vault-get-started.md) hat diese Ressourcengruppe den Namen **ContosoResourceGroup**, und wir nutzen auch wieder den Standort „Asien, Osten“. Ersetzen Sie diese Werte gegebenenfalls durch Ihre eigenen:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Wenn Sie sich für die Verwendung eines bestehenden Speicherkontos entscheiden, muss dieses das gleiche Abonnement wie Ihr Schlüsseltresor verwenden. Anstelle des klassischen Bereitstellungsmodells muss das Konto außerdem das Azure Resource Manager-Bereitstellungsmodell verwenden.
>
>

## <a id="identify"></a>Identifizieren des Schlüsseltresors für Ihre Protokolle

Im [Tutorial zu den ersten Schritten](key-vault-get-started.md) lautete der Name des Schlüsseltresors **ContosoKeyVault**. Wir werden diesen Namen weiterhin verwenden und die Details in einer Variablen namens **kv** speichern:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Aktivieren der Protokollierung

Zum Aktivieren der Protokollierung für den Schlüsseltresor verwenden wir das Cmdlet **Set-AzDiagnosticSetting** zusammen mit den Variable, die wir für das neue Speicherkonto und den Schlüsseltresor erstellt haben. Außerdem legen wir das Flag **-Enabled** auf **$true** und die Kategorie auf **AuditEvent** (einzige Kategorie für die Key Vault-Protokollierung) fest:

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Die Ausgabe sieht wie folgt aus:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Diese Ausgabe bestätigt, dass die Protokollierung für Ihren Schlüsseltresor jetzt aktiviert ist und Informationen in Ihrem Speicherkonto gespeichert werden.

Optional können Sie eine Aufbewahrungsrichtlinie für Ihre Protokolle festlegen, mit der ältere Protokolle automatisch gelöscht werden. Richten Sie die Aufbewahrungsrichtlinie z. B. wie folgt ein: Legen Sie das Flag **-RetentionEnabled** auf **$true** und den Parameter **-RetentionInDays** auf **90** fest, sodass Protokolle, die älter sind als 90 Tage, automatisch gelöscht werden.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Protokollierte Daten:

* Alle authentifizierten REST-API-Anforderungen, z. B. auch Anforderungen, die aufgrund von Zugriffsberechtigungen, Systemfehlern oder fehlerhaften Anforderungen nicht erfolgreich sind.
* Vorgänge im Schlüsseltresor selbst, z. B. Erstellung, Löschung und Festlegung von Schlüsseltresor-Zugriffsrichtlinien und Aktualisierung von Schlüsseltresor-Attributen wie Tags.
* Vorgänge mit Schlüsseln und Geheimnissen im Schlüsseltresor, einschließlich:
  * Erstellen, Ändern oder Löschen dieser Schlüssel oder Geheimnisse.
  * Signieren, Verifizieren, Verschlüsseln, Entschlüsseln, Ver- und Entpacken von Schlüsseln, Erhalten von Geheimnissen und Auflisten von Schlüsseln und Geheimnissen (und deren Versionen).
* Bei nicht authentifizierten Anforderungen wird eine 401-Antwort zurückgegeben. Beispiele sind Anforderungen ohne Bearertoken, falsch formatierte oder abgelaufene Anforderungen oder Anforderungen, deren Token ungültig ist.  

## <a id="access"></a>Zugreifen auf Ihre Protokolle

Key Vault-Protokolle werden im Container **insights-logs-auditevent** im von Ihnen angegebenen Speicherkonto gespeichert. Zur Anzeige der Protokolle müssen Sie Blobs herunterladen.

Erstellen Sie zunächst eine Variable für den Containernamen. Sie werden diese Variable im weiteren Verlauf der exemplarischen Vorgehensweise verwenden.

```powershell
$container = 'insights-logs-auditevent'
```

Geben Sie Folgendes ein, um alle Blobs in diesem Container aufzulisten:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Die Ausgabe sieht in etwa wie folgt aus:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Wie Sie in dieser Ausgabe sehen, wird für die Blobs eine Benennungskonvention genutzt: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Für die Werte für Datum und Uhrzeit wird UTC verwendet.

Da dasselbe Speicherkonto zum Erfassen von Protokollen für mehrere Ressourcen verwendet werden kann, ist die vollständige Ressourcen-ID im Blobnamen sehr hilfreich, um nur auf die benötigten Blobs zuzugreifen bzw. diese herunterzuladen. Zuerst wird aber beschrieben, wie Sie alle Blobs herunterladen.

Erstellen Sie einen Ordner zum Herunterladen der Blobs. Beispiel:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Rufen Sie anschließend eine Liste mit allen Blobs ab:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Leiten Sie diese Liste an **Get-AzStorageBlobContent** um, um die Blobs in den Zielordner herunterzuladen:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Beim Ausführen dieses zweiten Befehls wird mit dem Trennzeichen **/** in den Blobnamen eine vollständige Ordnerstruktur unter dem Zielordner erstellt. Sie verwenden diese Struktur, um die Blobs herunterzuladen und als Dateien zu speichern.

Verwenden Sie Platzhalter, um Blobs selektiv herunterzuladen. Beispiel:

* Bei Verwendung mehrerer Schlüsseltresore und einem Download von Protokollen nur für einen Schlüsseltresor mit dem Namen CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Wenn Sie über mehrere Ressourcengruppen verfügen und nur Protokolle für eine Ressourcengruppe herunterladen möchten, verwenden Sie `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Wenn Sie alle Protokolle für den Monat Januar 2019 herunterladen möchten, verwenden Sie `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Sie können sich nun ansehen, was in den Protokollen enthalten ist. Aber bevor wir damit fortfahren, sollten Sie noch zwei weitere Befehle kennen:

* Zum Abfragen des Status von Diagnoseeinstellungen für Ihre Schlüsseltresorressource: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Zum Deaktivieren der Protokollierung für Ihre Schlüsseltresorressource: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpretieren der Key Vault-Protokolle

Einzelne Blobs werden als Text und formatiert als JSON-Blob gespeichert. Schauen wir uns einen Beispielprotokolleintrag an. Führen Sie den folgenden Befehl aus:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Er gibt einen Protokolleintrag zurück, der dem folgenden ähnlich ist:

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

In der folgenden Tabelle sind die Feldnamen und Beschreibungen aufgeführt:

| Feldname | BESCHREIBUNG |
| --- | --- |
| **time** |Datum und Uhrzeit (UTC). |
| **Ressourcen-ID** |Azure Resource Manager-Ressourcen-ID Für Schlüsseltresor-Protokolle ist dies immer die Schlüsseltresor-Ressourcen-ID. |
| **operationName** |Name des Vorgangs, wie in der folgenden Tabelle beschrieben |
| **operationVersion** |Die vom Client angeforderte REST-API-Version. |
| **category** |Der Typ des Ergebnisses. Für Key Vault-Protokolle ist **AuditEvent** der einzige verfügbare Wert. |
| **resultType** |Das Ergebnis der REST-API-Anforderung. |
| **resultSignature** |HTTP-Status |
| **resultDescription** |Zusätzliche Beschreibung zum Ergebnis, falls verfügbar |
| **durationMs** |Verarbeitungsdauer der REST-API-Anforderung in Millisekunden. Die Netzwerklatenz ist hierin nicht enthalten, sodass die auf der Clientseite gemessene Zeit unter Umständen nicht mit diesem Zeitraum übereinstimmt. |
| **callerIpAddress** |Die IP-Adresse des Clients, der die Anforderung gestellt hat. |
| **correlationId** |Optionale GUID, die vom Client zum Korrelieren von clientseitigen Protokollen mit dienstseitigen Protokollen (Schlüsseltresor) übergeben werden kann |
| **Identität** |Identität des Tokens, das in der REST-API-Anforderung angegeben wurde. Dies ist normalerweise ein „Benutzer“, ein „Dienstprinzipal“ oder die Kombination „Benutzer + App-ID“, wie bei einer Anforderung, die auf einem Azure PowerShell-Cmdlet basiert. |
| **properties** |Informationen, die je nach Vorgang (**operationName**) variieren. In den meisten Fällen enthält dieses Feld Clientinformationen (vom Client übergebene Zeichenfolge „useragent“), den genauen REST-API-Anforderungs-URI und den HTTP-Statuscode. Wenn ein Objekt als Ergebnis einer Anforderung (z. B. **KeyCreate** oder **VaultGet**) zurückgegeben wird, enthält es außerdem den Schlüssel-URI (als „id“), Tresor-URI oder URI des Geheimnisses. |

Die Feldwerte unter **operationName** liegen im *ObjectVerb*-Format vor. Beispiel:

* Alle Schlüsseltresorvorgänge verfügen über das Format `Vault<action>`, z. B. `VaultGet` und `VaultCreate`.
* Alle Schlüsselvorgänge verfügen über das Format `Key<action>`, z. B. `KeySign` und `KeyList`.
* Alle Geheimnisvorgänge verfügen über das Format `Secret<action>`, z. B. `SecretGet` und `SecretListVersions`.

Die folgende Tabelle enthält die **operationName**-Werte und die entsprechenden REST-API-Befehle:

| operationName | REST-API-Befehl |
| --- | --- |
| **Authentifizierung** |Authentifizieren über Azure Active Directory-Endpunkt |
| **VaultGet** |[Abrufen von Informationen über einen Schlüsseltresor](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Erstellen oder Aktualisieren eines Schlüsseltresors](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Löschen eines Schlüsseltresors](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Erstellen oder Aktualisieren eines Schlüsseltresors](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Auflisten aller Schlüsseltresore in einer Ressourcengruppe](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Erstellen eines Schlüssels](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Abrufen von Informationen zu einem Schlüssel](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importieren eines Schlüssels in einen Tresor](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Sichern eines Schlüssels](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Löschen eines Schlüssels](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Wiederherstellen eines Schlüssels](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Signieren mit einem Schlüssel](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Überprüfen mit einem Schlüssel](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Umschließen eines Schlüssels](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Aufheben der Umschließung eines Schlüssels](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Verschlüsseln mit einem Schlüssel](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Mit einem Schlüssel entschlüsseln](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Aktualisieren eines Schlüssels](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Auflisten der Schlüssel in einem Tresor](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Auflisten der Versionen eines Schlüssels](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Erstellen eines geheimen Schlüssels](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Abrufen eines Geheimnisses](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Aktualisieren eines Geheimnisses](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Löschen eines Geheimnisses](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Auflisten von Geheimnissen in einem Tresor](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Auflisten von Versionen eines Geheimnisses](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Verwenden von Azure Monitor-Protokollen

Sie können die Key Vault-Lösung in Azure Monitor verwenden, um **AuditEvent**-Protokolle von Key Vault zu überprüfen. In Azure Monitor-Protokollen verwenden Sie Protokollabfragen, um Daten zu analysieren und die benötigten Informationen zu erhalten. 

Weitere Informationen, z. B. zur Einrichtung, finden Sie unter [Azure Key Vault-Lösung in Azure Monitor-Protokollen](../azure-monitor/insights/azure-key-vault.md). Dieser Artikel enthält auch eine Anleitung für die Migration von der alten Key Vault-Lösung, die während der Vorschauphase von Azure Monitor-Protokollen bereitgestellt wurde. Damit haben Sie Ihre Protokolle zuerst an ein Azure Storage-Konto weitergeleitet und Azure Monitor-Protokolle für das Lesen von diesem Konto konfiguriert.

## <a id="next"></a>Nächste Schritte

Ein Tutorial zur Verwendung von Azure Key Vault in einer .NET-Webanwendung finden Sie unter [Verwenden von Azure Key Vault aus einer Webanwendung](tutorial-net-create-vault-azure-web-app.md).

Eine Referenz zur Programmierung finden Sie im [Entwicklerhandbuch für den Azure-Schlüsseltresor](key-vault-developers-guide.md).

Eine Liste der Azure PowerShell 1.0-Cmdlets für Azure Key Vault finden Sie unter [Azure Key Vault-Cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Ein Tutorial zur Schlüsselrotation und Protokollüberwachung mit Azure Key Vault finden Sie unter [Einrichten von Key Vault mit End-to-End-Schlüsselrotation und Überwachung](key-vault-key-rotation-log-monitoring.md).
