---
title: Problembehandlung – Azure IoT Hub-Fehler „429001 ThrottlingException“
description: Grundlegendes zum Beheben des Fehlers „429001 ThrottlingException“
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d2f12a6982886eeaa375151c5b8a73acc573aab9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545360"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

In diesem Artikel werden die Ursachen des Fehlers **429001 ThrottlingException** und die Lösungen dafür beschrieben.

## <a name="symptoms"></a>Symptome

Ihre Anforderungen an IoT Hub schlagen mit der Meldung **429001 ThrottlingException** fehl.

## <a name="cause"></a>Ursache

IoT Hub-[Drosselungslimits](./iot-hub-devguide-quotas-throttling.md), die bei dem angeforderten Vorgang überschritten wurden.

## <a name="solution"></a>Lösung

Überprüfen Sie, ob Sie das Drosselungslimit erreichen, indem Sie Ihre Metrik *Versuche zum Senden von Telemetrienachrichten* mit den oben angegebenen Limits vergleichen. Sie können auch die Metrik *Anzahl von Drosselungsfehlern* überprüfen. Weitere Informationen zu diesen Metriken finden Sie unter [Gerätetelemetriemetriken](monitor-iot-hub-reference.md#device-telemetry-metrics). Informationen dazu, wie Sie mithilfe von Metriken Ihren IoT-Hub überwachen können, finden Sie unter [Überwachen von IoT-Hub](monitor-iot-hub.md).

IoT Hub gibt „429 ThrottlingException“ nur zurück, nachdem das Limit während eines zu langen Zeitraums überschritten wurde. Dies geschieht, damit Ihre Nachrichten nicht gelöscht werden, wenn Ihr IoT Hub Burstdatenverkehr abruft. In der Zwischenzeit verarbeitet IoT Hub die Nachrichten mit der Drosselungsrate, die langsam sein kann, wenn zu viel Datenverkehr im Backlog vorhanden ist. Weitere Informationen finden Sie unter [Traffic-Shaping](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Nächste Schritte

Erwägen Sie, [Ihre IoT Hub-Instanz zentral hochzuskalieren](./iot-hub-scaling.md), wenn Sie die Kontingent- oder Drosselungslimits erreichen.