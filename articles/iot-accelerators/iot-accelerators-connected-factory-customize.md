---
title: 'Anpassen der Lösung für verbundene Factorys: Azure | Microsoft Docs'
description: Beschreibung der Verhaltensanpassung des Solution Accelerators für eine verbundene Factory.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a092859517ee6ac41e62e55fe95f72b8bfdf87d5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011199"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Anpassen der Anzeige von Daten von Ihren OPC UA-Servern durch die Lösung für eine verbundene Factory

> [!IMPORTANT]
> Während wir diesen Artikel aktualisieren, können Sie unter [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) den Inhalt auf dem neuesten Stand lesen.

Die Lösung für eine verbundene Factory aggregiert und zeigt Daten von OPC UA-Servern an, die mit der Lösung verbunden sind. Sie können in Ihrer Lösung zu den OPC UA-Servern navigieren und Befehle an diese senden. Weitere Informationen zu OPC UA finden Sie in den [häufig gestellten Fragen zu verbundenen Factorys](iot-accelerators-faq-cf.md).

Beispiele für aggregierte Daten in der Lösung sind die anlagenübergreifende Effizienz (Overall Equipment Efficiency, OEE) und Leistungsindikatoren (Key Performance Indicators, KPIs), die Sie im Dashboard auf Werks-, Fertigungsstraßen- und Stationsebene anzeigen können. Der folgende Screenshot zeigt die OEE- und KPI-Werte für die Station **Montage** in der **Fertigungsstraße 1** im Werk **München**:

![Beispiel für OEE- und KPI-Werte in der Lösung][img-oee-kpi]

Die Lösung ermöglicht Ihnen das Anzeigen detaillierter Informationen in bestimmten Datenelementen, die von den *Stationen* genannten OPC UA-Servern stammen. Der folgende Screenshot zeigt grafische Darstellungen der Anzahl der an einer bestimmten Station gefertigten Teile:

![Grafische Darstellungen der Anzahl gefertigter Teile][img-manufactured-items]

Wenn Sie auf eines der Diagramme klicken, können Sie die Daten mithilfe von Time Series Insights (TSI) weiter untersuchen:

![Untersuchen von Daten mit Time Series Insights][img-tsi]

Dieser Artikel beschreibt Folgendes:

- Das Verfügbarmachen der Daten in den verschiedenen Ansichten
- Das Anpassen der Anzeige der Daten durch die Lösung

## <a name="data-sources"></a>Datenquellen

Die Lösung für eine verbundene Factory zeigt Daten von OPC UA-Servern an, die mit der Lösung verbunden sind. Die Standardinstallation umfasst mehrere OPC UA-Server, auf denen eine Werkssimulation ausgeführt wird. Sie können eigene OPC UA-Server hinzufügen, die Sie [über ein Gateway] [lnk-connect-cf] mit der Lösung verbinden.

Sie können im Dashboard die Datenelemente durchsuchen, die ein verbundener OPC UA-Server an Ihre Lösung senden kann:

1. Wählen Sie **Browser** aus, um zur Ansicht **OPC UA-Server auswählen** zu navigieren:

    ![Navigieren zur Ansicht „Select an OPC UA server“ (OPC UA-Server auswählen)][img-select-server]

1. Wählen Sie einen Server aus, und klicken Sie auf **Connect** (Verbinden). Klicken Sie auf **Proceed** (Fortfahren), wenn die Sicherheitswarnung angezeigt wird.

    > [!NOTE]
    > Diese Warnung wird nur einmal je Server angezeigt und richtet eine Vertrauensstellung zwischen Lösungsdashboard und Server ein.

1. Sie können die Datenelemente durchsuchen, die der Server an die Lösung senden kann. Elemente, die an die Lösung gesendet werden, weisen ein Häkchen auf:

    ![Veröffentlichte Elemente][img-published]

1. Wenn Sie ein *Administrator* in der Lösung sind, können Sie ein Datenelement veröffentlichen, um es in der Lösung für die verbundene Factory verfügbar zu machen. Als Administrator können Sie auch den Wert von Datenelementen und Aufrufmethoden im OPC UA-Server ändern.

## <a name="map-the-data"></a>Zuordnen der Daten

Mithilfe der Lösung für eine verbundene Factory werden die vom OPC UA-Server stammenden veröffentlichten Datenelemente den verschiedenen Ansichten in der Lösung zugeordnet und aggregiert. Die Lösung für eine verbundene Factory wird in Ihrem Azure-Konto zur Verfügung gestellt, sobald Sie die Lösung bereitstellen. Diese Zuordnungsinformationen werden in der Lösung für eine verbundene Factory in Visual Studio in einer JSON-Datei gespeichert. Sie können diese JSON-Konfigurationsdatei in der Lösung für eine verbundene Factory in Visual Studio anzeigen und ändern. Nachdem Sie eine Änderung vorgenommen haben, können Sie die Lösung erneut bereitstellen.

Sie können die Konfigurationsdatei für folgende Aufgaben verwenden:

- Bearbeiten der vorhandenen simulierten Werke, Fertigungsstraßen und Stationen
- Zuordnen von Daten von realen OPC UA-Servern, die Sie mit der Lösung verbinden.

Weitere Informationen zum Zuordnen und Aggregieren der Daten für Ihre spezifischen Anforderungen finden Sie unter [Konfigurieren des Solution Accelerators für eine verbundene Factory](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Bereitstellen der Änderungen

Nach Abschluss der Änderungen an der Datei **ContosoTopologyDescription.json** müssen Sie die Lösung für eine verbundene Factory erneut in Ihrem Azure-Konto bereitstellen.

Das Repository **azure-iot-connected-factory** enthält das PowerShell-Skript **build.ps1**, mit dem Sie die Lösung erneut erstellen und bereitstellen können.

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über den Solution Accelerator für eine verbundene Factory:

* [Berechtigungen für die Website „azureiotsolutions.com“][lnk-permissions]
* [Verbundene Factory: Häufig gestellte Fragen](iot-accelerators-faq-cf.md)
* [Häufig gestellte Fragen][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md