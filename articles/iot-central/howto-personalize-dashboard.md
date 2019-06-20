---
title: Erstellen persönlicher Dashboards für Azure IoT Central | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Benutzer Ihre persönlichen Dashboards erstellen und verwalten.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a1641c4f3ae4eaa175657db8ff4064fa2202aea6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053013"
---
# <a name="create-and-manage-personal-dashboards"></a>Erstellen und Verwalten persönlicher Dashboards

Das **Dashboard** ist die Seite, die geladen wird, wenn Sie zum ersten Mal zu Ihrer Anwendung navigieren. Ein **Builder** in Ihrer Anwendung definiert das Standardanwendungsdashboard für alle Benutzer. Sie können dieses Standarddashboard durch Ihr eigenes, personalisiertes Anwendungsdashboard ersetzen. Sie können über mehrere Dashboards mit verschiedene Daten verfügen und zwischen diesen wechseln.

## <a name="create-dashboard"></a>Erstellen von Dashboards

Der folgende Screenshot zeigt das Dashboard in einer Anwendung, das über die Vorlage **Beispiel Contoso** erstellt wurde. Sie können das Standardanwendungsdashboard durch ein persönliches Dashboard ersetzen. Wählen Sie dazu oben rechts auf der Seite **+ Neu** aus.

![Dashboard für Anwendungen auf Basis der „Beispiel-Contoso“-Vorlage](media/howto-personalize-dashboard/defaultdashboard.png)

Indem Sie **+ Neu** auswählen, wird der Dashboard-Editor geöffnet. Im Editor können Sie Ihrem Dashboard einen Namen geben und Elemente aus der Bibliothek auswählen. Die Bibliothek enthält die Kacheln und Dashboardprimitive, mit denen Sie das Dashboard anpassen können.

![Dashboardbibliothek](media/howto-personalize-dashboard/dashboardeditor.png)

Beispielsweise können Sie eine Kachel **Geräteeinstellungen und -eigenschaften** hinzufügen, um Werte für Einstellungen und Eigenschaften für eines der von Ihnen verwalteten Geräte anzuzeigen. Wählen Sie dazu zuerst eine **Gerätevorlage** und dann eine **Geräteinstanz** aus. Danach geben Sie der Kachel einen Titel und wählen eine anzuzeigende **Einstellung** oder **Eigenschaft** aus. Der folgende Screenshot zeigt die Einstellung **Lüftergeschwindigkeit**, die ausgewählt wurde, um sie zur Kachel hinzuzufügen. Wählen Sie **Fertig** aus, um die Änderung in Ihrem Dashboard zu speichern.

![Formular „Gerätedetails konfigurieren“ mit Details zu Einstellungen und Eigenschaften](media/howto-personalize-dashboard/dashboardsetting.png)

Wenn Sie jetzt Ihr persönliches Dashboard öffnen, wird Sie die neue Kachel mit der Einstellung **Lüftergeschwindigkeit** für das Gerät angezeigt:

![Registerkarte „Dashboard“ mit Anzeige der Einstellungen und Eigenschaften für die Kachel](media/howto-personalize-dashboard/personaldashboard.png)

Sie können andere Kacheltypen in der Bibliothek ausprobieren, um herauszufinden, wie Sie Ihre persönlichen Dashboards weiter anpassen können.

## <a name="manage-dashboards"></a>Dashboards verwalten

Sie können über mehrere persönliche Dashboards verfügen und zwischen ihnen wechseln oder das Standardanwendungsdashboard auswählen:

![Dashboard wechseln](media/howto-personalize-dashboard/switchdashboards.png)

Sie können Ihre persönlichen Dashboards bearbeiten und diejenigen löschen, die Sie nicht mehr benötigen:

![Dashboard löschen](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun gelernt haben, wie man persönliche Dashboards erstellt und verwaltet, können Sie Folgendes Aufgaben ausführen:

> [!div class="nextstepaction"]
> [Lernen Sie, wie Sie Ihre Anwendungseinstellungen verwalten](howto-manage-preferences.md)
