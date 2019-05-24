---
title: Includedatei
description: Includedatei
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147725"
---
### <a name="code-walkthrough"></a>Exemplarische Vorgehensweise mit Code

In diesem Abschnitt werden Hauptbestandteile des Beispielcodes beschrieben, und es wird erläutert, wie sie mit dem Solution Accelerator für die Remoteüberwachung im Zusammenhang stehen.

Der folgende Codeausschnitt zeigt, wie die gemeldeten Eigenschaften definiert werden, die die Funktionen des Geräts beschreiben. Zu diesen Eigenschaften zählen folgende:

- Der Standort des Geräts, damit es vom Solution Accelerator zur Karte hinzugefügt werden kann
- Die aktuelle Firmwareversion
- Die Liste der vom Gerät unterstützten Methoden
- Das Schema der vom Gerät gesendeten Telemetrienachrichten

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Das Beispiel enthält eine **serializeToJson**-Funktion, die diese Datenstruktur mithilfe der Parson-Bibliothek serialisiert.

Das Beispiel enthält mehrere Rückruffunktionen, die Informationen an die Konsole ausgibt, wenn der Client mit dem Solution Accelerator interagiert:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Der folgende Codeausschnitt zeigt die Funktion **device_method_callback**. Diese Funktion bestimmt die Aktion, die ausgeführt werden muss, wenn ein Methodenaufruf vom Solution Accelerator empfangen wird. Die Funktion empfängt im **userContextCallback**-Parameter einen Verweis auf die Datenstruktur der **Kältemaschine** (Chiller). Der Wert von **userContextCallback** wird festgelegt, wenn die Rückruffunktion in der **main**-Funktion konfiguriert wird:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Wenn der Solution Accelerator die Firmwareupdatemethode aufruft, deserialisiert das Beispiel die JSON-Nutzlast und startet einen Hintergrundthread zum Abschließen des Updateprozesses. Der folgende Codeausschnitt zeigt die Funktion **do_firmware_update**, die für den Thread ausgeführt wird:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Der folgende Codeausschnitt zeigt, wie der Client eine Telemetrienachricht an den Solution Accelerator sendet. Die Nachrichteneigenschaften enthalten das Nachrichtenschema, damit der Solution Accelerator die Telemetrie auf dem Dashboard anzeigen kann:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

Die **main**-Funktion im Beispiel führt die folgenden Aktionen aus:

- Initialisieren und Herunterfahren des SDK-Subsystems
- Initialisieren der Datenstruktur der **Kältemaschine** (Chiller)
- Senden der gemeldeten Eigenschaften an den Solution Accelerator
- Konfigurieren der Funktion für den Gerätemethodenrückruf
- Senden von simulierten Telemetriewerten an den Solution Accelerator

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
