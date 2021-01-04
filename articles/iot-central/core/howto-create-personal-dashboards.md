---
title: Erstellen persönlicher Dashboards für Azure IoT Central | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Benutzer Ihre persönlichen Dashboards erstellen und verwalten.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 68bb4e2ac525270004a698471cd44955cd25fefc
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146108"
---
# <a name="create-and-manage-multiple-dashboards"></a>Erstellen und Verwalten mehrerer Dashboards

Das **Dashboard** ist die Seite, die geladen wird, wenn Sie zum ersten Mal zu Ihrer Anwendung navigieren. Ein **Builder** in Ihrer Anwendung definiert das Standardanwendungsdashboard für alle Benutzer. Außerdem können Sie ein eigenes, personalisiertes Anwendungsdashboard erstellen. Sie können über mehrere Dashboards mit verschiedene Daten verfügen und zwischen diesen wechseln.

Wenn Sie ein **Administrator** für die Anwendung sind, können Sie auch bis zu 10 Dashboards auf Anwendungsebene erstellen, die Sie für andere Benutzer der Anwendung freigeben können. Nur **Administratoren** haben die Möglichkeit zum Erstellen, Bearbeiten und Löschen von Dashboards auf Anwendungsebene.  

## <a name="create-dashboard"></a>Erstellen von Dashboards

Der folgende Screenshot zeigt das Dashboard in einer Anwendung, die über die Vorlage **Benutzerdefinierte Anwendung** erstellt wurde. Sie können das Standardanwendungsdashboard durch ein persönliches Dashboard ersetzen oder – als Administrator – ein anderes Dashboard auf Anwendungsebene. Wählen Sie dazu oben links auf der Seite **+ Neu** aus.

> [!div class="mx-imgBorder"]
> ![Dashboard für Anwendungen, die auf der Vorlage „Benutzerdefinierte Anwendung“ basieren](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Wenn Sie **+ Neu** auswählen, wird der Dashboard-Editor geöffnet. Im Editor können Sie Ihrem Dashboard einen Namen geben und Elemente aus der Bibliothek auswählen. Die Bibliothek enthält die Kacheln und Dashboardprimitive, mit denen Sie das Dashboard anpassen können.

> [!div class="mx-imgBorder"]
> ![Dashboardbibliothek](media/howto-create-personal-dashboards/dashboard-library.png)

Sollten Sie ein **Administrator** für die Anwendung sein, erhalten Sie die Möglichkeit zum Erstellen eines Dashboards auf persönlicher Ebene oder eines Dashboards auf Anwendungsebene. Wenn Sie ein Dashboard auf persönlicher Ebene erstellen, wird es nur für Sie angezeigt. Wenn Sie ein Dashboard auf Anwendungsebene erstellen, wird es für jeden Benutzer der Anwendung angezeigt. Nachdem Sie einen Titel eingegeben und den Typ des zu erstellenden Dashboards ausgewählt haben, können Sie Kacheln später speichern und hinzufügen. Wenn Sie aber schon bereit sind und eine Gerätevorlage und Geräteinstanz hinzugefügt haben, können Sie jetzt Ihre erste Kachel erstellen.  

> [!div class="mx-imgBorder"]
> ![Formular „Gerätedetails konfigurieren“ mit Details zu „Temperatur“](media/howto-create-personal-dashboards/device-details.png)

Sie können beispielsweise eine Kachel **Telemetrie** für die aktuelle Temperatur des Geräts hinzufügen. Gehen Sie folgendermaßen vor:

1. Wählen Sie eine **Gerätevorlage** aus.
1. Wählen Sie in **Geräte** das Gerät aus, das auf einer Dashboardkachel angezeigt werden soll. Dann wird eine Liste der Geräteeigenschaften angezeigt, die auf der Kachel verwendet werden können.
1. Wenn Sie die Kachel auf dem Dashboard erstellen möchten, klicken Sie auf **Temperatur** , und ziehen Sie sie auf den Dashboardbereich. Sie können auch auf das Kontrollkästchen neben **Temperatur** und dann auf **Kachel hinzufügen** klicken. Der folgende Screenshot zeigt das Auswählen einer Gerätevorlage und eines Geräts und dann das Erstellen der Kachel „Temperaturtelemetrie“ auf dem Dashboard.
1. Wählen Sie oben links **Speichern** aus, um die Änderungen des Dashboards zu speichern.

> [!div class="mx-imgBorder"]
> ![Registerkarte „Dashboard“ mit Details zur Kachel „Temperatur“](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Wenn Sie jetzt Ihr persönliches Dashboard öffnen, wird die neue Kachel mit der Einstellung **Temperatur** für das Gerät angezeigt:

> [!div class="mx-imgBorder"]
> ![Screenshot der neuen Kachel mit den Temperatureinstellungen für das Gerät](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Sie können andere Kacheltypen in der Bibliothek ausprobieren, um herauszufinden, wie Sie Ihre persönlichen Dashboards weiter anpassen können.

Weitere Informationen zur Verwendung von Kacheln in Azure IoT Central finden Sie unter [Hinzufügen von Kacheln zu Ihrem Dashboard](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Dashboards verwalten

Sie können mehrere persönliche Dashboards haben und zwischen ihnen wechseln oder aus einem der Standardanwendungsdashboards auswählen:

> [!div class="mx-imgBorder"]
> ![Wechseln zwischen Dashboards](media/howto-create-personal-dashboards/switch-dashboards.png)

Sie können Ihre persönlichen Dashboards bearbeiten und die nicht mehr benötigten löschen. Als **Administrator** haben Sie auch die Möglichkeit zum Bearbeiten oder Löschen von Dashboards auf Anwendungsebene.

> [!div class="mx-imgBorder"]
> ![Löschen von Dashboards](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie persönliche Dashboards erstellen und verwalten, können Sie als Nächstes [erfahren, wie Sie Ihre Anwendungseinstellungen verwalten](howto-manage-preferences.md).
