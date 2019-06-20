---
title: Grundlegendes zur Versionsverwaltung von Gerätevorlagen für Ihre Azure IoT Central-Apps | Microsoft-Dokumentation
description: Durchlaufen Sie Ihre Gerätevorlagen, indem Sie neue Versionen erstellen, ohne Ihre gerade angeschlossenen Geräte zu beeinträchtigen.
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d4f9617a5c2ba6f6cf8dc261845aa98e33d70a55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60649186"
---
# <a name="create-a-new-device-template-version"></a>Erstellen einer neuen Gerätevorlagenversion

Azure IoT Central ermöglicht die schnelle Entwicklung von IoT-Anwendungen. Sie können schnell Ihre Entwürfe für Gerätevorlagen durchlaufen, indem Sie Messungen, Einstellungen oder Eigenschaften hinzufügen, bearbeiten oder löschen. Einige dieser Änderungen können für die aktuell angeschlossenen Geräte störend sein. Azure IoT Central erkennt diese Änderungen und bietet eine Möglichkeit, diese Updates sicher auf den Geräten bereitzustellen.

Eine Gerätevorlage weist bei Ihrer Erstellung eine Versionsnummer auf. Standardmäßig lautet die Versionsnummer 1.0.0. Wenn Sie eine Gerätevorlage bearbeiten und diese Änderung Auswirkungen auf angeschlossene Geräte haben könnte, fordert Azure IoT Central Sie auf, eine neue Gerätevorlagenversion zu erstellen.

> [!NOTE]
> Weitere Informationen zum Erstellen einer Gerätevorlage finden Sie unter [Einrichten einer Gerätevorlage](howto-set-up-template.md).

## <a name="changes-that-prompt-a-version-change"></a>Änderungen mit Aufforderung zu einem Versionswechsel

Im Allgemeinen führen Änderungen an den Einstellungen oder Eigenschaften Ihrer Gerätevorlage zu einem Versionswechsel.

> [!NOTE]
> Beim Ändern einer Gerätevorlage werden Sie jedoch nicht zum Erstellen einer neuen Version aufgefordert, wenn keine Geräte verbunden sind oder höchstens ein Gerät verbunden ist.

Die folgende Liste beschreibt die Benutzeraktionen, die eine neue Version erforderlich machen könnten:

* Eigenschaften (erforderlich)
    * Hinzufügen oder Löschen einer erforderlichen Eigenschaft
    * Ändern des Feldnamens einer Eigenschaft, wobei der Feldname von Ihren Geräten zum Senden von Nachrichten verwendet wird.
*  Eigenschaften (optional)
    * Löschen einer optionalen Eigenschaft
    * Ändern des Feldnamens einer Eigenschaft, wobei der Feldname von Ihren Geräten zum Senden von Nachrichten verwendet wird.
    * Ändern einer optionalen Eigenschaft in eine erforderliche Eigenschaft
*  Einstellungen
    * Hinzufügen oder Löschen einer Einstellung
    * Ändern des Feldnamens einer Einstellung, wobei der Feldname von Ihren Geräten zum Senden und Empfangen von Nachrichten verwendet wird.

## <a name="what-happens-on-version-change"></a>Was geschieht bei einer Versionsänderung?

Was passiert mit Regeln und Gerätedashboards bei einem Versionswechsel?

**Regeln** können Bedingungen enthalten, die von Eigenschaften abhängig sind. Wenn Sie eine oder mehrere dieser Eigenschaften entfernt haben, können diese Regeln in Ihrer neuen Gerätevorlagenversion verletzt werden. Sie können zu diesen speziellen Regeln wechseln und die Bedingungen aktualisieren, um die Regeln festzulegen. Die Regeln für Ihre Vorgängerversion sollten ohne Auswirkungen funktionieren.

**Gerätedashboards** können mehrere Typen von Kacheln enthalten. In einigen der Kacheln können Einstellungen und Eigenschaften vorhanden sein. Wird eine in einer Kachel verwendete Eigenschaft oder Einstellung entfernt, ist die Kachel vollständig oder teilweise fehlerhaft. Sie können zur Kachel wechseln und das Problem beheben, indem Sie die Kachel entfernen oder den Inhalt der Kachel aktualisieren.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrieren eines Geräts zwischen Gerätevorlageversionen

Es lassen sich mehrere Versionen der Gerätevorlage erstellen. Im Laufe der Zeit werden Sie über mehrere angeschlossene Geräte verfügen, die diese Gerätevorlagen verwenden. Sie können Geräte von einer Version Ihrer Gerätevorlage zu einer anderen migrieren. Die folgenden Schritte beschreiben, wie Sie ein Gerät migrieren:

1. Wechseln Sie zur **Device Explorer**-Seite.
1. Wählen Sie das Gerät aus, das Sie zu einer anderen Version migrieren möchten.
1. Wählen Sie **Gerät migrieren**.
1. Wählen Sie die Versionsnummer aus, zu der Sie das Gerät migrieren möchten, und wählen Sie **Migrieren**.

![Migrieren eines Geräts](media/howto-version-devicetemplate/pick-version.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Gerätevorlageversionen in Ihrer Azure IoT Central-Anwendung verwendet werden, wird als Nächstes der folgende Schritt empfohlen:

> [!div class="nextstepaction"]
> [Erstellen von Telemetrieregeln](howto-create-telemetry-rules.md)