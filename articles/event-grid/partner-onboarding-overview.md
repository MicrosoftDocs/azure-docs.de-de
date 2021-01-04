---
title: Übersicht über das Partneronboarding (Azure Event Grid)
description: Hier finden Sie eine Übersicht darüber, wie Sie als Event Grid-Partner das Onboarding durchführen können.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 2a2e33395cabd368d5d5d870dd0461e4cbd37e0d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081194"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Übersicht über das Partneronboarding (Azure Event Grid)

In diesem Artikel wird beschrieben, wie Sie Azure Event Grid-Partnerressourcen privat verwenden und zu einem öffentlich verfügbaren Partnerthementyp werden.

Sie benötigen keine besondere Berechtigung, um mit der Verwendung der mit dem Veröffentlichen von Ereignissen als Event Grid-Partner verknüpften Event Grid-Ressourcentypen zu beginnen. Tatsächlich können Sie diese heute zum privaten Veröffentlichen von Ereignissen in Ihren Azure-Abonnements und zum Testen des Ressourcenmodells verwenden, wenn Sie überlegen, Partner zu werden.

> [!NOTE]
> Eine Schritt-für-Schritt-Anleitung für das Onboarding als Event Grid-Partner mithilfe des Azure-Portal finden Sie unter [Durchführen des Onboardings als Event Grid-Partner (Azure-Portal)](onboard-partner.md). 

## <a name="how-partner-events-work"></a>Funktionsweise von Partnerereignissen
Das Feature „Partnerereignisse“ verwendet die vorhandene Architektur, die von Event Grid bereits zum Veröffentlichen von Ereignissen von Azure-Ressourcen wie Azure Storage und Azure IoT Hub verwendet wird, und stellt diese Tools zur freien Verwendung öffentlich zur Verfügung. Standardmäßig können Sie diese Tools nur in Ihrem Azure-Abonnement verwenden. Wenn Sie Ihre Ereignisse öffentlich verfügbar machen möchten, füllen Sie das Formular aus, und [wenden Sie sich an das Event Grid-Team](mailto:gridpartner@microsoft.com).

Mit dem Feature „Partnerereignisse“ können Sie Ereignisse in Azure Event Grid für die Nutzung durch mehrere Mandanten veröffentlichen.

## <a name="onboarding-and-event-publishing-overview"></a>Übersicht über das Onboarding und die Ereignisveröffentlichung

### <a name="partner-flow"></a>Ablauf für Partner

1. Erstellen Sie einen Azure-Mandanten, falls Sie noch keinen besitzen.
1. Verwenden der Azure-CLI zum Erstellen einer neuen Event Grid-`partnerRegistration`. Diese Ressource umfasst Informationen wie Anzeigename, Beschreibung, Setup-URI usw.

    ![Erstellen eines Partnerthemas](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Erstellen Sie in jeder Region, in der Sie Ereignisse veröffentlichen möchten, mindestens einen Partnernamespace. Der Event Grid-Dienst stellt einen Veröffentlichungsendpunkt (z. B. `https://contoso.westus-1.eventgrid.azure.net/api/events`) und Zugriffsschlüssel zur Verfügung.

    ![Erstellen von Partnernamespaces](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Bieten Sie Kunden die Möglichkeit, in Ihrem System anzugeben, dass sie an einem Partnerthema interessiert sind.
1. Teilen Sie dem Event Grid-Team mit, dass Sie Ihren Partnerthementyp veröffentlichen möchten.

### <a name="customer-flow"></a>Ablauf für Kunden

1. Ihr Kunde besucht das Azure-Portal, um die Azure-Abonnement-ID und die Ressourcengruppe zu notieren, in der er das Partnerthema erstellen möchte.
1. Der Kunde fordert über Ihr System ein Partnerthema an. Daraufhin erstellen Sie einen Ereignistunnel in Ihrem Partnernamespace.
1. Event Grid erstellt im Azure-Abonnement und der Ressourcengruppe des Kunden ein Partnerthema mit dem Status **Ausstehend**.

    ![Erstellen von Ereigniskanälen](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Der Kunde aktiviert das Partnerthema über das Azure-Portal. Ereignisse können nun von Ihrem Dienst zum Azure-Abonnement des Kunden gelangen.

    ![Aktivieren eines Partnerthemas](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Ressourcenmodell
Das folgende Ressourcenmodell ist für Partnerereignisse vorgesehen.

### <a name="partner-registrations"></a>Partnerregistrierungen
* Ressource: `partnerRegistrations`
* Verwendet von: Partner
* Beschreibung: Diese Ressource erfasst die globalen Metadaten des SaaS-Partners (Software-as-a-Service), z. B. Name, Anzeigename, Beschreibung, Setup-URI.
    
    Das Erstellen oder Aktualisieren einer Partnerregistrierung ist ein Self-Service-Vorgang für die Partner. Diese Self-Service-Funktion ermöglicht Partnern das Erstellen und Testen des gesamten End-to-End-Ablaufs.
    
    Nur von Microsoft genehmigte Partnerregistrierungen können von Kunden entdeckt werden.
* Umfang: Die Erstellung erfolgt im Azure-Abonnement des Partners. Metadaten sind für Kunden sichtbar, nachdem sie veröffentlicht wurden.

### <a name="partner-namespaces"></a>Partnernamespaces
* Ressource: `partnerNamespaces`
* Verwendet von: Partner
* Beschreibung: Diese Ressource stellt eine regionale Ressource zum Veröffentlichen von Kundenereignissen bereit. Jeder Partnernamespace verfügt über einen Veröffentlichungsendpunkt und Authentifizierungsschlüssel. Über den Namespace fordert der Partner auch ein Partnerthema für einen bestimmten Kunden an und listet aktive Kunden auf.
* Umfang: Diese Ressource befindet sich im Abonnement des Partners.

### <a name="event-channel"></a>Ereigniskanal
* Ressource: `partnerNamespaces/eventChannels`
* Verwendet von: Partner
* Beschreibung: Die Ereigniskanäle stellen eine Spiegelung des Partnerthemas des Kunden dar. Wenn Sie einen Ereigniskanal erstellen und das Azure-Abonnement und die Ressourcengruppe des Kunden in den Metadaten angeben, signalisieren Sie Event Grid, ein Partnerthema für den Kunden zu erstellen. Event Grid gibt einen Azure Resource Manager-Aufruf aus, um im Abonnement des Kunden ein entsprechendes Partnerthema zu erstellen. Das Partnerthema wird mit dem Status „Ausstehend“ erstellt. Es gibt eine 1:1-Verbindung zwischen den einzelnen Ereigniskanälen und Partnerthemen.
* Umfang: Diese Ressource befindet sich im Abonnement des Partners.

### <a name="partner-topics"></a>Partnerthemen
* Ressource: `partnerTopics`
* Verwendet von: Kunden
* Beschreibung: Partnerthemen ähneln benutzerdefinierten Themen und Systemthemen in Event Grid. Jedes Partnerthema wird einer bestimmten Quelle (z. B. `Contoso:myaccount`) und einem bestimmten Partnerthementyp (z. B. Contoso) zugeordnet. Kunden erstellen Ereignisabonnements im Partnerthema, um Ereignisse an verschiedene Ereignishandler weiterzuleiten.

    Kunden können diese Ressource nicht direkt erstellen. Die einzige Möglichkeit zum Erstellen eines Partnerthemas ist ein Partnervorgang, bei dem ein Ereigniskanal erstellt wird.
* Umfang: Diese Ressource befindet sich im Abonnement des Kunden.

### <a name="partner-topic-types"></a>Partnerthementypen
* Ressource: `partnerTopicTypes`
* Verwendet von: Kunden
* Beschreibung: Partnerthementypen sind mandantenweite Ressourcentypen, mit denen Kunden die Liste der genehmigten Partnerthementypen ermitteln können. Die URL sieht wie folgt aus: https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes).
* Umfang: Global

## <a name="publish-events-to-event-grid"></a>Veröffentlichen von Ereignissen in Event Grid
Wenn Sie einen Partnernamespace in einer Azure-Region erstellen, erhalten Sie einen regionalen Endpunkt und entsprechende Authentifizierungsschlüssel. Veröffentlichen Sie an diesem Endpunkt Batches von Ereignissen für alle Kundenereigniskanäle in diesem Namespace. Azure Event Grid ordnet jedes Ereignis basierend auf dem Quellfeld im Ereignis den entsprechenden Partnerthemen zu.

### <a name="event-schema-cloudevents-v10"></a>Ereignisschema: CloudEvents v1.0
Veröffentlichen Sie mithilfe des CloudEvents 1.0-Schemas Ereignisse in Azure Event Grid. Event Grid unterstützt sowohl den strukturierten Modus als auch den Batchmodus. CloudEvents 1.0 ist das einzige unterstützte Ereignisschema für Partnernamespaces.

### <a name="example-flow"></a>Beispielablauf

1.  Der Veröffentlichungsdienst führt eine HTTP POST-Anforderung auf `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` aus.
1.  Schließen Sie einen Headerwert namens aeg-sas-key in die Anforderung ein, der einen Schlüssel für die Authentifizierung enthält. Dieser Schlüssel wird während der Erstellung des Partnernamespaces bereitgestellt. Ein gültiger Headerwert ist beispielsweise aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
1.  Legen Sie für den Content-Type-Header Folgendes fest: „application/cloudevents-batch+json; charset=UTF-8a“.
1.  Führen Sie für die Veröffentlichungs-URL eine HTTP POST-Abfrage mit einem Batch von Ereignissen der entsprechenden Region aus. Beispiel:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Nach dem Veröffentlichen am Endpunkt des Partnernamespaces erhalten Sie eine Antwort. Die Antwort ist ein HTTP-Standardantwortcode. Einige häufige Antworten lauten:

| Ergebnis                             | Antwort              |
|------------------------------------|-----------------------|
| Erfolg                            | 200 – OK                |
| Fehlerhaftes Format der Ereignisdaten    | 400 – Ungültige Anforderung       |
| Ungültiger Zugriffsschlüssel                 | 401 – Nicht autorisiert      |
| Falscher Endpunkt                 | 404 – Nicht gefunden         |
| Array oder Ereignis überschreitet Größengrenzwerte | 413 Nutzlast zu groß |

## <a name="references"></a>References

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM-Vorlage](/azure/templates/microsoft.eventgrid/allversions)
  * [ARM-Vorlagenschema](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST-APIs](/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [CLI-Erweiterung](/cli/azure/ext/eventgrid/)

### <a name="sdks"></a>SDKs
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Nächste Schritte
- [Partnerthemen in Azure Event Grid (Vorschau)](partner-events-overview.md)
- [Onboarding-Formular für Partnerthemen](https://aka.ms/gridpartnerform)
- [Auth0-Partnerthema](auth0-overview.md)
- [Verwenden des Auth0-Partnerthemas](auth0-how-to.md)
