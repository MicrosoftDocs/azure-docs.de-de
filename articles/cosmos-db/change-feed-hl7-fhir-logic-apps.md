---
title: Änderungsfeed für HL7 FHIR-Ressourcen – Azure Cosmos DB
description: Hier erfahren Sie, wie Sie mithilfe von Azure Logic Apps, Azure Cosmos DB und Service Bus Änderungsbenachrichtigungen für HL7 FHIR-Krankenakten einrichten.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: 49ef63969bd603c25d120dc5cb93ed30dda04241
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257268"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Benachrichtigen von Patienten über Änderungen an HL7 FHIR-Krankenakten mithilfe von Logic Apps und Azure Cosmos DB

Azure-MVP Howard Edidin wurde kürzlich von einem Unternehmen aus dem Gesundheitswesen kontaktiert, das seinem Patientenportal eine neue Funktion hinzufügen wollte. Das Unternehmen wollte Benachrichtigungen an Patienten senden, wenn deren Krankenakte aktualisiert wurde, und die Patienten sollten in der Lage sein, diese Updates zu abonnieren. 

In diesem Artikel wird die Änderungsfeed-Benachrichtigungslösung erläutert, die mithilfe von Azure Cosmos DB, Logic Apps und Service Bus für dieses Unternehmen erstellt wurde. 

## <a name="project-requirements"></a>Anforderungen des Projekts
- Anbieter senden HL7-Ressourcen als C-CDA-Dokumente (Consolidated-Clinical Document Architecture) im XML-Format. Zu C-CDA-Dokumenten zählen nahezu alle Arten von klinischen Dokumenten – etwa Informationen zu Familienanamnese und Impfungen, aber auch Verwaltungs-, Workflow- und Finanzdokumente. 
- C-CDA-Dokumente werden in [HL7 FHIR-Ressourcen](https://hl7.org/fhir/2017Jan/resourcelist.html) im JSON-Format konvertiert.
- Geänderte FHIR-Ressourcendokumente werden per E-Mail im JSON-Format gesendet.

## <a name="solution-workflow"></a>Lösungsworkflow 

Für das Projekt waren folgende allgemeine Workflowschritte erforderlich: 
1. Konvertieren von C-CDA-Dokumenten in FHIR-Ressourcen
2. Ausführen wiederholter Triggerabfragen für geänderte FHIR-Ressourcen 
2. Aufrufen einer benutzerdefinierten Anwendung (FhirNotificationApi), um eine Verbindung mit Azure Cosmos DB herzustellen und mittels Abfrage zu überprüfen, ob neue oder geänderte Dokumente vorliegen
3. Speichern der Antwort in der Service Bus-Warteschlange
4. Abrufen neuer Nachrichten in der Service Bus-Warteschlange
5. Senden von E-Mail-Benachrichtigungen an Patienten

## <a name="solution-architecture"></a>Lösungsarchitektur
Für diese Lösung sind drei Logik-Apps erforderlich, um die oben genannten Anforderungen zu erfüllen und den Lösungsworkflow abzuschließen. Folgende Logik-Apps werden benötigt:
1. **HL7 FHIR-Zuordnungs-App:** Empfängt das HL7 C-CDA-Dokument, transformiert es in die FHIR-Ressource und speichert es in Azure Cosmos DB.
2. **EGA-App:** Fragt das Azure Cosmos DB-FHIR-Repository ab und speichert die Antwort in einer Service Bus-Warteschlange. Diese Logik-App verwendet eine [API-App](#api-app), um neue und geänderte Dokumente abzurufen.
3. **Prozessbenachrichtigungs-App:** Sendet eine E-Mail-Benachrichtigung mit den FHIR-Ressourcendokumenten als Nachrichtentext.

![Die drei Logik-Apps dieser FHIR HL7-Lösung für das Gesundheitswesen](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>In der Lösung verwendete Azure-Dienste

#### <a name="azure-cosmos-db-sql-api"></a>SQL-API von Azure Cosmos DB
Azure Cosmos DB ist das Repository für die FHIR-Ressourcen, wie in der folgenden Abbildung zu sehen.

![Das in diesem HL7 FHIR-Gesundheitstutorial verwendete Azure Cosmos DB-Konto](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Logik-Apps wickeln den Workflowprozess ab. Die folgenden Screenshots zeigen die für diese Lösung erstellten Logik-Apps. 


1. **HL7 FHIR-Zuordnungs-App:** Empfängt das HL7 C-CDA-Dokument und transformiert es unter Verwendung des Enterprise Integration Packs für Logic Apps in eine FHIR-Ressource. Das Enterprise Integration Pack wickelt die Zuordnung von C-CDA zu FHIR ab.

    ![Die Logik-App zum Empfangen von HL7 FHIR-Krankenakten](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **EGA-App:** Fragt das Azure Cosmos DB-FHIR-Repository ab und speichert die Antwort in einer Service Bus-Warteschlange. Den Code für die GetNewOrModifiedFHIRDocuments-App finden Sie weiter unten.

    ![Die Logik-App zum Abfragen von Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Prozessbenachrichtigungs-App:** Sendet eine E-Mail-Benachrichtigung mit den FHIR-Ressourcendokumenten als Nachrichtentext.

    ![Die Logik-App zum Senden von Patienten-E-Mails mit der HL7 FHIR-Ressource als Text](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
Die folgende Abbildung zeigt die Patientenwarteschlange. Der Wert der Tag-Eigenschaft wird für den E-Mail-Betreff verwendet.

![Die Service Bus-Warteschlange aus diesem HL7 FHIR-Tutorial](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Eine API-App
Eine API-App stellt eine Verbindung mit Azure Cosmos DB her und fragt neue oder geänderte FHIR-Dokumente nach Ressourcentyp ab. Diese App verfügt über einen Controller (**FhirNotificationApi**) mit einem Vorgang (**GetNewOrModifiedFhirDocuments**). Weitere Informationen finden Sie unter [Quelle für die API-App](#api-app-source).

Wir verwenden die [`CreateDocumentChangeFeedQuery`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx)-Klasse aus der SQL .NET-API von Azure Cosmos DB. Weitere Informationen finden Sie im [Artikel zum Änderungsfeed](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>GetNewOrModifiedFhirDocuments-Vorgang

**Eingaben**
- Datenbank-ID (DatabaseId)
- Sammlungs-ID (CollectionId)
- Name des HL7 FHIR-Ressourcentyps
- Boolescher Wert: Am Anfang beginnen
- Integer: Anzahl zurückgegebener Dokumente

**Ausgaben**
- Erfolg: Statuscode: 200, Antwort: Liste mit Dokumenten (JSON-Array)
- Fehler: Statuscode: 404, Antwort: „No Documents found for '*resource name'* Resource“ (Für die Ressource „Ressourcenname“ wurden keine Dokumente gefunden)

<a id="api-app-source"></a>

**Quelle für die API-App**

```csharp

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modified FHIR Documents By Resource Type " +
                "from Last Run Date or Beginning of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modified Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Testen von „FhirNotificationApi“ 

Die folgende Abbildung zeigt, wie [FhirNotificationApi](#api-app-source) mithilfe von Swagger getestet wurde.

![Die zum Testen der API-App verwendete Swagger-Datei](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Dashboard des Azure-Portals

Die folgende Abbildung zeigt alle im Azure-Portal ausgeführten Azure-Dienste für diese Lösung.

![Das Azure-Portal mit allen verwendeten Diensten aus diesem HL7 FHIR-Tutorial](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Zusammenfassung

- Sie haben gelernt, dass Azure Cosmos DB native Unterstützung für Benachrichtigungen für neue oder geänderte Dokumente bietet und wie einfach sich diese nutzen lassen. 
- Mit Logic Apps können Sie Workflows erstellen, ohne Code schreiben zu müssen.
- Und über Azure Service Bus-Warteschlangen können Sie die Verteilung der HL7 FHIR-Dokumente abwickeln.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Cosmos DB finden Sie auf der [Azure Cosmos DB-Startseite](https://azure.microsoft.com/services/cosmos-db/). Weitere Informationen zu Logic Apps finden Sie unter [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


