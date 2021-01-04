---
title: Verwenden von kundenseitig verwalteten Speicherkonten in Azure Monitor Log Analytics
description: Verwenden Sie Ihr eigenes Speicherkonto für Log Analytics-Szenarios.
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: f221237bee441ec78d726dabf476d1085a27071d
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095303"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Verwenden von kundenseitig verwalteten Speicherkonten in Azure Monitor Log Analytics

Log Analytics verwendet Azure Storage für eine Vielzahl von Szenarios. Dies wird in der Regel automatisch verwaltet. In einigen Fällen ist jedoch erforderlich, dass Sie Ihr eigenes Speicherkonto bereitstellen und verwalten. Solche Konten werden auch als kundenseitig verwaltete Speicherkonten bezeichnet. In diesem Dokument werden die Verwendung von kundenseitig verwaltetem Speicher für die Erfassung von WAD- und LAD-Protokollen, Private Link-spezifische Szenarios und die Verschlüsselung mit kundenseitig verwalteten Schlüsseln (Customer-Managed Keys, CMKs) ausführlich erläutert. 

> [!NOTE]
> Es wird empfohlen, keine Abhängigkeiten von den Inhalten zu verwenden, die Log Analytics in den kundenseitig verwalteten Speicher hochlädt, da sich Formatierung und Inhalt ändern können.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Erfassen von Azure-Diagnose-Erweiterungsprotokollen (WAD/LAD)
Die Erweiterungs-Agents für die Azure-Diagnose (auch WAD für Windows-Agents und LAD für Linux-Agents) erfassen verschiedene Betriebssystemprotokolle und speichern sie in einem kundenseitig verwalteten Speicherkonto. Anschließend können Sie diese Protokolle in Log Analytics importieren, um sie zu überprüfen und zu analysieren.
So erfassen Sie Azure-Diagnose-Erweiterungsprotokolle in Ihrem Speicherkonto: Verbinden Sie das Speicherkonto als Speicherdatenquelle über das [Azure-Portal](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) oder durch Aufrufen der [Storage Insights-API](/rest/api/loganalytics/storage%20insights/createorupdate) mit Ihrem Log Analytics-Arbeitsbereich.

Unterstützte Datentypen:
* syslog
* Windows-Ereignisse
* Service Fabric
* ETW-Ereignisse
* IIS-Protokolle

## <a name="using-private-links"></a>Verwenden von privaten Verbindungen
Kundenseitig verwaltete Speicherkonten sind bei einigen Anwendungsfällen erforderlich, wenn private Verbindungen verwendet werden, um eine Verbindung mit Azure Monitor-Ressourcen herzustellen. Ein solcher Fall ist die Erfassung von benutzerdefinierten Protokollen oder IIS-Protokollen. Diese Datentypen werden zunächst als Blobs in ein zwischengeschaltetes Azure Storage-Konto hochgeladen und dann erst in einen Arbeitsbereich importiert. Einige Azure Monitor-Lösungen verwenden möglicherweise ebenfalls Speicherkonten, um große Dateien (z. B. Watson-Speicherabbilddateien) zu speichern, die von der Azure Security Center-Lösung verwendet werden. 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>Private Link-Szenarios, die einen kundenseitig verwalteten Speicher erfordern
* Erfassung von benutzerdefinierten Protokollen und IIS-Protokollen
* Zulassen des Sammelns von Watson-Speicherabbilddateien durch die ASC-Lösung

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>Verwenden eines kundenseitig verwalteten Speicherkontos über eine Private Link-Instanz
##### <a name="workspace-requirements"></a>Arbeitsbereichsanforderungen
Beim Herstellen einer Verbindung mit Azure Monitor über eine private Verbindung können Log Analytics-Agents nur Protokolle an Arbeitsbereiche senden, die über eine private Verbindung mit Ihrem Netzwerk verknüpft sind. Diese Regel erfordert, dass Sie ein Azure Monitor Private Link-Bereichsobjekt (Azure Monitor Private Link Scope, AMPLS) ordnungsgemäß konfigurieren, es mit Ihren Arbeitsbereichen verbinden und dann den Azure Monitor Private Link-Bereich über eine private Verbindung mit Ihrem Netzwerk verbinden. Weitere Informationen zur AMPLS-Konfiguration finden Sie unter [Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Monitor](./private-link-security.md). 
##### <a name="storage-account-requirements"></a>Anforderungen an das Speicherkonto
Damit das Speicherkonto erfolgreich eine Verbindung mit Ihrer Private Link-Instanz herstellen kann, muss Folgendes der Fall sein:
* Das Speicherkonto muss sich in Ihrem VNet oder einem über Peering damit verbundenen Netzwerk befinden und über eine private Verbindung mit Ihrem VNet verbunden sein. Dies ermöglicht es Agents in Ihrem VNet, Protokolle an das Speicherkonto senden.
* Es muss sich in derselben Region befinden wie der Arbeitsbereich, mit dem es verknüpft ist.
* Es muss den Zugriff auf das Speicherkonto durch Azure Monitor zulassen. Wenn Sie nur ausgewählte Netzwerke auf Ihr Speicherkonto zugreifen lassen, sollten Sie außerdem die folgende Ausnahme aktivieren: „Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“. Dadurch kann Log Analytics die in diesem Speicherkonto erfassten Protokolle lesen.
* Wenn Ihr Arbeitsbereich auch Datenverkehr aus anderen Netzwerken verarbeitet, sollten Sie das Speicherkonto so konfigurieren, dass eingehender Datenverkehr aus diesen Netzwerken/dem Internet zugelassen wird.

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>Verknüpfen des Speicherkontos mit einem Log Analytics-Arbeitsbereich
Sie können Ihr Speicherkonto über die [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) oder die [REST-API](/rest/api/loganalytics/linkedstorageaccounts) mit dem Arbeitsbereich verknüpfen. Verwendbare Werte für dataSourceType:
* „CustomLogs“, um den Speicher für die Erfassung von benutzerdefinierten Protokollen und IIS-Protokollen zu verwenden
* „AzureWatson“, um den Speicher für Watson-Speicherabbilddateien zu verwenden, die von der ASC-Lösung (Azure Security Center) hochgeladen wurden Weitere Informationen zum Verwalten der Datenaufbewahrung, zum Ersetzen eines verknüpften Speicherkontos und zum Überwachen der Speicherkontoaktivität finden Sie unter [Verwalten von verknüpften Speicherkonten](#managing-linked-storage-accounts). 

## <a name="encrypting-data-with-cmk"></a>Verschlüsseln von Daten mit kundenseitig verwalteten Schlüsseln
Azure Storage verschlüsselt alle ruhenden Daten in einem Speicherkonto. Standardmäßig verschlüsselt der Dienst Daten mit von Microsoft verwalteten Schlüsseln (Microsoft-Managed Key, MMK). Azure Storage lässt Sie jedoch alternativ kundenseitig verwaltete Schlüssel (CMKs) aus Azure Key Vault für das Verschlüsseln Ihrer Speicherdaten verwenden. Sie können entweder Ihre eigenen Schlüssel in Azure Key Vault importieren oder Schlüssel über die Azure Key Vault-APIs generieren.
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>CMK-Szenarios, die einen kundenseitig verwalteten Speicher erfordern
* Verschlüsseln von Protokollwarnungsabfragen mit CMKs
* Verschlüsseln von gespeicherten Abfragen mit CMKs

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Anwenden von CMKs auf kundenseitig verwaltete Speicherkonten
##### <a name="storage-account-requirements"></a>Anforderungen an das Speicherkonto
Das Speicherkonto und der Schlüsseltresor müssen sich in derselben Region befinden, dürfen aber zu verschiedenen Abonnements gehören. Weitere Informationen zur Azure Storage-Verschlüsselung und -Schlüsselverwaltung finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Anwenden von CMKs auf Ihre Speicherkonten
Verwenden Sie das [Azure-Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) oder die [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), um Ihr Azure Storage-Konto für die Verwendung von kundenseitig verwalteten Schlüsseln mit Azure Key Vault zu konfigurieren. 

## <a name="managing-linked-storage-accounts"></a>Verwalten von verknüpften Speicherkonten

Verwenden Sie zum Verknüpfen oder Aufheben der Verknüpfung von Speicherkonten mit Ihrem Arbeitsbereich die [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) oder die [REST-API](/rest/api/loganalytics/linkedstorageaccounts).

##### <a name="create-or-modify-a-link"></a>Erstellen oder Ändern einer Verknüpfung
Wenn Sie ein Speicherkonto mit einem Arbeitsbereich verknüpfen, verwendet Log Analytics ab dann dieses Speicherkonto anstelle des Speicherkontos des Diensts. Sie haben folgende Möglichkeiten: 
* Registrieren Sie mehrere Speicherkonten, um die Last durch Protokolle auf sie zu verteilen.
* Verwenden Sie dasselbe Speicherkonto für mehrere Arbeitsbereiche.

##### <a name="unlink-a-storage-account"></a>Aufheben der Verknüpfung eines Speicherkontos
Wenn Sie ein Speicherkonto nicht mehr verwenden möchten, müssen Sie die Verknüpfung des Speichers mit dem Arbeitsbereich aufheben. Das Aufheben der Verknüpfung aller Speicherkonten mit einem Arbeitsbereich führt dazu, dass Log Analytics versucht, dienstseitig verwaltete Speicherkonten zu verwenden. Wenn Ihr Netzwerk nur eingeschränkt auf das Internet zugreifen kann, sind diese Speicher möglicherweise nicht verfügbar, und bei allen Szenarios, die Speicher benötigen, tritt ein Fehler auf.

##### <a name="replace-a-storage-account"></a>Ersetzen eines Speicherkontos
So ersetzen Sie ein für die Erfassung verwendetes Speicherkonto:
1.  **Erstellen Sie eine Verknüpfung mit einem neuen Speicherkonto.** Die Protokollierungs-Agents rufen die aktualisierte Konfiguration ab und beginnen damit, Daten auch an den neuen Speicher zu senden. Dies kann einige Minuten dauern.
2.  **Heben Sie dann die Verknüpfung mit dem alten Speicherkonto auf, sodass die Agents nicht mehr in das entfernte Konto schreiben.** Der Erfassungsprozess liest weiterhin Daten aus diesem Konto, bis sämtliche Daten erfasst wurden. Löschen Sie das alte Speicherkonto erst, wenn Sie sicher sind, dass alle Protokolle erfasst wurden.

### <a name="maintaining-storage-accounts"></a>Verwalten von Speicherkonten
##### <a name="manage-log-retention"></a>Verwalten der Protokollaufbewahrung
Wenn Sie Ihr eigenes Speicherkonto verwenden, sind Sie für die Aufbewahrung verantwortlich. Anders ausgedrückt: Log Analytics löscht keine Protokolle, die in Ihrem privaten Speicher gespeichert sind. Stattdessen sollten Sie eine Richtlinie einrichten, um die Last wie gewünscht zu verarbeiten.

##### <a name="consider-load"></a>Berücksichtigung der Last
Speicherkonten können eine bestimmte Last durch Lese- und Schreibanforderungen verarbeiten, bevor sie mit der Drosselung von Anforderungen beginnen. (Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](../../storage/common/scalability-targets-standard-account.md).) Diese Drosselung wirkt sich auf die für die Erfassung von Protokollen benötigte Zeit aus. Wenn Ihr Speicherkonto überlastet ist, registrieren Sie ein zusätzliches Speicherkonto, um die Last auf die beiden Konten zu verteilen. Sehen Sie sich zur Überwachung der Kapazität und Leistung Ihres Speicherkontos die entsprechenden [Erkenntnisse im Azure-Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview) an.

### <a name="related-charges"></a>Anfallende Gebühren
Die Gebühren für Speicherkonten werden anhand der Menge der gespeicherten Daten, des Speichertyps und des Redundanztyps berechnet. Details dazu finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs) und [Azure Table Storage – Preise](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Monitor](private-link-security.md).
- Informieren Sie sich über [kundenseitig verwaltete Schlüssel in Azure Monitor](customer-managed-keys.md).
