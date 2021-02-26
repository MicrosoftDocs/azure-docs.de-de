---
title: Exportieren der Metriken von Azure IoT-Konnektor für FHIR (Vorschau) über die Diagnoseeinstellungen
description: In diesem Artikel wird erläuter, wie Sie Metriken von Azure IoT-Konnektor für FHIR (Vorschau) über die Diagnoseeinstellungen exportieren.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 00abad784048b67e9d89c12b9be3f631f586fb07
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574530"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Exportieren der Metriken von Azure IoT-Konnektor für FHIR (Vorschau) über die Diagnoseeinstellungen

In diesem Artikel erfahren Sie, wie Sie Azure IoT-Konnektor für FHIR&#174;*-Metrikenprotokolle (Fast Healthcare Interoperability Resources) exportieren. Die Metrikprotokollierung wird über die Funktion [**Diagnoseeinstellungen**](../azure-monitor/essentials/diagnostic-settings.md) im Azure-Portal ermöglicht. 

> [!TIP]
> Befolgen Sie die Anweisungen in [Aktivieren der Diagnoseprotokollierung in Azure-API for FHIR und Azure IoT-Connector für FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir), um die Überwachungsprotokollierung einzurichten.

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Aktivieren der Metrikprotokollierung für Azure IoT-Konnektor für FHIR (Vorschau)
1. Um die Metrikprotokollierung für Azure IoT-Konnektor für FHIR zu aktivieren, wählen Sie Ihren Azure API for FHIR-Dienst im Azure-Portal aus. 

2. Navigieren zu den **Diagnoseeinstellungen** 

3. Wählen Sie **+ Diagnoseeinstellung hinzufügen**  aus.

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT-Konnektor1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Geben Sie einen Namen in das Dialogfeld **Name der Diagnoseeinstellung** ein.

5. Wählen Sie die Methode aus, die Sie für den Zugriff auf Ihre Diagnoseprotokolle verwenden möchten:

    1. **Archivieren Sie Protokolle zur (manuellen) Überprüfung in einem Speicherkonto**. Das Speicherkonto, das Sie verwenden möchten, muss bereits erstellt worden sein.
    2. **Streamen Sie die Protokolle zu Event Hub**, damit sie von einem Dienst eines Drittanbieters oder einer benutzerdefinierten Analyselösung erfasst werden können. Sie müssen einen Event Hub-Namespace und eine Event Hub-Richtlinie erstellen, ehe Sie diesen Schritt konfigurieren können.
    3. **Streamen Sie die Protokolle zum Log Analytics-Arbeitsbereich** in Azure Monitor. Sie müssen Ihren Log Analytics-Arbeitsbereich erstellen, bevor Sie diese Option auswählen können.

6. Wählen Sie für Azure IoT-Konnektor für FHIR die Metriken **Fehler, Datenverkehr und Wartezeit** aus.  Wählen Sie weitere Metrikkategorien aus, die für Azure API for FHIR erfasst werden sollen.

7. Wählen Sie **Speichern** aus.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT-Konnektor2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Es kann bis zu 15 Minuten dauern, bis die ersten Metrikprotokolle im Repository Ihrer Wahl angezeigt werden.  
 
Weitere Informationen zum Arbeiten mit Diagnoseprotokollen finden Sie in der [Dokumentation zum Azure-Ressourcenprotokoll](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="conclusion"></a>Zusammenfassung 
Zugriff auf Metrikprotokolle zu haben, ist für die Überwachung und Problembehandlung von entscheidender Bedeutung.  Azure IoT-Konnektor für FHIR ermöglicht es Ihnen, diese Aufgaben mithilfe von Metrikprotokollen durchzuführen. 

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie häufig gestellte Fragen zu Azure IoT-Konnektor für FHIR.

>[!div class="nextstepaction"]
>[Azure IoT-Konnektor für FHIR – Häufig gestellte Fragen](fhir-faq.md)

*Im Azure-Portal wird Azure IoT-Konnektor für FHIR als „IoT-Konnektor (Vorschau)“ bezeichnet. FHIR ist eine eingetragene Marke von HL7 und wird mit Genehmigung von HL7 verwendet.