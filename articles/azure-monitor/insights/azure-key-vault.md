---
title: Azure Key Vault-Lösung in Azure Monitor | Microsoft-Dokumentation
description: Sie können die Azure Key Vault-Lösung in Azure Monitor verwenden, um Azure Key Vault-Protokolle zu überprüfen.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: bwren
ms.openlocfilehash: 481b643f2f7201a2a1745c7aef9ddd81883da020
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498347"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault-Analyse-Lösung in Azure Monitor

![Key Vault-Symbol](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Sie können die Azure Key Vault-Lösung in Azure Monitor verwenden, um AuditEvent-Protokolle von Azure Key Vault zu überprüfen.

Wenn Sie die Lösung verwenden möchten, müssen Sie die Protokollierung für Azure Key Vault-Diagnosen aktivieren und sie an einen Log Analytics-Arbeitsbereich weiterleiten. Die Protokolle müssen nicht in Azure Blob Storage geschrieben werden.

> [!NOTE]
> Im Januar 2017 wurde die Methode geändert, mit der das Senden von Protokollen aus Key Vault an Log Analytics unterstützt wird. Wenn in der Titelleiste der von Ihnen verwendeten Key Vault-Lösung *(veraltet)* angezeigt wird, informieren Sie sich unter [Migrieren aus der alten Key Vault-Lösung](#migrating-from-the-old-key-vault-solution) über die Schritte, die Sie befolgen müssen.
>
>

## <a name="install-and-configure-the-solution"></a>Installieren und Konfigurieren der Lösung
Gehen Sie folgendermaßen vor, um die Azure Key Vault-Lösung zu installieren und zu konfigurieren:

1. Fügen Sie mithilfe des unter [Hinzufügen von Azure Monitor-Lösungen aus dem Lösungskatalog](../../azure-monitor/insights/solutions.md) beschriebenen Prozesses Ihrem Log Analytics-Arbeitsbereich die Azure Key Vault-Lösung hinzu.
2. Aktivieren Sie entweder im [Portal](#enable-key-vault-diagnostics-in-the-portal) oder mit [PowerShell](#enable-key-vault-diagnostics-using-powershell) die Diagnoseprotokollierung für die zu überwachenden Key Vault-Ressourcen.

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Aktivieren der Key Vault-Diagnose im Portal

1. Wechseln Sie im Azure-Portal zu der zu überwachenden Key Vault-Ressource.
2. Wählen Sie *Diagnoseeinstellungen*, um die folgende Seite zu öffnen.

   ![Abbildung der Kachel „Azure Key Vault“](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Klicken Sie auf *Diagnose aktivieren*, um die folgende Seite zu öffnen.

   ![Abbildung der Kachel „Azure Key Vault“](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Geben Sie einen Namen für die Diagnoseeinstellung an.
5. Klicken Sie auf das Kontrollkästchen *An Log Analytics senden*.
6. Wählen Sie einen vorhandenen Log Analytics-Arbeitsbereich, oder erstellen Sie einen Arbeitsbereich.
7. Um *AuditEvent*-Protokolle zu aktivieren, klicken Sie auf das Kontrollkästchen unter „Protokoll“.
8. Klicken Sie auf *Speichern*, um die Protokollierung der Diagnose im Log Analytics-Arbeitsbereich zu aktivieren.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Aktivieren der Key Vault-Diagnose mit PowerShell
Das folgende PowerShell-Skript zeigt ein Beispiel für die Verwendung von `Set-AzDiagnosticSetting` zur Aktivierung der Diagnoseprotokollierung für Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Überprüfen von Details zur Datensammlung von Azure Key Vault
Die Azure Key Vault-Lösung erfasst Diagnoseprotokolle direkt aus der Key Vault-Instanz.
Die Protokolle müssen nicht in Azure Blob Storage geschrieben werden, und es wird kein Agent für die Datensammlung benötigt.

Die folgende Tabelle enthält die Datensammlungsmethoden und andere Details dazu, wie Daten für Azure Key Vault erfasst werden.

| Plattform | Direkt-Agent | System Center Operations Manager-Agent | Azure | Operations Manager erforderlich? | Daten vom Operations Manager-Agent über Verwaltungsgruppe gesendet | Sammlungshäufigkeit |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | Bei der Ankunft |

## <a name="use-azure-key-vault"></a>Verwenden von Azure Key Vault
Zeigen Sie nach der [Installation der Lösung](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) die Key Vault-Daten an, indem Sie in Azure Monitor auf der Seite **Übersicht** auf die Kachel **Key Vault-Analyse** klicken. Sie können diese Seite im Menü **Azure Monitor** öffnen, indem Sie im Abschnitt **Insights** auf **Mehr** klicken. 

![Abbildung der Kachel „Azure Key Vault“](media/azure-key-vault/log-analytics-keyvault-tile.png)

Nachdem Sie auf die Kachel **Key Vault-Analyse** geklickt haben, können Sie Zusammenfassungen der Protokolle anzeigen und einen Drilldown zu Details für die folgenden Kategorien durchführen:

* Umfang aller Schlüsseltresorvorgänge über einen Zeitraum
* Fehler bei der Ausführung von Volumes über einen Zeitraum
* Durchschnittliche Ausführungslatenz nach Vorgang
* Quality of Service für Vorgänge mit der Anzahl von Vorgängen, die mehr als 1.000 ms dauern, sowie eine Liste der Vorgänge, die mehr als 1.000 ms dauern

![Abbildung des Azure Key Vault-Dashboards](media/azure-key-vault/log-analytics-keyvault01.png)

![Abbildung des Azure Key Vault-Dashboards](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>So zeigen Sie Details zu einzelnen Vorgängen an
1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **Key Vault-Analyse**.
2. Prüfen Sie im **Azure Key Vault**-Dashboard die Zusammenfassungsinformationen in einem der Blätter, und klicken Sie dann auf einen Eintrag, um ausführliche Informationen auf der Seite „Protokollsuche“ anzuzeigen.

    Sie können auf jeder Seite für die Protokollsuche die Ergebnisse nach Zeit, detaillierte Ergebnisse und Ihren Protokollsuchverlauf anzeigen. Außerdem können Sie nach Facets filtern, um die Ergebnisse einzugrenzen.

## <a name="azure-monitor-log-records"></a>Protokolldatensätze in Azure Monitor
Die Azure Key Vault-Lösung analysiert Datensätze vom Typ **KeyVaults**, die aus den [AuditEvent-Protokollen](../../key-vault/key-vault-logging.md) in Azure-Diagnose gesammelt wurden.  Eigenschaften für diese Datensätze finden Sie in der folgenden Tabelle:  

| Eigenschaft | Description |
|:--- |:--- |
| Type |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |IP-Adresse des Clients, der die Anforderung gestellt hat |
| Category (Kategorie) | *AuditEvent* |
| CorrelationId |Optionale GUID, die vom Client zum Korrelieren von clientseitigen Protokollen mit dienstseitigen Protokollen (Schlüsseltresor) übergeben werden kann |
| DurationMs |Verarbeitungsdauer der REST-API-Anforderung in Millisekunden. Die Netzwerklatenz ist in diesem Zeitraum nicht enthalten, sodass die auf der Clientseite gemessene Zeit unter Umständen nicht mit diesem Zeitraum übereinstimmt. |
| httpStatusCode_d |Der von der Anforderung zurückgegebene HTTP-Statuscode (beispielsweise *200*) |
| id_s |Eindeutige ID der Anforderung |
| identity_claim_appid_g | GUID für die Anwendungs-ID |
| OperationName |Der Name des Vorgangs, wie unter [Azure Key Vault-Protokollierung](../../key-vault/key-vault-logging.md) beschrieben |
| OperationVersion |Die vom Client angeforderte REST-API-Version (beispielsweise *2015-06-01*) |
| requestUri_s |Der URI der Anforderung |
| Resource |Der Name des Schlüsseltresors |
| ResourceGroup |Die Ressourcengruppe des Schlüsseltresors |
| resourceId |Azure-Ressourcen-Manager-Ressourcen-ID. Für Key Vault-Protokolle ist dies die Key Vault-Ressourcen-ID. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |HTTP-Status (beispielsweise *OK*) |
| ResultType |Ergebnis der REST-API-Anforderung (beispielsweise *Erfolgreich*) |
| SubscriptionId |Die ID des Azure-Abonnements mit dem Schlüsseltresor |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrieren aus der alten Key Vault-Lösung
Im Januar 2017 wurde die Methode geändert, mit der das Senden von Protokollen aus Key Vault an Log Analytics unterstützt wird. Diese Änderungen bieten folgende Vorteile:
+ Protokolle werden direkt in einen Log Analytics-Arbeitsbereich geschrieben, sodass kein Speicherkonto mehr notwendig ist
+ Geringere Latenz ab dem Zeitpunkt, zu dem Protokolle generiert werden, bis zu ihrer Verfügbarkeit in Log Analytics
+ Weniger Konfigurationsschritte
+ Ein allgemeines Format für alle Typen der Azure-Diagnose

So verwenden Sie die aktualisierte Lösung:

1. [Konfigurieren Sie, dass die Diagnose von Key Vault aus direkt an einen Log Analytics-Arbeitsbereich gesendet wird.](#enable-key-vault-diagnostics-in-the-portal)  
2. Aktivieren Sie die Azure Key Vault-Lösung mithilfe des unter [Hinzufügen von Azure Monitor-Lösungen aus dem Lösungskatalog](../../azure-monitor/insights/solutions.md) beschriebenen Prozesses.
3. Aktualisieren Sie alle gespeicherten Abfragen, Dashboards oder Warnungen zur Verwendung des neuen Datentyps.
   + Der Typ wird geändert von: KeyVaults in AzureDiagnostics. Sie können ResourceType verwenden, um nach Key Vault-Protokollen zu filtern.
   + Verwenden Sie `AzureDiagnostics | where ResourceType'=="VAULTS"` statt `KeyVaults`.
   + Felder: (Bei Feldnamen wird zwischen Groß- und Kleinschreibung unterschieden.)
   + Ändern Sie für jedes Feld, dessen Name das Suffix \_s, \_d oder \_g enthält, das erste Zeichen in einen Kleinbuchstaben.
   + Für jedes Feld, dessen Name das Suffix \_o enthält, werden die Daten basierend auf den geschachtelten Feldnamen in einzelne Felder aufgeteilt. Der UPN des Aufrufers wird z.B. in einem Feld `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s` gespeichert.
   + Feld CallerIpAddress wurde in CallerIPAddress geändert.
   + Feld RemoteIPCountry ist nicht mehr vorhanden.
4. Entfernen Sie die Lösung *Key Vault Analytics (veraltet)* . Wenn Sie PowerShell einsetzen, verwenden Sie `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`.

Daten, die vor der Änderung gesammelt wurden, werden in der neuen Lösung nicht angezeigt. Sie können die Abfrage dieser Daten mithilfe des alten Typs und der alten Feldnamen fortsetzen.

## <a name="troubleshooting"></a>Problembehandlung
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie [Protokollabfragen in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md), um detaillierte Azure Key Vault-Daten anzuzeigen.
