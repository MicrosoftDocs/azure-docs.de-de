---
title: 'Schnellstart: Bereitstellen von Azure IoT-Konnektor für FHIR (Vorschauversion) mithilfe des Azure-Portals'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals das Feature „Azure IoT-Konnektor für FHIR“ von Azure API for FHIR bereitstellen, konfigurieren und verwenden.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: f36d842e14c91850bfeba47e9fef61d4747c33a9
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630565"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>Schnellstart: Bereitstellen von Azure IoT-Konnektor für FHIR (Vorschauversion) mithilfe des Azure-Portals

Azure IoT-Konnektor für FHIR&#174; (Fast Healthcare Interoperability Resources)* ist ein optionales Feature von Azure API for FHIR, das die Erfassung der Daten von IoMT-Geräten (Internet of Medical Things) ermöglicht. Während der Vorschauphase steht das Feature „Azure IoT-Konnektor für FHIR“ kostenlos zur Verfügung. In diesem Schnellstart erfahren Sie, wie die folgenden Aufgaben ausgeführt werden:
- Bereitstellen und Konfigurieren von Azure IoT-Konnektor für FHIR über das Azure-Portal
- Verwenden eines simulierten Geräts zum Senden von Daten an Azure IoT-Konnektor für FHIR
- Anzeigen von Ressourcen, die von Azure IoT-Konnektor für FHIR in Azure API for FHIR erstellt wurden

## <a name="prerequisites"></a>Voraussetzungen

- Ein aktives Azure-Abonnement. ([Kostenloses Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- Eine Azure API for FHIR-Ressource: [Bereitstellen von Azure API for FHIR mithilfe des Azure-Portals](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Navigieren zur Azure API for FHIR-Ressource

Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zur **Azure API for FHIR**-Ressource, für die Sie das Feature „Azure IoT-Konnektor für FHIR“ erstellen möchten.

[![Azure API for FHIR-Ressource](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

Klicken Sie im Navigationsmenü auf der linken Seite im Abschnitt **Add-Ins** auf **IoT Connector (preview)** (IoT-Konnektor (Vorschauversion)), um die Seite **IoT Connectors** (IoT-Konnektor-Instanzen) zu öffnen.

[![Feature „IoT-Konnektor“](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>Erstellen einer neuen Instanz von Azure IoT-Konnektor für FHIR (Vorschauversion)

Klicken Sie auf die Schaltfläche **Hinzufügen**, um die Seite **Create IoT Connector** (IoT-Konnektor erstellen) zu öffnen.

[![Hinzufügen einer IoT-Konnektor-Instanz](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Geben Sie Einstellungen für die neue Instanz von Azure IoT-Konnektor für FHIR ein. Klicken Sie auf die Schaltfläche **Erstellen**, und warten Sie die Bereitstellung von Azure IoT-Konnektor für FHIR ab.

> [!NOTE]
> Für diese Installation muss für das Dropdownmenü **Auflösungstyp** der Wert **Erstellen** ausgewählt werden. 

[![Erstellen einer IoT-Konnektor-Instanz](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Einstellung|Wert|BESCHREIBUNG |
|---|---|---|
|Connectorname|Ein eindeutiger Name|Geben Sie einen Namen zum Identifizieren von Azure IoT-Konnektor für FHIR ein. Dieser Name muss innerhalb einer Azure API for FHIR-Ressource eindeutig sein. Der Name darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss mit einem Buchstaben oder einer Zahl beginnen und enden und 3 bis 24 Zeichen lang sein.|
|Auflösungstyp|Nachschlagen oder Erstellen|Wählen Sie **Nachschlagen** aus, wenn Sie einen Out-of-Band-Prozess zum Erstellen der FHIR-Ressourcen vom Typ [Gerät](https://www.hl7.org/fhir/device.html) und [Patient](https://www.hl7.org/fhir/patient.html) in Ihrer Azure API for FHIR-Instanz verwenden. Azure IoT-Konnektor für FHIR verwendet beim Erstellen der FHIR-Ressource [Beobachtung](https://www.hl7.org/fhir/observation.html) einen Verweis auf diese Ressourcen zur Darstellung der Gerätedaten. Wählen Sie **Erstellen** aus, wenn von Azure IoT-Konnektor für FHIR Barebone-Ressourcen für Geräte und Patienten in Ihrer Azure API for FHIR-Instanz erstellt und dabei die entsprechenden Bezeichnerwerte in den Gerätedaten herangezogen werden sollen.|

Nach Abschluss der Installation wird die neu erstellte Instanz von Azure IoT-Konnektor für FHIR auf der Seite **IoT Connectors** (IoT-Konnektor-Instanzen) angezeigt.

[![Erstellte IoT-Konnektor-Instanz](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>Konfigurieren von Azure IoT-Konnektor für FHIR (Vorschauversion)

Azure IoT-Konnektor für FHIR benötigt zwei Zuordnungsvorlagen, um Gerätemeldungen in FHIR-basierte Beobachtungsressourcen zu transformieren: **Gerätezuordnung** und **FHIR-Zuordnung**. Ihre Instanz von Azure IoT-Konnektor für FHIR ist voll funktionsfähig, wenn diese Zuordnungen hochgeladen wurden.

[![Fehlende IoT-Konnektor-Zuordnungen](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Klicken Sie zum Hochladen der Zuordnungsvorlagen auf die neu bereitgestellte Instanz von Azure IoT-Konnektor für FHIR, um die Seite **IoT-Konnektor** aufzurufen.

[![Klicken auf die IoT-Konnektor-Instanz](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Gerätezuordnung

Mit der Vorlage für die Gerätezuordnung werden Gerätedaten in ein normalisiertes Schema transformiert. Klicken Sie auf der Seite **IoT-Konnektor** auf die Schaltfläche **Configure device mapping** (Gerätezuordnung konfigurieren), um die Seite **Gerätezuordnung** aufzurufen. 

[![IoT-Konnektor: Klicken auf „Configure device mapping“ (Gerätezuordnung konfigurieren)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Fügen Sie auf der Seite **Gerätezuordnung** das folgende Skript zum JSON-Editor hinzu, und klicken Sie auf **Speichern**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![IoT-Konnektor: Gerätezuordnung](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)

#### <a name="fhir-mapping"></a>FHIR-Zuordnung

Mit der Vorlage für die FHIR-Zuordnung wird eine normalisierte Meldung in eine FHIR-basierte Beobachtungsressource transformiert. Klicken Sie auf der Seite **IoT-Konnektor** auf die Schaltfläche **Configure FHIR mapping** (FHIR-Zuordnung konfigurieren), um die Seite **FHIR-Zuordnung** aufzurufen.  

[![IoT-Konnektor: Klicken auf „Configure FHIR mapping“ (FHIR-Zuordnung konfigurieren)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

Fügen Sie auf der Seite **FHIR-Zuordnung** das folgende Skript zum JSON-Editor hinzu, und klicken Sie auf **Speichern**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![IoT-Konnektor: FHIR-Zuordnung](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Generieren einer Verbindungszeichenfolge

IoMT-Geräte benötigen eine Verbindungszeichenfolge, um eine Verbindung mit Azure IoT-Konnektor für FHIR herzustellen und Nachrichten an Azure IoT-Konnektor für FHIR zu senden. Wählen Sie auf der Seite **IoT-Konnektor** für die neu bereitgestellte Instanz von Azure IoT-Konnektor für FHIR die Schaltfläche **Manage client connections** (Clientverbindungen verwalten) aus. 

[![IoT-Konnektor: Klicken Auf „Manage client connections“ (Clientverbindungen verwalten)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

Klicken Sie auf der Seite **Verbindungen** auf die Schaltfläche **Hinzufügen**, um eine neue Verbindung zu erstellen. 

[![IoT-Konnektor: Verbindungen](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Geben Sie im Überlagerungsfenster einen Anzeigenamen für diese Verbindung ein, und wählen Sie die Schaltfläche **Erstellen** aus.

[![IoT-Konnektor: neue Verbindung](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Wählen Sie auf der Seite **Verbindungen** die neu erstellte Verbindung aus, und kopieren Sie den Wert des Felds **Primäre Verbindungszeichenfolge** aus dem Überlagerungsfenster auf der rechten Seite.

[![IoT-Konnektor: Verbindungszeichenfolge](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Speichern Sie diese Verbindungszeichenfolge für einen späteren Schritt. 

## <a name="connect-your-devices-to-iot"></a>Herstellen einer Verbindung zwischen Geräten und IoT

Azure bietet eine umfangreiche Sammlung von IoT-Produkten zum Herstellen einer Verbindung mit und zum Verwalten von IoT-Geräten. Sie können mithilfe von Azure IoT Hub eigene PaaS-basierte Lösungen erstellen oder mit Azure IoT Central mit einer verwalteten IoT-App-Plattform beginnen. In diesem Tutorial nutzen Sie Azure IoT Central. Diese Lösung bietet branchenspezifische Lösungsvorlagen, die Ihnen den Einstieg erleichtern.

Stellen Sie die [Anwendungsvorlage für die ständige Überwachung von Patienten](../iot-central/healthcare/tutorial-continuous-patient-monitoring.md#create-an-application-template) bereit. Diese Vorlage enthält zwei simulierte Geräte, die Echtzeitdaten erzeugen, um Ihnen den Einstieg zu erleichtern: **Smart Vitals Patch** und **Smart Knee Brace**.

> [!NOTE]
> Wenn Ihre echten Geräte bereit sind, können Sie dieselbe IoT Central-Anwendung verwenden, um das [Onboarding für Ihre Geräte durchzuführen](../iot-central/core/howto-set-up-template.md) und Gerätesimulatoren zu ersetzen. Ihre Gerätedaten werden ebenfalls automatisch an FHIR übermittelt. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Verbinden Ihrer IoT-Daten mit Azure IoT-Konnektor für FHIR (Vorschauversion)
> [!WARNING]
> Die in dieser Schnellstartanleitung bereitgestellte Vorlage für die Gerätezuordnung ist für den Datenexport (Legacy) in IoT Central konzipiert.

Nach der Bereitstellung Ihrer IoT Central-Anwendung beginnen Ihre beiden vordefinierten simulierten Geräte mit dem Generieren von Telemetriedaten. In diesem Tutorial erfassen Sie die Telemetriedaten vom Simulator *Smart Vitals Patch* in FHIR über Azure IoT-Konnektor für FHIR. Wenn Sie Ihre IoT-Daten in Azure IoT-Konnektor für FHIR exportieren möchten, sollten Sie einen [fortlaufenden Datenexport in IoT Central einrichten](../iot-central/core/howto-export-data-legacy.md). Gehen Sie auf der Seite für den kontinuierlichen Datenexport wie folgt vor:
- Wählen Sie als Exportziel *Azure Event Hubs* aus.
- Wählen Sie für das Feld **Event Hubs-Namespace** den Wert *Verbindungszeichenfolge verwenden* aus.
- Geben Sie die Verbindungszeichenfolge von Azure IoT-Konnektor für FHIR an, die Sie in einem vorherigen Schritt für das Feld **Verbindungszeichenfolge** abgerufen haben.
- Übernehmen Sie für die Option **Telemetrie** die Option *Ein* für das Feld **Zu exportierende Daten**.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Anzeigen von Gerätedaten in Azure API for FHIR

Mithilfe von Postman können Sie die FHIR-basierten Überwachungsressourcen anzeigen, die von Azure IoT-Konnektor für FHIR in Azure API for FHIR erstellt wurden. Richten Sie [Postman für den Zugriff auf Azure API for FHIR](access-fhir-postman-tutorial.md) ein, und senden Sie eine Anforderung vom Typ `GET` an `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4`, um FHIR-Überwachungsressourcen mit Herzfrequenz anzuzeigen. 

> [!TIP]
> Vergewissern Sie sich, dass Ihr Benutzer über die erforderlichen Zugriffsrechte für die Azure API for FHIR-Datenebene verfügt. Verwenden Sie die [rollenbasierte Zugriffssteuerung in Azure](configure-azure-rbac.md) (Azure Role-Based Access Control, Azure RBAC), um die erforderlichen Datenebenenrollen zuzuweisen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine Instanz von Azure IoT-Konnektor für FHIR nicht mehr benötigen, können Sie sie löschen. Entfernen Sie dazu die zugehörige Ressourcengruppe, den zugehörigen Azure API for FHIR-Dienst oder die Instanz von Azure IoT-Konnektor für FHIR selbst. 

Um eine Instanz von Azure IoT-Konnektor für FHIR direkt zu entfernen, wählen Sie die Instanz auf der Seite **IoT Connectors** (IoT-Konnektor-Instanzen) aus, um die Seite **IoT-Konnektor** aufzurufen, und klicken Sie auf die Schaltfläche **Löschen**. Wählen Sie **Ja** aus, wenn Sie zur Bestätigung aufgefordert werden. 

[![Löschen einer IoT-Konnektor-Instanz](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie das Feature „Azure IoT-Konnektor für FHIR“ in Ihrer Azure API for FHIR-Ressource bereitgestellt. Weitere Informationen zu Azure IoT-Konnektor für FHIR finden Sie in den folgenden Schritten:

Machen Sie sich mit verschiedenen Datenflussphasen in Azure IoT-Konnektor für FHIR vertraut:

>[!div class="nextstepaction"]
>[Datenfluss im Azure IoT-Konnektor für FHIR](iot-data-flow.md)

Erfahren Sie, wie Sie IoT Connector mithilfe von Geräte- und FHIR-Zuordnungsvorlagen konfigurieren:

>[!div class="nextstepaction"]
>[Zuordnungsvorlagen im Azure IoT-Konnektor für FHIR](iot-mapping-templates.md)

*Im Azure-Portal wird Azure IoT-Konnektor für FHIR als „IoT-Konnektor (Vorschau)“ bezeichnet. FHIR ist eine eingetragene Marke von HL7 und wird mit Genehmigung von HL7 verwendet.