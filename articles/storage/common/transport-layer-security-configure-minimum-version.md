---
title: Erzwingen der erforderlichen Mindestversion der Transport Layer Security (TLS) für eingehende Anforderungen
titleSuffix: Azure Storage
description: Konfigurieren Sie ein Speicherkonto so, dass eine Mindestversion der Transport Layer Security (TLS) für Clients erforderlich ist, die Anforderungen an Azure Storage senden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 683f0e070ad77add62ed76eabd70b42ba15f012e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498131"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Erzwingen der erforderliche Mindestversion der Transport Layer Security (TLS) für Anforderungen an ein Speicherkonto

Die Kommunikation zwischen einer Clientanwendung und einem Azure Storage-Konto wird mithilfe der Transport Layer Security (TLS) verschlüsselt. Die TLS ist ein kryptografisches Standardprotokoll, mit dem Datenschutz und Datenintegrität zwischen Clients und Diensten über das Internet sichergestellt werden. Weitere Informationen zur TLS finden Sie unter [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Azure Storage unterstützt derzeit drei Versionen des TLS-Protokolls: 1.0, 1.1 und 1.2. In Azure Storage wird TLS 1.2 für öffentliche HTTPS-Endpunkte verwendet, aber TLS 1.0 und TLS 1.1 werden aus Gründen der Abwärtskompatibilität weiterhin unterstützt.

Standardmäßig ermöglichen Azure Storage-Konten Clients das Senden und Empfangen von Daten mit der ältesten TLS-Version, TLS 1.0, und höheren Versionen. Wenn Sie strengere Sicherheitsmaßnahmen durchsetzen möchten, können Sie Ihr Speicherkonto so konfigurieren, dass Clients Daten mit einer neueren TLS-Version senden und empfangen müssen. Wenn für ein Speicherkonto eine TLS-Mindestversion erforderlich ist, verursachen alle Anforderungen mit einer älteren Version einen Fehler.

In diesem Artikel wird beschrieben, wie Sie ein DRAG-Framework (Detection-Remediation-Audit-Governance) zur kontinuierlichen Verwaltung einer sicheren TLS für Ihre Speicherkonten verwenden können.

Informationen zum Angeben einer bestimmten TLS-Version beim Senden einer Anforderung aus einer Clientanwendung finden Sie unter [Konfigurieren der Transport Layer Security (TLS) für eine Clientanwendung](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Ermitteln der von Clientanwendungen verwendeten TLS-Version

Wenn Sie für Ihr Speicherkonto eine TLS-Mindestversion erzwingen, werden Anforderungen von Clients, die Daten mit einer älteren TLS-Version senden, möglicherweise abgelehnt. Um die Auswirkungen der Konfiguration einer TLS-Mindestversion auf Clientanwendungen zu verstehen, empfiehlt Microsoft, die Protokollierung für Ihr Azure Storage-Konto zu aktivieren und die Protokolle nach einem bestimmten Zeitraum zu analysieren. So können Sie erkennen, welche TLS-Versionen von Clientanwendungen verwendet werden.

Für die Protokollierung der Anforderungen an Ihr Azure Storage-Konto und die Ermittlung der vom Client verwendeten TLS-Version können Sie die Azure Storage-Protokollierung in Azure Monitor (Vorschau) verwenden. Weitere Informationen finden Sie unter [Überwachen von Azure Storage](../blobs/monitor-blob-storage.md).

Die Azure Storage-Protokollierung in Azure Monitor unterstützt die Verwendung von Protokollabfragen für die Analyse von Protokolldaten. Für die Abfrage von Protokollen können Sie einen Azure Log Analytics-Arbeitsbereich verwenden. Weitere Informationen zu Protokollabfragen finden Sie unter [Tutorial: Erste Schritte mit Log Analytics-Abfragen](../../azure-monitor/log-query/log-analytics-tutorial.md).

Wenn Sie Azure Storage-Daten mit Azure Monitor protokollieren und mit Azure Log Analytics analysieren möchten, müssen Sie zunächst eine Diagnoseeinstellung erstellen, die angibt, welche Anforderungstypen und für welche Speicherdienste Daten protokolliert werden sollen. Führen Sie zum Erstellen einer Diagnoseeinstellung im Azure-Portal die folgenden Schritte aus:

1. Registrieren Sie sich für die [Azure Storage-Protokollierung in Azure Monitor (Vorschauversion)](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Erstellen Sie in dem Abonnement, das Ihr Azure Storage-Konto enthält, einen neuen Log Analytics-Arbeitsbereich. Nachdem Sie die Protokollierung für Ihr Speicherkonto konfiguriert haben, sind die Protokolle im Log Analytics-Arbeitsbereich verfügbar. Weitere Informationen finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Klicken Sie im Abschnitt „Überwachung“ auf **Diagnoseeinstellungen (Vorschau)** .
1. Wählen Sie den Azure Storage-Dienst aus, für den Sie Anforderungen protokollieren möchten. Wählen Sie beispielsweise **Blob** aus, um Anforderungen an den Blobspeicher zu protokollieren.
1. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.
1. Geben Sie einen Namen für die Diagnoseeinstellung an.
1. Wählen Sie unter **Kategoriedetails** im Abschnitt **Protokoll** aus, welche Typen von Anforderungen protokolliert werden sollen. Sie können Lese-, Schreib- und Löschanforderungen protokollieren. Wenn Sie z. B. **StorageRead** und **StorageWrite** auswählen, werden Lese- und Schreibanforderungen an den ausgewählten Dienst protokolliert.
1. Wählen Sie unter **Zieldetails** die Option **An Log Analytics senden** aus. Wählen Sie Ihr Abonnement und den zuvor erstellten Log Analytics-Arbeitsbereich aus, wie in der folgenden Abbildung zu sehen:

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Screenshot: Erstellen einer Diagnoseeinstellung für die Protokollierung von Anforderungen":::

Nachdem Sie die Diagnoseeinstellung erstellt haben, werden Anforderungen für das Speicherkonto gemäß dieser Einstellung protokolliert. Weitere Informationen finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](../../azure-monitor/platform/diagnostic-settings.md).

Eine Referenz der Felder, die in Azure Storage-Protokollen in Azure Monitor verfügbar sind, finden Sie unter [Ressourcenprotokolle (Vorschau)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Abfragen protokollierter Anforderungen nach TLS-Version

Azure Storage-Protokolle in Azure Monitor enthalten die TLS-Version, die zum Senden einer Anforderung an ein Speicherkonto verwendet wird. Mithilfe der Eigenschaft **TlsVersion** können Sie die TLS-Version einer protokollierten Anforderung überprüfen.

Öffnen Sie Ihren Arbeitsbereich für die Protokollanalyse, um festzustellen, wie viele Anforderungen in den letzten sieben Tagen an Blobspeicher mit verschiedenen Versionen von TLS gestellt wurden. Fügen Sie dann die folgende Abfrage in eine neue Protokollabfrage ein, und führen Sie sie aus. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Die Ergebnisse zeigen die Anzahl der Anforderungen, die über die einzelnen TLS-Versionen gesendet wurden:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Screenshot der Ergebnisse der Log Analytics-Abfrage zur Rückgabe der TLS-Version":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Abfragen protokollierter Anforderungen nach IP-Adresse des Aufrufers und Benutzer-Agent-Header

Azure Storage-Protokolle in Azure Monitor enthalten auch die IP-Adresse des Aufrufers und den Benutzer-Agent-Header, damit Sie ermitteln können, welche Clientanwendungen auf das Speicherkonto zugegriffen haben. Durch eine Analyse dieser Werte können Sie entscheiden, ob Clientanwendungen auf die Verwendung einer neueren TLS-Version aktualisiert werden müssen oder ob eine Clientanforderung problemlos abgelehnt werden kann, wenn sie nicht mit der TLS-Mindestversion gesendet wird.

Fügen Sie die folgende Abfrage in eine neue Protokollabfrage ein und führen Sie diese aus, um festzustellen, welche Clients in den letzten sieben Tagen Anforderungen mit einer Version von TLS gestellt haben, die älter als TLS 1.2 ist. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Beheben von Sicherheitsrisiken mit einer TLS-Mindestversion

Wenn Sie sicher sind, dass der Datenverkehr von Clients, die ältere Versionen von TLS verwenden, minimal ist oder dass es akzeptabel ist, dass bei Anforderungen, die mit einer älteren Version von TLS gestellt wurden, Fehler auftreten, dann können Sie mit der Erzwingung einer TLS-Mindestversion für Ihr Speicherkonto beginnen. Die Anforderung, dass Clients eine TLS-Mindestversion verwenden müssen, um Anforderungen an ein Speicherkonto zu stellen, ist Teil einer Strategie zur Minimierung der Sicherheitsrisiken für Ihre Daten.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>Konfigurieren der TLS-Mindestversion für ein Speicherkonto

Legen Sie zum Konfigurieren der TLS-Mindestversion für ein Speicherkonto die **MinimumTlsVersion**-Version für das Konto fest. Diese Eigenschaft ist für alle Speicherkonten verfügbar, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden. Weitere Informationen zum Azure Resource Manager-Bereitstellungsmodell finden Sie unter [Speicherkontoübersicht](storage-account-overview.md).

Die Eigenschaft **MinimumTlsVersion** wird nicht standardmäßig festgelegt und gibt erst dann einen Wert zurück, nachdem Sie sie explizit festgelegt haben.  Wenn der Eigenschaftswert **NULL** ist, lässt das Speicherkonto Anforderungen zu, die mit TLS Version 1.0 oder höher gesendet werden.

# <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie ein Speicherkonto über das Azure-Portal erstellen, wird standardmäßig die TLS-Mindestversion auf 1.2 festgelegt.

Führen Sie die folgenden Schritte aus, um die TLS-Mindestversion für ein bestehendes Speicherkonto im Azure-Portal zu konfigurieren:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie die Einstellung **Konfiguration** aus.
1. Wählen Sie unter **TLS-Mindestversion** in der Dropdownliste die erforderliche TLS-Mindestversion für den Zugriff auf Daten in diesem Speicherkonto aus, wie in der folgenden Abbildung dargestellt.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Screenshot zur Konfiguration der TLS-Mindestversion im Azure-Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installieren Sie [Azure PowerShell Version 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) oder höher, um die TLS-Mindestversion für ein Speicherkonto mit PowerShell zu konfigurieren. Konfigurieren Sie als nächstes die Eigenschaft **MinimumTLSVersion** für ein neues oder vorhandenes Speicherkonto. Gültige Werte für **MinimumTlsVersion** sind `TLS1_0`, `TLS1_1` und `TLS1_2`.

Das folgende Beispiel erstellt ein Speicherkonto und legt **MinimumTLSVersion** auf TLS 1.1 fest, aktualisiert dann das Konto und setzt **MinimumTLSVersion** auf TLS 1.2. Das Beispiel ruft auch den jeweiligen Eigenschaftswert ab. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Installieren Sie die Azure-Befehlszeilenschnittstelle, Version 2.9.0 oder höher, um die TLS-Mindestversion für ein Speicherkonto mit der Azure-Befehlszeilenschnittstelle zu konfigurieren. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli). Konfigurieren Sie als nächstes die Eigenschaft **minimumTlsVersion** für ein neues oder vorhandenes Speicherkonto. Gültige Werte für **minimumTlsVersion** sind `TLS1_0`, `TLS1_1` und `TLS1_2`.

Im folgenden Beispiel wird ein Speicherkonto erstellt und **minimaleTLSVersion** auf TLS 1.1 festgelegt. Anschließend wird das Konto aktualisiert und die Eigenschaft **minimumTLSVersion** auf TLS 1.2 festgelegt. Das Beispiel ruft auch den jeweiligen Eigenschaftswert ab. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Vorlage](#tab/template)

Erstellen Sie zum Konfigurieren der TLS-Mindestversion für ein Speicherkonto mit einer Vorlage eine Vorlage mit der Eigenschaft **MinimumTLSVersion**, die auf `TLS1_0`, `TLS1_1` oder `TLS1_2` festgelegt ist. Die folgenden Schritte beschreiben, wie eine Vorlage im Azure-Portal erstellt wird.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.
1. Geben Sie in **Marketplace durchsuchen** den Begriff **Vorlagenbereitstellung** ein, und drücken Sie dann die **EINGABETASTE**.
1. Wählen Sie **Vorlagenbereitstellung (Bereitstellen mit benutzerdefinierten Vorlagen) (Vorschau)** , dann **Erstellen** und anschließend die Option **Eigene Vorlage im Editor erstellen** aus.
1. Fügen Sie im Vorlagen-Editor den folgenden JSON-Code ein, um ein neues Konto zu erstellen, und legen Sie die TLS-Mindestversion auf TLS 1.2 fest. Denken Sie daran, die Platzhalter in eckigen Klammern durch Ihre eigenen Werte zu ersetzen.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Speichern Sie die Vorlage.
1. Geben Sie den Ressourcengruppenparameter an, wählen Sie dann die Schaltfläche **Überprüfen + erstellen** aus, um die Vorlage bereitzustellen und ein Speicherkonto mit der konfigurierten Eigenschaft **MinimumTLSVersion** zu erstellen.

---

> [!NOTE]
> Nachdem Sie die TLS-Mindestversion für das Speicherkonto aktualisiert haben, kann es bis zu 30 Sekunden dauern, bis die Änderung vollständig weitergegeben wurde.

Die Konfiguration der TLS-Mindestversion erfordert Version 2019-04-01 oder höher des Azure Storage-Ressourcenanbieters. Weitere Informationen finden Sie unter [REST-API des Azure Storage-Ressourcenanbieters](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Überprüfen der erforderlichen TLS-Mindestversion für mehrere Konten

Wenn Sie die erforderliche TLS-Mindestversion für eine Gruppe von Speicherkonten mit optimaler Leistung überprüfen möchten, können Sie den Azure Resource Graph-Explorer im Azure-Portal verwenden. Weitere Informationen zur Verwendung des Resource Graph-Explorers finden Sie unter [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../../governance/resource-graph/first-query-portal.md).

Wenn Sie die folgende Abfrage im Resource Graph-Explorer ausführen, wird eine Liste der Speicherkonten zurückgegeben und für jedes Konto die TLS-Mindestversion angezeigt:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>Testen der TLS-Mindestversion auf einem Client

Wenn Sie testen möchten, ob mit einer älteren Version durchgeführte Aufrufe durch die erforderliche TLS-Mindestversion für ein Speicherkonto verhindert werden, können Sie einen Client für die Verwendung einer älteren TLS-Version konfigurieren. Weitere Informationen zum Konfigurieren eines Clients für die Verwendung einer bestimmten TLS-Version finden Sie unter [Konfigurieren der Transport Layer Security (TLS) für eine Clientanwendung](transport-layer-security-configure-client-version.md).

Wenn ein Client auf ein Speicherkonto über eine TLS-Version zugreift, die nicht der für das Konto konfigurierten TLS-Mindestversion entspricht, gibt Azure Storage einen Fehler mit dem Fehlercode 400 zurück (Ungültige Anforderung). Zudem wird in einer Meldung darauf hingewiesen, dass die verwendete TLS-Version für Anforderungen an dieses Speicherkonto nicht gestattet ist.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Verwenden von Azure Policy zur Überwachung der Konformität

Wenn Sie eine große Anzahl von Speicherkonten besitzen, möchten Sie vielleicht eine Überwachung durchführen, um sicherzustellen, dass alle Konten für die von Ihrer Organisation benötigte TLS-Mindestversion konfiguriert sind. Verwenden Sie Azure Policy, um eine Reihe von Speicherkonten auf ihre Konformität zu überwachen. Azure Policy ist ein Dienst, mit dem Sie Richtlinien zum Anwenden von Regeln auf Azure-Ressourcen erstellen, zuweisen und verwalten können. Azure Policy hilft Ihnen, die Konformität dieser Ressourcen mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel sicherzustellen. Weitere Informationen finden Sie in der [Übersicht über Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Erstellen einer Richtlinie mit der Auswirkung „Audit“

Azure Policy unterstützt Auswirkungen, die bestimmen, was passiert, wenn eine Richtlinie anhand einer Ressource ausgewertet wird. Die Auswirkung „Audit“ erzeugt eine Warnung, wenn eine Ressource nicht konform ist, beendet aber die Anforderung nicht. Weitere Informationen zu Auswirkungen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](../../governance/policy/concepts/effects.md).

Führen Sie die folgenden Schritte aus, um eine Richtlinie mit Auswirkung „Audit“ für die TLS-Mindestversion mit dem Azure-Portal zu erstellen:

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie unter dem Abschnitt **Autorisierung** die Option **Definitionen** aus.
1. Wählen Sie **Richtliniendefinition hinzufügen** aus, um eine neue Richtliniendefinition zu erstellen.
1. Wählen Sie für das Feld **Definitionsspeicherort** die Schaltfläche **Mehr** aus, um anzugeben, wo sich die Ressource für die Überwachungsrichtlinie befindet.
1. Geben Sie einen Namen für die Richtlinie an. Sie können optional eine Beschreibung und eine Kategorie angeben.
1. Fügen Sie unter **Richtlinienregel** die folgende Richtliniendefinition zum Abschnitt **policyRule** hinzu.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
              "equals": "TLS1_2"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Speichern Sie die Richtlinie.

### <a name="assign-the-policy"></a>Zuweisen der Richtlinie

Anschließend weisen Sie die Richtlinie einer Ressource zu. Der Umfang der Richtlinie entspricht der Ressource und den darunter liegenden Ressourcen. Weitere Informationen zur Zuweisung von Richtlinien finden Sie unter [Azure Policy-Zuweisungsstruktur](../../governance/policy/concepts/assignment-structure.md).

Führen Sie die folgenden Schritte aus, um die Richtlinie dem Azure-Portal zuzuweisen:

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie unter dem Abschnitt **Autorisierung** die Option **Zuweisungen** aus.
1. Wählen Sie **Richtlinie zuweisen** aus, um eine neue Richtlinienzuweisung zu erstellen.
1. Wählen Sie für das Feld **Umfang** den Umfang der Richtlinienzuweisung aus.
1. Wählen Sie für das Feld **Richtliniendefinition** die Schaltfläche **Mehr** und dann die im vorherigen Abschnitt definierte Richtlinie aus der Liste aus.
1. Geben Sie einen Namen für die Richtlinienzuweisung an. Die Angabe einer Beschreibung ist optional.
1. Behalten Sie für **Richtlinienerzwingung** die Einstellung *Aktiviert* bei. Diese Einstellung hat keine Auswirkung auf die Überwachungsrichtlinie.
1. Klicken Sie zum Erstellen der Zuweisung auf **Überprüfen + erstellen**.

### <a name="view-compliance-report"></a>Anzeigen des Konformitätsberichts

Nachdem Sie die Richtlinie zugewiesen haben, können Sie den Konformitätsbericht anzeigen. Der Konformitätsbericht für eine Überwachungsrichtlinie gibt Auskunft darüber, welche Speicherkonten nicht mit der Richtlinie konform sind. Weitere Informationen finden Sie unter [Abrufen von Daten zur Richtlinienkonformität](../../governance/policy/how-to/get-compliance-data.md).

Es kann einige Minuten dauern, bis der Konformitätsbericht nach Erstellung der Richtlinienzuweisung verfügbar ist.

Führen Sie die folgenden Schritte aus, um den Konformitätsbericht im Azure-Portal anzuzeigen:

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie **Compliance** aus.
1. Filtern Sie die Ergebnisse nach dem Namen der Richtlinienzuweisung, die Sie im vorherigen Schritt erstellt haben. Der Bericht zeigt, wie viele Ressourcen nicht mit der Richtlinie konform sind.
1. Sie können einen Drilldown in den Bericht ausführen, um weitere Details anzuzeigen, einschließlich einer Liste von Speicherkonten, die nicht konform sind.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Screenshot mit Konformitätsbericht für Überwachungsrichtlinien für TLS-Mindestversion":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Erzwingen der TLS-Mindestversion mit Azure Policy

Azure Policy unterstützt die Cloudgovernance, indem es sicherstellt, dass Azure-Ressourcen den Anforderungen und Standards entsprechen. Um die Anforderung einer TLS-Mindestversion für die Speicherkonten in Ihrer Organisation zu erzwingen, können Sie eine Richtlinie erstellen, die die Erstellung eines neuen Speicherkontos verhindert, das die TLS-Mindestversion auf eine ältere Version von TLS festlegt als die, die von der Richtlinie vorgegeben ist. Diese Richtlinie verhindert auch alle Konfigurationsänderungen an einem bestehenden Konto, wenn die Einstellung der TLS-Mindestversion für dieses Konto nicht mit der Richtlinie übereinstimmt.

Die Erzwingungsrichtlinie nutzt die Auswirkung „Deny“, um eine Anforderung zu verhindern, die ein Speicherkonto erstellen oder ändern würde, sodass die TLS-Mindestversion nicht mehr mit den Standards Ihrer Organisation übereinstimmt. Weitere Informationen zu Auswirkungen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](../../governance/policy/concepts/effects.md).

Um eine Richtlinie mit der Auswirkung „Deny“ für eine TLS-Mindestversion zu erstellen, die kleiner als TLS 1.2 ist, befolgen Sie dieselben Schritte, die unter [Verwenden von Azure Policy zur Überwachung der Konformität](#use-azure-policy-to-audit-for-compliance) beschrieben sind, aber stellen Sie den folgenden JSON-Code im Abschnitt **policyRule** der Richtliniendefinition zur Verfügung:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
          "equals": "TLS1_2"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Nachdem Sie die Richtlinie mit der Auswirkung „Deny“ erstellt und einem Bereich zugewiesen haben, kann ein Benutzer kein Speicherkonto mit einer TLS-Mindestversion erstellen, die älter als 1.2 ist. Ebenso wenig kann ein Benutzer Konfigurationsänderungen an einem bestehenden Speicherkonto vornehmen, für das derzeit eine TLS-Mindestversion erforderlich ist, die älter als 1.2 ist. Ein entsprechender Versuch führt zu einem Fehler. Die erforderliche TLS-Mindestversion für das Speicherkonto muss auf 1.2 festgelegt werden, um mit der Kontoerstellung oder -konfiguration fortfahren zu können.

Die folgende Abbildung zeigt den Fehler, der beim Erstellen eines Speicherkontos mit der TLS-Mindestversion von TLS 1.0 (dem Standard für ein neues Konto) auftritt, wenn eine Richtlinie mit der Auswirkung „Deny“ erfordert, dass die TLS-Mindestversion auf TLS 1.2 festgelegt wird.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="Screenshot mit dem Fehler, der beim Erstellen eines Speicherkontos bei einem Verstoß gegen die Richtlinie auftritt":::

## <a name="network-considerations"></a>Überlegungen zu Netzwerken

Wenn ein Client eine Anforderung an ein Speicherkonto sendet, stellt der Client zunächst eine Verbindung mit dem öffentlichen Endpunkt des Speicherkontos her, ehe Anforderungen bearbeitet werden. Die Einstellung der TLS-Mindestversion wird nach dem Herstellen der Verbindung überprüft. Wenn für die Anforderung eine frühere Version von TLS als die durch die Einstellung angegebene verwendet wird, ist die Verbindung weiterhin erfolgreich, aber die Anforderung schlägt letztendlich fehl. Weitere Informationen zu öffentlichen Endpunkten für Azure Storage finden Sie unter [Syntax von Ressourcen-URIs](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren der Transport Layer Security (TLS) für eine Clientanwendung](transport-layer-security-configure-client-version.md)
- [Sicherheitsempfehlungen für Blob Storage](../blobs/security-recommendations.md)