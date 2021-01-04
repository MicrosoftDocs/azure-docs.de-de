---
title: Anzeigen und Konfigurieren der Metriken von Azure IoT-Konnektor für FHIR (Vorschau)
description: In diesem Artikel wird erläutert, wie Sie Azure IoT-Konnektor für FHIR-Metriken (Vorschau) anzeigen und konfigurieren.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 79a7b18986f4bed503cd5493a7d74325a13fe535
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630514"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Anzeigen und Konfigurieren der Metriken von Azure IoT-Konnektor für FHIR (Vorschau) 

In diesem Artikel erfahren Sie, wie Sie Azure IoT-Konnektor für FHIR&#174;*-Metriken (Fast Healthcare Interoperability Resources) anzeigen und konfigurieren.

> [!TIP]
> Informationen zum Einrichten des Exports von Metrikdaten finden Sie unter [Exportieren der Metriken von Azure IoT-Konnektor für FHIR (Vorschau) über die Diagnoseeinstellungen](./iot-metrics-diagnostics-export.md).

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Anzeigen von Metriken für Azure IoT-Konnektor für FHIR (Vorschau)

1. Melden Sie sich beim Azure-Portal an, und wählen Sie Ihren Azure API for FHIR-Dienst aus. 

2. Wählen Sie im linken Bereich **Metriken** aus. 

3. Wählen Sie die Registerkarte **IoT-Konnektor** aus.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Screenshot des Bereichs „IoT-Konnektor“ mit Liniendiagrammen, die die Anzahl eingehender und normalisierter Nachrichten anzeigen" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Wählen Sie einen IoT-Konnektor aus, um die zugehörigen Metriken anzuzeigen. Diesem Azure API for FHIR-Dienst sind beispielsweise vier IoT-Konnektor-Instanzen (*connector 1*, *connector 2* usw.) zugeordnet.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Screenshot des Bereichs „IoT-Konnektor“ mit den IoT-Konnektor-Registerkarten 1, 2, 3 und 4" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. Wählen Sie den Zeitraum (z. B. **1 Stunde**, **24 Stunden**, **7 Tage** oder **Benutzerdefiniert**) der IoT-Konnektor-Metriken an, die Sie anzeigen möchten. Auf der Registerkarte **Benutzerdefiniert** können Sie spezifische Uhrzeit-/Datumskombinationen zum Anzeigen von IoT-Konnektor-Metriken erstellen.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Screenshot des Bereichs „IoT-Konnektor“, in dem für „connector 1“ ein Liniendiagramm mit einem Zeitraum von 1 Stunde angezeigt wird" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Metriktypen für Azure IoT-Konnektor für FHIR (Vorschau) 

> [!TIP]
> Weitere Informationen zum Datenfluss im Azure IoT-Konnektor für FHIR finden Sie unter [Datenfluss in Azure IoT-Konnektor für FHIR (Vorschau)](./iot-data-flow.md) und [Azure IoT-Konnektor für FHIR (Vorschauversion): Leitfaden zur Problembehandlung](./iot-troubleshoot-guide.md), um weitere Informationen zu Fehlermeldungen und Fixes zu erhalten.

In der folgenden Tabelle sind die IoT-Konnektor-Metriken aufgeführt, die Sie anzeigen können:

|Metriktyp|Zweck der Metrik| 
|-----------|--------------|
|Anzahl eingehender Nachrichten|Zeigt die Anzahl empfangener unformatierter Nachrichten an (z. B. die Geräteereignisse).|
|Anzahl normalisierter Nachrichten|Zeigt die Anzahl normalisierter Nachrichten an.|
|Anzahl von Nachrichtengruppen|Zeigt die Anzahl von Gruppen an, in denen innerhalb des angegebenen Zeitfensters Nachrichten aggregiert wurden.|
|Durchschnittliche Wartezeit der Normalisierungsphase|Zeigt die durchschnittliche Wartezeit der Normalisierungsphase an. In der Normalisierungsphase werden eingehende unformatierte Nachrichten normalisiert.|
|Durchschnittliche Wartezeit der Gruppierungsphase|Zeigt die durchschnittliche Wartezeit der Gruppierungsphase an. In der Gruppierungsphase werden Puffer-, Aggregations- und Gruppierungsvorgänge für normalisierte Nachrichten durchgeführt.| 
|Gesamtfehlerzahl|Zeigt die Gesamtanzahl von Fehlern an.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Fokussieren auf und Konfigurieren der Metriken von Azure IoT-Konnektor für FHIR (Vorschau)

In diesem Beispiel geht es um die Metrik für die **Anzahl eingehender Nachrichten**.

1. Wählen Sie einen Zeitpunkt aus, auf den Sie sich konzentrieren möchten.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Screenshot des Metrikbereichs „Anzahl eingehender Nachrichten“, in dem ein einzelner Zeitpunkt im Liniendiagramm hervorgehoben ist" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. Im Bereich **Anzahl eingehender Nachrichten** können Sie die Metrik mithilfe der Optionen **Metrik hinzufügen**, **Filter hinzufügen** oder **Teilung anwenden**. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Screenshot des Metrikbereichs „Anzahl eingehender Nachrichten“ mit hervorgehobenen Schaltflächen „Metrik hinzufügen“, „Filter hinzufügen“ und „Teilung anwenden“" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Zusammenfassung 
Der Zugriff auf Metriken der Datenebene ist für die Überwachung und Problembehandlung von entscheidender Bedeutung. Azure IoT-Konnektor für FHIR unterstützt Sie mit Metriken bei diesen Aufgaben. 

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Antworten auf häufig gestellte Fragen zu Azure IoT-Konnektor für FHIR.

>[!div class="nextstepaction"]
>[Azure IoT-Konnektor für FHIR: häufig gestellte Fragen](fhir-faq.md)

*Im Azure-Portal wird Azure IoT-Konnektor für FHIR als „IoT-Konnektor (Vorschau)“ bezeichnet. FHIR ist eine eingetragene Marke von HL7 und wird mit Genehmigung von HL7 verwendet. 
