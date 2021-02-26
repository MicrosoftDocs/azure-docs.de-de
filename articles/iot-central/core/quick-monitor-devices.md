---
title: 'Schnellstart: Überwachen Ihrer Geräte in Azure IoT Central'
description: 'Schnellstart: Hier erfahren Sie, wie Sie als Operator mit der Azure IoT Central-Anwendung Ihre Geräte überwachen können.'
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c63a9833e6b9a9b243d289d79428ddef1468253
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833880"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Schnellstart: Überwachen Ihrer Geräte mithilfe von Azure IoT Central

*Dieser Artikel gilt für Betreiber, Ersteller und Administratoren.*

Diese Schnellstartanleitung richtet sich an Bediener und erläutert, wie Sie mithilfe Ihrer Microsoft Azure IoT Central-Anwendung Ihre Geräte überwachen und Einstellungen ändern.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, sollten Sie die drei vorherigen Schnellstartanleitungen durcharbeiten: [Erstellen einer Azure IoT Central-Anwendung (Previewfunktionen)](./quick-deploy-iot-central.md), [Schnellstart: Hinzufügen eines simulierten Geräts zu Ihrer IoT Central-Anwendung (Previewfunktionen)](./quick-create-simulated-device.md) und [Schnellstart: Konfigurieren von Regeln und Aktionen für Ihr Gerät in Azure IoT Central (Previewfunktionen)](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Empfangen einer Benachrichtigung

Azure IoT Central sendet Benachrichtigungen für Geräte in Form von E-Mails. Als Ersteller haben Sie eine Regel hinzugefügt, die dafür sorgt, dass eine Benachrichtigung an den Operator gesendet wird, wenn die Luftfeuchtigkeit an einem verbundenen Gerätesensor einen Schwellenwert übersteigt. Als Operator überprüfen Sie Ihre E-Mails auf Benachrichtigungen.

Öffnen Sie die E-Mail, die Sie am Ende der Schnellstartanleitung [Konfigurieren von Regeln und Aktionen für Ihr Gerät in Azure IoT Central](quick-configure-rules.md) erhalten haben. Wählen Sie in der E-Mail den Link zum Gerät aus:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Screenshot: Benachrichtigungs-E-Mail":::

Die Ansicht **Übersicht** für das simulierte Gerät, das Sie in den vorherigen Schnellstartanleitungen erstellt haben, wird in Ihrem Browser geöffnet:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Screenshot: Übersicht über das Gerät, durch das die Benachrichtigung ausgelöst wurde":::

## <a name="investigate-an-issue"></a>Untersuchen eines Problems

Als Bediener können Sie in den Ansichten **Übersicht**, **Info** und **Befehle** Informationen zum Gerät anzeigen. Der Ersteller hat die Ansicht **Gerät verwalten** erstellt, damit Sie Geräteinformationen bearbeiten und Geräteeigenschaften festlegen können.

Das Diagramm auf dem Dashboard zeigt einen Plot der Luftfeuchtigkeit des Geräts. Sie entscheiden, dass die Luftfeuchtigkeit für das Gerät zu hoch ist.

## <a name="remediate-an-issue"></a>Beheben eines Problems

Eine Änderung am Gerät können Sie über die Seite **Gerät verwalten** vornehmen.

Ändern Sie **Zieltemperatur** in „80“, um das Gerät zu erwärmen und die Luftfeuchtigkeit zu verringern. Wählen Sie **Speichern** aus, um das Gerät zu aktualisieren. Wenn das Gerät die Einstellungsänderung bestätigt, ändert sich der Status der Eigenschaft in **Synchronisiert**:

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Screenshot: Aktualisierte Zieltemperatureinstellung für das Gerät":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

* Empfangen einer Benachrichtigung
* Untersuchen eines Problems
* Beheben eines Problems

Nachdem Sie nun wissen, wie Sie Ihr Gerät überwachen, wird als Nächstes der folgende Schritt empfohlen:

> [!div class="nextstepaction"]
> [Einrichten und Verwalten einer Gerätevorlage (Previewfunktionen)](howto-set-up-template.md)
