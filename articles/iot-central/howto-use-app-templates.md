---
title: Verwenden von Anwendungsvorlagen in Azure IoT Central | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Operator Gerätegruppen in der Azure IoT Central-Anwendung verwenden.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498588"
---
# <a name="use-application-templates"></a>Verwenden von Anwendungsvorlagen

In diesem Artikel wird beschrieben, wie Sie als Solution Manager Anwendungsvorlagen erstellen und verwenden.

Wenn Sie eine Azure IoT Central-Anwendung erstellen, können Sie unter verschiedenen integrierten Beispielvorlagen auswählen. Sie können auch eigene Anwendungsvorlagen von vorhandenen IoT Central-Anwendungen erstellen. Anschließend können Sie bei der Erstellung neuer Anwendung Ihre eigenen Anwendungsvorlagen verwenden.

Beim Erstellen einer Anwendungsvorlage werden die folgenden Elemente aus Ihrer vorhandenen Anwendung einbezogen:

- Das standardmäßige Anwendungsdashboard, einschließlich des Dashboardlayouts und aller von Ihnen definierten Kacheln.
- Gerätevorlagen, einschließlich Messungen, Einstellungen, Eigenschaften, Befehle und Dashboard.
- Regeln – Alle Regeldefinitionen werden eingeschlossen. Aktionen werden jedoch nicht eingeschlossen, mit Ausnahme von E-Mail-Aktionen.
- Gerätegruppen, einschließlich Gerätezustände und Dashboards.

> [!WARNING]
> Wenn ein Dashboard Kacheln enthält, in denen Informationen zu bestimmten Geräten angezeigt werden, erscheint in der neuen Anwendung die Meldung **Die angeforderte Ressource wurde nicht gefunden** in diesen Kacheln. Sie müssen diese Kacheln erneut konfigurieren, damit in der neuen Anwendung Informationen zu Geräten angezeigt werden.

Die folgenden Elemente sind nicht enthalten, wenn Sie eine Anwendungsvorlage erstellen:

- Geräte
- Benutzer
- Auftragsdefinitionen
- Definitionen für den kontinuierlichen Datenexport

Diese Elemente müssen allen von einer Anwendungsvorlage erstellten Anwendungen manuell hinzugefügt werden.

## <a name="create-an-application-template"></a>Erstellen einer Anwendungsvorlage

So erstellen Sie eine Anwendungsvorlage von einer vorhandenen IoT Central-Anwendung:

1. Wechseln Sie in Ihrer Anwendung zum Abschnitt **Verwaltung**.
1. Wählen Sie die Option zum **** Exportieren von Anwendungsvorlagen aus.
1. Geben Sie auf der Seite zum **** Exportieren von Anwendungsvorlagen einen Namen und eine Beschreibung für Ihre Vorlage ein.
1. Wählen Sie die Schaltfläche **Exportieren** aus, um die Anwendungsvorlage zu erstellen. Jetzt können Sie den **Freigabefähigen Link** kopieren, mit dem andere Benutzer eine neue Anwendung von der Vorlage erstellen können:

![Erstellen einer Anwendungsvorlage](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Verwenden einer Anwendungsvorlage

Um eine Anwendungsvorlage zum Erstellen einer neuen IoT Central-Anwendung zu verwenden, benötigen Sie einen zuvor erstellten **Freigabefähigen Link**. Fügen Sie den **Freigabefähigen Link** in die Adressleiste Ihres Browsers ein. Die Seite **Anwendung erstellen** wird angezeigt. Dabei ist Ihre benutzerdefinierte Anwendungsvorlage ausgewählt:

![Erstellen einer Anwendung von einer Vorlage](media/howto-use-app-templates/create-app.png)

Wählen Sie Ihren Zahlungsplan aus, und füllen Sie die übrigen Felder im Formular aus. Wählen Sie dann **Erstellen** aus, um von der Anwendungsvorlage eine neue IoT Central-Anwendung zu erstellen.

## <a name="manage-application-templates"></a>Verwalten von Anwendungsvorlagen

Auf der Seite zum **** Exportieren von Anwendungsvorlagen können Sie die Anwendungsvorlage löschen oder aktualisieren.

Wenn Sie eine Anwendungsvorlage löschen, ist es nicht mehr möglich, den zuvor generierten freigabefähigen Link zum Erstellen neuer Anwendungen zu verwenden.

Um Ihre Anwendungsvorlage zu aktualisieren, ändern Sie auf der Seite zum **** Exportieren von Anwendungsvorlagen den Namen oder die Beschreibung der Vorlage. Wählen Sie dann erneut die Schaltfläche **Exportieren** aus. Durch diese Aktion wird ein neuer **freigabefähiger Link** generiert. Dadurch werden vorherige URLs für **freigabefähige Links** ungültig.

## <a name="next-steps"></a>Nächste Schritte

Sie wissen jetzt, wie Sie Anwendungsvorlagen verwenden. Informieren Sie sich als Nächstes, wie Sie [IoT Central über das Azure-Portal verwalten](howto-manage-iot-central-from-portal.md).
