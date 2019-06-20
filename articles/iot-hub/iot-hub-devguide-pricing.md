---
title: Grundlegendes zu den Preisen von Azure IoT Hub | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Informationen zur Funktionsweise von Messungen und zur Preisgestaltung für IoT Hub (einschließlich Praxisbeispielen).'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 9b6db1b7171652ea5ace4db370b72dc22b6bdc90
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626229"
---
# <a name="azure-iot-hub-pricing-information"></a>Azure IoT Hub – Preisinformationen

[Azure IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub) enthält die allgemeinen Informationen zu den verschiedenen SKUs und die Preise für IoT Hub. Dieser Artikel enthält weitere Informationen dazu, wie die verschiedenen IoT Hub-Funktionen als Nachrichten von IoT Hub getaktet werden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Gebühren pro Vorgang

| Vorgang | Abrechnungsinformationen | 
| --------- | ------------------- |
| Identitätsregistrierungsvorgänge <br/> (Erstellen, Abrufen, Auflisten, Aktualisieren, Löschen) | Keine Gebühren. |
| D2C-Nachrichten | Erfolgreich gesendete Nachrichten werden beim Eingang in IoT Hub in Blöcken von 4 KB berechnet. Eine Nachricht mit 6 KB beispielsweise wird als zwei Nachrichten berechnet. |
| C2D-Nachrichten | Erfolgreich gesendete Nachrichten werden in Blöcken von 4 KB berechnet, sodass eine Nachricht mit 6 KB beispielsweise wie zwei Nachrichten berechnet wird. |
| Dateiuploads | Die Dateiübertragung an Azure Storage wird von IoT Hub nicht getaktet. Nachrichten für die Initiierung und den Abschluss der Dateiübertragung werden als Nachrichten mit 4-KB-Taktung berechnet. Für die Übertragung einer Datei mit 10MB werden z.B. zusätzlich zu den Azure Storage-Kosten zwei Nachrichten berechnet. |
| Direkte Methoden | Erfolgreiche Methodenanforderungen werden in Blöcken von 4KB berechnet, und Antworten werden als zusätzliche Nachrichten in Blöcken von 4KB berechnet. Anforderungen an nicht verbundene Geräte werden als Nachrichten in Blöcken von 4 KB berechnet. Eine Methode mit einem Text von 4KB, die zu einer Antwort ohne Text vom Gerät führt, wird z.B. als zwei Nachrichten berechnet. Eine Methode mit einem Text von 6 KB, die zu einer Antwort mit 1 KB vom Gerät führt, wird als zwei Nachrichten für die Anforderung plus eine Nachricht für die Antwort berechnet. |
| Zwillingslesevorgänge vom Gerät und Modul | Zwillingslesevorgänge vom Gerät und Modul und vom Lösungs-Back-End werden als Nachrichten in Blöcken mit jeweils 512 Bytes berechnet. Ein Lesevorgang von einem 6-KB-Zwilling wird beispielsweise als zwölf Nachrichten berechnet. |
| Geräte- und Modulzwillingsupdates (Tags und Eigenschaften) | Zwillingsupdates vom Gerät oder Modul und vom Lösungs-Back-End werden als Nachrichten in Blöcken mit jeweils 512 Bytes berechnet. Ein Lesevorgang von einem 6-KB-Zwilling wird beispielsweise als zwölf Nachrichten berechnet. |
| Abfragen von Geräte- und Modulzwillingen | Abfragen werden als Nachrichten je nach Ergebnisgröße in Blöcken von 512 Byte berechnet. |
| Auftragsvorgänge <br/> (Erstellen, Aktualisieren, Auflisten, Löschen) | Keine Gebühren. |
| Vorgänge vom Typ „Aufträge pro Gerät“ | Auftragsvorgänge (z.B. Zwillingsupdates und Methoden) werden normal berechnet. Ein Auftrag, der zu 1000 Methodenaufrufen mit 1-KB-Anforderungen und Antworten mit leerem Textbereich führt, wird als 1000 Nachrichten berechnet. |
| Keep-Alive-Nachrichten | Wenn Sie AMQP- oder MQTT-Protokolle verwenden, werden Nachrichten zum Herstellen der Verbindung und bei der Aushandlung ausgetauschte Nachrichten nicht in Rechnung gestellt. |

> [!NOTE]
> Bei allen Berechnungen der Größen wird die Nutzlastgröße in Byte berücksichtigt (Protokollframing wird ignoriert). Bei Nachrichten, die über Eigenschaften und Text verfügen, wird die Größe unabhängig vom Protokoll berechnet. Weitere Informationen finden Sie unter [IoT Hub-Nachrichtenformat](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Beispiel 1:

Ein Gerät sendet eine 1-KB-D2C-Nachricht pro Minute an IoT Hub, die dann von Azure Stream Analytics gelesen wird. Das Lösungs-Back-End ruft auf dem Gerät alle zehn Minuten eine Methode (mit einer Nutzlast von 512Bytes) auf, um eine bestimmte Aktion auszulösen. Das Gerät antwortet auf die Methode mit einem Ergebnis von 200 Byte.

Das Gerät verbraucht Folgendes:

* 1 Nachricht × 60 Minuten × 24 Stunden = 1440 Nachrichten pro Tag für die D2C-Nachrichten.
* Zwei Anforderungen plus Antwort × 6 Vorkommen pro Stunde × 24 Stunden = 288 Nachrichten für die Methoden.

Diese Berechnung ergibt eine Gesamtsumme von 1728 Nachrichten pro Tag.

## <a name="example-2"></a>Beispiel 2:

Ein Gerät sendet jede Stunde eine D2C-Nachricht mit 100 KB. Außerdem aktualisiert es alle vier Stunden seinen Gerätezwilling mit 1-KB-Nutzlasten. Das Lösungs-Back-End liest einmal pro Tag den 14-KB-Gerätezwilling und aktualisiert ihn mit 512-Byte-Nutzlasten, um Konfigurationen zu ändern.

Das Gerät verbraucht Folgendes:

* 25 (100 KB/4 KB) Nachrichten × 24 Stunden für D2C-Nachrichten.
* 2 Nachrichten (1 KB/0,5 KB) × 6 Vorkommen pro Tag für Updates der Gerätezwillinge.

Diese Berechnung ergibt eine Gesamtsumme von 612 Nachrichten pro Tag.

Das Lösungs-Back-End verbraucht 28 Nachrichten (14 KB/0,5 KB) für das Lesen vom Gerätezwilling sowie eine Nachricht für die Aktualisierung. Dies ergibt insgesamt 29 Nachrichten.

Zusammen verbrauchen das Gerät und das Lösungs-Back-End also 641 Nachrichten pro Tag.
