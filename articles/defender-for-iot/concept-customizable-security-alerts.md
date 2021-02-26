---
title: Benutzerdefinierte Sicherheitswarnungen
description: Erfahren Sie etwas über anpassbare Sicherheitswarnungen und empfohlene Wartungsvorgänge unter Verwendung der Features und Dienste von Defender für IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 021b05176da141fad2f4555b0617b5a4a51e453f
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809285"
---
# <a name="defender-for-iot-custom-security-alerts"></a>Defender für IoT: benutzerdefinierte Sicherheitswarnungen

Defender für IoT analysiert Ihre IoT-Lösung ständig mithilfe von Advanced Analytics- und Threat Intelligence-Funktionen, um Sie vor schädlichen Aktivitäten zu warnen.

Es wird empfohlen, benutzerdefinierte Warnungen im Einklang mit Ihren Kenntnissen zum erwarteten Geräteverhalten zu erstellen, um sicherzustellen, dass Warnungen die effizientesten Indikatoren für potenzielle Gefährdung in Ihrer eigenen Organisationsbereitstellung und -landschaft bieten.

Die Defender für IoT-Warnungen in den folgenden Listen können Sie basierend auf dem erwarteten IoT Hub- und Geräteverhalten definieren. Weitere Informationen zum Anpassen der einzelnen Warnungen finden Sie unter [Erstellen von benutzerdefinierten Warnungen](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Integrierte benutzerdefinierte Warnungen in IoT Hub

| severity | Warnungsname | Datenquelle | BESCHREIBUNG | Vorschlag zur Problemlösung |
|--|--|--|--|--|
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl von Cloud-zu-Gerät-Nachrichten im AMQP-Protokoll liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Anzahl von Cloud-zu-Gerät-Nachrichten (AMQP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |  |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl abgelehnter Cloud-zu-Gerät-Nachrichten im AMQP-Protokoll liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Anzahl der vom Gerät abgelehnten Cloud-zu-Gerät-Nachrichten (AMQP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |  |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl von Gerät-zu-Cloud-Nachrichten im AMQP-Protokoll liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Menge der Gerät-zu-Cloud-Nachrichten (AMQP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |  |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl direkter Methodenaufrufe liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Menge der direkten Methodenaufrufen innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |  |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl von Dateiuploads liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Menge von Dateiuploads innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |  |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl von Cloud-zu-Gerät-Nachrichten im HTTP-Protokoll liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Menge der Cloud-zu-Gerät-Nachrichten (HTTP-Protokoll) in einem Zeitfenster liegt nicht innerhalb des konfigurierten zulässigen Bereichs. |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl abgelehnter Cloud-zu-Gerät-Nachrichten im HTTP-Protokoll liegt nicht innerhalb des zulässigen Bereichs. | IoT Hub | Die Menge der Cloud-zu-Gerät-Nachrichten (HTTP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl von Gerät-zu-Cloud-Nachrichten im HTTP-Protokoll liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Anzahl der Gerät-zu-Cloud-Nachrichten (HTTP-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |  |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl von Cloud-zu-Gerät-Nachrichten im MQTT-Protokoll liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Menge der Cloud-zu-Gerät-Nachrichten (MQTT-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |  |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl abgelehnter Cloud-zu-Gerät-Nachrichten im MQTT-Protokoll liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Anzahl der vom Gerät abgelehnten Cloud-zu-Gerät-Nachrichten (MQTT-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl von Gerät-zu-Cloud-Nachrichten im MQTT-Protokoll liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Menge der Gerät-zu-Cloud-Nachrichten (MQTT-Protokoll) innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl von Befehlswarteschlangenbereinigungen liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Menge der Befehlswarteschlangenbereinigungen innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |  |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl von Modulzwillingsupdates liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Menge der Modulzwillingsupdates innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl nicht autorisierter Vorgänge liegt außerhalb des zulässigen Bereichs. | IoT Hub | Die Menge nicht autorisierter Vorgänge innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |


## <a name="agent-based-security-custom-alerts"></a>Benutzerdefinierte Warnungen bei Agent-basierter Sicherheit

| severity | Warnungsname | Datenquelle | BESCHREIBUNG | Vorschlag zur Problemlösung |
|--|--|--|--|--|
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl aktiver Verbindungen liegt außerhalb des zulässigen Bereichs. | Klassisches Sicherheitsmodul, Azure RTOS | Die Anzahl aktiver Verbindungen innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. | Überprüfen Sie die Geräteprotokolle. Ermitteln Sie den Ursprung der Verbindung, und bestimmen Sie, ob er schädlich ist. Ist er schädlich, entfernen Sie potenzielle Schadsoftware, und versuchen Sie, die Herkunft nachzuvollziehen. Ist er nicht schädlich, fügen Sie die Quelle der Liste mit zulässigen Verbindungen hinzu. |
| Niedrig | Benutzerdefinierte Warnung: Es wurde eine Verbindung mit einer unzulässigen IP-Adresse erstellt. | Klassisches Sicherheitsmodul, Azure RTOS | Es wurde eine Verbindung mit einer IP-Adresse erstellt, die nicht in der Liste zulässiger IP-Adressen enthalten ist. | Überprüfen Sie die Geräteprotokolle. Ermitteln Sie den Ursprung der Verbindung, und bestimmen Sie, ob er schädlich ist. Ist er schädlich, entfernen Sie potenzielle Schadsoftware, und versuchen Sie, die Herkunft nachzuvollziehen. Ist er nicht schädlich, fügen Sie die Quelle der Liste mit zulässigen IP-Adressen hinzu. |
| Niedrig | Benutzerdefinierte Warnung: Die Anzahl nicht erfolgreicher lokaler Anmeldungen liegt außerhalb des zulässigen Bereichs. | Klassisches Sicherheitsmodul, Azure RTOS | Die Anzahl nicht erfolgreicher lokaler Anmeldungen innerhalb eines bestimmten Zeitfensters liegt außerhalb des zurzeit konfigurierten und zulässigen Bereichs. |  |
| Niedrig | Benutzerdefinierte Warnung: Anmeldung eines Benutzers, der nicht in der Liste mit zugelassenen Benutzern enthalten ist | Klassisches Sicherheitsmodul, Azure RTOS | Ein lokaler Benutzer, der nicht in Ihrer Liste mit zugelassenen Benutzern enthalten und beim Gerät angemeldet ist | Navigieren Sie beim Speichern von Rohdaten zu Ihrem Protokollanalysekonto, und verwenden Sie die Daten, um das Gerät zu untersuchen und die Quelle zu ermitteln. Korrigieren Sie anschließend die Positiv-/Sperrliste für diese Einstellungen. Speichern Sie derzeit keine Rohdaten, korrigieren Sie auf dem Gerät die Zulassungs-/Blockierungsliste für diese Einstellungen. |
| Niedrig | Benutzerdefinierte Warnung: Ein unzulässiger Prozesses wurde ausgeführt. | Klassisches Sicherheitsmodul, Azure RTOS | Auf dem Gerät wurde ein unzulässiger Prozess ausgeführt. | Navigieren Sie beim Speichern von Rohdaten zu Ihrem Protokollanalysekonto, und verwenden Sie die Daten, um das Gerät zu untersuchen und die Quelle zu ermitteln. Korrigieren Sie anschließend die Positiv-/Sperrliste für diese Einstellungen. Speichern Sie derzeit keine Rohdaten, korrigieren Sie auf dem Gerät die Zulassungs-/Blockierungsliste für diese Einstellungen. |
|

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Warnungen anpassen](quickstart-create-custom-alerts.md).
- [Übersicht](overview.md) über den Defender für IoT-Dienst
- Erfahren Sie, wie Sie auf Ihre [Sicherheitsdaten zugreifen](how-to-security-data-access.md).
- Erfahren Sie mehr über das [Untersuchen eines Geräts](how-to-investigate-device.md).
