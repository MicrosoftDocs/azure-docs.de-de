---
title: 'Tutorial: Exportieren von Daten und Visualisieren von Erkenntnissen in Azure IoT Central'
description: In diesem Tutorial wird beschrieben, wie Sie Daten aus IoT Central exportieren und Erkenntnisse in einem Power BI-Dashboard visualisieren.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: fca9a72fa237a11a02b6d4fbe30ebba5857384cf
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346366"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Tutorial: Exportieren von Daten aus Azure IoT Central und Visualisieren von Erkenntnissen in Power BI


In den beiden vorherigen Tutorials haben Sie eine IoT Central-Anwendung erstellt und angepasst, indem Sie die Anwendungsvorlage **In-Store-Analyse – Auschecken** verwendet haben. In diesem Tutorial konfigurieren Sie Ihre IoT Central-Anwendung für den Export der erfassten Telemetriedaten von den Geräten. Anschließend nutzen Sie Power BI zum Erstellen eines benutzerdefinierten Dashboards für den Store Manager, um die aus den Telemetriedaten gewonnenen Erkenntnisse zu visualisieren.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Konfigurieren Sie eine IoT Central-Anwendung, um Telemetriedaten in einen Event Hub zu exportieren.
> * Verwenden Sie Logic Apps, um Daten von einem Event Hub an ein Power BI-Streamingdataset zu senden.
> * Erstellen Sie ein Power BI-Dashboard, um die Daten des Streamingdatasets zu visualisieren.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Durchgearbeitete Tutorials [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) und [Anpassen des Dashboards für Bediener und Verwalten von Geräten in Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard.md).
* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
* Ein Power BI-Konto. Führen Sie zunächst die Registrierung für ein [kostenloses Power BI Pro-Testkonto](https://app.powerbi.com/signupredirect?pbi_source=web) durch, falls Sie kein Power BI-Konto besitzen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie Ihren Event Hub und die Logik-App erstellen, müssen Sie eine Ressourcengruppe für die Verwaltung erstellen. Die Ressourcengruppe sollte sich an demselben Speicherort wie Ihre IoT Central-Anwendung **In-Store-Analyse – Auschecken** befinden. So erstellen Sie eine Ressourcengruppe:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich die Option **Ressourcengruppen** aus. Wählen Sie anschließend **Hinzufügen**.
1. Wählen Sie unter **Abonnement** den Namen des Azure-Abonnements aus, das Sie zum Erstellen Ihrer IoT Central-Anwendung verwendet haben.
1. Geben Sie unter **Ressourcengruppe** den Namen _retail-store-analysis_* ein.
1. Wählen Sie unter **Region** dieselbe Region wie für die IoT Central-Anwendung aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Wählen Sie auf der Seite **Bewerten + erstellen** die Option **Erstellen**.

Sie verfügen in Ihrem Abonnement nun über eine Ressourcengruppe mit dem Namen **retail-store-analysis**.

## <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Bevor Sie die Überwachungsanwendung für den Einzelhandel für den Export von Telemetriedaten konfigurieren können, müssen Sie einen Event Hub für die exportierten Daten erstellen. Die folgenden Schritte verdeutlichen, wie Sie Ihren Event Hub erstellen:

1. Wählen Sie im Azure-Portal oben links auf dem Bildschirm die Option **Ressource erstellen** aus.
1. Geben Sie unter **Marketplace durchsuchen** den Suchbegriff _Event Hubs_ ein, und drücken Sie dann die **EINGABETASTE**.
1. Klicken Sie auf der Seite **Event Hubs** auf **Erstellen**.
1. Gehen Sie auf der Seite **Namespace erstellen** wie folgt vor:
    * Geben Sie einen eindeutigen Namen für den Namespace ein, z. B. _ihrname-retail-store-analysis_. Das System überprüft, ob dieser Name verfügbar ist.
    * Wählen Sie den Tarif **Basic** aus.
    * Wählen Sie dasselbe **Abonnement** aus, das Sie zum Erstellen Ihrer IoT Central-Anwendung verwendet haben.
    * Wählen Sie die Ressourcengruppe **retail-store-analysis** aus.
    * Wählen Sie denselben Speicherort aus, den Sie für Ihre IoT Central-Anwendung verwendet haben.
    * Klicken Sie auf **Erstellen**. Unter Umständen müssen Sie ein paar Minuten warten, bis die Ressourcen bereitgestellt wurden.
1. Navigieren Sie im Portal zur Ressourcengruppe **retail-store-analysis**. Warten Sie, bis die Bereitstellung abgeschlossen ist. Unter Umständen müssen Sie **Aktualisieren** auswählen, um den Bereitstellungsstatus zu aktualisieren. Sie können den Erstellungsstatus des Event Hub-Namespace auch unter **Benachrichtigungen** überprüfen.
1. Wählen Sie in der Ressourcengruppe **retail-store-analysis** den **Event Hubs-Namespace** aus. Die Startseite für Ihren **Event Hubs-Namespace** wird im Portal angezeigt.

Da Sie nun über einen **Event Hubs-Namespace** verfügen, können Sie einen **Event Hub** für Ihre IoT Central-Anwendung erstellen:

1. Wählen Sie im Portal auf der Startseite Ihres **Event Hubs-Namespace** die Option **+ Event Hub** aus.
1. Geben Sie auf der Seite **Event Hub erstellen** den Namen _store-telemetry_ ein, und wählen Sie dann die Option **Erstellen** aus.

Sie können den Event Hub jetzt nutzen, wenn Sie den Datenexport aus Ihrer IoT Central-Anwendung konfigurieren:

![Event Hub](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Konfigurieren des Datenexports

Da Sie jetzt über einen Event Hub verfügen, können Sie Ihre Anwendung **In-Store-Analyse – Auschecken** für den Export von Telemetriedaten von den verbundenen Geräten konfigurieren. Die folgenden Schritte verdeutlichen, wie Sie den Export konfigurieren:

1. Melden Sie sich an Ihrer IoT Central-Anwendung **In-Store-Analyse – Auschecken** an.
1. Wählen Sie im Bereich auf der linken Seite die Option **Datenexport** aus.
1. Wählen Sie **Neu > Azure Event Hubs** aus.
1. Geben Sie unter **Anzeigename** den Namen _Telemetry export_ (Telemetrieexport) ein.
1. Wählen Sie Ihren **Event Hubs-Namespace** aus.
1. Wählen Sie den Event Hub **store-telemetry** aus.
1. Deaktivieren Sie im Abschnitt **Zu exportierende Daten** die Optionen **Geräte** und **Gerätevorlagen**.
1. Wählen Sie **Speichern** aus.

Es kann einige Minuten dauern, bis der Datenexport abgeschlossen ist und Telemetriedaten an Ihren Event Hub gesendet werden. Sie können den Status des Exports auf der Seite **Datenexporte** anzeigen:

![Konfiguration des kontinuierlichen Datenexports](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Erstellen der Power BI-Datasets

In Ihrem Power BI-Dashboard werden Daten aus Ihrer Überwachungsanwendung für den Einzelhandel angezeigt. Bei dieser Lösung nutzen Sie Power BI-Streamingdatasets als Datenquelle für das Power BI-Dashboard. In diesem Abschnitt definieren Sie das Schema der Streamingdatasets, damit die Logik-App Daten vom Event Hub weiterleiten kann. Die folgenden Schritte verdeutlichen, wie Sie zwei Streamingdatasets für die Umgebungssensoren und ein Streamingdataset für den Auslastungssensor erstellen:

1. Melden Sie sich bei Ihrem **Power BI**-Konto an.
1. Wählen Sie **Arbeitsbereiche** und dann **Arbeitsbereich erstellen** aus.
1. Geben Sie auf der Seite **Arbeitsbereich erstellen** unter **Arbeitsbereichsname** den Namen _In-Store-Analyse – Auschecken_ ein.
1. Scrollen Sie auf der Begrüßungsseite des Arbeitsbereichs (**Welcome to the In-Store Analytics – Checkout Workspace**) ganz nach unten, und wählen Sie **Überspringen** aus.
1. Wählen Sie auf der Seite des Arbeitsbereichs die Option **Erstellen > Streamingdataset** aus.
1. Wählen Sie auf der Seite **Neues Streamingdataset** die Option **API** und dann **Weiter** aus.
1. Geben Sie unter **Datasetname** den Namen _Zone 1 sensor_ (Sensor Zone 1) ein.
1. Geben Sie die drei **Werte aus Datenstrom** in der folgenden Tabelle ein:

    | Wertname  | Werttyp |
    | ----------- | ---------- |
    | Timestamp   | Datetime   |
    | Luftfeuchtigkeit    | Number     |
    | Temperatur | Number     |

1. Aktivieren Sie die Option **Verlaufsdatenanalyse**.
1. Wählen Sie **Erstellen** und dann **Fertig** aus.
1. Erstellen Sie ein weiteres Streamingdataset mit dem Namen **Zone 2 sensor** (Sensor Zone 2) und dem gleichen Schema und identischen Einstellungen wie für das Streamingdataset **Zone 1 sensor** (Sensor Zone 1).

Sie verfügen jetzt über zwei Streamingdatasets. Die Logik-App leitet Telemetriedaten von den beiden Umgebungssensoren, die mit Ihrer Anwendung **In-Store-Analyse – Auschecken** verbunden sind, an diese beiden Datasets weiter:

![Zonendatasets](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Bei dieser Lösung wird ein Streamingdataset für jeden Sensor verwendet, da es in Power BI nicht möglich ist, Filter auf Streamingdaten anzuwenden.

Zudem benötigen Sie ein Streamingdataset für die Telemetriedaten der Auslastung:

1. Wählen Sie auf der Seite des Arbeitsbereichs die Option **Erstellen > Streamingdataset** aus.
1. Wählen Sie auf der Seite **Neues Streamingdataset** die Option **API** und dann **Weiter** aus.
1. Geben Sie unter **Datasetname** den Namen _Occupancy sensor_ (Auslastungssensor) ein.
1. Geben Sie die fünf **Werte aus Datenstrom** in der folgenden Tabelle ein:

    | Wertname     | Werttyp |
    | -------------- | ---------- |
    | Timestamp      | Datetime   |
    | Länge der Schlange 1 | Number     |
    | Länge der Schlange 2 | Number     |
    | Wartezeit 1   | Number     |
    | Wartezeit 2   | Number     |

1. Aktivieren Sie die Option **Verlaufsdatenanalyse**.
1. Wählen Sie **Erstellen** und dann **Fertig** aus.

Sie verfügen jetzt über ein drittes Streamingdataset, in dem Werte des simulierten Auslastungssensors gespeichert werden. Dieser Sensor meldet die Länge der Schlangen an den beiden Kassen des Geschäfts und die Wartezeit für Kunden in diesen Schlangen:

![Auslastungsdataset](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Erstellen einer Logik-App

Bei dieser Lösung liest die Logik-App Telemetriedaten vom Event Hub, analysiert sie und sendet sie anschließend an die von Ihnen erstellten Power BI-Streamingdatasets.

Bevor Sie die Logik-App erstellen, benötigen Sie die Geräte-IDs der beiden RuuviTag-Sensoren, die Sie im Tutorial [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) mit Ihrer IoT Central-Anwendung verbunden haben:

1. Melden Sie sich an Ihrer IoT Central-Anwendung **In-Store-Analyse – Auschecken** an.
1. Wählen Sie im linken Bereich die Option **Geräte** aus. Wählen Sie anschließend **RuuviTag** aus.
1. Notieren Sie sich die **Geräte-IDs**. Im folgenden Screenshot lauten die IDs **f5dcf4ac32e8** und **e29ffc8d5326**:

    ![Geräte-IDs](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

Die folgenden Schritte verdeutlichen, wie Sie im Azure-Portal die Logik-App erstellen:

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und wählen Sie oben links auf dem Bildschirm die Option **Ressource erstellen** aus.
1. Geben Sie unter **Marketplace durchsuchen** den Suchbegriff _Logik-App_ ein, und drücken Sie dann die **EINGABETASTE**.
1. Wählen Sie auf der Seite **Logik-App** die Option **Erstellen** aus.
1. Auf der Seite für die Erstellung der **Logik-App**:
    * Geben Sie einen eindeutigen Namen für Ihre Logik-App ein, z. B. _ihrname-retail-store-analysis_.
    * Wählen Sie dasselbe **Abonnement** aus, das Sie zum Erstellen Ihrer IoT Central-Anwendung verwendet haben.
    * Wählen Sie die Ressourcengruppe **retail-store-analysis** aus.
    * Wählen Sie denselben Speicherort aus, den Sie für Ihre IoT Central-Anwendung verwendet haben.
    * Klicken Sie auf **Erstellen**. Unter Umständen müssen Sie ein paar Minuten warten, bis die Ressourcen bereitgestellt wurden.
1. Navigieren Sie im Azure-Portal zu Ihrer neuen Logik-App.
1. Scrollen Sie auf der Seite **Designer für Logik-Apps** nach unten, und wählen Sie die Option **Leere Logik-App** aus.
1. Geben Sie unter **Connectors und Trigger durchsuchen** den Suchbegriff _Event Hubs_ ein.
1. Wählen Sie unter **Trigger** die Option **Wenn Ereignisse im Event Hub verfügbar sind** aus.
1. Geben Sie unter **Verbindungsname** den Namen _Store telemetry_ (Geschäftstelemetrie) ein, und wählen Sie Ihren **Event Hubs-Namespace** aus.
1. Wählen Sie die Richtlinie **RootManageSharedAccess** und dann die Option **Erstellen** aus.
1. Unter der Aktion **Wenn Ereignisse im Event Hub verfügbar sind**:
    * Wählen Sie unter **Name des Event Hubs** die Option **store-telemetry** aus.
    * Wählen Sie unter **Inhaltstyp** die Option **application/json** aus.
    * Legen Sie **Intervall** auf den Wert „3“ und **Häufigkeit** auf „Sekunden“ fest.
1. Wählen Sie **Speichern** aus, um Ihre Logik-App zu speichern.

Wählen Sie **Codeansicht** aus, um die Logik Ihrem Logik-App-Entwurf hinzuzufügen:

1. Ersetzen Sie `"actions": {},` durch den unten angegebenen JSON-Code. Ersetzen Sie die beiden Platzhalter `[YOUR RUUVITAG DEVICE ID 1]` und `[YOUR RUUVITAG DEVICE ID 2]` durch die IDs, die Sie sich für Ihre beiden RuuviTag-Geräte notiert haben:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Wählen Sie **Speichern** und dann **Designer** aus, um die visuelle Version der von Ihnen hinzugefügten Logik anzuzeigen:

    ![Logik-App-Entwurf](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Wählen Sie **Switch by DeviceID** (Nach Geräte-ID wechseln) aus, um die Aktion zu erweitern. Wählen Sie anschließend **Zone 1 environment** (Umgebung Zone 1) und die Option **Aktion hinzufügen** aus.
1. Geben Sie unter **Connectors und Aktionen durchsuchen** den Suchbegriff **Power BI** ein, und drücken Sie anschließend die **EINGABETASTE**.
1. Wählen Sie die Aktion **Zeilen einem Dataset hinzufügen (Vorschauversion)** aus.
1. Wählen Sie **Anmelden** aus, und folgen Sie den Aufforderungen, um sich an Ihrem Power BI-Konto anzumelden.
1. Gehen Sie nach Abschluss des Anmeldevorgangs in der Aktion **Zeilen einem Dataset hinzufügen** wie folgt vor:
    * Wählen Sie **In-Store-Analyse – Auschecken** als Arbeitsbereich aus.
    * Wählen Sie **Zone 1 sensor** (Sensor Zone 1) als Dataset aus.
    * Wählen Sie **RealTimeData** als Tabelle aus.
    * Wählen Sie **Neuen Parameter hinzufügen** und dann die Felder **Zeitstempel**, **Luftfeuchtigkeit** und **Temperatur** aus.
    * Wählen Sie das Feld **Zeitstempel** und dann in der Liste **Dynamischer Inhalt** die Option **x-opt-enqueuedtime** aus.
    * Wählen Sie das Feld **Luftfeuchtigkeit** und dann neben **Parse Telemetry** (Telemetriedaten analysieren) die Option **Mehr anzeigen** aus. Wählen Sie anschließend **Luftfeuchtigkeit** aus.
    * Wählen Sie das Feld **Temperatur** und dann neben **Parse Telemetry** (Telemetriedaten analysieren) die Option **Mehr anzeigen** aus. Wählen Sie anschließend **Temperatur** aus.
    * Wählen Sie **Speichern**, um Ihre Änderungen zu speichern. Die Aktion **Zone 1 environment** (Umgebung Zone 1) sieht wie im folgenden Screenshot aus: ![Zone 1 environment (Umgebung Zone 1)](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Wählen Sie die Aktion **Zone 2 environment** (Umgebung Zone 2) und dann die Option **Aktion hinzufügen** aus.
1. Geben Sie unter **Connectors und Aktionen durchsuchen** den Suchbegriff **Power BI** ein, und drücken Sie anschließend die **EINGABETASTE**.
1. Wählen Sie die Aktion **Zeilen einem Dataset hinzufügen (Vorschauversion)** aus.
1. In der Aktion **Zeilen einem Dataset hinzufügen 2**:
    * Wählen Sie **In-Store-Analyse – Auschecken** als Arbeitsbereich aus.
    * Wählen Sie **Zone 2 sensor** (Sensor Zone 2) als Dataset aus.
    * Wählen Sie **RealTimeData** als Tabelle aus.
    * Wählen Sie **Neuen Parameter hinzufügen** und dann die Felder **Zeitstempel**, **Luftfeuchtigkeit** und **Temperatur** aus.
    * Wählen Sie das Feld **Zeitstempel** und dann in der Liste **Dynamischer Inhalt** die Option **x-opt-enqueuedtime** aus.
    * Wählen Sie das Feld **Luftfeuchtigkeit** und dann neben **Parse Telemetry** (Telemetriedaten analysieren) die Option **Mehr anzeigen** aus. Wählen Sie anschließend **Luftfeuchtigkeit** aus.
    * Wählen Sie das Feld **Temperatur** und dann neben **Parse Telemetry** (Telemetriedaten analysieren) die Option **Mehr anzeigen** aus. Wählen Sie anschließend **Temperatur** aus.
    Wählen Sie **Speichern**, um Ihre Änderungen zu speichern.  Die Aktion **Zone 2 environment** (Umgebung Zone 2) sieht wie im folgenden Screenshot aus: ![Zone 2 environment (Umgebung Zone 2)](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Wählen Sie die Aktion **Occupancy** (Auslastung) und dann die Aktion **Switch by Interface ID** (Nach Schnittstellen-ID wechseln) aus.
1. Wählen Sie die Aktion **Dwell Time interface** (Schnittstelle für Wartezeit) und dann die Option **Aktion hinzufügen** aus.
1. Geben Sie unter **Connectors und Aktionen durchsuchen** den Suchbegriff **Power BI** ein, und drücken Sie anschließend die **EINGABETASTE**.
1. Wählen Sie die Aktion **Zeilen einem Dataset hinzufügen (Vorschauversion)** aus.
1. In der Aktion **Zeilen einem Dataset hinzufügen**:
    * Wählen Sie **In-Store-Analyse – Auschecken** als Arbeitsbereich aus.
    * Wählen Sie **Occupancy Sensor** (Auslastungssensor) als Dataset aus.
    * Wählen Sie **RealTimeData** als Tabelle aus.
    * Wählen Sie die Option **Neuen Parameter hinzufügen** und dann die Felder **Zeitstempel**, **Dwell Time 1** (Wartezeit 1) und **Dwell Time 2** (Wartezeit 2) aus.
    * Wählen Sie das Feld **Zeitstempel** und dann in der Liste **Dynamischer Inhalt** die Option **x-opt-enqueuedtime** aus.
    * Wählen Sie das Feld **Dwell Time 1** (Wartezeit 1) und dann neben **Parse Telemetry** (Telemetriedaten analysieren) die Option **Mehr anzeigen** aus. Wählen Sie anschließend **DwellTime1** aus.
    * Wählen Sie das Feld **Dwell Time 2** (Wartezeit 2) und dann neben **Parse Telemetry** (Telemetriedaten analysieren) die Option **Mehr anzeigen** aus. Wählen Sie anschließend **DwellTime2** aus.
    * Wählen Sie **Speichern**, um Ihre Änderungen zu speichern. Die Aktion **Dwell Time interface** (Schnittstelle für Wartezeit) sieht wie im folgenden Screenshot aus: ![Screenshot: Aktion „Dwell Time interface“ (Schnittstelle für Wartezeit)](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Wählen Sie die Aktion **People Count interface** (Schnittstelle für Personenanzahl) und dann die Option **Aktion hinzufügen** aus.
1. Geben Sie unter **Connectors und Aktionen durchsuchen** den Suchbegriff **Power BI** ein, und drücken Sie anschließend die **EINGABETASTE**.
1. Wählen Sie die Aktion **Zeilen einem Dataset hinzufügen (Vorschauversion)** aus.
1. In der Aktion **Zeilen einem Dataset hinzufügen**:
    * Wählen Sie **In-Store-Analyse – Auschecken** als Arbeitsbereich aus.
    * Wählen Sie **Occupancy Sensor** (Auslastungssensor) als Dataset aus.
    * Wählen Sie **RealTimeData** als Tabelle aus.
    * Wählen Sie **Neuen Parameter hinzufügen** und dann die Felder **Zeitstempel**, **Queue Length 1** (Länge der Schlange 1) und **Queue Length 2** (Länge der Schlange 2) aus.
    * Wählen Sie das Feld **Zeitstempel** und dann in der Liste **Dynamischer Inhalt** die Option **x-opt-enqueuedtime** aus.
    * Wählen Sie das Feld **Queue Length 1** (Länge der Schlange 1) und dann neben **Parse Telemetry** (Telemetriedaten analysieren) die Option **Mehr anzeigen** aus. Wählen Sie anschließend **count1** aus.
    * Wählen Sie das Feld **Queue Length 2** (Länge der Schlange 2) und dann neben **Parse Telemetry** (Telemetriedaten analysieren) die Option **Mehr anzeigen** aus. Wählen Sie anschließend **count2** aus.
    * Wählen Sie **Speichern**, um Ihre Änderungen zu speichern. Die Aktion **People Count interface** (Schnittstelle für Personenanzahl) sieht wie im folgenden Screenshot aus: ![Aktion „Occupancy“ (Auslastung)](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

Die Logik-App wird automatisch ausgeführt. Navigieren Sie zum Anzeigen des Status jeder Ausführung im Azure-Portal zur Seite **Übersicht** für die Logik-App:

## <a name="create-a-power-bi-dashboard"></a>Erstellen eines Power BI-Dashboards

Nun fließen Telemetriedaten aus Ihrer IoT Central-Anwendung über Ihren Event Hub. Anschließend werden die Event Hub-Nachrichten von der Logik-App analysiert und einem Power BI-Streamingdataset hinzugefügt. Nun können Sie ein Power BI-Dashboard zum Visualisieren der Telemetriedaten erstellen:

1. Melden Sie sich bei Ihrem **Power BI**-Konto an.
1. Wählen Sie **Arbeitsbereiche > In-Store-Analyse – Auschecken** aus.
1. Wählen Sie **Erstellen > Dashboard** aus.
1. Geben Sie **Store analytics** (Geschäftsanalyse) als Dashboardnamen an, und wählen Sie **Erstellen** aus.

### <a name="add-line-charts"></a>Hinzufügen von Liniendiagrammen

Fügen Sie vier Liniendiagrammkacheln hinzu, um die Werte für die Temperatur bzw. Luftfeuchtigkeit anzuzeigen, die von den beiden Umgebungssensoren gemeldet wurden. Verwenden Sie die Informationen in der unten angegebenen Tabelle, um die Kacheln zu erstellen. Wählen Sie jeweils zuerst **... (Weitere Optionen) > Kachel hinzufügen** aus, um eine Kachel hinzuzufügen. Wählen Sie **Benutzerdefinierte Streamingdaten** und anschließend **Weiter** aus:

| Einstellung | Diagramm 1 | Diagramm 2 | Diagramm 3 | Diagramm 4 |
| ------- | -------- | -------- | -------- | -------- |
| Dataset | Zone 1 sensor (Sensor Zone 1) | Zone 1 sensor (Sensor Zone 1) | Zone 2 sensor (Sensor Zone 2) | Zone 2 sensor (Sensor Zone 2) |
| Visualisierungstyp | Liniendiagramm | Liniendiagramm | Liniendiagramm | Liniendiagramm |
| Achse | Timestamp | Timestamp | Timestamp | Timestamp |
| Werte | Temperatur | Luftfeuchtigkeit | Temperatur | Luftfeuchtigkeit |
| Zeitfenster | 60 Minuten | 60 Minuten | 60 Minuten | 60 Minuten |
| Titel | Temperatur (1 Stunde) | Luftfeuchtigkeit (1 Stunde) | Temperatur (1 Stunde) | Luftfeuchtigkeit (1 Stunde) |
| Untertitel | Zone 1 | Zone 1 | Zone 2 | Zone 2 |

Im folgenden Screenshot sind die Einstellungen für das erste Diagramm dargestellt:

![Einstellungen für Liniendiagramm](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Hinzufügen von Karten zum Anzeigen von Umgebungsdaten

Fügen Sie vier Kartenkacheln hinzu, um die aktuellen Temperatur- bzw. Luftfeuchtigkeitswerte der beiden Umgebungssensoren anzuzeigen. Verwenden Sie die Informationen in der unten angegebenen Tabelle, um die Kacheln zu erstellen. Wählen Sie jeweils zuerst **... (Weitere Optionen) > Kachel hinzufügen** aus, um eine Kachel hinzuzufügen. Wählen Sie **Benutzerdefinierte Streamingdaten** und anschließend **Weiter** aus:

| Einstellung | Karte 1 | Karte 2 | Karte 3 | Karte 4 |
| ------- | ------- | ------- | ------- | ------- |
| Dataset | Zone 1 sensor (Sensor Zone 1) | Zone 1 sensor (Sensor Zone 1) | Zone 2 sensor (Sensor Zone 2) | Zone 2 sensor (Sensor Zone 2) |
| Visualisierungstyp | Karte | Karte | Karte | Karte |
| Felder | Temperatur | Luftfeuchtigkeit | Temperatur | Luftfeuchtigkeit |
| Titel | Temperatur (F) | Luftfeuchtigkeit (%) | Temperatur (F) | Luftfeuchtigkeit (%) |
| Untertitel | Zone 1 | Zone 1 | Zone 2 | Zone 2 |

Im folgenden Screenshot sind die Einstellungen für die erste Karte dargestellt:

![Karteneinstellungen](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Hinzufügen von Kacheln zum Anzeigen der Auslastungsdaten für Kassen

Fügen Sie vier Kartenkacheln hinzu, um für die beiden Kassen des Geschäfts die Länge der Schlange und die Wartezeit anzuzeigen. Verwenden Sie die Informationen in der unten angegebenen Tabelle, um die Kacheln zu erstellen. Wählen Sie jeweils zuerst **... (Weitere Optionen) > Kachel hinzufügen** aus, um eine Kachel hinzuzufügen. Wählen Sie **Benutzerdefinierte Streamingdaten** und anschließend **Weiter** aus:

| Einstellung | Karte 1 | Karte 2 | Karte 3 | Karte 4 |
| ------- | ------- | ------- | ------- | ------- |
| Dataset | Auslastungssensor | Auslastungssensor | Auslastungssensor | Auslastungssensor |
| Visualisierungstyp | Gruppiertes Säulendiagramm | Gruppiertes Säulendiagramm | Maßstab | Maßstab |
| Achse    | Timestamp | Timestamp | – | – |
| Wert | Wartezeit 1 | Wartezeit 2 | Länge der Schlange 1 | Länge der Schlange 2 |
| Zeitfenster | 60 Minuten | 60 Minuten |  – | – |
| Titel | Wartezeit | Wartezeit | Warteschlangenlänge | Warteschlangenlänge |
| Untertitel | Kasse 1 | Kasse 2 | Kasse 1 | Kasse 2 |

Ändern Sie die Größe und Anordnung der Kacheln in Ihrem Dashboard so, wie dies im folgenden Screenshot dargestellt ist:

![Screenshot: Power BI-Dashboard mit veränderter Größe und neu angeordneten Kacheln](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Sie können einige zusätzliche Grafikressourcen hinzufügen, um das Dashboard weiter anzupassen:

![Power BI-Dashboard](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Verwendung Ihrer IoT Central-Anwendung abgeschlossen haben, können Sie sie löschen. Melden Sie sich hierfür bei der Anwendung an, und navigieren Sie im Abschnitt **Verwaltung** zur Seite **Anwendungseinstellungen**.

Falls Sie die Anwendung behalten, aber die damit verbundenen Kosten reduzieren möchten, sollten Sie den Datenexport deaktivieren, mit dem Telemetriedaten an Ihren Event Hub gesendet werden.

Sie können den Event Hub und die Logik-App im Azure-Portal löschen, indem Sie die Ressourcengruppe **retail-store-analysis** löschen.

Sie können Ihre Power BI-Datasets und das Dashboard löschen, indem Sie den Arbeitsbereich auf der zugehörigen Seite mit den Power BI-Einstellungen löschen.

## <a name="next-steps"></a>Nächste Schritte

In diesen drei Tutorials wurde eine End-to-End-Lösung vorgestellt, für die die IoT Central-Anwendungsvorlage **In-Store-Analyse – Auschecken** verwendet wird. Sie haben Geräte mit der Anwendung verbunden, IoT Central zum Überwachen der Geräte verwendet und Power BI genutzt, um ein Dashboard zum Anzeigen von Erkenntnissen aus den Telemetriedaten des Geräts zu erstellen. Wir empfehlen Ihnen, als Nächstes eine der anderen IoT Central-Anwendungsvorlagen zu erkunden:

> [!div class="nextstepaction"]
> * [Erstellen von Lösungen für die Energiebranche mit IoT Central](../energy/overview-iot-central-energy.md)
> * [Erstellen von Lösungen für Behörden mit IoT Central](../government/overview-iot-central-government.md)
> * [Erstellen von Lösungen für das Gesundheitswesen mit IoT Central](../healthcare/overview-iot-central-healthcare.md)
