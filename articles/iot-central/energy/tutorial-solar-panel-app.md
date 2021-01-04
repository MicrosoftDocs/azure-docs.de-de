---
title: 'Tutorial: Erstellen einer App für die Überwachung von Solarpanels mit IoT Central'
description: 'Tutorial: Erfahren Sie, wie Sie eine Solarpanel-Anwendung mithilfe von Azure IoT Central-Anwendungsvorlagen erstellen.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d006bae8290c1f6ea564f9985727f4d6ff70bb04
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516753"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Tutorial: Erstellen und Durchlaufen der App-Vorlage für die Überwachung von Solarpanels 

In diesem Tutorial werden die Schritte zum Erstellen der Anwendung für die Überwachung von Solarpanels erläutert, die ein Beispielgerätemodell mit simulierten Daten enthält. In diesem Lernprogramm lernen Sie Folgendes:


> [!div class="checklist"]
> * Kostenloses Erstellen der Solarpanel-App
> * Anwendungsablauf
> * Bereinigen von Ressourcen


Falls Sie noch nicht über ein Abonnement verfügen, [erstellen Sie ein kostenloses Testkonto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Voraussetzungen
* Keine
* Ein Azure-Abonnement wird empfohlen, ist aber für das Testen nicht erforderlich.


## <a name="create-a-solar-panel-monitoring-app"></a>Erstellen einer App für die Überwachung von Solarelementen 

Sie können diese Anwendung in drei einfachen Schritten erstellen:

1. Öffnen Sie die [Azure IoT Central-Startseite](https://apps.azureiotcentral.com), und klicken Sie auf **Erstellen**, um eine neue Anwendung zu erstellen. 

1. Wählen Sie die Registerkarte **Energy** (Energie) aus, und klicken Sie auf der Anwendungskachel **Solar panel monitoring** (Überwachung von Solarpanels) auf **App erstellen**. 

    > [!div class="mx-imgBorder"]
    > ![Erstellen einer App](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. Über **App erstellen** wird das Formular **Neue Anwendung** geöffnet. Geben Sie wie in der nachfolgenden Abbildung dargestellt die erforderlichen Details ein:
    * **Anwendungsname**: Geben Sie einen Namen für die IoT Central-Anwendung an. 
    * **URL**: Geben Sie eine IoT Central-URL an. Die Eindeutigkeit wird auf der Plattform überprüft.
    * **Kostenlose 7-Tage-Testversion**: Wenn Sie bereits über ein Azure-Abonnement verfügen, wird die Standardeinstellung empfohlen. Falls Sie noch nicht über ein Azure-Abonnement verfügen, beginnen Sie mit einer kostenlosen Testversion.
    * **Abrechnungsinfo**: Die Anwendung selbst ist kostenlos. Die Detailangaben zu Verzeichnis, Azure-Abonnement und Region sind für die Bereitstellung der Ressourcen für die App erforderlich.
    * Klicken Sie unten auf der Seite auf die Schaltfläche **Erstellen**. Die App wird innerhalb etwa einer Minute erstellt.
        ![Formular für neue Anwendung](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Formular „Neue Anwendung“: Abrechnungsinformationen](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Überprüfen der Anwendung und der simulierten Daten

Sie können Ihre neu erstellte Solarpanel-App jederzeit ändern. Vergewissern Sie sich, dass die App wie erwartet bereitgestellt wurde und ausgeführt wird, bevor Sie sie ändern.

Wechseln Sie zum Überprüfen der App-Erstellung und der Datensimulation zum **Dashboard**. Wenn die Kacheln mit einigen Daten angezeigt werden, war die Bereitstellung Ihrer App erfolgreich. Bei der Datensimulation kann es einige Minuten dauern, bis die Daten generiert wurden. Warten Sie daher 1–2 Minuten. 

## <a name="application-walk-through"></a>Anwendungsablauf
Nachdem Sie die App-Vorlage erfolgreich bereitgestellt haben, enthält sie ein intelligentes Beispielmessgerät, ein Gerätemodell und ein Dashboard.

Adatum ist ein fiktives Energieversorgungsunternehmen, das Solarpanels überwacht und verwaltet. Im Dashboard für die Überwachung von Solarpanels werden die entsprechenden Eigenschaften, Daten und Beispielbefehle angezeigt. In diesem Dashboard können Bediener und Supportteams proaktiv die folgenden Aktivitäten durchführen, bevor daraus Supportfälle werden:
* Überprüfen der aktuellen Informationen zum Solarpanel und dessen Installationsposition auf der Karte
* Proaktives Überprüfen des Solarpanelstatus und des Verbindungsstatus
* Überprüfen der Trends zur Energieerzeugung und Temperatur zur Erfassung anomaler Muster
* Nachverfolgen der gesamten Energieerzeugung für Planungs- und Abrechnungszwecke
* Befehls- und Steuerungsvorgänge, z. B. Aktivieren des Panels und Aktualisieren der Firmwareversion In der Vorlage werden über die Befehlsschaltflächen die möglichen Funktionen angezeigt, jedoch keine realen Befehle gesendet.

> [!div class="mx-imgBorder"]
> ![Dashboard für die Überwachung von Solarpanels](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Geräte
Die App enthält ein Beispielgerät für ein Solarpanel. Sie können die Gerätedetails anzeigen, indem Sie auf die Registerkarte **Geräte** klicken.

> [!div class="mx-imgBorder"]
> ![Solarpanelgeräte](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Klicken Sie auf den Link für das Beispielgerät **SP0123456789**, um die Gerätedetails anzuzeigen. Auf der Seite **Update Properties** (Eigenschaften aktualisieren) können Sie die schreibbaren Eigenschaften des Gerätes aktualisieren und die aktualisierten Werte im Dashboard anzeigen. 

> [!div class="mx-imgBorder"]
> ![Solarpaneleigenschaften](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Gerätevorlage
Klicken Sie auf die Registerkarte **Device templates** (Gerätevorlagen), um das Gerätemodell für Solarpanels anzuzeigen. Das Modell umfasst eine vordefinierte Schnittstelle für Daten, Eigenschaften, Befehle und Ansichten.

> [!div class="mx-imgBorder"]
> ![Vorlage für Solarpanelgeräte](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese Anwendung nicht mehr verwenden möchten, können Sie sie mit den folgenden Schritten löschen:

1. Öffnen Sie im linken Bereich die Registerkarte „Verwaltung“.
1. Wählen Sie „Anwendungseinstellungen“ aus, und klicken Sie unten auf der Seite auf die Schaltfläche „Löschen“. 

    > [!div class="mx-imgBorder"]
    > ![Löschen der Anwendung](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Nächste Schritte
* Informationen zur App-Architektur für Solarpanel finden Sie im folgenden Artikel: 
> [!div class="nextstepaction"]
> [Azure IoT Central – App-Architektur für Solarpanel](./concept-iot-central-solar-panel-app.md)
* Erstellen Sie kostenlos Anwendungsvorlagen für Solarpanels: [Solarpanel-App](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Weitere Informationen zu IoT Central finden Sie in der [Übersicht über IoT Central](../index.yml).
