---
title: Kundenseitig verwaltete Schlüssel in Azure Monitor
description: Informationen und Schritte zum Konfigurieren kundenseitig verwalteter Schlüssel für das Verschlüsseln von Daten in Ihren Log Analytics-Arbeitsbereichen mithilfe eines Azure Key Vault-Schlüssels.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 11/18/2020
ms.openlocfilehash: 17648b9bc973285764bb0bd6242506122a043780
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454254"
---
# <a name="azure-monitor-customer-managed-key"></a>Kundenseitig verwaltete Schlüssel in Azure Monitor 

Dieser Artikel enthält Hintergrundinformationen und Schritte zum Konfigurieren kundenseitig verwalteter Schlüssel für Ihre Log Analytics-Arbeitsbereiche. Nach der Konfiguration werden alle Daten, die an Ihre Arbeitsbereiche gesendet werden, mit Ihrem Azure Key Vault-Schlüssel verschlüsselt.

Es wird empfohlen, vor der Konfiguration die [Einschränkungen](#limitationsandconstraints) weiter unten zu überprüfen.

## <a name="customer-managed-key-overview"></a>Übersicht über kundenseitig verwaltete Schlüssel

Die [Verschlüsselung ruhender Daten](../../security/fundamentals/encryption-atrest.md) ist eine übliche Datenschutz- und Sicherheitsanforderung in Organisationen. Sie können die Verschlüsselung ruhender Daten vollständig von Azure verwalten lassen, wobei Ihnen verschiedene Optionen zum genauen Verwalten der Verschlüsselung und Verschlüsselungsschlüssel bereitstehen.

Mit Azure Monitor wird sichergestellt, dass alle Daten und gespeicherten Abfragen im Ruhezustand mit von Microsoft verwalteten Schlüsseln (MMK) verschlüsselt werden. Azure Monitor bietet auch eine Option für die Verschlüsselung mithilfe eines eigenen Schlüssels, der in Ihrer [Azure Key Vault](../../key-vault/general/overview.md)-Instanz gespeichert ist und Ihnen die Kontrolle darüber gibt, den Zugriff auf Ihre Daten jederzeit zu widerrufen. Die Verwendung der Verschlüsselung durch Azure Monitor entspricht der Funktionsweise der [Azure Storage-Verschlüsselung](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption).

Der kundenseitig verwaltete Schlüssel wird auf dedizierten Log Analytics-Clustern bereitgestellt, die eine höhere Schutzebene und Kontrolle bieten. In dedizierten Clustern erfasste Daten werden zweimal verschlüsselt: einmal auf der Dienstebene mit von Microsoft verwalteten Schlüsseln oder kundenseitig verwalteten Schlüsseln und einmal auf der Infrastrukturebene mit zwei verschiedenen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln. Die [doppelte Verschlüsselung](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) schützt vor dem Szenario, dass einer der Verschlüsselungsalgorithmen oder Schlüssel kompromittiert wurde. In diesem Fall werden die Daten weiterhin durch die zusätzliche Verschlüsselungsebene geschützt. Ein dedizierter Cluster ermöglicht Ihnen außerdem das Schützen Ihrer Daten mit [Lockbox](#customer-lockbox-preview).

Daten, die in den letzten 14 Tagen erfasst wurden, werden für einen effizienten Betrieb der Abfrage-Engine auch im Hot-Cache (SSD-gestützt) aufbewahrt. Diese Daten bleiben unabhängig von der Konfiguration kundenseitig verwalteter Schlüssel mit Microsoft-Schlüsseln verschlüsselt, aber Ihre Kontrolle über SSD-Daten entspricht der [Schlüsselsperrung](#key-revocation). Wir arbeiten daran, dass SSD-Daten in der ersten Hälfte des Jahres 2021 mit kundenseitig verwalteten Schlüsseln verschlüsselt werden.

Beim [Preismodell für Log Analytics-Cluster](./manage-cost-storage.md#log-analytics-dedicated-clusters) werden Kapazitätsreservierungen ab 1000 GB/Tag verwendet.

> [!IMPORTANT]
> Aufgrund temporärer Kapazitätseinschränkungen ist vor dem Erstellen eines Clusters eine Vorabregistrierung erforderlich. Verwenden Sie Ihre Kontakte zu Microsoft, oder öffnen Sie eine Supportanfrage zum Registrieren Ihrer Abonnements-IDs.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Funktionsweise von kundenseitig verwalteten Schlüsseln in Azure Monitor

Azure Monitor nutzt eine systemseitig zugewiesene verwaltete Identität, um Zugriff auf Azure Key Vault zu gewähren. Eine systemseitig zugewiesene verwaltete Identität kann nur einer einzigen Azure-Ressource zugeordnet werden, während die Identität des Log Analytics Clusters auf Clusterebene unterstützt wird. Dadurch ist vorgegeben, dass die Funktion auf einem dedizierten Log Analytics-Cluster bereitgestellt wird. Zur Unterstützung von kundenseitig verwalteten Schlüsseln in mehreren Arbeitsbereichen wird eine neue Log Analytics-*Clusterressource* als temporäre Identitätsverbindung zwischen Key Vault und Log Analytics-Arbeitsbereichen eingesetzt. Der Log Analytics-Clusterspeicher verwendet die der *Clusterressource* zugeordnete verwaltete Identität für die Authentifizierung bei Azure Key Vault über Azure Active Directory. 

Nach der Konfiguration werden alle Daten, die in die mit Ihrem dedizierten Cluster verknüpften Arbeitsbereiche eingegeben werden, mit Ihrem Schlüssel in Key Vault verschlüsselt. Sie können die Verknüpfung von Arbeitsbereichen mit dem Cluster jederzeit aufheben. Neue Daten werden dann im Log Analytics-Speicher erfasst und mit dem Microsoft-Schlüssel verschlüsselt, während Sie die neuen und alten Daten nahtlos abfragen können.


![Übersicht über kundenseitig verwaltete Schlüssel](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics-*Clusterressource* mit verwalteter Identität und Berechtigungen für Key Vault. Die Identität wird an den zugrunde liegenden dedizierten Log Analytics-Clusterspeicher weitergegeben.
3. Dedizierter Log Analytics-Cluster
4. Mit der *Clusterressource* verknüpfte Arbeitsbereiche 

## <a name="encryption-keys-operation"></a>Vorgang für Verschlüsselungsschlüssel

An der Speicherdatenverschlüsselung sind drei Arten von Schlüsseln beteiligt:

- **KEK** (Key Encryption Key): Schlüsselverschlüsselungsschlüssel (Ihr kundenseitig verwalteter Schlüssel)
- **AEK** (Account Encryption Key): Kontoverschlüsselungsschlüssel
- **DEK** (Data Encryption Key): Datenverschlüsselungsschlüssel

Es gelten die folgenden Regeln:

- Die Speicherkonten im Log Analytics-Cluster generieren einen eindeutigen Verschlüsselungsschlüssel für jedes Speicherkonto, der als AEK bezeichnet wird.
- Der AEK dient zum Ableiten von DEKs, bei denen es sich um die Schlüssel handelt, die zum Verschlüsseln der einzelnen, auf den Datenträger geschriebenen Datenblöcke verwendet werden.
- Wenn Sie den Schlüssel in Key Vault konfigurieren und im Cluster darauf verweisen, sendet Azure Storage Anforderungen an Azure Key Vault, um den AEK zu packen und zu entpacken und so Verschlüsselungs- und Entschlüsselungsvorgänge für Daten auszuführen.
- Der KEK verlässt niemals den Key Vault, und im Fall eines HSM-Schlüssels verlässt dieser niemals die Hardware.
- Azure Storage verwendet die der *Clusterressource* zugeordnete verwaltete Identität für die Authentifizierung und den Zugriff auf Azure Key Vault über Azure Active Directory.

## <a name="customer-managed-key-provisioning-procedure"></a>Bereitstellungsverfahren für kundenseitig verwalteten Schlüssel

1. Registrieren Ihres Abonnements, um Clustererstellung zuzulassen
1. Erstellen von Azure Key Vault und Speichern des Schlüssels
1. Erstellen eines Clusters
1. Gewähren von Berechtigungen für Key Vault
1. Verknüpfen von Log Analytics-Arbeitsbereichen

Die Konfiguration kundenseitig verwalteter Schlüssel wird im Azure-Portal nicht unterstützt, und die Bereitstellung erfolgt über [PowerShell](/powershell/module/az.operationalinsights/)-, [CLI](/cli/azure/monitor/log-analytics)- oder [REST](/rest/api/loganalytics/)-Anforderungen.

### <a name="asynchronous-operations-and-status-check"></a>Asynchrone Vorgänge und Statusüberprüfung

Einige Konfigurationsschritte werden asynchron ausgeführt, da sie nicht schnell abgeschlossen werden können. Der `status` in der Antwort enthält eine der folgenden Angaben: „Wird ausgeführt“, „Wird aktualisiert“, „Wird gelöscht“, „Erfolgreich“ oder „Fehler“ einschließlich des Fehlercodes.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

–

# <a name="powershell"></a>[PowerShell](#tab/powershell)

–

# <a name="rest"></a>[REST](#tab/rest)

Wenn REST verwendet und akzeptiert wird, gibt die Antwort anfänglich den HTTP-Statuscode 200 (OK) und einen Header mit der Eigenschaft *Azure-AsyncOperation* zurück:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Sie können den Status des asynchronen Vorgangs prüfen, indem Sie eine GET-Anforderung für den Headerwert *Azure-AsyncOperation* senden:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

### <a name="allowing-subscription"></a>Zulassen eines Abonnements

> [!IMPORTANT]
> Die Funktion für kundenseitig verwaltete Schlüssel ist regional. Azure Key Vault, Cluster und verknüpfte Log Analytics-Arbeitsbereiche müssen sich in der gleichen Region befinden, können jedoch in unterschiedlichen Abonnements enthalten sein.

### <a name="storing-encryption-key-kek"></a>Speichern des Verschlüsselungsschlüssels (KEK)

Erstellen Sie eine Azure Key Vault-Instanz oder verwenden Sie eine vorhandene Instanz, um einen Schlüssel zu generieren oder importieren, der für die Datenverschlüsselung verwendet werden soll. Azure Key Vault muss als wiederherstellbar konfiguriert werden, um Ihren Schlüssel und den Zugriff auf Ihre Daten in Azure Monitor zu schützen. Sie können diese Konfiguration unter den Eigenschaften in Ihrer Key Vault-Instanz überprüfen. Sowohl *Vorläufiges Löschen* als auch *Bereinigungsschutz* sollten aktiviert sein.

![Einstellungen für vorläufiges Löschen und Löschschutz](media/customer-managed-keys/soft-purge-protection.png)

Diese Einstellungen können in Key Vault über die CLI und PowerShell aktualisiert werden:

- [Vorläufiges Löschen](../../key-vault/general/soft-delete-overview.md)
- [Bereinigungsschutz](../../key-vault/general/soft-delete-overview.md#purge-protection) schützt vor erzwungenem Löschen des Geheimnisses/Tresors auch nach vorläufigem Löschen

### <a name="create-cluster"></a>Cluster erstellen

Folgen Sie dem im [Artikel zu dedizierten Clustern](../log-query/logs-dedicated-clusters.md#creating-a-cluster) beschriebenen Verfahren. 

> [!IMPORTANT]
> Kopieren und speichern Sie die Antwort, da Sie die Detail in den nächsten Schritten benötigen werden.

### <a name="grant-key-vault-permissions"></a>Erteilen von Key Vault-Berechtigungen

Erstellen Sie eine Zugriffsrichtlinie in Key Vault, um Berechtigungen für Ihren Cluster zu erteilen. Diese Berechtigungen werden von der zugrunde liegenden Azure Monitor Storage-Instanz für die Datenverschlüsselung verwendet. Öffnen Sie Ihre Key Vault-Instanz im Azure-Portal, und klicken Sie auf „Zugriffsrichtlinien“ und dann auf „+ Zugriffsrichtlinie hinzufügen“, um eine Richtlinie mit den folgenden Einstellungen zu erstellen:

- Schlüsselberechtigungen: Wählen Sie die Berechtigungen „Abrufen“, „Schlüssel packen“ und „Schlüssel entpacken“ aus.
- Prinzipal auswählen: Geben Sie den Namen des Clusters oder den Wert von „principal-id“ ein, der in der Antwort des vorherigen Schritts zurückgegeben wurde.

![Erteilen von Key Vault-Berechtigungen](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Die Berechtigung *Abrufen* ist erforderlich, damit sichergestellt werden kann, dass Key Vault als wiederherstellbar konfiguriert ist, um Ihren Schlüssel und den Zugriff auf Ihre Azure Monitor-Daten zu schützen.

### <a name="update-cluster-with-key-identifier-details"></a>Aktualisieren des Clusters mit Schlüsselbezeichnerdetails

Alle Vorgänge im Cluster erfordern die Berechtigung für die Aktion „Microsoft.OperationalInsights/clusters/write“. Diese Berechtigung kann über den Besitzer oder einen Mitwirkenden erteilt werden, der die Aktion „ */write“ enthält, oder über die Rolle „Log Analytics-Mitwirkender“, die die Aktion „Microsoft.OperationalInsights/* “ enthält.

Mit diesem Schritt wird Azure Monitor Storage über den neuen Schlüssel und die Version informiert, die für die Datenverschlüsselung verwendet werden soll. Nach der Aktualisierung wird der neue Schlüssel zum Packen und Entpacken des Speicherschlüssels (AEK) verwendet.

Wählen Sie in Azure Key Vault die aktuelle Version Ihres Schlüssels aus, um die Schlüsselbezeichnerdetails abzurufen.

![Erteilen von Key Vault-Berechtigungen](media/customer-managed-keys/key-identifier-8bit.png)

Aktualisieren Sie „KeyVaultProperties“ im Cluster mit Schlüsselbezeichnerdetails.

Der Vorgang ist asynchron und kann einige Zeit in Anspruch nehmen.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Antwort**

Die Weitergabe des Schlüsselbezeichners dauert einige Minuten. Sie können den Aktualisierungsstatus auf zwei Arten überprüfen:
1. Kopieren Sie den URL-Wert von „Azure-AsyncOperation“ aus der Antwort, und befolgen Sie die [Überprüfung des Status asynchroner Vorgänge](#asynchronous-operations-and-status-check).
2. Senden Sie eine GET-Anforderung für den Cluster, und überprüfen Sie die Eigenschaften *KeyVaultProperties*. Die zuletzt aktualisierten Schlüsselbezeichnerdetails sollten in der Antwort zurückgegeben werden.

Nachdem die Aktualisierung des Schlüsselbezeichners abgeschlossen ist, sollte die Antwort auf die GET-Anforderung wie folgt aussehen: 200 OK und Header
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

### <a name="link-workspace-to-cluster"></a>Verknüpfen von Arbeitsbereich und Cluster

Sie müssen über Schreibberechtigungen (write) sowohl für Ihren Arbeitsbereich als auch für den Cluster verfügen, um diesen Vorgang auszuführen. Dies umfasst die folgenden Aktionen:

- Für den Arbeitsbereich: Microsoft.OperationalInsights/workspaces/write
- Im Cluster: Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Dieser Schritt sollte nur nach Abschluss der Log Analytics-Clusterbereitstellung ausgeführt werden. Wenn Sie vor der Bereitstellung Arbeitsbereiche verknüpfen und Daten erfassen, werden die erfassten Daten gelöscht und können nicht wiederhergestellt werden.

Dieser Vorgang ist asynchron und kann einige Zeit in Anspruch nehmen.

Folgen Sie dem im [Artikel zu dedizierten Clustern](../log-query/logs-dedicated-clusters.md#link-a-workspace-to-the-cluster) beschriebenen Verfahren.

## <a name="key-revocation"></a>Schlüsselsperrung

Sie können den Zugriff auf Daten widerrufen, indem Sie den Schlüssel deaktivieren oder die Zugriffsrichtlinie für den Cluster in Ihrer Key Vault-Instanz löschen. Im Log Analytics-Clusterspeicher werden Änderungen der Schlüsselberechtigungen immer innerhalb einer Stunde oder früher berücksichtigt, und der Speicher steht dann nicht mehr zur Verfügung. Alle neuen Daten, die in Arbeitsbereichen erfasst wurden, die mit Ihrem Cluster verknüpft sind, werden gelöscht und können nicht wiederhergestellt werden, auf die Daten kann nicht zugegriffen werden, und Abfragen an diese Arbeitsbereiche schlagen fehl. Zuvor erfasste Daten verbleiben im Speicher, solange der Cluster und Ihre Arbeitsbereiche nicht gelöscht werden. Daten, auf die nicht zugegriffen werden kann, unterliegen der Datenaufbewahrungsrichtlinie und werden bereinigt, sobald der Aufbewahrungszeitraum abgelaufen ist. 

Die in den letzten 14 Tagen erfassten Daten werden für einen effizienten Betrieb der Abfrage-Engine auch im Hot-Cache (SSD-gestützt) aufbewahrt. Diese werden beim Schlüsselsperrungsvorgang gelöscht, und es kann dann ebenfalls nicht mehr darauf zugegriffen werden.

Storage fragt Key Vault in regelmäßigen Abständen ab und versucht, den Verschlüsselungsschlüssel zu entpacken. Sobald der Zugriff erfolgt, werden Datenerfassung und Abfrage innerhalb von 30 Minuten fortgesetzt.

## <a name="key-rotation"></a>Schlüsselrotation

Die Rotation des kundenseitig verwalteten Schlüssels erfordert eine explizite Aktualisierung des Clusters mit der neuen Schlüsselversion in Azure Key Vault. Befolgen Sie die Anweisungen im Schritt „Aktualisieren des Clusters mit Schlüsselbezeichnerdetails“. Wenn Sie die neuen Schlüsselbezeichnerdetails im Cluster nicht aktualisieren, verwendet der Log Analytics-Clusterspeicher weiterhin den vorherigen Schlüssel zur Verschlüsselung. Wenn Sie den alten Schlüssel deaktivieren oder löschen, bevor Sie den neuen Schlüssel im Cluster aktualisieren, wird der Status der [Schlüsselsperrung](#key-revocation) aktiv.

Nach der Schlüsselrotation kann auf alle Ihre Daten weiter zugegriffen werden, da Daten immer mit dem Kontoverschlüsselungsschlüssel (Account Encryption Key, AEK) verschlüsselt werden, während AEK nun mit der neuen Version des Schlüsselverschlüsselungsschlüssels (Key Encryption Key, KEK) in Key Vault verschlüsselt wird.

## <a name="customer-managed-key-for-queries"></a>Kundenseitig verwalteter Schlüssel für Abfragen

Die in Log Analytics verwendete Abfragesprache ist ausdrucksstark und kann vertrauliche Informationen in Kommentaren enthalten, die Sie Abfragen oder in der Abfragesyntax hinzufügen. Einige Organisationen verlangen, dass diese Informationen als Teil der Richtlinie für kundenseitig verwaltete Schlüssel geschützt werden, und Sie müssen Ihre Abfragen mit Ihrem Schlüssel verschlüsselt speichern. Azure Monitor ermöglicht Ihnen das Speichern von Abfragen für *gespeicherter Suchvorgänge* und *Protokollwarnungen* mit Verschlüsselung mit Ihrem Schlüssel in Ihrem eigenen Speicherkonto, sofern Sie mit Ihrem Arbeitsbereich verbunden sind. 

> [!NOTE]
> Log Analytics-Abfragen können je nach verwendetem Szenario in verschiedenen Speichern abgelegt werden. Abfragen bleiben unabhängig von der Konfiguration kundenseitig verwalteter Schlüssel in den folgenden Szenarien mit von Microsoft verwalteten Schlüsseln (MMK) verschlüsselt: Arbeitsmappen in Azure Monitor, Azure-Dashboards, Azure-Logik-App, Azure Notebooks und Automation Runbooks.

Wenn Sie Bring Your Own Storage (BYOS) verwenden und es mit Ihrem Arbeitsbereich verknüpfen, lädt der Dienst Abfragen für *gespeicherte Suchen* und *Protokollwarnungen* in Ihr Speicherkonto hoch. Dies bedeutet, dass Sie das Speicherkonto und die [Richtlinie für die Verschlüsselung ruhender Daten](../../storage/common/customer-managed-keys-overview.md) entweder mit dem gleichen Schlüssel steuern, den Sie zum Verschlüsseln von Daten im Log Analytics-Cluster verwenden, oder mit einem anderen Schlüssel. Sie sind jedoch auch für die mit diesem Speicherkonto verbundenen Kosten verantwortlich. 

**Überlegungen vor dem Festlegen des kundenseitig verwalteten Schlüssels für Abfragen**
* Sie müssen für Ihren Arbeitsbereich und das Speicherkonto über die Berechtigung „Schreiben“ verfügen.
* Stellen Sie sicher, dass Sie Ihr Speicherkonto in derselben Region erstellen, in der sich Ihr Log Analytics-Arbeitsbereich befindet.
* Die *gespeicherten Suchvorgänge* im Speicher werden als Dienstartefakte angesehen und ihr Format kann sich ändern.
* Vorhandene *gespeicherte Suchvorgänge* werden aus dem Arbeitsbereich entfernt. Kopieren Sie alle *gespeicherten Suchvorgänge*, die Sie vor der Konfiguration benötigen. Sie können Ihre *gespeicherten Suchvorgänge* mithilfe von [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) anzeigen.
* Der Abfrageverlauf wird nicht unterstützt, und Sie können keine Abfragen sehen, die Sie ausgeführt haben.
* Sie können mit dem Arbeitsbereich ein einzelnes Speicherkonto zum Speichern von Abfragen verknüpfen, es kann aber sowohl für Abfragen für *gespeicherte Suchvorgänge* als auch *Protokollwarnungen* verwendet werden.
* Anheften an das Dashboard wird nicht unterstützt.

**Konfigurieren von BYOS für Abfragen für gespeicherte Suchvorgänge**

Verknüpfen Sie das Speicherkonto für *Abfragen* mit Ihrem Arbeitsbereich. Abfragen für *gespeicherte Suchvorgänge* werden in Ihrem Speicherkonto gespeichert. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Nach der Konfiguration werden alle neuen Abfragen *gespeicherter Suchvorgänge* in Ihrem Speicher gespeichert.

**Konfigurieren von BYOS für Abfragen für Protokollwarnungen**

Verknüpfen Sie das Speicherkonto für *Warnungen* mit Ihrem Arbeitsbereich. Abfragen für *Protokollwarnungen* werden in Ihrem Speicherkonto gespeichert. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Nach der Konfiguration werden alle neuen Warnungsabfragen in Ihrem Speicher gespeichert.

## <a name="customer-lockbox-preview"></a>Kunden-Lockbox (Vorschau)

Lockbox bietet Ihnen die Möglichkeit, die Anforderung eines Microsoft-Technikers für den Zugriff auf Ihre Daten während einer Supportanfrage zu genehmigen oder abzulehnen.

In Azure Monitor haben Sie diese Kontrolle über Daten in Arbeitsbereichen, die mit Ihrem dedizierten Log Analytics-Cluster verknüpft sind. Die Lockbox-Kontrolle gilt für Daten, die in einem dedizierten Log Analytics-Cluster gespeichert sind, wo sie in Speicherkonten des Clusters unter Ihrem Lockbox-geschützten Abonnement isoliert aufbewahrt werden.  

Weitere Informationen finden Sie unter [Kunden-Lockbox für Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md).

## <a name="customer-managed-key-operations"></a>Vorgänge für kundenseitig verwaltete Schlüssel

- **Abrufen aller Cluster in einer Ressourcengruppe**
  
  # <a name="azure-portal"></a>[Azure portal](#tab/portal)

  –

  # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster list --resource-group "resource-group-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Antwort**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

  ---

- **Abrufen aller Cluster in einem Abonnement**

  # <a name="azure-portal"></a>[Azure portal](#tab/portal)

  –

  # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster list
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Antwort**
    
  Dieselbe Antwort wie bei „Cluster in einer Ressourcengruppe“, jedoch im Abonnementbereich.

  ---

- **Aktualisieren der *Kapazitätsreservierung* in einem Cluster**

  Wenn sich das Datenvolumen Ihrer verknüpften Arbeitsbereiche im Laufe der Zeit ändert und Sie die Kapazitätsreservierungsebene entsprechend aktualisieren möchten. Führen Sie die Schritte zum [Aktualisieren des Clusters](#update-cluster-with-key-identifier-details) aus, und geben Sie den neuen Kapazitätswert an. Dieser kann im Bereich von 1000 bis 3000 GB pro Tag liegen und in 100er Schritten aktualisiert werden. Wenn Sie mehr als 3000 GB pro Tag benötigen, wenden Sie sich an Ihren Microsoft-Kontakt. Beachten Sie, dass Sie nicht den vollständigen REST-Anforderungstext angeben müssen, aber die SKU einschließen sollten:

  # <a name="azure-portal"></a>[Azure portal](#tab/portal)

  –

  # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity daily-ingestion-gigabyte
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity daily-ingestion-gigabyte
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": daily-ingestion-gigabyte
    }
  }
  ```

  ---

- **Aktualisieren von *billingType* im Cluster**

  Die Eigenschaft *billingType* bestimmt die Abrechnungszuordnung für den Cluster und dessen Daten:
  - *Cluster* (Standard): Die Abrechnung wird dem Abonnement zugeordnet, das Ihre Clusterressource hostet.
  - *Arbeitsbereiche*: Die Abrechnung wird den Abonnements zugeordnet, die Ihre Arbeitsbereiche proportional hosten.
  
  Führen Sie die Schritte zum [Aktualisieren des Clusters](#update-cluster-with-key-identifier-details) aus, und geben Sie den neuen Wert für „billingType“ an. Beachten Sie, dass Sie nicht den vollständigen REST-Anforderungstext angeben müssen und *billingType* einschließen sollten:

  # <a name="azure-portal"></a>[Azure portal](#tab/portal)

  –

  # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

  –

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  –

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

  ---

- **Aufheben der Verknüpfung mit dem Arbeitsbereich**

  Sie brauchen Schreibberechtigungen für den Arbeitsbereich und den Cluster, um diesen Vorgang auszuführen. Sie können die Verknüpfung eines Arbeitsbereichs mit Ihrem Cluster jederzeit aufheben. Neue erfasste Daten werden nach dem Aufheben der Verknüpfung im Log Analytics-Speicher gespeichert und mit dem Microsoft-Schlüssel verschlüsselt. Sie können Daten, die vor und nach dem Aufheben der Verknüpfung im Arbeitsbereich erfasst wurden, nahtlos abfragen, solange der Cluster mit gültigem Key Vault-Schlüssel bereitgestellt und konfiguriert wurde.

  Dieser Vorgang ist asynchron und kann einige Zeit in Anspruch nehmen.

  # <a name="azure-portal"></a>[Azure portal](#tab/portal)

  –

  # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --name "cluster-name" --workspace-name "workspace-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---

- **Überprüfen des Verknüpfungsstatus des Arbeitsbereichs**
  
  Führen Sie den Get-Vorgang für den Arbeitsbereich aus, und beobachten Sie, ob die *clusterResourceId*-Eigenschaft unter *features* in der Antwort vorhanden ist. Der verknüpfte Arbeitsbereich verfügt über die *clusterResourceId*-Eigenschaft.

  # <a name="azure-portal"></a>[Azure portal](#tab/portal)

  –

  # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

   ```rest
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---

- **Löschen des Clusters**

  Sie brauchen Schreibberechtigungen für den Cluster, um diesen Vorgang auszuführen. Es wird ein vorläufiger Löschvorgang durchgeführt, um die Wiederherstellung Ihres Clusters mit seinen Daten innerhalb von 14 Tagen zu ermöglichen, unabhängig davon, ob der Löschvorgang versehentlich oder gezielt durchgeführt wurde. Der Clustername bleibt während des Zeitraums des vorläufigen Löschens reserviert, und Sie können keinen neuen Cluster mit diesem Namen erstellen. Nach Ablauf des Zeitraums des vorläufigen Löschens wird der Clustername freigegeben, und der Cluster sowie die Daten werden dauerhaft gelöscht und können nicht wiederhergestellt werden. Der verknüpfte Arbeitsbereich ist nach dem Löschvorgang nicht mehr mit dem Cluster verknüpft. Neue erfasste Daten werden im Log Analytics-Speicher gespeichert und mit dem Microsoft-Schlüssel verschlüsselt. 
  
  Das Aufheben der Verknüpfung ist ein asynchroner Vorgang und kann bis zu 90 Minuten dauern.

  # <a name="azure-portal"></a>[Azure portal](#tab/portal)

  –

  # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster delete --resource-group "resource-group-name" --name "cluster-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---
  
- **Wiederherstellen von Cluster und Daten** 
  
  Ein Cluster, der in den letzten 14 Tagen gelöscht wurde, befindet sich im vorläufig gelöschten Zustand und kann zusammen mit den Daten wiederhergestellt werden. Da bei der Löschung des Clusters die Verknüpfung aller Arbeitsbereiche aufgehoben wurde, müssen Sie die Arbeitsbereiche nach der Wiederherstellung des Clusters neu zuordnen. Dieser Wiederherstellungsvorgang wird derzeit manuell von der Produktgruppe durchgeführt. Verwenden Sie Ihren Microsoft-Kanal, oder öffnen Sie eine Supportanfrage zum Wiederherstellen des gelöschten Clusters.

## <a name="limitations-and-constraints"></a>Einschränkungen

- Ein kundenseitig verwalteter Schlüssel wird auf dedizierten Log Analytics-Clustern unterstützt und eignet sich für Kunden, die 1 TB oder mehr pro Tag senden.

- Die maximale Anzahl von Clustern pro Region und Abonnement beträgt 2.

- Das Maximum der verknüpften Arbeitsbereiche für den Cluster beträgt 1000.

- Sie können einen Arbeitsbereich mit Ihrem Cluster verknüpfen und dann die Verknüpfung aufheben. Die Anzahl der Verknüpfungen in einem bestimmten Arbeitsbereich ist innerhalb eines Zeitraums von 30 Tagen auf 2 begrenzt.

- Die Arbeitsbereichverknüpfung mit einem Cluster sollte NUR ausgeführt werden, nachdem Sie sichergestellt haben, dass die Log Analytics-Clusterbereitstellung abgeschlossen wurde. Daten, die vor dem Abschluss an Ihren Arbeitsbereich gesendet wurden, werden gelöscht und können nicht wiederhergestellt werden.

- Die Verschlüsselung mit kundenseitig verwaltetem Schlüssel gilt für Daten, die nach dem Konfigurationszeitpunkt neu erfasst werden. Daten, die vor der Konfiguration erfasst wurden, bleiben mit dem Microsoft-Schlüssel verschlüsselt. Sie können vor und nach der Konfiguration des kundenseitig verwalteten Schlüssels erfasste Daten nahtlos abfragen.

- Azure Key Vault muss als wiederherstellbar konfiguriert werden. Die folgenden Eigenschaften sind standardmäßig nicht aktiviert und sollten mithilfe der CLI oder PowerShell konfiguriert werden:<br>
  - [Vorläufiges Löschen](../../key-vault/general/soft-delete-overview.md)
  - Der [Bereinigungsschutz](../../key-vault/general/soft-delete-overview.md#purge-protection) sollte aktiviert werden, wenn Sie sich auch nach dem vorläufigen Löschen vor dem erzwungenen Löschen des Geheimnis/Schlüsseltresors schützen möchten.

- Das Verschieben eines Clusters in eine andere Ressourcengruppe oder ein anderes Abonnement wird derzeit nicht unterstützt.

- Azure Key Vault, Cluster und verknüpfte Arbeitsbereiche müssen sich in derselben Region und in demselben Azure Active Directory (Azure AD)-Mandanten befinden, können jedoch in unterschiedlichen Abonnements enthalten sein.

- Die Arbeitsbereichverknüpfung mit dem Cluster schlägt fehl, wenn er mit einem anderen Cluster verknüpft ist.

- Lockbox ist in China derzeit nicht verfügbar. 

- [Doppelte Verschlüsselung](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) wird automatisch für Cluster konfiguriert, die ab Oktober 2020 in unterstützten Regionen erstellt werden. Sie können überprüfen, ob Ihr Cluster für doppelte Verschlüsselung konfiguriert ist. Dazu verwenden Sie eine GET-Anforderung für den Cluster und beobachten den Wert der Eigenschaft `"isDoubleEncryptionEnabled"`. Bei Clustern mit aktivierter doppelter Verschlüsselung lautet dieser `true`. 
  - Wenn Sie einen Cluster erstellen und die Fehlermeldung „Die doppelte Verschlüsselung für Cluster wird von <Regionsname> nicht unterstützt.“ erhalten, können Sie den Cluster trotzdem ohne doppelte Verschlüsselung erstellen. Fügen Sie die Eigenschaft `"properties": {"isDoubleEncryptionEnabled": false}` im REST-Anforderungstext hinzu.
  - Die Einstellung für doppelte Verschlüsselung kann nach dem Erstellen des Clusters nicht mehr geändert werden.

## <a name="troubleshooting"></a>Problembehandlung

- Verhalten bei Key Vault-Verfügbarkeit
  - Im Normalbetrieb: Der AEK wird für kurze Zeiträume von Storage zwischengespeichert und in regelmäßigen Abständen zum Entpacken in Key Vault zurückgeführt.
    
  - Vorübergehende Verbindungsfehler: Storage handhabt vorübergehende Fehler (Timeouts, Verbindungsfehler, DNS-Probleme), indem Schlüssel für eine kurze Zeit im Cache verbleiben können und dadurch kurze Unterbrechungen der Verfügbarkeit überbrückt werden. Die Abfrage- und Erfassungsfunktionen werden ohne Unterbrechung fortgesetzt.
    
  - Livewebsite: Eine Nichtverfügbarkeit von ungefähr 30 Minuten führt dazu, dass das Speicherkonto nicht mehr zur Verfügung steht. Die Abfragefunktion ist nicht verfügbar, und erfasste Daten werden über mehrere Stunden mithilfe des Microsoft-Key zwischengespeichert, um Datenverluste zu vermeiden. Wenn der Zugriff auf Key Vault wiederhergestellt ist, steht die Abfragefunktion wieder zur Verfügung, und die temporär zwischengespeicherten Daten werden in den Datenspeicher aufgenommen und mit kundenseitig verwaltetem Schlüssel verschlüsselt.

  - Key Vault-Zugriffsrate: Die Häufigkeit, mit der Azure Monitor Storage für Pack- und Entpackvorgänge auf Key Vault zugreift, liegt zwischen 6 und 60 Sekunden.

- Wenn Sie einen Cluster erstellen und „KeyVaultProperties“ sofort angeben, tritt bei dem Vorgang möglicherweise ein Fehler auf, da die Zugriffsrichtlinie erst definiert werden kann, nachdem die Systemidentität des Clusters zugewiesen wurde.

- Wenn Sie einen vorhandenen Cluster mit „KeyVaultProperties“ aktualisieren und die Zugriffsrichtlinie für den Schlüsselabruf in Key Vault nicht vorhanden ist, schlägt der Vorgang fehl.

- Wenn beim Erstellen eines Clusters ein Konfliktfehler auftritt, haben Sie Ihren Cluster möglicherweise in den letzten 14 Tagen gelöscht und dieser befindet sich nun im Zeitraum des vorläufigen Löschens. Der Clustername bleibt während des Zeitraums des vorläufigen Löschens reserviert, und Sie können keinen neuen Cluster mit diesem Namen erstellen. Der Name wird nach Ablauf des Zeitraums des vorläufigen Löschens freigegeben, wenn der Cluster dauerhaft gelöscht wird.

- Wenn Sie den Cluster aktualisieren, während ein Vorgang läuft, schlägt der Vorgang fehl.

- Wenn Sie den Cluster nicht bereitstellen, vergewissern Sie sich, dass sich Azure Key Vault, Cluster und verknüpfte Log Analytics-Arbeitsbereiche in der gleichen Region befinden. Sie können in unterschiedlichen Abonnements enthalten sein.

- Wenn Sie die Schlüsselversion in Key Vault aktualisieren und die neuen Schlüsselbezeichnerdetails im Cluster nicht aktualisieren, verwendet der Log Analytics-Cluster weiterhin den vorherigen Schlüssel, und auf die Daten kann nicht mehr zugegriffen werden. Aktualisieren Sie neue Schlüsselbezeichnerdetails im Cluster, um mit der Datenerfassung fortzufahren und Daten abfragen zu können.

- Einige Vorgänge dauern lange und können einige Zeit in Anspruch nehmen. Dies sind Erstellen des Clusters, Aktualisieren des Clusterschlüssels und Löschen des Clusters. Sie können den Vorgangsstatus auf zwei Arten überprüfen:
  1. Kopieren Sie bei Verwendung von REST den URL-Wert von „Azure-AsyncOperation“ aus der Antwort, und befolgen Sie die [Überprüfung des Status asynchroner Vorgänge](#asynchronous-operations-and-status-check).
  2. Senden Sie eine GET-Anforderung an den Cluster oder Arbeitsbereich, und beobachten Sie die Antwort. Beispielsweise weist der nicht verknüpfte Arbeitsbereich unter *features* nicht die *clusterResourceId* auf.

- Fehlermeldungen
  
  Clustererstellung:
  -  400 – Der Clustername ist ungültig. Der Clustername darf die Zeichen „a–z“, „A–Z“, „0–9“ und die Länge „3–63“ enthalten.
  -  400 – Der Text der Anforderung ist NULL oder hat ein ungültiges Format.
  -  400 – Der SKU-Name ist ungültig. Legen Sie den SKU-Namen auf „capacityReservation“ fest.
  -  400 – Kapazität wurde bereitgestellt, aber SKU ist nicht „capacityReservation“. Legen Sie den SKU-Namen auf „capacityReservation“ fest.
  -  400 – Fehlende Kapazität in SKU. Legen Sie den Kapazitätswert in Schritten von 100 (GB) auf „1.000“ oder höher fest.
  -  400 – Die Kapazität in SKU liegt nicht im Bereich. Sie sollte mindestens 1.000 und bis zur maximal zulässigen Kapazität betragen, die in Ihrem Arbeitsbereich unter „Nutzung und geschätzte Kosten“ zur Verfügung steht.
  -  400 – Die Kapazität ist für 30 Tage gesperrt. Eine Verringerung der Kapazität ist 30 Tage nach dem Update zulässig.
  -  400 – Es wurde keine SKU festgelegt. Legen Sie den SKU-Namen auf „capacityReservation“ und den Kapazitätswert in Schritten von 100 (GB) auf „1.000“ oder höher fest.
  -  400 – „Identität“ ist NULL oder leer. Legen Sie die Identität mit dem Typ „systemAssigned“ fest.
  -  400 – „KeyVaultProperties“ werden bei der Erstellung festgelegt. Aktualisieren Sie „KeyVaultProperties“ nach der Clustererstellung.
  -  400 – Der Vorgang kann jetzt nicht ausgeführt werden. Der asynchrone Vorgang befindet sich in einem anderen Zustand als „erfolgreich“. Der Cluster muss seinen Vorgang beenden, bevor ein Aktualisierungsvorgang ausgeführt wird.

  Clusterupdate
  -  400 –-Der Cluster befindet sich im Zustand „wird gelöscht“. Asynchroner Vorgang wird ausgeführt. Der Cluster muss seinen Vorgang beenden, bevor ein Aktualisierungsvorgang ausgeführt wird.
  -  400 – „KeyVaultProperties“ ist nicht leer, hat aber ein ungültiges Format. Lesen Sie [Key Identifier Update](#update-cluster-with-key-identifier-details) (Aktualisierung des Schlüsselbezeichners).
  -  400 – Fehler beim Überprüfen des Schlüssels in Key Vault. Mögliche Ursachen: Fehlende Berechtigungen, oder der Schlüssel ist nicht vorhanden. Vergewissern Sie sich, dass Sie die [Schlüssel- und Zugriffsrichtlinie in Key Vault](#grant-key-vault-permissions) festgelegt haben.
  -  400 – Der Schlüssel kann nicht wiederhergestellt werden. Key Vault muss auf „Vorläufiges Löschen und Löschschutz“ festgelegt werden. Lesen Sie die [Dokumentation zu Key Vault](../../key-vault/general/soft-delete-overview.md).
  -  400 – Der Vorgang kann jetzt nicht ausgeführt werden. Warten Sie, bis der asynchrone Vorgang beendet wurde, und versuchen Sie es erneut.
  -  400 –-Der Cluster befindet sich im Zustand „wird gelöscht“. Warten Sie, bis der asynchrone Vorgang beendet wurde, und versuchen Sie es erneut.

  Clusterabruf:
    -  404 – Der Cluster wurde nicht gefunden; möglicherweise wurde er gelöscht. Wenn Sie versuchen, einen Cluster mit diesem Namen zu erstellen, und es zu einem Konflikt kommt, ist der Cluster 14 Tage lang im Zustand „Vorläufig gelöscht“. Sie können den Support kontaktieren, um ihn wiederherzustellen, oder einen anderen Namen zum Erstellen eines neuen Clusters verwenden. 

  Clusterlöschung
    -  409 – Ein Cluster kann im Bereitstellungsstatus nicht gelöscht werden. Warten Sie, bis der asynchrone Vorgang beendet wurde, und versuchen Sie es erneut.

  Arbeitsbereichverknüpfung:
  -  404 – Arbeitsbereich nicht gefunden. Der von Ihnen angegebene Arbeitsbereich ist nicht vorhanden oder wurde gelöscht.
  -  409 – Arbeitsbereichverknüpfung oder Aufheben der Verknüpfung in Bearbeitung.
  -  400 – Der Cluster wurde nicht gefunden, der angegebene Cluster ist nicht vorhanden, oder er wurde gelöscht. Wenn Sie versuchen, einen Cluster mit diesem Namen zu erstellen, und es zu einem Konflikt kommt, ist der Cluster 14 Tage lang im Zustand „Vorläufig gelöscht“. Sie können den Support kontaktieren, um ihn wiederherzustellen.

  Aufheben der Arbeitsbereichverknüpfung:
  -  404 – Arbeitsbereich nicht gefunden. Der von Ihnen angegebene Arbeitsbereich ist nicht vorhanden oder wurde gelöscht.
  -  409 – Arbeitsbereichverknüpfung oder Aufheben der Verknüpfung in Bearbeitung.