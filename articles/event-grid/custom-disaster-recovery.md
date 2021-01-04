---
title: Notfallwiederherstellung für benutzerdefinierte Themen in Azure Event Grid
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie Ihre Ereignisarchitektur einrichten, um eine Wiederherstellung durchzuführen, wenn in einer Region Fehler für den Event Grid-Dienst auftreten.
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e37cb6a0679ee2e249de4ed8fa31c40d5082ea4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020142"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Erstellen einer eigenen Notfallwiederherstellung für benutzerdefinierte Themen in Event Grid
Die Notfallwiederherstellung konzentriert sich auf die Wiederherstellung nach einem schwerwiegenden Ausfall der Anwendungsfunktionalität. In diesem Tutorial wird Schritt für Schritt erläutert, wie Sie Ihre Ereignisarchitektur einrichten, um eine Wiederherstellung durchzuführen, wenn in einer bestimmten Region Fehler des Event Grid-Diensts auftreten.

In diesem Tutorial erfahren Sie, wie Sie eine Aktiv/Passiv-Failoverarchitektur für benutzerdefinierte Themen in Event Grid erstellen. Zum Durchführen des Failovers spiegeln Sie Ihre Themen und Abonnements in zwei Regionen, und anschließend verwalten Sie ein Failover, wenn ein Thema fehlerhaft wird. Die Architektur in diesem Tutorial führt ein Failover für den gesamten neuen Datenverkehr aus. Beachten Sie, dass bereits aktive Ereignisse bei diesem Setup erst wiederhergestellt werden, wenn die betroffene Region wieder fehlerfrei ist.

> [!NOTE]
> Event Grid unterstützt jetzt die automatische Notfallwiederherstellung mit Georeplikation (GeoDR) auf Serverseite. Sie können weiterhin die clientseitige Notfallwiederherstellungslogik implementieren, wenn Sie den Failoverprozess präziser steuern möchten. Ausführliche Informationen zur automatischen Notfallwiederherstellung mit Georeplikation finden Sie unter [Server-side geo disaster recovery in Azure Event Grid](geo-disaster-recovery.md) (Serverseitige Notfallwiederherstellung mit Georeplikation in Azure Event Grid).

## <a name="create-a-message-endpoint"></a>Erstellen eines Nachrichtenendpunkts

Zum Testen der Failoverkonfiguration benötigen Sie einen Endpunkt, an dem Ihre Ereignisse empfangen werden. Der Endpunkt ist nicht Teil der Failoverinfrastruktur, sondern fungiert als Ereignishandler, um das Durchführen von Tests zu erleichtern.

Um die Tests zu vereinfachen, stellen Sie eine [vorgefertigte Web-App](https://github.com/Azure-Samples/azure-event-grid-viewer) bereit, die die Ereignismeldungen anzeigt. Die bereitgestellte Lösung umfasst einen App Service-Plan, eine App Service-Web-App und Quellcode von GitHub.

1. Wählen Sie **Deploy to Azure** (In Azure bereitstellen), um die Lösung für Ihr Abonnement bereitzustellen. Geben Sie im Azure-Portal Werte für die Parameter an.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Aquent." /></a>

1. Die Bereitstellung kann einige Minuten dauern. Nach erfolgreichem Abschluss der Bereitstellung können Sie Ihre Web-App anzeigen und sich vergewissern, dass sie ausgeführt wird. Navigieren Sie hierzu in einem Webbrowser zu `https://<your-site-name>.azurewebsites.net`.
Notieren Sie diese URL. Sie benötigen sie später noch.

1. Die Website wird angezeigt, aber es wurden noch keine Ereignisse bereitgestellt.

   ![Anzeigen der neuen Website](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Erstellen der primären und sekundären Themen

Erstellen Sie zunächst zwei Event Grid-Themen. Diese Themen dienen als Ihr primäres und Ihr sekundäres Thema. Standardmäßig werden die Ereignisse durch Ihr primäres Thema weitergeleitet. Wenn in der primären Region ein Dienstausfall auftritt, übernimmt die sekundäre Region die Verarbeitung.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie in der linken oberen Ecke des Azure-Hauptmenüs **Alle Dienste** aus, suchen Sie nach **Event Grid**, und wählen Sie **Event Grid-Themen** aus.

   ![Menü „Event Grid-Themen“](./media/custom-disaster-recovery/select-topics-menu.png)

    Klicken Sie auf den Stern neben „Event Grid-Themen“, um den Eintrag dem Ressourcenmenü hinzuzufügen, damit Sie künftig leichter darauf zugreifen können.

1. Wählen Sie im Menü „Event Grid-Themen“ die Option **+ HINZUFÜGEN** aus, um Ihr primäres Thema zu erstellen.

   * Geben Sie einen logischen Namen für das Thema an, und fügen Sie „-primär“ als Suffix hinzu, um die Nachverfolgung zu erleichtern.
   * Die Region dieses Themas ist Ihre primäre Region.

     ![Dialogfeld zum Erstellen des primären Event Grid-Themas](./media/custom-disaster-recovery/create-primary-topic.png)

1. Navigieren Sie nach der Erstellung des Themas zu diesem Thema, und kopieren Sie den **Themenendpunkt**. Sie benötigen den URI später.

    ![Primäres Event Grid-Thema](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Rufen Sie den Zugriffsschlüssel für das Thema ab. Diesen benötigen Sie ebenfalls an späterer Stelle. Klicken Sie im Ressourcenmenü auf **Zugriffsschlüssel**, und kopieren Sie Schlüssel 1.

    ![Abrufen des Schlüssels für das primäre Thema](./media/custom-disaster-recovery/get-primary-access-key.png)

1. Klicken Sie auf dem Blatt „Thema“ auf **+ Ereignisabonnement**, um ein Abonnement zu erstellen, das das Thema mit der in den Voraussetzungen für das Tutorial erstellten Ereignisempfänger-Website verbindet.

   * Geben Sie einen logischen Namen für das Ereignisabonnement an, und fügen Sie „-primär“ als Suffix hinzu, um die Nachverfolgung zu erleichtern.
   * Wählen Sie den Endpunkttyp „Webhook“ aus.
   * Legen Sie den Endpunkt auf die Ereignis-URL Ihres Ereignisempfängers fest. Die URL sollte in etwa wie folgt aussehen: `https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Screenshot, der die Seite „Ereignisabonnement erstellen: Basic“ mit hervorgehobenen Werten für „Name“, „Endpunkttyp“ und „Endpunkt“ zeigt.](./media/custom-disaster-recovery/create-primary-es.png)

1. Wiederholen Sie die obigen Schritte zum Erstellen des sekundären Themas und Abonnements. Ersetzen Sie dabei das Suffix „-primär“ durch „-sekundär“, um die Nachverfolgung zu erleichtern. Denken Sie daran, für dieses Abonnement eine andere Azure-Region auszuwählen. Sie können eine beliebige Region auswählen, es wird jedoch empfohlen, [Azure-Regionspaare](../best-practices-availability-paired-regions.md) zu verwenden. Durch das Platzieren des sekundären Themas und Abonnements in einer anderen Region wird sichergestellt, dass Ihre neuen Ereignisse auch bei einem Ausfall der primären Region weitergeleitet werden.

Sie sollten nun über Folgendes verfügen:

   * Eine Ereignisempfänger-Website zu Testzwecken
   * Ein primäres Thema in Ihrer primären Region
   * Ein primäres Ereignisabonnement, das Ihr primäres Thema mit der Ereignisempfänger-Website verbindet
   * Ein sekundäres Thema in Ihrer sekundären Region
   * Ein sekundäres Ereignisabonnement, das Ihr primäres Thema mit der Ereignisempfänger-Website verbindet

## <a name="implement-client-side-failover"></a>Implementieren des clientseitigen Failovers

Da Sie nun über ein regional redundantes Themen- und Abonnementpaar verfügen, können Sie das clientseitige Failover implementieren. Dazu stehen mehrere Möglichkeiten zur Auswahl, alle Failoverimplementierungen besitzen jedoch die folgende Funktion: Wenn ein Thema nicht mehr fehlerfrei ist, wird Datenverkehr zum anderen Thema umgeleitet.

### <a name="basic-client-side-implementation"></a>Grundlegende clientseitige Implementierung

Der folgende Code ist ein einfacher .NET-Herausgeber, der immer zuerst versucht, die Veröffentlichung in Ihrem primären Thema vorzunehmen. Wenn dies nicht möglich ist, erfolgt dann ein Failover zum sekundären Thema. In beiden Fällen wird außerdem mit einem GET-Vorgang für `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health` die Integritäts-API des anderen Themas überprüft. Ein fehlerfreies Thema sollte immer mit **200 OK** antworten, wenn ein GET-Vorgang für den Endpunkt **/api/Health** ausgeführt wird.

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>Ausprobieren

Sie haben nun alle Komponenten eingerichtet und können Ihre Failoverimplementierung testen. Führen Sie das obige Beispiel in Visual Studio Code oder Ihrer bevorzugten Umgebung aus. Ersetzen Sie die folgenden vier Werte durch die Endpunkte und Schlüssel Ihrer Themen:

   * primaryTopic: Endpunkt für Ihr primäres Thema
   * secondaryTopic: Endpunkt für Ihr sekundäres Thema
   * primaryTopicKey: Schlüssel für Ihr primäres Thema
   * secondaryTopicKey: Schlüssel für Ihr sekundäres Thema

Versuchen Sie, den Ereignisherausgeber auszuführen. Ihre Testereignisse sollten wie unten dargestellt im Event Grid-Viewer angezeigt werden.

![Primäres Event Grid-Ereignisabonnement](./media/custom-disaster-recovery/event-grid-viewer.png)

Um sicherzustellen, dass das Failover funktioniert, können Sie einige Zeichen im Schlüssel Ihres primären Themas ändern, sodass es nicht mehr gültig ist. Versuchen Sie dann erneut, den Herausgeber auszuführen. Es sollten weiterhin neue Ereignisse im Event Grid-Viewer angezeigt werden. Wenn Sie sich aber Ihre Konsole ansehen, werden Sie feststellen, dass die Ereignisse jetzt über das sekundäre Thema veröffentlicht werden.

### <a name="possible-extensions"></a>Mögliche Erweiterungen

Es gibt viele Möglichkeiten, dieses Beispiel entsprechend Ihren Anforderungen zu erweitern. Bei Szenarien mit hohem Volumen kann es sinnvoll sein, die Integritäts-API in regelmäßigen Abständen unabhängig zu überprüfen. Auf diese Weise müssten Sie sie beim Ausfall eines Themas nicht bei jeder einzelnen Veröffentlichung überprüfen. Sobald Sie wissen, dass ein Thema nicht fehlerfrei ist, können Sie Ereignisse standardmäßig über das sekundäre Thema veröffentlichen.

Ebenso können Sie basierend auf Ihren spezifischen Anforderungen eine Failbacklogik implementieren. Falls die Veröffentlichung im nächstgelegenen Rechenzentrum zum Verringern der Wartezeit für Sie entscheidend ist, können Sie die Integritäts-API eines Themas, für das ein Failover ausgeführt wurde, in regelmäßigen Abständen testen. Sobald es wieder fehlerfrei ist, wissen Sie, dass das Failback zum nächstgelegenen Rechenzentrum sicher durchgeführt werden kann.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Ereignisse an einem HTTP-Endpunkt empfangen](./receive-events.md).
- Entdecken Sie, wie Sie [Ereignisse an Hybrid Connections weiterleiten](./custom-event-to-hybrid-connection.md).
- Informieren Sie sich über die [Notfallwiederherstellung mit Azure DNS und Traffic Manager](../networking/disaster-recovery-dns-traffic-manager.md).
