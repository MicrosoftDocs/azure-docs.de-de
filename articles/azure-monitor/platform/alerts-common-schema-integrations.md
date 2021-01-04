---
title: Integrieren des allgemeinen Warnungsschemas mit Logic Apps
description: Erfahren Sie, wie Sie eine Logik-App erstellen, die das allgemeine Warnungsschema nutzt, um all Ihre Warnungen zu verarbeiten.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 1eb96248f68923da5ff5223f57fac1bffaf4ed04
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000418"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Integrieren des allgemeinen Warnungsschemas mit Logic Apps

In diesem Artikel wird erläutert, wie Sie eine Logik-App erstellen, die das allgemeine Warnungsschema nutzt, um all Ihre Warnungen zu verarbeiten.

## <a name="overview"></a>Übersicht

Das [allgemeine Warnungsschema](./alerts-common-schema.md) stellt ein standardisiertes und erweiterbares JSON-Schema für alle verschiedenen Warnungstypen bereit. Das allgemeine Warnungsschema entfaltet seinen größten Nutzen bei programmgesteuerter Verwendung – durch Webhooks, Runbooks und Logik-Apps. In diesem Artikel wird veranschaulicht, wie eine einzelne Logik-App zur Verarbeitung aller Warnungen erstellt werden kann. Die gleichen Prinzipien können auch auf andere programmgesteuerte Methoden angewendet werden. Die in diesem Artikel beschriebene Logik-App erstellt klar definierte Variablen für die [Felder in „Essentials“](alerts-common-schema-definitions.md#essentials) und veranschaulicht zudem, wie die Logik je nach [Warnungstyp](alerts-common-schema-definitions.md#alert-context) verarbeitet wird.


## <a name="prerequisites"></a>Voraussetzungen 

In diesem Artikel wird davon ausgegangen, dass Sie mit Folgendem vertraut sind: 
* Einrichten von Warnungsregeln ([Metrik](./alerts-metric.md), [Protokoll](./alerts-log.md), [Aktivitätsprotokoll](./alerts-activity-log.md))
* Einrichten von [Aktionsgruppen](./action-groups.md)
* Aktivieren des [allgemeinen Warnungsschemas](./alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) aus Aktionsgruppen heraus

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Erstellen einer Logik-App, die das allgemeine Warnungsschema nutzt

1. Führen Sie die [Schritte zum Erstellen einer Logik-App](./action-groups-logic-app.md) aus. 

1.  Wählen Sie den folgenden Trigger aus: **Beim Empfang einer HTTP-Anforderung**.

    ![Logik-App-Trigger](media/action-groups-logic-app/logic-app-triggers.png "Logik-App-Trigger")

1.  Wählen Sie **Bearbeiten** aus, um den HTTP-Anforderungstrigger zu ändern.

    ![HTTP-Anforderungstrigger](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-Anforderungstrigger")


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

    ![Hinzufügen einer Aktion](media/action-groups-logic-app/add-action.png "Hinzufügen einer Aktion")

1. In dieser Phase können Sie basierend auf Ihren spezifischen Geschäftsanforderungen eine Vielzahl von Connectors hinzufügen (Microsoft Teams, Slack, Salesforce usw.). Sie können die Felder in „Essentials“ ohne weitere Konfiguration verwenden. 

    ![Wichtige Felder](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Wichtige Felder")
    
    Alternativ dazu können Sie mit der Option „Ausdruck“ eine Bedingungslogik basierend auf dem Warnungstyp erstellen.

    ![Logik-App-Ausdruck](media/alerts-common-schema-integrations/logic-app-expressions.png "Logik-App-Ausdruck")
    
     Im [Feld „monitoringService“](alerts-common-schema-definitions.md#alert-context) können Sie den Warnungstyp eindeutig identifizieren, auf dessen Basis Sie die Bedingungslogik erstellen.

    
    Der folgende Codeausschnitt überprüft beispielsweise, ob es sich bei der Warnung um eine Application Insights-basierte Protokollwarnung handelt. Ist dies der Fall, werden die Suchergebnisse ausgegeben. Andernfalls wird „N/V“ ausgegeben.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Erfahren Sie mehr über das [Schreiben von Logik-App-Ausdrücken](../../logic-apps/workflow-definition-language-functions-reference.md#logical-comparison-functions).

    


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr über Aktionsgruppen](./action-groups.md).
* [Erfahren Sie mehr über das allgemeine Warnungsschema](./alerts-common-schema.md).