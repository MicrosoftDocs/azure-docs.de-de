---
title: Verwenden des Connected Factory-Dashboards – Azure | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Ihre industriellen IoT-Geräte mithilfe der Features des Connected Factory-Dashboards überwachen und verwalten können.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 797bd97940aa49db87b4ca4dd96f140208cd69b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011216"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Verwenden der Dashboardfunktionen des Solution Accelerators für Connected Factory

> [!IMPORTANT]
> Während wir diesen Artikel aktualisieren, können Sie unter [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) den Inhalt auf dem neuesten Stand lesen.

Im Schnellstart [Bereitstellen einer cloudbasierten Lösung zum Verwalten industrieller IoT-Geräte](quickstart-connected-factory-deploy.md) haben Sie erfahren, wie Sie im Dashboard navigieren und auf Alarme reagieren. Hier lernen Sie zusätzliche Dashboardfunktionen kennen, mit denen Sie Ihre industriellen IoT-Geräte überwachen und verwalten können.

## <a name="apply-filters"></a>Anwenden von Filtern

Sie können die im Dashboard angezeigten Informationen entweder im Bereich **Factorystandorte** oder im Bereich **Alarme** filtern.

1. Klicken Sie auf den **Trichter**, um eine Liste mit verfügbaren Filtern im Bereich mit den Factorystandorten oder im Bereich mit den Alarmen anzuzeigen.

1. Der Filterbereich wird angezeigt:

    [![Filter des Solution Accelerators für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Wählen Sie den gewünschten Filter aus, und klicken Sie auf **Anwenden**. Sie können auch beliebigen Text in die Filterfelder eingeben.

1. Anschließend wird der Filter angewendet. Das zusätzliche Trichtersymbol zeigt an, dass ein Filter angewendet wird:

    [![Angewendeter Filter des Solution Accelerators für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Ein aktiver Filter hat keine Auswirkung auf die angezeigten OEE- und KPI-Werte, sondern dient nur zum Filtern des Listeninhalts.

1. Um einen Filter zu löschen, klicken Sie auf den Trichter und dann im Filterbereich auf **Löschen**.

## <a name="browse-an-opc-ua-server"></a>Durchsuchen eines OPC UA-Servers

Beim Bereitstellen des Solution Accelerators stellen Sie automatisch simulierte OPC UA-Server bereit, die Sie im Dashboard durchsuchen können. Simulierte Server erleichtern Ihnen, den Solution Accelerator zu testen, ohne dass Sie echte Server bereitstellen müssen.

1. Klicken Sie in der Navigationsleiste des Dashboards auf das **Browsersymbol**:

    [![Serverbrowser für den Solution Accelerator für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Wählen Sie einen Server aus der Liste aus, die alle für Sie im Solution Accelerator bereitgestellten Server anzeigt:

    [![Serverliste des Solution Accelerators für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Klicken Sie auf **Verbinden**. Ein Sicherheitsdialogfeld wird angezeigt. Klicken Sie für die Simulation auf **Fortsetzen**.

1. Klicken Sie zum Erweitern eines Knotens in der Serverstruktur auf den gewünschten Knoten. Knoten, die Telemetriedaten veröffentlichen, sind mit einem Häkchen gekennzeichnet:

    [![Serverstruktur des Solution Accelerators für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Klicken Sie mit der rechten Maustaste auf ein Element, um Daten für den Knoten zu lesen, zu schreiben und zu veröffentlichen oder den Knoten aufzurufen. Es hängt von den Berechtigungen und Attributen des Knotens ab, welche Aktionen verfügbar sind. Bei der Option zum Lesen wird ein Kontextbereich mit dem Wert des jeweiligen Knotens angezeigt. Bei der Option zum Schreiben wird ein Kontextbereich angezeigt, in dem Sie einen neuen Wert eingeben können. Bei der Option zum Aufrufen wird ein Knoten angezeigt, über den Sie die Parameter für den Aufruf eingeben können.

## <a name="publish-a-node"></a>Veröffentlichen eines Knotens

Beim Durchsuchen eines *simulierten OPC UA-Servers* können Sie auch die Veröffentlichung neuer Knoten wählen. Sie können die Telemetrie dieser Knoten in der Lösung analysieren. Diese *simulierten OPC UA-Server* erleichtern das Testen des Solution Accelerators, ohne dass echte Geräte bereitgestellt werden müssen.

1. Navigieren Sie zu einem Knoten in der Browserstruktur des OPC UA-Servers, den Sie veröffentlichen möchten.

1. Klicken Sie mit der rechten Maustaste auf den Knoten. Klicken Sie auf **Veröffentlichen**:

    [![Veröffentlichen des Solution Accelerator-Knotens für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Es wird ein Kontextbereich mit dem Hinweis angezeigt, dass die Veröffentlichung erfolgreich war. Der Knoten wird in der Stationsebenenansicht mit einem Häkchen gekennzeichnet:

    [![Erfolgreiche Veröffentlichung des Solution Accelerator-Knotens für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Befehl und Steuerung

Mit verbundenen Factorys können Sie Ihre Industrieanlagen direkt über die Cloud steuern. Sie können dieses Feature nutzen, um auf Alarme zu reagieren, die von der Anlage generiert werden. Beispielsweise können Sie einen Befehl an das Gerät bzw. die Anlage senden, damit sie ein Druckreduzierventil öffnet. Sie finden die verfügbaren Befehle in der Browserstruktur des OPC UA-Servers unter dem Knoten **StationCommands**. In diesem Szenario wird an einer Montagestation einer Fertigungsstraße in München ein Druckreduzierventil geöffnet. Um die Funktionen für Befehle und Steuerung zu verwenden, benötigen Sie für die Bereitstellung des Solution Accelerators die Rolle **Administrator**.

1. Navigieren Sie zum Knoten **StationCommands** in der Browserstruktur des OPC UA-Servers für München, Fertigungslinie 0, Montagestation.

1. Wählen Sie den Befehl, den Sie verwenden möchten. Klicken Sie mit der rechten Maustaste auf den Knoten **OpenPressureReleaseValve**. Klicken Sie auf **Aufrufen**:

    [![Aufrufbefehl für den Solution Accelerator für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Ein Kontextbereich wird angezeigt. Er enthält Informationen zur aufzurufenden Methode und zu den Parameterdetails. Klicken Sie auf **Aufrufen**:

    [![Aufrufparameter für den Solution Accelerator für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Der Kontextbereich wird aktualisiert, um Sie darüber zu informieren, dass der Methodenaufruf erfolgreich war. Sie können überprüfen, ob der Aufruf erfolgreich war, indem Sie den Wert des Knotens für die Druckeinstellung ablesen, der als Folge des Aufrufs aktualisiert wurde.

    [![Erfolgreicher Aufruf im Solution Accelerator für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Abläufe im Hintergrund

Wenn Sie einen Solution Accelerator bereitstellen, werden im Bereitstellungsprozess mehrere Ressourcen im gewählten Azure-Abonnement erstellt. Sie können diese Ressourcen im Azure-[Portal](https://portal.azure.com) ansehen. Der Bereitstellungsprozess erstellt eine **Ressourcengruppe** mit einem Namen basierend auf dem Namen, den Sie für Ihren Solution Accelerator wählen:

[![Ressourcengruppe des Solution Accelerators für Connected Factory](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Sie können die Einstellungen der einzelnen Ressourcen anzeigen, indem Sie sie in der Ressourcengruppe in der Liste mit den Ressourcen auswählen.

Den Quellcode für den Solution Accelerators finden Sie im GitHub-Repository [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory).

Wenn Sie fertig sind, können Sie den Solution Accelerator auf der Website [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) aus Ihrem Azure-Abonnement löschen. Über diese Website können Sie problemlos alle Ressourcen löschen, die beim Erstellen des Solution Accelerators bereitgestellt wurden.

> [!NOTE]
> Führen Sie den Löschvorgang auf der Website [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) durch, um sicherzustellen, dass der Solution Accelerator vollständig gelöscht wird. Löschen Sie nicht die Ressourcengruppe im Portal.

## <a name="next-steps"></a>Nächste Schritte

Sie haben einen funktionierenden Solution Accelerator bereitgestellt und können nun mit den ersten Schritten mit IoT-Solution Accelerators fortfahren. Lesen Sie dazu die folgenden Artikel:

* [Konfigurieren des Solution Accelerators für Connected Factory](iot-accelerators-connected-factory-configure.md)
* [Berechtigungen für die Website „azureiotsolutions.com“](iot-accelerators-permissions.md)
