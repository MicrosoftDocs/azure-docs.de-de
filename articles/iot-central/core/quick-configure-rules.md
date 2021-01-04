---
title: 'Schnellstart: Konfigurieren von Regeln und Aktionen in Azure IoT Central'
description: In dieser Schnellstartanleitung wird gezeigt, wie Sie als Ersteller telemetriebasierte Regeln und Aktionen in Ihrer Azure IoT Central-Anwendung konfigurieren.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 99846a5f2435398d13c436460a2756b1b021a1be
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990202"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Schnellstart: Konfigurieren von Regeln und Aktionen für Ihr Gerät in Azure IoT Central

*Dieser Artikel gilt für Betreiber, Ersteller und Administratoren.*

In dieser Schnellstartanleitung erstellen Sie eine Regel, durch die eine E-Mail gesendet wird, wenn die von einem Gerätesensor gemeldete Luftfeuchtigkeit 55 Prozent übersteigt.

## <a name="prerequisites"></a>Voraussetzungen

Erstellen Sie zunächst mithilfe der beiden vorherigen Schnellstartanleitungen [Erstellen einer Azure IoT Central-Anwendung](./quick-deploy-iot-central.md) und [Hinzufügen eines simulierten Geräts zu Ihrer IoT Central-Anwendung](./quick-create-simulated-device.md) die Gerätevorlage **Sensor Controller** (Sensorcontroller), um sie hier verwenden zu können.

## <a name="create-a-telemetry-based-rule"></a>Erstellen einer telemetriebasierten Regel

1. Wählen Sie im linken Bereich die Option **Regeln** aus, um Ihrer Anwendung eine neue telemetriebasierte Regel hinzuzufügen.

1. Wählen Sie **+ Neu** aus, um eine neue Regel zu erstellen.

1. Geben Sie als Regelname **Environmental humidity** (Luftfeuchtigkeit der Umgebung) ein.

1. Wählen Sie im Abschnitt **Zielgeräte** die Gerätevorlage **Sensor Controller** aus. Diese Option filtert die Geräte, auf die die Regel angewendet wird, nach Gerätevorlagentyp. Sie können weitere Filterkriterien hinzufügen, indem Sie **+ Filter** auswählen.

1. Im Abschnitt **Bedingungen** definieren Sie, wodurch Ihre Regel ausgelöst wird. Verwenden Sie die folgenden Informationen, um eine Bedingung basierend auf Temperaturtelemetrie zu definieren:

    | Feld        | Wert            |
    | ------------ | ---------------- |
    | Messung  | SensorHumid      |
    | Operator     | größer als  |
    | Wert        | 55               |

    Wählen Sie **+ Bedingung** aus, um weitere Bedingungen hinzuzufügen.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Screenshot: Regelbedingung":::

1. Wählen Sie **+ E-Mail** aus, um eine E-Mail-Aktion hinzuzufügen, die bei Auslösung der Regel ausgeführt werden soll.

1. Nutzen Sie beim Festlegen der Aktion die Informationen in der folgenden Tabelle, und wählen Sie anschließend **Fertig** aus:

    | Einstellung   | Wert                                             |
    | --------- | ------------------------------------------------- |
    | `Display name` | Operator-E-Mail-Aktion                          |
    | To        | Ihre E-Mail-Adresse                                |
    | Notizen     | Die Luftfeuchtigkeit der Umgebung hat den Schwellenwert überschritten. |

    > [!NOTE]
    > Um eine E-Mail-Benachrichtigung zu erhalten, muss es sich bei der E-Mail-Adresse um eine [Benutzer-ID in der Anwendung](howto-administer.md) handeln, und dieser Benutzer muss sich mindestens einmal bei der Anwendung angemeldet haben.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Screenshot: Regel mit hinzugefügter E-Mail-Aktion":::

1. Wählen Sie **Speichern** aus. Ihre Regel ist auf der Seite **Regeln** aufgeführt.

## <a name="test-the-rule"></a>Testen der Regel

Die Regel wird kurz nach dem Speichern aktiv. Wenn die in der Regel definierten Bedingungen erfüllt sind, wird von Ihrer Anwendung eine E-Mail an die in der Aktion angegebene Adresse gesendet.

> [!NOTE]
> Deaktivieren Sie die Regel nach Abschluss der Tests, um keine weiteren Warnungen in Ihrem Posteingang zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

* Erstellen einer telemetriebasierten Regel
* Hinzufügen einer Aktion

Weitere Informationen zur Überwachung von Geräten, die mit Ihrer Anwendung verbunden sind, finden Sie in der folgenden Schnellstartanleitung:

> [!div class="nextstepaction"]
> [Überwachen Ihrer Geräte mithilfe von Azure IoT Central](quick-monitor-devices.md)
