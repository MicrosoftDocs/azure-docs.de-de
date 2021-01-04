---
title: Dedizierte Azure Monitor-Protokollcluster
description: Kunden, die mehr als 1 TB an Überwachungsdaten pro Tag erfassen, können ggf. dedizierte Cluster anstelle freigegebener Cluster verwenden.
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: d2446e866c0e12d50a0759373682f4f62bc4bba0
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512221"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Dedizierte Azure Monitor-Protokollcluster

Dedizierte Azure Monitor-Protokollcluster sind eine Bereitstellungsoption, die erweiterte Funktionen für Kunden von Azure Monitor-Protokollen bietet. Kunden mit dedizierten Clustern können die Arbeitsbereiche auswählen, die auf diesen Clustern gehostet werden sollen.

Folgende Funktionen erfordern dedizierte Cluster:

- **[Kundenseitig verwaltete Schlüssel](../platform/customer-managed-keys.md)** : Die Clusterdaten werden mithilfe von Schlüsseln verschlüsselt, die vom Kunden bereitgestellt und gesteuert werden.
- **[Lockbox](../platform/customer-managed-keys.md#customer-lockbox-preview)** : Kunden können Zugriffsanforderungen von Mitarbeitern des Microsoft-Supports für Daten steuern.
- **[Doppelte Verschlüsselung](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** schützt vor dem Szenario, dass einer der Verschlüsselungsalgorithmen oder Schlüssel kompromittiert wurde. In diesem Fall werden die Daten weiterhin durch die zusätzliche Verschlüsselungsebene geschützt.
- **[Mehrere Arbeitsbereiche](../log-query/cross-workspace-query.md)** : Wenn ein Kunde mehr als einen Arbeitsbereich für die Produktion verwendet, ist es möglicherweise sinnvoll, einen dedizierten Cluster zu verwenden. Arbeitsbereichsübergreifende Abfragen werden schneller ausgeführt, wenn sich alle Arbeitsbereiche im selben Cluster befinden. Möglicherweise ist die Verwendung eines dedizierten Clusters auch kostengünstiger, da die zugewiesenen Tarife für die Kapazitätsreservierung die gesamte Clustererfassung berücksichtigen und auf alle Arbeitsbereiche angewendet werden, auch wenn einige davon klein sind und nicht für den Kapazitätsreservierungsrabatt berechtigt sind.

Dedizierte Cluster erfordern es, dass sich Kunden zur Verwendung einer Datenerfassung von mindestens 1 TB pro Tag verpflichten. Die Migration zu einem dedizierten Cluster ist einfach. Es gibt keinen Datenverlust und keine Dienstunterbrechung. 

> [!IMPORTANT]
> Dedizierte Cluster werden genehmigt und für Produktionsbereitstellungen vollständig unterstützt. Aufgrund temporärer Kapazitätseinschränkungen ist es jedoch erforderlich, dass Ihre Vorabregistrierung das Feature verwendet. Verwenden Sie Ihre Kontakte zu Microsoft zum Angeben Ihrer Abonnement-IDs.

## <a name="management"></a>Verwaltung 

Dedizierte Cluster werden über eine Azure-Ressource verwaltet, die Azure Monitor-Protokollcluster darstellt. Alle Vorgänge werden für diese Ressource mithilfe von PowerShell oder der REST-API ausgeführt.

Nachdem der Cluster erstellt wurde, ist es möglich, ihn zu konfigurieren und Arbeitsbereiche mit ihm zu verknüpfen. Wenn ein Arbeitsbereich mit einem Cluster verknüpft ist, befinden sich neue Daten, die an den Arbeitsbereich gesendet werden, im Cluster. Mit einem Cluster können nur Arbeitsbereiche verknüpft werden, die sich in der gleichen Region wie der Cluster befinden. Das Aufheben der Verknüpfung von Arbeitsbereichen mit einem Cluster ist mit Einschränkungen möglich. Weitere Einzelheiten zu diesen Einschränkungen finden Sie in diesem Artikel. 

In dedizierten Clustern erfasste Daten werden zweimal verschlüsselt: einmal auf der Dienstebene mit von Microsoft verwalteten Schlüsseln oder [kundenseitig verwaltetem Schlüssel](../platform/customer-managed-keys.md) und einmal auf der Infrastrukturebene mit zwei verschiedenen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln. Die [doppelte Verschlüsselung](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) schützt vor dem Szenario, dass einer der Verschlüsselungsalgorithmen oder Schlüssel kompromittiert wurde. In diesem Fall werden die Daten weiterhin durch die zusätzliche Verschlüsselungsebene geschützt. Ein dedizierter Cluster ermöglicht Ihnen außerdem das Schützen Ihrer Daten mit [Lockbox](../platform/customer-managed-keys.md#customer-lockbox-preview).

Für alle Vorgänge auf Clusterebene ist die Aktionsberechtigung `Microsoft.OperationalInsights/clusters/write` für den Cluster erforderlich. Diese Berechtigung kann über den Besitzer oder einen Mitwirkenden erteilt werden, der die Aktion `*/write` enthält, oder über die Rolle „Analytics-Mitwirkender“, die die Aktion `Microsoft.OperationalInsights/*` enthält. Weitere Informationen zu den Log Analytics-Berechtigungen finden Sie unter [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](../platform/manage-access.md). 


## <a name="cluster-pricing-model"></a>Preismodell für Cluster

Dedizierte Log Analytics-Cluster verwenden ein Preismodell für die Kapazitätsreservierung mit mindestens 1000 GB/Tag. Jeder über die Reservierung hinausgehende Verbrauch wird entsprechend der nutzungsbasierten Bezahlung berechnet.  Preisinformationen zur Kapazitätsreservierung finden Sie auf der Seite [Azure Monitor – Preise]( https://azure.microsoft.com/pricing/details/monitor/).  

Die Reservierungsebene für die Clusterkapazität wird programmgesteuert mit Azure Resource Manager mithilfe des `Capacity`-Parameters unter `Sku` konfiguriert. `Capacity` wird in Einheiten von GB und in Schritten von 100 GB pro Tag in Werten von 1000 GB/Tag oder mehr angegeben.

Für die Abrechnung des Verbrauchs in einem Cluster stehen zwei Modi zur Verfügung. Diese können beim Konfigurieren Ihres Clusters mithilfe des Parameters `billingType` festgelegt werden. 

1. **Cluster** (Standardeinstellung): In diesem Fall werden erfasste Daten auf der Clusterebene abgerechnet. Die erfassten Datenmengen aus den einzelnen Arbeitsbereichen, die einem Cluster zugeordnet sind, werden aggregiert, um die tägliche Abrechnung für den Cluster zu berechnen. 

2. **Arbeitsbereiche**: Die Kapazitätsreservierungskosten für Ihren Cluster werden proportional den Arbeitsbereichen im Cluster zugeordnet (nach Berücksichtigung der knotenspezifischen Zuordnungen von [Azure Security Center](../../security-center/index.yml) für den jeweiligen Arbeitsbereich).

Wenn Ihr Arbeitsbereich den Legacytarif pro Knoten verwendet, wird er, wenn er mit einem Cluster verknüpft ist, basierend auf den in Bezug auf die Kapazitätsreservierung des Clusters erfassten Daten und nicht mehr pro Knoten abgerechnet. Datenzuweisungen pro Knoten von Azure Security Center werden weiterhin angewendet.

Weitere Informationen zur Abrechnung für dedizierte Log Analytics-Cluster finden Sie [hier]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters).


## <a name="creating-a-cluster"></a>Erstellen eines Clusters

Erstellen Sie zunächst Clusterressourcen, um mit dem Erstellen eines dedizierten Clusters zu beginnen.

Die folgenden Eigenschaften müssen angegeben werden:

- **ClusterName**: Wird zu Verwaltungszwecken verwendet. Benutzer werden für diesen Namen nicht offengelegt.
- **ResourceGroupName**: Wie bei jeder Azure-Ressource gehören Cluster zu einer Ressourcengruppe. Es wird empfohlen, dass Sie eine zentrale IT-Ressourcengruppe verwenden, da Cluster in der Regel von vielen Teams in der Organisation gemeinsam genutzt werden. Weitere Entwurfsaspekte finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../platform/design-logs-deployment.md).
- **Standort**: Ein Cluster befindet sich in einer bestimmten Azure-Region. Nur Arbeitsbereiche, die sich in dieser Region befinden, können mit diesem Cluster verknüpft werden.
- **SkuCapacity**: Beim Erstellen einer *Clusterressource* müssen Sie die *Kapazitätsreservierungsebene* (sku) angeben. Die *Kapazitätsreservierungsebene* kann im Bereich von 1.000 bis 3.000 GB pro Tag liegen. Sie können sie bei Bedarf später in 100er Schritten aktualisieren. Wenn Sie eine Kapazitätsreservierungsebene von mehr als 3.000 GB pro Tag benötigen, kontaktieren Sie uns unter LAIngestionRate@microsoft.com. Weitere Informationen zu den Clusterkosten finden Sie unter [Verwalten von Kosten für Log Analytics-Cluster](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters).

Nachdem Sie die *Clusterressource* erstellt haben, können Sie sie mit *sku*, *keyVaultProperties oder *billingType* aktualisieren. Weitere Einzelheiten finden Sie unten.

> [!WARNING]
> Die Clustererstellung löst die Ressourcenzuordnung und -bereitstellung aus. Es kann bis zu einer Stunde dauern, bis dieser Vorgang abgeschlossen ist. Es wird empfohlen, ihn asynchron auszuführen.

Das Benutzerkonto, mit dem die Cluster erstellt werden, muss über die Standardberechtigung für die Erstellung von Azure-Ressourcen verfügen: `Microsoft.Resources/deployments/*` und die Clusterschreibberechtigung `(Microsoft.OperationalInsights/clusters/write)`.

### <a name="create"></a>Erstellen 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Call* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Antwort*

200 OK und ein Header

### <a name="check-provisioning-status"></a>Überprüfen des Bereitstellungsstatus

Es dauert eine Weile, bis die Bereitstellung des Log Analytics-Clusters abgeschlossen ist. Sie können den Bereitstellungsstatus auf verschiedene Weise überprüfen:

- Führen Sie den PowerShell-Befehl „Get-AzOperationalInsightsCluster“ mit dem Ressourcengruppennamen aus, und überprüfen Sie die Eigenschaft „ProvisioningState“. Während der Bereitstellung lautet dieser Wert *ProvisioningAccount*, und nach Abschluss des Vorgangs lautet er *Succeeded*.
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Kopieren Sie den URL-Wert von „Azure-AsyncOperation“ aus der Antwort, und befolgen Sie die Überprüfung des Status asynchroner Vorgänge.

- Senden Sie eine GET-Anforderung für die *Clusterressource*, und überprüfen Sie den Wert für *provisioningState*. Während der Bereitstellung lautet dieser Wert *ProvisioningAccount*, und nach Abschluss des Vorgangs lautet er *Succeeded*.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
   Authorization: Bearer <token>
   ```

   **Antwort**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

Die GUID *principalId* wird vom verwalteten Identitätsdienst für die *Clusterressource* generiert.

## <a name="change-cluster-properties"></a>Ändern der Clustereigenschaften

Nachdem Sie Ihre *Clusterressource* erstellt und vollständig bereitgestellt haben, können Sie zusätzliche Eigenschaften auf Clusterebene mithilfe von PowerShell oder der REST-API bearbeiten. Abgesehen von den Eigenschaften, die während der Clustererstellung verfügbar sind, können zusätzliche Eigenschaften erst festgelegt werden, nachdem der Cluster bereitgestellt wurde:

- **keyVaultProperties**: Dient zum Konfigurieren von Azure Key Vault, der zum Bereitstellen eines [kundenseitig verwalteten Azure Monitor-Schlüssels](../platform/customer-managed-keys.md#customer-managed-key-provisioning-procedure) verwendet wird. Sie enthält die folgenden Parameter:  *KeyVaultUri*, *KeyName*, *KeyVersion*. 
- **billingType**: Die Eigenschaft *billingType* bestimmt die Abrechnungszuordnung für die *Clusterressource* und deren Daten:
  - **Cluster** (Standard): Die Kapazitätsreservierungskosten für Ihren Cluster werden der *Clusterressource* zugeordnet.
  - **Arbeitsbereiche**: Die Kapazitätsreservierungskosten für Ihren Cluster werden proportional den Arbeitsbereichen im Cluster zugeordnet. Wenn die Gesamtmenge der erfassten Daten unter der Kapazitätsreservierung liegt, wird ein Teil des Verbrauchs über die *Clusterressource* abgerechnet. Weitere Informationen zum Clusterpreismodell finden Sie unter [Dedizierte Log Analytics-Cluster](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters). 

> [!NOTE]
> Die Eigenschaft *billingType* wird in PowerShell nicht unterstützt.
> Clustereigenschaftsaktualisierungen werden möglicherweise asynchron ausgeführt und können einige Zeit in Anspruch nehmen.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> Sie können *Clusterressource*, *sku*, *keyVaultProperties* oder *billingType* mithilfe von PATCH aktualisieren.

Beispiel: 

*Call*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

„KeyVaultProperties“ enthält die Details des Key Vault-Schlüsselbezeichners.

*Antwort*

200 OK und Header

### <a name="check-cluster-update-status"></a>Überprüfen des Status der Clusteraktualisierung

Die Weitergabe des Schlüsselbezeichners dauert einige Minuten. Sie können den Aktualisierungsstatus auf zwei Arten überprüfen:

- Kopieren Sie den URL-Wert von „Azure-AsyncOperation“ aus der Antwort, und befolgen Sie die Überprüfung des Status asynchroner Vorgänge. 

   OR

- Senden Sie eine GET-Anforderung für die *Clusterressource*, und überprüfen Sie die Eigenschaften *KeyVaultProperties*. Die zuletzt aktualisierten Schlüsselbezeichnerdetails sollten in der Antwort zurückgegeben werden.

   Nachdem die Aktualisierung des Schlüsselbezeichners abgeschlossen ist, sollte die Antwort auf die GET-Anforderung für die *Clusterressource* wie folgt aussehen:

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

## <a name="link-a-workspace-to-the-cluster"></a>Verknüpfen eines Arbeitsbereichs mit dem Cluster

Wenn ein Arbeitsbereich mit einem dedizierten Cluster verknüpft ist, werden neue Daten, die in den Arbeitsbereich erfasst werden, an den neuen Cluster weitergeleitet, während vorhandene Daten im vorhandenen Cluster verbleiben. Wenn der dedizierte Cluster mithilfe von kundenseitig verwalteten Schlüsseln (Customer-managed keys, CMK) verschlüsselt wird, werden nur neue Daten mit dem Schlüssel verschlüsselt. Das System abstrahiert diesen Unterschied von den Benutzern, und die Benutzer fragen den Arbeitsbereich wie gewohnt ab, während das System clusterübergreifende Abfragen für das Back-End ausführt.

Ein Cluster kann mit bis zu 100 Arbeitsbereichen verknüpft werden. Verknüpfte Arbeitsbereiche befinden sich in derselben Region wie der Cluster. Um das System-Back-End zu schützen und die Fragmentierung von Daten zu vermeiden, kann ein Arbeitsbereich nicht mehr als zweimal pro Monat mit einem Cluster verknüpft werden.

Um den Verknüpfungsvorgang auszuführen, benötigen Sie Schreibberechtigungen für den Arbeitsbereich und die *Clusterressource*:

- Im Arbeitsbereich: *Microsoft.OperationalInsights/workspaces/write*
- In der *Clusterressource*: *Microsoft.OperationalInsights/clusters/write*

Zusätzlich zu den Abrechnungsaspekten behält der verknüpfte Arbeitsbereich seine eigenen Einstellungen wie etwa die Länge der Datenaufbewahrung bei.
Der Arbeitsbereich und der Cluster können sich in verschiedenen Abonnements befinden. Es ist möglich, dass sich der Arbeitsbereich und der Cluster in unterschiedlichen Mandanten befinden, wenn Azure Lighthouse verwendet wird, um beide einem einzelnen Mandanten zuzuordnen.

Wie jeder Clustervorgang kann das Verknüpfen eines Arbeitsbereichs nur nach Abschluss der Log Analytics-Clusterbereitstellung ausgeführt werden.

> [!WARNING]
> Das Verknüpfen eines Arbeitsbereichs mit einem Cluster erfordert es, mehrere Back-End-Komponenten zu synchronisieren und die Cachehydration zu gewährleisten. Dieser Vorgang kann bis zu zwei Stunden in Anspruch nehmen. Es wird empfohlen, ihn asynchron auszuführen.


**PowerShell**

Verwenden Sie den folgenden PowerShell-Befehl zum Verknüpfen eines Clusters:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Verwenden Sie den folgenden REST-Aufruf zum Verknüpfen eines Clusters:

*Senden*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Antwort*

200 OK und Header.

### <a name="using-customer-managed-keys-with-linking"></a>Verwenden kundenseitig verwalteter Schlüssel mit Verknüpfung

Wenn Sie kundenseitig verwaltete Schlüssel verwenden, werden erfasste Daten nach dem Zuordnungsvorgang mit Ihrem verwalteten Schlüssel verschlüsselt gespeichert, was bis zu 90 Minuten dauern kann. 

Sie können den Zuordnungsstatus des Arbeitsbereichs auf zwei Arten überprüfen:

- Kopieren Sie den URL-Wert von „Azure-AsyncOperation“ aus der Antwort, und befolgen Sie die Überprüfung des Status asynchroner Vorgänge.

- Senden Sie eine [Workspaces – Get](/rest/api/loganalytics/workspaces/get)-Anforderung und beobachten Sie die Antwort. Der zugehörige Arbeitsbereich verfügt unter „features“ über „clusterResourceId“.

Eine Sendeanforderung sieht wie folgt aus:

*Senden*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

*Antwort*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>Aufheben der Verknüpfung eines Arbeitsbereichs mit einem dedizierten Cluster

Sie können die Verknüpfung eines Arbeitsbereichs mit einem Cluster aufheben. Nach dem Aufheben der Verknüpfung eines Arbeitsbereichs mit dem Cluster werden neue Daten, die diesem Arbeitsbereich zugeordnet sind, nicht an den dedizierten Cluster gesendet. Außerdem erfolgt die Abrechnung des Arbeitsbereichs nicht mehr über den Cluster. Alte Daten des nicht verknüpften Arbeitsbereichs können im Cluster verbleiben. Wenn diese Daten mithilfe von kundenseitig verwalteten Schlüsseln (Customer-managed keys, CMK) verschlüsselt werden, werden die Key Vault-Geheimnisse beibehalten. Das System abstrahiert diese Änderung von Log Analytics-Benutzern. Benutzer können den Arbeitsbereich einfach wie gewohnt abfragen. Das System führt bei Bedarf clusterübergreifende Abfragen für das Back-End ohne einen Hinweis auf die Benutzer durch.  

> [!WARNING] 
> Es gibt eine Begrenzung von zwei Verknüpfungsvorgängen pro Arbeitsbereich innerhalb eines Monats. Berücksichtigen und planen Sie das Aufheben der Verknüpfung von Aktionen gründlich. 

## <a name="delete-a-dedicated-cluster"></a>Löschen eines dedizierten Clusters

Eine dedizierte Clusterressource kann gelöscht werden. Sie müssen die Verknüpfung aller Arbeitsbereiche mit dem Cluster aufheben, bevor Sie ihn löschen. Sie brauchen Schreibberechtigungen für die *Clusterressource*, um diesen Vorgang auszuführen. 

Nachdem die Clusterressource gelöscht wurde, wechselt der physische Cluster in einen Bereinigungs- und Löschvorgang. Beim Löschen eines Clusters werden alle Daten gelöscht, die auf dem Cluster gespeichert waren. Die Daten stammen unter Umständen aus Arbeitsbereichen, die in der Vergangenheit mit dem Cluster verknüpft wurden.

Eine *Clusterressource*, die in den letzten 14 Tagen gelöscht wurde, befindet sich im vorläufig gelöschten Zustand und kann zusammen mit den Daten wiederhergestellt werden. Da bei der Löschung der *Clusterressource* die Zuordnung aller Arbeitsbereiche zur *Clusterressource* aufgehoben wurde, müssen Sie die Arbeitsbereiche nach der Wiederherstellung neu zuordnen. Der Wiederherstellungsvorgang kann vom Benutzer nicht durchgeführt werden. Wenden Sie sich daher bei Wiederherstellungsanfragen an Ihren Microsoft-Kanal oder den Support.

Innerhalb von 14 Tagen nach dem Löschvorgang ist der Name der Clusterressource reserviert und kann nicht von anderen Ressourcen verwendet werden.

**PowerShell**

Verwenden Sie den folgenden PowerShell-Befehl zum Löschen eines Clusters:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Verwenden Sie den folgenden REST-Aufruf zum Löschen eines Clusters:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Antwort**

  200 – OK



## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Abrechnung dedizierter Log Analytics-Cluster](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters).
- Erfahren Sie mehr über das [Entwerfen von Log Analytics-Arbeitsbereichen](../platform/design-logs-deployment.md).
