---
title: Smart Detection – ungewöhnliche fehlgeschlagene Anforderungen in Application Insights | Microsoft Docs
description: Macht Sie auf ungewöhnliche Änderungen bei der Rate fehlgeschlagener Anforderungen an Ihre Web-App aufmerksam und bietet eine Analyse der Diagnose. Es ist keine Konfiguration erforderlich.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 978b63d74e6be4104ff53eef66e9633c78b90eb8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510633"
---
# <a name="smart-detection---failure-anomalies"></a>Smart Detection – ungewöhnliche fehlgeschlagene Anforderungen
[Application Insights](./app-insights-overview.md) warnt Sie automatisch und nahezu in Echtzeit, wenn es für Ihre Web-App zu einer ungewöhnlichen Häufung bei den fehlgeschlagenen Anforderungen kommt. Die Lösung erkennt eine ungewöhnliche Zunahme der Rate fehlerhafter HTTP-Anforderungen oder Abhängigkeitsaufrufen. Nicht erfolgreiche Anforderungen weisen normalerweise Antwortcodes im Bereich 400 oder höher auf. Um Sie bei der Selektierung und Diagnose des Problems zu unterstützen, wird in den Warnungsdetails eine Analyse der Merkmale der Fehler und der zugehörigen Anwendungsdaten angegeben. Außerdem werden Links zum Application Insights-Portal zur weiteren Diagnose bereitgestellt. Diese Funktion muss nicht eingerichtet oder konfiguriert werden, da sie Machine Learning-Algorithmen verwendet, um die normale Fehlerrate zu bestimmen.

Dieses Feature funktioniert für alle Web-Apps, die in der Cloud oder auf Ihren eigenen Servern gehostet werden und Anwendungsanforderungs- oder Abhängigkeitsdaten generieren. Es kann beispielsweise sein, dass Sie über eine Workerrolle verfügen, die [TrackRequest()](./api-custom-events-metrics.md#trackrequest) oder [TrackDependency()](./api-custom-events-metrics.md#trackdependency) aufruft.

Nachdem Sie [Application Insights für Ihr Projekt](./app-insights-overview.md) eingerichtet haben und Ihre App eine bestimmte Mindestmenge von Daten generiert hat, benötigt Smart Detection 24 Stunden, um das normale Verhalten Ihrer App zu ermitteln, bevor die Erkennung aktiviert wird und Warnungen senden kann.

Hier ist ein Beispiel für eine Warnung angegeben:

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="Beispiel für eine intelligente Erkennung mit Clusteranalyse im Umfeld des Fehlers" lightbox="./media/proactive-failure-diagnostics/013.png":::

Die Warnungsdetails enthalten Folgendes:

* Die Fehlerrate im Vergleich zum normalen App-Verhalten
* Die Anzahl von betroffenen Benutzern, damit Sie direkt wissen, wie ernst die Lage ist.
* Ein charakteristisches Muster für die Fehler. Dieses Beispiel enthält einen bestimmten Antwortcode, einen Anforderungsnamen (Vorgang) und eine Anwendungsversion. So wissen Sie sofort, wo im Code Sie suchen müssen. Weitere Möglichkeiten sind beispielsweise ein bestimmter Browser oder Clientbetriebssystem.
* Die Ausnahme, Protokollablaufverfolgungen und Abhängigkeitsfehler (Datenbanken oder andere externe Komponenten), die den charakterisierten Fehlern zugeordnet zu sein scheinen.
* Dies ist direkt mit relevanten Suchvorgängen in den Daten in Application Insights verknüpft.

## <a name="benefits-of-smart-detection"></a>Vorteile von Smart Detection
Normale [Metrikwarnungen](../platform/alerts-log.md) informieren Sie, dass möglicherweise ein Problem vorliegt. Per Smart Detection wird dagegen die Diagnose für Sie gestartet und ein großer Teil der Analyseschritte ausgeführt, die Sie ansonsten selbst ausführen müssten. Sie erhalten die Ergebnisse fein säuberlich verpackt und können so schnell zur Ursache des Problems vordringen.

## <a name="how-it-works"></a>Funktionsweise
Bei Smart Detection werden die von Ihrer App erhaltenen Daten und insbesondere die Fehlerraten überwacht. Diese Regel ermittelt die Anzahl von Anforderungen, bei denen die `Successful request`-Eigenschaft auf „false“ festgelegt ist, sowie die Anzahl von Abhängigkeitsaufrufen, bei denen die `Successful call`-Eigenschaft auf „false“ festgelegt ist. Für Anforderungen gilt standardmäßig `Successful request == (resultCode < 400)` (es sei denn, Sie haben benutzerdefinierten Code geschrieben, um Ihre eigenen [TrackRequest](./api-custom-events-metrics.md#trackrequest)-Aufrufe zu [filtern](./api-filtering-sampling.md#filtering) oder zu erstellen). 

Die Leistung Ihrer App weist ein typisches Verhaltensmuster auf. Einige Anforderungen oder Abhängigkeitsaufrufe sind anfälliger für Fehler als andere, und die gesamte Fehlerrate kann mit zunehmender Auslastung steigen. Bei Smart Detection werden diese Anomalien mithilfe von maschinellem Lernen ermittelt.

Wenn Daten aus Ihrer Web-App in Application Insights eingehen, wird beim Smart Detection-Vorgang das aktuelle Verhalten mit den Mustern der letzten Tage verglichen. Wenn ein ungewöhnlicher Anstieg der Fehlerrate im Vergleich zur vorherigen Leistung beobachtet wird, wird eine Analyse ausgelöst.

Wenn eine Analyse ausgelöst wird, führt der Dienst eine Clusteranalyse für die fehlgeschlagene Anforderung aus, um in den Werten ein Muster zu ermitteln, durch die sich die Fehler charakterisieren lassen. 

Im obigen Beispiel hat die Analyse ermittelt, dass die meisten Fehler zu einem bestimmten Ergebniscode, Anforderungsnamen, Server-URL-Host und einer Rolleninstanz gehören. 

Wenn Ihr Dienst mit diesen Aufrufen instrumentiert wurde, sucht der Analyzer nach einer Ausnahme und einem Abhängigkeitsfehler, die bzw. der zu Anforderungen im ermittelten Cluster gehört, sowie nach einem Beispiel für Ablaufverfolgungsprotokolle, die diesen Anforderungen zugeordnet sind.

Das Analyseergebnis wird Ihnen als Warnung gesendet, sofern Sie nichts anderes konfiguriert haben.

Wie auch bei den [manuell festgelegten Warnungen](../platform/alerts-log.md), können Sie den Status der ausgelösten Warnung untersuchen. Sie können die Warnung beseitigen, nachdem der Fehler behoben wurde. Konfigurieren Sie die Warnungsregeln auf der Seite „Warnungen“ Ihrer Application Insights-Ressource. Im Gegensatz zu anderen Warnungen müssen Sie Smart Detection jedoch nicht einrichten oder konfigurieren. Wenn Sie möchten, können Sie sie deaktivieren oder die Ziel-E-Mail-Adressen ändern.

### <a name="alert-logic-details"></a>Details zur Warnungslogik

Die Warnungen werden durch unseren proprietären Algorithmus für maschinelles Lernen ausgelöst, daher können wir keinen genauen Implementierungsdetails mitteilen. Dennoch verstehen wir, dass Sie in einigen Fällen mehr über die Funktionsweise der zugrunde liegenden Logik wissen müssen. Folgende primären Faktoren werden ausgewertet, um zu ermitteln, ob eine Warnung ausgelöst werden sollte: 

* Analyse des Prozentsatzes an Fehlern bei Anforderungen bzw. Abhängigkeiten in einem gleitenden Zeitfenster von 20 Minuten.
* Vergleich des Fehlerprozentsatzes der letzten 20 Minuten mit dem Prozentsatz der letzten 40 Minuten und der letzten sieben Tage sowie Suche nach signifikanten Abweichungen, die die Standardabweichung um das x-Fache überschreiten.
* Verwendung eines anpassbaren Limits für den minimal zulässigen Fehlerprozentsatz, der je nach Umfang an Anforderungen und Abhängigkeiten der App variiert.
* Es ist eine Logik vorhanden, mit der die Überwachungsmeldung für die ausgelöste Warnung automatisch aufgehoben werden kann, wenn das Problem für eine Dauer von 8 bis 24 Stunden nicht mehr erkannt wurde.
  Hinweis: Beim aktuellen Entwurf wird keine Benachrichtigung oder Aktion gesendet, wenn eine Smart Detection-Warnung aufgehoben wird. Im Azure-Portal können Sie überprüfen, ob eine Smart Detection-Warnung aufgehoben wurde.

## <a name="configure-alerts"></a>Konfigurieren von Warnungen

Sie können die Warnungsregel von Smart Detection im Portal deaktivieren oder Azure Resource Manager verwenden ([siehe Vorlagenbeispiel](./proactive-arm-config.md)).

Diese Warnungsregel wird mit einer zugehörigen [Aktionsgruppe](../platform/action-groups.md) mit dem Namen „Application Insights Smart Detection“ erstellt, die E-Mail- und Webhookaktionen enthält und erweitert werden kann, um beim Auftreten der Warnung zusätzliche Aktionen auszulösen.

> [!NOTE]
> Von dieser Warnungsregel gesendete E-Mail-Benachrichtigungen werden jetzt standardmäßig an Benutzer gesendet, denen die Rollen „Benutzer mit Leseberechtigung für Überwachungsdaten“ oder „Mitwirkender an der Überwachung“ zugewiesen sind. Weitere Informationen hierzu finden Sie [hier](./proactive-email-notification.md).
> Von dieser Warnungsregel gesendete Benachrichtigungen basieren auf dem [allgemeinen Warnungsschema](../platform/alerts-common-schema.md).
>

Öffnen Sie die Seite „Warnungen“. Warnungsregeln vom Typ „Fehleranomalien“ werden zusammen mit allen Warnungen aufgeführt, die Sie ggf. manuell festgelegt haben, und Sie können sehen, ob diese derzeit den Status „Warnung“ aufweisen.

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="Klicken Sie auf der Application Insights-Ressourcenseite auf die Kachel „Warnungen“ und dann auf „Warnungsregeln verwalten“." lightbox="./media/proactive-failure-diagnostics/021.png":::

Klicken Sie auf die Warnung, um sie zu konfigurieren.

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="Bildschirm „Regelkonfiguration“." lightbox="./media/proactive-failure-diagnostics/032.png":::

Beachten Sie, dass Sie eine Warnungsregel vom Typ „Fehleranomalien“ deaktivieren oder löschen können, aber das Erstellen einer weiteren Warnungsregel unter derselben Application Insights-Ressource nicht möglich ist.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Beispiel für Warnung „Fehleranomalien“: Beispiel für Webhooknutzlast

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
                            "property": "DetectionSummary",
                            "displayName": "What was detected?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "FormattedOccurenceTime",
                            "displayName": "When did this occur?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "DetectedFailureRate",
                            "displayName": "Detected failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "NormalFailureRate",
                            "displayName": "Normal failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "chartType": "Line",
                            "xAxisType": "Date",
                            "yAxisType": "Percentage",
                            "xAxisName": "",
                            "yAxisName": "",
                            "property": "FailureRateChart",
                            "displayName": "Failure rate over last 12 hours",
                            "type": "Chart",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "defaultLoad": true,
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Selektieren und Diagnostizieren einer Warnung

Eine Warnung gibt an, dass ein ungewöhnlicher Anstieg bei der Rate der Anforderungsfehler erkannt wurde. Wahrscheinlich liegt ein Problem mit Ihrer App oder deren Umgebung vor.

Klicken Sie zur weiteren Untersuchung auf die Option zum Anzeigen aller Informationen in Application Insights. Über die Links auf dieser Seite gelangen Sie direkt zu einer [Suchseite](./diagnostic-search.md), die nach den relevanten Anforderungen, Ausnahmen, Abhängigkeiten oder Ablaufverfolgungen gefiltert ist. 

Sie können auch das [Azure-Portal](https://portal.azure.com) öffnen, zur Application Insights-Ressource für Ihre App wechseln und die Seite „Fehler“ öffnen.

Wenn Sie auf „Fehlerdiagnose“ klicken, erhalten Sie weitere Details und können das Problem leichter beheben.

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="Diagnosesuche." lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

Anhand des Prozentsatzes der Anforderungen und der Anzahl der betroffenen Benutzer können Sie entscheiden, wie dringend das Problem ist. Im Beispiel oben verdeutlicht die Fehlerrate von 78,5 %, die einer normalen Fehlerrate von 2,2 % gegenübersteht, dass ganz offensichtlich Probleme bestehen. Allerdings sind nur 46 Benutzer betroffen. Wenn es sich um Ihre App handeln würde, könnten Sie beurteilen, wie schwerwiegend dies ist.

In vielen Fällen können Sie das Problem über den Anforderungsnamen, die Ausnahme, den Abhängigkeitsfehler und die Ablaufverfolgungsdaten schnell diagnostizieren.

In diesem Beispiel ist eine Ausnahme für die SQL-Datenbank aufgetreten, weil das Anforderungslimit erreicht wurde.

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="Details zu Anforderungen mit Fehlern." lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>Überprüfen aktueller Warnungen

Klicken Sie auf der Application Insights-Ressourcenseite auf **Warnungen**, um die zuletzt ausgelösten Warnungen anzuzeigen:

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="Zusammenfassung von Warnungen." lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>Wo liegt der Unterschied?
Smart Detection für ungewöhnliche fehlgeschlagene Anforderungen ergänzt andere ähnliche, aber doch verschiedene Features von Application Insights.

* [Metrikwarnungen](../platform/alerts-log.md) werden von Ihnen festgelegt und können eine Vielzahl von Metriken überwachen, z. B. die CPU-Belegung, Anforderungsraten, Seitenladezeiten usw. Sie können sie z. B. einsetzen, um rechtzeitig benachrichtigt zu werden, wenn weitere Ressourcen hinzugefügt werden müssen. Im Gegensatz dazu deckt Smart Detection einen kleinen Bereich kritischer Metriken ab (zurzeit nur die Rate von fehlerhaften Anforderungen), durch die Sie nahezu in Echtzeit benachrichtigt werden, sobald die Rate der fehlerhaften Anforderungen für Ihre Web-App im Vergleich zum normalen Verhalten der Web-App ansteigt. Im Gegensatz zu Metrikwarnungen werden bei Smart Detection als Reaktion auf Verhaltensänderungen automatisch Schwellenwerte festgelegt und aktualisiert. Darüber hinaus werden von Smart Detection auch die Diagnoseschritte eingeleitet, damit Sie Zeit für die Problembehebung sparen.

* [Smart Detection für Leistungsprobleme](proactive-performance-diagnostics.md) verwendet zudem intelligente Funktionen, um ungewöhnliche Muster in Ihren Metriken zu ermitteln, und muss nicht von Ihnen konfiguriert werden. Im Gegensatz zu Smart Detection für ungewöhnliche fehlgeschlagene Anforderungen besteht der Zweck von Smart Detection für Leistungsprobleme jedoch in der Ermittlung von Nutzungssegmenten, die beispielsweise durch bestimmte Seiten in einem bestimmten Browsertyp nicht zufriedenstellend verarbeitet werden. Die Analyse wird täglich ausgeführt, und falls ein Ergebnis gefunden wird, ist dies wahrscheinlich wesentlich weniger dringend als eine Warnung. Im Gegensatz dazu wird die Analyse fehlgeschlagener Anforderungen für eingehende Anwendungsdaten kontinuierlich ausgeführt, und Sie werden innerhalb weniger Minuten benachrichtigt, wenn die Serverfehlerraten höher als erwartet ausfallen.

## <a name="if-you-receive-a-smart-detection-alert"></a>Wenn Sie eine Smart Detection-Warnung erhalten
*Warum habe ich diese Warnung erhalten?*

* Im Vergleich zu den normalen Grundwerten des vorhergehenden Zeitraums wurde ein ungewöhnlicher Anstieg der Anforderungsfehlerrate festgestellt. Nach der Analyse der Fehler und zugeordneten Anwendungsdaten ist davon auszugehen, dass ein Problem vorliegt, das Sie untersuchen sollten.

*Bedeutet die Benachrichtigung, dass ich definitiv ein Problem habe?*

* Wir versuchen, bei Störungen oder Beeinträchtigungen der App eine Warnung zu senden, aber nur Sie können die Semantik oder die Auswirkungen auf die App oder Benutzer vollständig einschätzen.

*Wird Einblick in meine Anwendungsdaten gewährt?*

* Nein. Der Dienst ist vollständig automatisch. Nur Sie erhalten die Benachrichtigungen. Ihre Daten sind [privat](./data-retention-privacy.md).

*Muss ich diese Benachrichtigung abonnieren?*

* Nein. Jede Anwendung, die Anforderungsdaten sendet, verfügt über die Smart Detection-Warnungsregel.

*Kann ich das Abonnement abbestellen oder die Benachrichtigungen stattdessen an meine Kollegen senden lassen?*

* Ja, klicken Sie unter „Warnungsregeln“ auf die Regel für Smart Detection, um sie zu konfigurieren. Sie können die Warnung deaktivieren oder die Empfänger für die Warnung ändern.

*Ich habe die E-Mail verloren. Wo finde ich die Benachrichtigungen im Portal?*

* In den Aktivitätsprotokollen. Öffnen Sie in Azure die Application Insights-Ressource für Ihre App, und wählen Sie dann Aktivitätsprotokolle aus.

*Einige Warnungen betreffen bekannte Probleme. Diese Warnungen möchte ich nicht erhalten.*

* Sie können das Feature zum Unterdrücken von [Aktionsregeln](../platform/alerts-action-rules.md) verwenden.

## <a name="next-steps"></a>Nächste Schritte
Mit den folgenden Diagnosetools können Sie die Daten Ihrer App untersuchen:

* [Metrik-Explorer](../platform/metrics-charts.md)
* [Suchexplorer](./diagnostic-search.md)
* [Analytics: Leistungsfähige Abfragesprache](../log-query/log-analytics-tutorial.md)

Intelligente Erkennungen laufen automatisch ab. Vielleicht möchten Sie aber weitere Warnungen einrichten?

* [Einrichten von Warnungen in Application Insights](../platform/alerts-log.md)
* [Verfügbarkeitswebtests](./monitor-web-app-availability.md)