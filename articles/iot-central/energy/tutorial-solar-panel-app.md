---
title: 'Tutorial: Erstellen einer App für die Überwachung von Solarpaneln mit Azure IoT Central'
description: 'Tutorial: Hier erfahren Sie, wie Sie mithilfe von Azure IoT Central-Anwendungsvorlagen eine Solarpanel-Anwendung erstellen.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 509e31919dd974da253cd0478a70f889cc060fae
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831789"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Tutorial: Erstellen und Erkunden der App-Vorlage für die Überwachung von Solarpaneln 

In diesem Tutorial werden die Schritte zum Erstellen einer Anwendung für die Überwachung von Solarpaneln erläutert, die ein Beispielgerätemodell mit simulierten Daten enthält. In diesem Tutorial lernen Sie Folgendes:


> [!div class="checklist"]
> * Kostenloses Erstellen einer Solarpanel-App
> * Einführung in die Anwendung
> * Bereinigen von Ressourcen


Sollten Sie noch nicht über ein Abonnement verfügen, [erstellen Sie ein kostenloses Testkonto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial müssen keine Voraussetzungen erfüllt werden. Ein Azure-Abonnement wird zwar empfohlen, ist aber nicht erforderlich.


## <a name="create-a-solar-panel-monitoring-app"></a>Erstellen einer App für die Überwachung von Solarelementen 

Sie können diese Anwendung in drei einfachen Schritten erstellen:

1. Navigieren Sie zu [Azure IoT Central](https://apps.azureiotcentral.com). Wählen Sie zum Erstellen einer neuen Anwendung die Option **Erstellen** aus. 

1. Wählen Sie die Registerkarte **Energie** aus. Wählen Sie unter **Überwachung von Solarpaneln** die Option **App erstellen** aus. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Erstellungsoptionen in Azure IoT Central](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. Geben Sie im Dialogfeld **Neue Anwendung** die erforderlichen Details ein, und wählen Sie anschließend **Erstellen** aus:
    * **Anwendungsname**: Wählen Sie einen Namen für Ihre Azure IoT Central-Anwendung. 
    * **URL**: Wählen Sie eine Azure IoT Central-URL. Die Angabe wird von der Plattform auf Eindeutigkeit überprüft.
    * **Tarif**: Wenn Sie bereits über ein Azure-Abonnement verfügen, wird die Standardeinstellung empfohlen. Falls Sie noch nicht über ein Azure-Abonnement verfügen, beginnen Sie mit der kostenlosen Testversion.
    * **Abrechnungsinfo**: Die Anwendung selbst ist kostenlos. Die Angaben zu Verzeichnis, Azure-Abonnement und Region sind für die Bereitstellung der Ressourcen für die App erforderlich.
        ![Screenshot: Neue Anwendung](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Screenshot: Abrechnungsinfo](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Überprüfen der Anwendung und der simulierten Daten

Sie können Ihre neue Solarpanel-App jederzeit ändern. Vergewissern Sie sich zunächst, dass die App wie erwartet bereitgestellt wurde und ausgeführt wird, bevor Sie sie ändern.

Wechseln Sie zum Überprüfen der App-Erstellung und der Datensimulation zum **Dashboard**. Wenn die Kacheln mit einigen Daten angezeigt werden, war die Bereitstellung Ihrer App erfolgreich. Bei der Datensimulation kann es einige Minuten dauern, bis die Daten generiert wurden. 

## <a name="application-walk-through"></a>Anwendungsablauf
Nach erfolgreicher Bereitstellung der App-Vorlage empfiehlt es sich, die App etwas genauer in Augenschein zu nehmen. Wie Sie sehen, beinhaltet sie ein exemplarisches intelligentes Zählergerät, ein Gerätemodell und ein Dashboard.

Adatum ist ein fiktives Energieversorgungsunternehmen, das Solarpanel überwacht und verwaltet. Im Dashboard für die Überwachung von Solarpanels werden die entsprechenden Eigenschaften, Daten und Beispielbefehle angezeigt. Dieses Dashboard ermöglicht es Ihnen oder Ihrem Supportteam, folgende Aktivitäten proaktiv durchzuführen, bevor Probleme mit zusätzlichem Supportaufwand auftreten:
* Überprüfen der aktuellen Panelinformationen und des Installationsorts auf der Karte
* Überprüfen von Panel- und Verbindungsstatus
* Überprüfen der Trends bei Energieerzeugung und Temperatur, um Anomalien zu erkennen
* Nachverfolgen der gesamten Energieerzeugung zu Planungs- und Abrechnungszwecken
* Aktivieren eines Panels und Aktualisieren der Firmwareversion bei Bedarf In der Vorlage werden über die Befehlsschaltflächen die möglichen Funktionen angezeigt, jedoch keine echten Befehle gesendet.

> [!div class="mx-imgBorder"]
> ![Screenshot: Vorlage für die Überwachung von Solarpaneln: Dashboard](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Geräte
Die App enthält ein Beispielgerät für ein Solarpanel. Wählen Sie zum Anzeigen von Gerätedetails die Option **Geräte** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot: Vorlage für die Überwachung von Solarpaneln: Geräte](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Wählen Sie das Beispielgerät (**SP0123456789**) aus. Auf der Registerkarte **Update Properties** (Eigenschaften aktualisieren) können Sie die schreibbaren Eigenschaften des Geräts aktualisieren und die aktualisierten Werte im Dashboard anzeigen. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Vorlage für die Überwachung von Solarpaneln: Registerkarte zum Aktualisieren von Eigenschaften](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Gerätevorlage
Wählen Sie die Registerkarte **Gerätevorlagen** aus, um das Gerätemodell für Solarpanel anzuzeigen. Das Modell verfügt über vordefinierte Schnittstellen für Daten, Eigenschaften, Befehle und Ansichten.

> [!div class="mx-imgBorder"]
> ![Screenshot: Vorlage für die Überwachung von Solarpaneln: Gerätevorlagen](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese Anwendung nicht mehr benötigen, können Sie sie mit den folgenden Schritten löschen:

1. Wählen Sie im linken Bereich die Option **Verwaltung** aus.
1. Wählen Sie **Anwendungseinstellungen** > **Löschen** aus. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Vorlage für die Überwachung von Solarpaneln: Verwaltung](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Nächste Schritte
 
> [!div class="nextstepaction"]
> [Azure IoT Central – App-Architektur für Solarpanel](./concept-iot-central-solar-panel-app.md)

