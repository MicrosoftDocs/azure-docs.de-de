---
title: Benachrichtigung eines nicht unter Azure ausgeführten Systems durch eine klassische Metrikwarnung mithilfe eines Webhooks
description: Erfahren Sie, wie Sie Azure-Metrikwarnungen an andere, Azure-fremde Systeme umleiten.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 264f3eb042a3c29523ed93df93dfa6d45c00ae87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345776"
---
# <a name="have-a-classic-metric-alert-notify-a-non-azure-system-using-a-webhook"></a>Benachrichtigung eines nicht unter Azure ausgeführten Systems durch eine klassische Metrikwarnung mithilfe eines Webhooks
Mithilfe von Webhooks können Benutzer eine Azure-Warnbenachrichtigung zur Nachbearbeitung oder Ausführung benutzerdefinierter Aktionen an andere Systeme weiterleiten. Sie können einen Webhook für eine Warnung verwenden, um sie an Dienste weiterzuleiten, die SMS-Nachrichten versenden, um Fehler zu protokollieren, um ein Team per Chat-/Messagingdienst zu benachrichtigen oder um verschiedene andere Aktionen auszuführen. 

In diesem Artikel wird beschrieben, wie Sie einen Webhook für eine Azure-Metrikwarnung festlegen. Außerdem erfahren Sie, wie die Nutzlast für die HTTP POST-Anforderung an einen Webhook aussieht. Informationen zur Einrichtung und zum Schema einer Azure-Aktivitätsprotokollwarnung (Warnung für Ereignisse) finden Sie unter [Call a webhook on an Azure activity log alert](alerts-log-webhook.md) (Aufrufen eines Webhooks für eine Azure-Aktivitätsprotokollwarnung).

Azure-Warnungen senden den Inhalt von Warnungen mittels HTTP POST im JSON-Format an einen Webhook-URI, den Sie beim Erstellen der Warnung angeben. Das Schema wird weiter unten in diesem Artikel erläutert. Der URI muss ein gültiger HTTP- oder HTTPS-Endpunkt sein. Bei Aktivierung einer Warnung veröffentlicht Azure jeweils einen Eintrag pro Anforderung.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurieren von Webhooks über das Azure-Portal
Sie können den Webhook-URI im [Azure-Portal](https://portal.azure.com/) unter **Create/Update Alerts** (Warnungen erstellen/aktualisieren) hinzufügen oder aktualisieren.

![Hinzufügen eines Warnungsregelbereichs](./media/alerts-webhooks/Alertwebhook.png)

Sie können auch [Azure PowerShell-Cmdlets](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), eine [plattformübergreifende Befehlszeilenschnittstelle](../../azure-monitor/platform/cli-samples.md#work-with-alerts) oder [Azure Monitor-REST-APIs](https://msdn.microsoft.com/library/azure/dn933805.aspx) verwenden, um eine Warnung mit Veröffentlichung an einen Webhook-URI zu konfigurieren.

## <a name="authenticate-the-webhook"></a>Authentifizieren des Webhooks
Die Authentifizierung des Webhooks kann mithilfe der tokenbasierten Autorisierung erfolgen. Der Webhook-URI wird mit einer Token-ID gespeichert. Beispiel: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Nutzlast und Schema
Der POST-Vorgang enthält für alle metrikbasierten Warnungen die folgende JSON-Nutzlast und das folgende Schema:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Feld | Erforderlich | Feste Gruppe von Werten | Notizen |
|:--- |:--- |:--- |:--- |
| status |J |Activated, Resolved |Der Status der Warnung auf der Grundlage der festgelegten Bedingungen. |
| context |J | |Der Warnungskontext. |
| timestamp |J | |Der Zeitpunkt, an dem die Warnung ausgelöst wurde. |
| id |J | |Jede Warnungsregel verfügt über eine eindeutige ID. |
| name |J | |Der Name der Warnung. |
| description |J | |Eine Beschreibung der Warnung. |
| conditionType |J |Metric, Event |Zwei Arten von Warnungen werden unterstützt: Metrik- und Ereigniswarnungen. Metrikwarnungen basieren auf einer Metrikbedingung. Ereigniswarnungen basieren auf einem Ereignis im Aktivitätsprotokoll. Überprüfen Sie mithilfe dieses Werts, ob die Warnung auf einer Metrik oder einem Ereignis basiert. |
| condition |J | |Die spezifischen Felder, die anhand des Werts von **conditionType** überprüft werden. |
| metricName |Für Metrikwarnungen | |Der Name der Metrik, die definiert, welche Elemente mit der Regel überwacht werden. |
| metricUnit |Für Metrikwarnungen |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds |Die in der Metrik zulässige Einheit. Siehe [Zulässige Werte](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Für Metrikwarnungen | |Der tatsächliche Wert der Metrik, die die Warnung ausgelöst hat. |
| threshold |Für Metrikwarnungen | |Der Schwellenwert, bei dem die Warnung aktiviert wird. |
| windowSize |Für Metrikwarnungen | |Der Zeitraum, in dem die Aktivität der Warnung basierend auf dem Schwellenwert überwacht wird. Der Wert muss zwischen fünf Minuten und einem Tag liegen. Der Wert muss im Format der Zeitspanne nach ISO 8601 angegeben werden. |
| timeAggregation |Für Metrikwarnungen |Average, Last, Maximum, Minimum, None, Total |Legt fest, wie die erfassten Daten im Zeitverlauf kombiniert werden sollen. Der Standardwert ist "Average". Siehe [Zulässige Werte](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Für Metrikwarnungen | |Der Operator, der verwendet wird, um die aktuellen Metrikdaten mit dem festgelegten Schwellenwert zu vergleichen. |
| subscriptionId |J | |Die Azure-Abonnement-ID. |
| resourceGroupName |J | |Der Name der Ressourcengruppe für die betroffene Ressource. |
| resourceName |J | |Der Ressourcenname der betroffenen Ressource. |
| resourceType |J | |Der Ressourcentyp der betroffenen Ressource. |
| resourceId |J | |Die Ressourcen-ID der betroffenen Ressource. |
| resourceRegion |J | |Die Region oder der Speicherort der betroffenen Ressource. |
| portalLink |J | |Ein direkter Link zur Ressourcenzusammenfassungsseite des Portals. |
| properties |N |Optional |Ein Satz von Schlüssel-Wert-Paaren mit Details zum Ereignis. Beispiel: `Dictionary<String, String>`. Das Feld "properties" ist optional. Auf einer angepassten Benutzeroberfläche oder in einem Workflow, der auf der Logik-App beruht, können Benutzer Schlüssel-Wert-Paare eingeben, die über die Nutzlast übergeben werden können. Alternativ können benutzerdefinierte Eigenschaften direkt über den Webhook-URI an den Webhook zurückgegeben werden (als Abfrageparameter). |

> [!NOTE]
> Das Feld **properties** kann nur mithilfe der [Azure Monitor REST-APIs](https://msdn.microsoft.com/library/azure/dn933805.aspx) festgelegt werden.
>
>

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über Azure-Warnungen und Webhooks im Video [Integrate Azure Alerts with PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080) (Integrieren von Azure-Warnungen mit PagerDuty).
* Erfahren Sie, wie Sie [Azure Automation-Skripts (Runbooks) für Azure-Warnungen ausführen](https://go.microsoft.com/fwlink/?LinkId=627081).
* Informieren Sie sich über das [Senden einer SMS-Nachricht über Twilio auf der Grundlage einer Azure-Warnung mithilfe einer Logik-App](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Informieren Sie sich über das [Senden einer Slack-Nachricht auf der Grundlage einer Azure-Warnung mithilfe einer Logik-App](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Informieren Sie sich über das [Senden einer Nachricht an eine Azure-Warteschlange auf der Grundlage einer Azure-Warnung mithilfe einer Logik-App](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

