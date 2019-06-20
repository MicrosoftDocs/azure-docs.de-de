---
title: Integrieren des allgemeinen Warnungsschemas mit Logic Apps
description: Erfahren Sie, wie Sie eine Logik-App erstellen, die das allgemeine Warnungsschema nutzt, um all Ihre Warnungen zu verarbeiten.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734891"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Integrieren des allgemeinen Warnungsschemas mit Logic Apps

In diesem Artikel wird erläutert, wie Sie eine Logik-App erstellen, die das allgemeine Warnungsschema nutzt, um all Ihre Warnungen zu verarbeiten.

## <a name="overview"></a>Übersicht

Das [allgemeine Warnungsschema](https://aka.ms/commonAlertSchemaDocs) stellt ein standardisiertes und erweiterbares JSON-Schema für alle verschiedenen Warnungstypen bereit. Das allgemeine Warnungsschema entfaltet seinen größten Nutzen bei programmgesteuerter Verwendung – durch Webhooks, Runbooks und Logik-Apps. In diesem Artikel wird veranschaulicht, wie eine einzelne Logik-App zur Verarbeitung aller Warnungen erstellt werden kann. Die gleichen Prinzipien können auch auf andere programmgesteuerte Methoden angewendet werden. Die in diesem Artikel beschriebene Logik-App erstellt klar definierte Variablen für die [Felder in „Essentials“](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields) und veranschaulicht zudem, wie die Logik je nach [Warnungstyp](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) verarbeitet wird.


## <a name="prerequisites"></a>Voraussetzungen 

In diesem Artikel wird davon ausgegangen, dass Sie mit Folgendem vertraut sind: 
* Einrichten von Warnungsregeln ([Metrik](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [Protokoll](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [Aktivitätsprotokoll](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Einrichten von [Aktionsgruppen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Aktivieren des [allgemeinen Warnungsschemas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) aus Aktionsgruppen heraus

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Erstellen einer Logik-App, die das allgemeine Warnungsschema nutzt

1. Führen Sie die [Schritte zum Erstellen einer Logik-App](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app) aus. 

1.  Wählen Sie den folgenden Trigger aus: **Beim Empfang einer HTTP-Anforderung**.

    ![Logik-App-Trigger](media/action-groups-logic-app/logic-app-triggers.png "Logik-App-Trigger")

1.  Wählen Sie **Bearbeiten** aus, um den HTTP-Anforderungstrigger zu ändern.

    ![HTTP-Anforderungstrigger ](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-Anforderungstrigger ")


1.  Kopieren Sie das folgende Schema, und fügen Sie es ein:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Wählen Sie **+** **Neuer Schritt** und anschließend **Aktion hinzufügen** aus.

    ![Aktion hinzufügen](media/action-groups-logic-app/add-action.png "Aktion hinzufügen")

1. In dieser Phase können Sie basierend auf Ihren spezifischen Geschäftsanforderungen eine Vielzahl von Connectors hinzufügen (Microsoft Teams, Slack, Salesforce usw.). Sie können die Felder in „Essentials“ ohne weitere Konfiguration verwenden. 

    ![Essentials-Felder](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Essentials-Felder")
    
    Alternativ dazu können Sie mit der Option „Ausdruck“ eine Bedingungslogik basierend auf dem Warnungstyp erstellen.

    ![Logik-App-Ausdruck](media/alerts-common-schema-integrations/logic-app-expressions.png "Logik-App-Ausdruck")
    
     Im [Feld „monitoringService“](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) können Sie den Warnungstyp eindeutig identifizieren, auf dessen Basis Sie die Bedingungslogik erstellen.

    
    Der folgende Codeausschnitt überprüft beispielsweise, ob es sich bei der Warnung um eine Application Insights-basierte Protokollwarnung handelt. Ist dies der Fall, werden die Suchergebnisse ausgegeben. Andernfalls wird „N/V“ ausgegeben.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Erfahren Sie mehr über das [Schreiben von Logik-App-Ausdrücken](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr über Aktionsgruppen](../../azure-monitor/platform/action-groups.md).
* [Erfahren Sie mehr über das allgemeine Warnungsschema](https://aka.ms/commonAlertSchemaDocs).

