---
title: 'Konzepte: Datenfluss im Feature „Azure IoT-Konnektor für FHIR“ (Vorschau) in Azure API for FHIR'
description: Grundlegendes zum Datenfluss im Azure IoT-Konnektor für FHIR (Vorschau). Der Azure IoT-Konnektor für FHIR (Vorschau) erfasst, normalisiert, gruppiert, transformiert und speichert IoMT-Daten in Azure API for FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 310be095af10f5ed5860c4f627caa0373be55835
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629664"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>Datenfluss in Azure IoT-Konnektor für FHIR (Vorschau)

Dieser Artikel bietet eine Übersicht über den Datenfluss im Azure IoT-Konnektor für Fast Healthcare Interoperability Resources (FHIR&#174;)*. Sie erhalten Informationen zu den verschiedenen Phasen der Datenverarbeitung im Azure IoT-Konnektor für FHIR, in denen Gerätedaten in FHIR-basierte [Observation](https://www.hl7.org/fhir/observation.html)-Ressourcen transformiert werden.

![Datenfluss im Azure IoT-Konnektor für FHIR](media/concepts-iot-data-flow/iot-connector-data-flow.png)

Das Diagramm oben zeigt allgemeine Datenflüsse unter Verwendung des Azure IoT-Konnektors für FHIR. 

Im Folgenden werden die verschiedenen Phasen beschrieben, die die Daten nach dem Empfang im Azure IoT-Konnektor für FHIR durchlaufen.

## <a name="ingest"></a>Erfassen
Die Erfassung ist die erste Phase, in der die Gerätedaten im Azure IoT-Konnektor für FHIR empfangen werden. Der Erfassungsendpunkt für Gerätedaten wird in einer Instanz von [Azure Event Hub](../event-hubs/index.yml) gehostet. Die Azure Event Hub-Plattform unterstützt hohe Skalierbarkeit und hohen Durchsatz mit der Möglichkeit, Millionen von Nachrichten pro Sekunde zu empfangen und zu verarbeiten. Außerdem wird ermöglicht, dass Nachrichten im Azure IoT-Konnektor für FHIR asynchron verarbeitet werden, sodass Geräte nicht mehr warten müssen, während die Gerätedaten verarbeitet werden.

> [!NOTE]
> Derzeit wird JSON als einziges Format für die Gerätedaten unterstützt.

## <a name="normalize"></a>Normalize
Die Normalisierung ist die nächste Phase, in der die Gerätedaten von der Azure Event Hub-Instanz oben abgerufen und mithilfe von Gerätezuordnungsvorlagen verarbeitet werden. Dieser Zuordnungsprozess führt zur Transformation der Gerätedaten in ein normalisiertes Schema. 

Der Normalisierungsprozess vereinfacht nicht nur die Datenverarbeitung in späteren Phasen, sondern bietet auch die Möglichkeit, eine Eingabenachricht in mehrere normalisierte Nachrichten zu projizieren. Ein Gerät kann beispielsweise mehrere Vitalparameter für Körpertemperatur, Pulsfrequenz, Blutdruck und Atemfrequenz in einer einzelnen Nachricht senden. Mit dieser Eingabenachricht werden vier separate FHIR-Ressourcen erstellt. Jede Ressource stellt einen anderen Vitalparameter dar, wobei die Eingabenachricht in vier verschiedene normalisierte Nachrichten projiziert wird.

## <a name="group"></a>Group
Die Gruppierung ist die nächste Phase, in der die verfügbaren normalisierten Nachrichten aus der vorherigen Phase nach drei verschiedenen Parametern gruppiert werden: Geräteidentität, Messungstyp und Zeitraum.

Die Gruppierungen nach Geräteidentität und Messungstyp ermöglichen die Verwendung des Messungstyps [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Dieser Typ bietet eine übersichtliche Möglichkeit zur Darstellung einer zeitbasierten Messreihe von einem Gerät in FHIR. Der Zeitraum steuert die Latenz, mit der die im Azure IoT-Konnektor für FHIR generierten Observation-Ressourcen in Azure API for FHIR geschrieben werden.

> [!NOTE]
> Der Wert für den Zeitraum ist standardmäßig auf 15 Minuten festgelegt und kann in der Vorschauversion nicht konfiguriert werden.

## <a name="transform"></a>Transformieren
In der Transformationsphase werden die gruppierten normalisierten Nachrichten über FHIR-Zuordnungsvorlagen verarbeitet. Nachrichten, die mit einem Vorlagentyp übereinstimmen, werden wie durch die Zuordnung angegeben in FHIR-basierte Observation-Ressourcen transformiert.

An diesem Punkt wird die [Device](https://www.hl7.org/fhir/device.html)-Ressource zusammen mit der zugehörigen [Patient](https://www.hl7.org/fhir/patient.html)-Ressource anhand des in der Nachricht vorhandenen Gerätebezeichners auch vom FHIR-Server abgerufen. Diese Ressourcen werden als Verweis auf die zu erstellende Observation-Ressource hinzugefügt.

> [!NOTE]
> Alle Identitätssuchen werden nach der Auflösung zwischengespeichert, um die Auslastung des FHIR-Servers zu verringern. Wenn Sie Geräte für mehrere Patienten wiederverwenden möchten, empfiehlt es sich, eine für den Patienten spezifische virtuelle Geräteressource zu erstellen und den Bezeichner des virtuellen Geräts in der Nachrichtennutzlast zu senden. Das virtuelle Gerät kann als übergeordnetes Element mit der tatsächlichen Geräteressource verknüpft werden.

Wenn auf dem FHIR-Server keine Geräteressource für einen bestimmten Gerätebezeichner vorhanden ist, hängt das Ergebnis vom Wert für `Resolution Type` ab, der zum Zeitpunkt der Erstellung festgelegt wurde. Wenn die Einstellung auf den Wert `Lookup` festgelegt ist, wird die betreffende Nachricht ignoriert. Die Pipeline verarbeitet weiterhin andere eingehende Nachrichten. Wenn diese Einstellung auf `Create` festgelegt ist, erstellt der Azure IoT-Konnektor für FHIR eine reine Device- und Patient-Ressource auf dem FHIR-Server.  

## <a name="persist"></a>Speichern
Nachdem die FHIR-basierte Observation-Ressource in der Transformationsphase generiert wurde, wird sie in Azure API for FHIR gespeichert. Wenn die FHIR-Ressource neu ist, wird sie auf dem Server erstellt. Wenn die FHIR-Ressource bereits vorhanden war, wird sie aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Klicken Sie unten auf den nächsten Schritt, um zu erfahren, wie Sie Geräte- und FHIR-Zuordnungsvorlagen erstellen.

>[!div class="nextstepaction"]
>[Zuordnungsvorlagen im Azure IoT-Konnektor für FHIR](iot-mapping-templates.md)

*Im Azure-Portal wird Azure IoT-Konnektor für FHIR als „IoT-Konnektor (Vorschau)“ bezeichnet. FHIR ist eine eingetragene Marke von HL7 und wird mit Genehmigung von HL7 verwendet. 