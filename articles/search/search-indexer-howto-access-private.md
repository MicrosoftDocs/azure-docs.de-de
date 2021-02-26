---
title: Indexerverbindungen über einen privaten Endpunkt
titleSuffix: Azure Cognitive Search
description: Konfigurieren Sie Indexerverbindungen für den Zugriff auf Inhalte aus anderen Azure-Ressourcen, die über einen privaten Endpunkt geschützt werden.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 762db9d165358f3347fc9b7f3aaaf39f0c762308
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063195"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Erstellen von Indexerverbindungen über einen privaten Endpunkt

Viele Azure-Ressourcen, z. B. Azure-Speicherkonten, können so konfiguriert werden, dass sie Verbindungen aus einer Liste virtueller Netzwerke akzeptieren und externe Verbindungen ablehnen, die aus einem öffentlichen Netzwerk stammen. Wenn Sie einen Indexer zum Indizieren von Daten in Azure Cognitive Search verwenden und sich Ihre Datenquelle in einem privaten Netzwerk befindet, können Sie eine ausgehende [private Endpunktverbindung](../private-link/private-endpoint-overview.md) erstellen, um die Daten zu erreichen.

Für diese Indexerverbindungsmethode gelten die beiden folgenden Anforderungen:

+ Die Azure-Ressource, die Inhalte oder Code bereitstellt, muss vorher beim [Azure Private Link-Dienst](https://azure.microsoft.com/services/private-link/) registriert werden.

+ Zum Ausführen des Azure Cognitive Search-Diensts ist mindestens der Basic-Tarif erforderlich. Im Free-Tarif steht dieses Feature nicht zur Verfügung. Wenn Ihr Indexer über ein Skillset verfügt, ist zusätzlich der Tarif Standard 2 (S2) oder höher erforderlich. Weitere Informationen finden Sie unter [Diensteinschränkungen](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>Freigegebene Verwaltungs-APIs für Private Link-Ressourcen

Private Endpunkte von gesicherten Ressourcen, die über Azure Cognitive Search-APIs erstellt wurden, werden als *freigegebene Private Link-Ressourcen* bezeichnet. Der Grund: Der Zugriff auf eine Ressource (z. B. ein Speicherkonto), die in den [Azure Private Link-Dienst](https://azure.microsoft.com/services/private-link/) integriert wurde, wird von Ihnen „freigegeben“.

Über seine Verwaltungs-REST-API stellt Azure Cognitive Search einen [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate)-Vorgang bereit, mit dem Sie den Zugriff aus einem Azure Cognitive Search-Indexer konfigurieren können.

Sie können private Endpunktverbindungen zu einigen Ressourcen nur mithilfe der Vorschauversion der Search Management-API (Version *2020-08-01-Preview* oder höher) erstellen, die in der folgenden Tabelle als *Vorschau* bezeichnet wird. Ressourcen ohne eine *Vorschau*-Bezeichnung können entweder mit der Vorschauversion oder der allgemein verfügbaren API-Version (*2020-08-01* oder höher) erstellt werden.

In der folgenden Tabelle sind die Azure-Ressourcen aufgeführt, für die Sie ausgehende private Endpunkte aus Azure Cognitive Search erstellen können. Zum Erstellen einer freigegebenen Private Link-Ressource geben Sie die **Gruppen-ID**-Werte genau so ein, wie sie in der API geschrieben werden. Bei den Werten wird Groß- und Kleinschreibung unterschieden.

| Azure-Ressource | Gruppen-ID |
| --- | --- |
| Azure Storage: Blob (oder) ADLS Gen2 | `blob`|
| Azure Storage: Tabellen | `table`|
| Azure Cosmos DB: SQL-API | `Sql`|
| Azure SQL-Datenbank | `sqlServer`|
| Azure Database for MySQL (Vorschau) | `mysqlServer`|
| Azure-Schlüsseltresor | `vault` |
| Azure Functions (Vorschau) | `sites` |

Sie können die Azure-Ressourcen, bei denen ausgehende private Endpunktverbindungen unterstützt werden, auch anhand der [Liste unterstützter APIs](/rest/api/searchmanagement/privatelinkresources/listsupported) abfragen.

Im weiteren Verlauf dieses Artikels wird zur Veranschaulichung der REST-API-Aufrufe eine Mischung aus [Azure CLI](https://docs.microsoft.com/cli/azure/) (oder [ARMClient](https://github.com/projectkudu/ARMClient), falls bevorzugt) und [Postman](https://www.postman.com/) (oder ein anderer HTTP-Client wie [cURL](https://curl.se/), falls bevorzugt) verwendet.

> [!NOTE]
> Die Beispiele in diesem Artikel basieren auf den folgenden Annahmen:
> * Der Name des Suchdiensts lautet _contoso-search_. Er ist in der Ressourcengruppe _contoso_ eines Abonnements mit der Abonnement-ID _00000000-0000-0000-0000-000000000000_ enthalten. 
> * Die Ressourcen-ID dieses Suchdiensts lautet _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_.

In den restlichen Beispielen wird gezeigt, wie der _contoso-search_-Dienst so konfiguriert werden kann, dass seine Indexer auf Daten aus dem sicheren Speicherkonto _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage_ zugreifen können.

## <a name="secure-your-storage-account"></a>Schützen Ihres Speicherkontos

Konfigurieren Sie das Speicherkonto so, dass der [Zugriff nur von bestimmten Subnetzen aus zulässig ist](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Wenn Sie diese Option im Azure-Portal auswählen und den Satz leer lassen, bedeutet dies, dass kein Datenverkehr aus virtuellen Netzwerken zulässig ist.

   ![Der Screenshot des Bereichs „Firewalls und virtuelle Netzwerke“ zeigt die Option zum Zulassen des Zugriffs auf ausgewählte Netzwerke. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> Sie können mithilfe des [Ansatzes „Vertrauenswürdige Microsoft-Dienste“](../storage/common/storage-network-security.md#trusted-microsoft-services) virtuelle Netzwerke oder IP-Einschränkungen für ein Speicherkonto umgehen. Außerdem können Sie den Suchdienst aktivieren, um auf Daten im Speicherkonto zuzugreifen. Informationen hierzu finden Sie unter [Indexer access to Azure Storage with the trusted service exception](search-indexer-howto-access-trusted-service-exception.md) (Indexer-Zugriff auf Azure Storage mit Ausnahme des vertrauenswürdigen Diensts). 
>
> Wenn Sie diesen Ansatz verwenden, erfolgt die Kommunikation zwischen Azure Cognitive Search und Ihrem Speicherkonto jedoch über die öffentliche IP-Adresse des Speicherkontos, über das sichere Microsoft-Backbone-Netzwerk.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Schritt 1: Erstellen einer freigegebenen Private Link-Ressource zum Speicherkonto

Wenn Sie bei Azure Cognitive Search die Erstellung einer ausgehenden privaten Endpunktverbindung mit dem Speicherkonto anfordern möchten, führen Sie den folgenden API-Aufruf aus (beispielsweise mithilfe der [Azure CLI](https://docs.microsoft.com/cli/azure/)): 

`az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json`

Alternativ können Sie auch [ARMClient](https://github.com/projectkudu/ARMClient) verwenden:

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Der Inhalt der Datei *create-pe.json*, die den Anforderungstext für die API darstellt, lautet so:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Bei Erfolg wird die Antwort `202 Accepted` zurückgegeben. Der Prozess der Erstellung eines ausgehenden privaten Endpunkts ist ein zeitintensiver (asynchroner) Vorgang. Er umfasst die Bereitstellung der folgenden Ressourcen:

* Ein privater Endpunkt, dem eine private IP-Adresse in einem `"Pending"`-Zustand zugeordnet wurde. Die private IP-Adresse wird aus dem Adressraum abgerufen, der dem virtuellen Netzwerk der Ausführungsumgebung für den suchdienstspezifischen privaten Indexer zugeordnet ist. Nach der Genehmigung des privaten Endpunkts stammt jede Kommunikation von Azure Cognitive Search zum Speicherkonto von der privaten IP-Adresse und einem sicheren Private Link-Kanal.

* Eine private DNS-Zone für den Ressourcentyp, basierend auf der `groupId`. Durch Bereitstellen dieser Ressource stellen Sie sicher, dass bei jedem DNS-Lookup zur privaten Ressource die dem privaten Endpunkt zugeordnete IP-Adresse verwendet wird.

Sorgen Sie dafür, dass Sie die richtige `groupId` für den Ressourcentyp angeben, für den Sie den privaten Endpunkt erstellen. Jeder Konflikt führt zu einer nicht erfolgreichen Antwortnachricht.

Wie bei allen asynchronen Azure-Vorgängen gibt der `PUT`-Aufruf einen `Azure-AsyncOperation`-Headerwert zurück, der wie folgt aussieht:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Sie können diesen URI in regelmäßigen Abständen abrufen, um den Status des Vorgangs zu erhalten. Bevor Sie den Vorgang fortsetzen, sollten Sie warten, bis der Status des freigegebenen Private Link-Ressourcenvorgangs einen Endzustand erreicht hat (d. h., der Status des Vorgangs lautet *Erfolgreich*).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01`

Vorgehensweise mit „ARMClient“:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Schritt 2a: Genehmigen der privaten Endpunktverbindung für das Speicherkonto

> [!NOTE]
> In diesem Abschnitt durchlaufen Sie mithilfe des Azure-Portals den Genehmigungsflow für einen privaten Endpunkt zum Speicher. Alternativ könnten Sie die [REST-API](/rest/api/storagerp/privateendpointconnections) verwenden, die über den Speicherressourcenanbieter zur Verfügung steht.
>
> Andere Anbieter, z. B. Azure Cosmos DB oder Azure SQL Server, bieten ähnliche Speicherressourcenanbieter-APIs für die Verwaltung privater Endpunktverbindungen.

1. Wählen Sie im Azure-Portal die Registerkarte **Private Endpunktverbindungen** für Ihr Speicherkonto aus. Nachdem der asynchrone Vorgang erfolgreich ausgeführt wurde, sollte eine Anforderung für eine private Endpunktverbindung mit der Anforderungsnachricht aus dem vorherigen API-Aufruf vorliegen.

   ![Der Screenshot des Azure-Portals zeigt den Bereich „Private Endpunktverbindungen“.](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Wählen Sie den privaten Endpunkt aus, der von Azure Cognitive Search erstellt wurde. Identifizieren Sie in der Spalte **Privater Endpunkt** die private Endpunktverbindung anhand des Namens, der in der vorherigen API angegeben wurde, wählen Sie **Genehmigen** aus, und geben Sie dann eine entsprechende Nachricht ein. Der Inhalt der Nachricht spielt keine Rolle. 

   Vergewissern Sie sich, dass die private Endpunktverbindung wie im folgenden Screenshot angezeigt wird. Es könnte zwischen ein und zwei Minuten dauern, bis der Status im Portal aktualisiert wird.

   ![Der Screenshot des Azure-Portals zeigt im Bereich „Private Endpunktverbindungen“ den Status „Genehmigt“.](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Nachdem die Anforderung für die private Endpunktverbindung genehmigt wurde, *kann* der Datenverkehr über den privaten Endpunkt fließen. Nachdem der private Endpunkt genehmigt wurde, erstellt Azure Cognitive Search die erforderlichen DNS-Zonenzuordnungen in der dafür erstellten DNS-Zone.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Schritt 2b: Abfragen des Status der freigegebenen Private Link-Ressource

Zur Bestätigung, dass die freigegebene Private Link-Ressource nach der Genehmigung aktualisiert wurde, rufen Sie deren Status über die [GET-API](/rest/api/searchmanagement/sharedprivatelinkresources/get) ab.

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Vorgehensweise mit „ARMClient“:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Wenn die Eigenschaft `properties.provisioningState` der Ressource `Succeeded` und die Eigenschaft `properties.status` dementsprechend `Approved` lautet, bedeutet dies, dass die freigegebene Private Link-Ressource funktionsfähig ist und der Indexer so konfiguriert werden kann, dass er über den privaten Endpunkt kommuniziert.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>Schritt 3: Konfigurieren der Indexerausführung in der privaten Umgebung

> [!NOTE]
> Sie können diesen Schritt vor der Genehmigung der privaten Endpunktverbindung ausführen. Bis zur Genehmigung der privaten Endpunktverbindung endet jeder Indexer, der die Kommunikation mit einer sicheren Ressource (z. B. dem Speicherkonto) versucht, mit einem vorübergehenden Fehlerstatus. Neue Indexer werden nicht erstellt. Sobald die private Endpunktverbindung genehmigt wurde, können Indexer auf das private Speicherkonto zugreifen.

1. [Erstellen Sie eine Datenquelle](/rest/api/searchservice/create-data-source), die auf das Speicherkonto und einen entsprechenden Container innerhalb des Speicherkontos verweist. Der folgende Screenshot zeigt diese Anforderung in Postman.

   ![Der Screenshot zeigt die Erstellung einer Datenquelle auf der Postman-Benutzeroberfläche.](media\search-indexer-howto-secure-access\create-ds.png )

1. Auf ähnliche Weise [erstellen Sie einen Index](/rest/api/searchservice/create-index) und [erstellen Sie optional ein Skillset](/rest/api/searchservice/create-skillset) über die REST-API.

1. [Erstellen Sie einen Indexer](/rest/api/searchservice/create-indexer), der auf die Datenquelle, den Index und das Skillset verweist, die Sie im vorhergehenden Schritt erstellt haben. Erzwingen Sie außerdem durch Festlegen der Indexerkonfigurationseigenschaft `executionEnvironment` auf `private`, dass der Indexer in der privaten Ausführungsumgebung ausgeführt wird.

   ![Der Screenshot zeigt die Erstellung eines Indexers auf der Postman-Benutzeroberfläche.](media\search-indexer-howto-secure-access\create-idr.png)

   Nachdem der Indexer erfolgreich erstellt wurde, sollte er damit beginnen, Inhalte aus dem Speicherkonto über die private Endpunktverbindung zu indizieren. Sie können den Status des Indexers mithilfe der [Indexer-Status-API](/rest/api/searchservice/get-indexer-status) überwachen.

> [!NOTE]
> Wenn Sie bereits Indexer haben, können Sie sie über die [PUT-API](/rest/api/searchservice/create-indexer) aktualisieren, indem Sie die Eigenschaft `executionEnvironment` auf `private` festzulegen.

## <a name="troubleshooting"></a>Problembehandlung

- Wenn Ihre Indexererstellung mit einer Fehlermeldung wie z. B. „Die Datenquellen-Anmeldeinformationen sind ungültig“ fehlschlägt, bedeutet dies, dass entweder der Status der privaten Endpunktverbindung noch nicht *Genehmigt* lautet oder die Verbindung nicht funktionsfähig ist. So beheben Sie das Problem: 
  * Rufen Sie den Status der freigegebenen Private Link-Ressource über die [GET-API](/rest/api/searchmanagement/sharedprivatelinkresources/get) ab. Wenn der Status *Genehmigt* lautet, überprüfen Sie die Eigenschaft `properties.provisioningState` der Ressource. Wenn der Status hier `Incomplete` lautet, bedeutet dies, dass einige der zugrunde liegenden Abhängigkeiten für die Ressource nicht eingerichtet werden konnten. Eine erneute Ausgabe der `PUT`-Anforderung zum erneuten Erstellen der freigegebenen Private Link-Ressource sollte das Problem beheben. Möglicherweise ist eine erneute Genehmigung erforderlich. Überprüfen Sie den Status der Ressource erneut, um sich zu vergewissern, dass das Problem behoben wurde.

- Wenn Sie den Indexer erstellen, ohne seine Eigenschaft `executionEnvironment` festzulegen, könnte die Erstellung erfolgreich sein. Sein Ausführungsverlauf wird aber zeigen, dass die Indexerausführungen nicht erfolgreich sind. So beheben Sie das Problem:
   * [Aktualisieren Sie den Indexer](/rest/api/searchservice/update-indexer), um die Ausführungsumgebung anzugeben.

- Wenn Sie den Indexer ohne Festlegen der Eigenschaft `executionEnvironment` erstellt haben und er erfolgreich ausgeführt wird, hat Azure Cognitive Search entschieden, dass seine Ausführungsumgebung die suchdienstspezifische *private* Umgebung ist. Dies kann sich ändern – je nach den vom Indexer verbrauchten Ressourcen, der Last für den Suchdienst und anderen Faktoren –, und zu einem späteren Zeitpunkt kann ein Fehler auftreten. So beheben Sie das Problem:
  * Wir empfehlen dringend, dass Sie die Eigenschaft `executionEnvironment` auf `private` festlegen und so sicherstellen, dass dabei in Zukunft kein Fehler mehr auftritt.

[Kontingente und Grenzwerte](search-limits-quotas-capacity.md) bestimmen, wie viele freigegebene Private Link-Ressourcen erstellt werden können und hängen von der SKU des Suchdiensts ab.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu privaten Endpunkten:

- [Was sind private Endpunkte?](../private-link/private-endpoint-overview.md)
- [Für private Endpunkte erforderliche DNS-Konfigurationen](../private-link/private-endpoint-dns.md)
