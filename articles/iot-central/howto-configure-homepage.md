---
title: Konfigurieren des Dashboards Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie als Ersteller das Standarddashboard Ihrer Azure IoT Central-Anwendung konfigurieren.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: b1edd4777839c947cf8f60bb6857f939553e2603
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65467613"
---
# <a name="configure-the-application-dashboard"></a>Konfigurieren des Anwendungsdashboards

Das **Dashboard** ist die Seite, die geladen wird, wenn zugriffsberechtigte Benutzer zur URL der Anwendung navigieren. Wenn Sie beim Erstellen Ihrer Anwendung die Anwendungsvorlagen **Beispiel Contoso** oder **Beispiel-Entwickler-Kits** ausgewählt haben, verfügt Ihre Anwendung über ein vordefiniertes Dashboard. Wenn Sie die Anwendungsvorlage **Benutzerdefinierte Anwendung** ausgewählt haben, ist Ihr Dashboard leer.

> [!NOTE]
> Benutzer können auch [ihre eigenen persönlichen Dashboards erstellen](howto-personalize-dashboard.md), die anstelle des standardmäßigen Dashboards der Anwendung verwendet werden können.

## <a name="add-tiles"></a>Hinzufügen von Kacheln

Der folgende Screenshot zeigt das Dashboard in einer Anwendung, das über die Vorlage **Beispiel Contoso** erstellt wurde. Wählen Sie zum Anpassen des Standarddashboards Ihrer Anwendung oben rechts auf der Seite die Option **Bearbeiten** aus.

![Dashboard für Anwendungen auf Basis der „Beispiel Contoso“-Vorlage](media/howto-configure-homepage/image1a.png)

Wenn Sie **Bearbeiten** auswählen, wird das Bibliotheksfenster des Dashboards geöffnet. Die Bibliothek enthält die Kacheln und Dashboardprimitive, mit denen Sie das Dashboard anpassen können.

![Dashboardbibliothek](media/howto-configure-homepage/image2a.png)

Beispielsweise können Sie eine Kachel **Geräteeinstellungen und -eigenschaften** hinzufügen, um eine Auswahl der aktuellen Werte für Einstellungen und Eigenschaften für ein Gerät anzuzeigen. Wählen Sie dazu zuerst eine **Gerätevorlage** und dann eine **Geräteinstanz** aus. Danach geben Sie der Kachel einen Titel und wählen eine anzuzeigende **Einstellung** oder **Eigenschaft** aus. Der folgende Screenshot zeigt Einstellungen und Eigenschaften, die zum Hinzufügen zur Kachel ausgewählt wurden. Wählen Sie **Fertig** aus, um die Änderung am Dashboard zu speichern.

![Formular „Gerätedetails konfigurieren“ mit Details zu Einstellungen und Eigenschaften](media/howto-configure-homepage/image3a.png)

Wenn ein Bediener jetzt das Standarddashboard der Anwendung anzeigt, wird die neue Kachel mit der Einstellung **Temperatur festlegen** für das Gerät angezeigt:

![Registerkarte „Dashboard“ mit Anzeige der Einstellungen und Eigenschaften für die Kachel](media/howto-configure-homepage/image4a.png)

Sie können weitere Kacheltypen in der Bibliothek untersuchen, um zu erfahren, wie Sie das Standarddashboard der Anwendung weiter anpassen können.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun gelernt haben, wie Sie das Standarddashboard der Azure IoT Central-Anwendung konfigurieren, haben Sie folgende Möglichkeiten:

> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie Bilder vorbereiten und hochladen.](howto-prepare-images.md)
