---
title: Unterschiede zur vorherigen Version
titleSuffix: Azure Digital Twins
description: Informationen zu den Änderungen in der neuen Version von Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: af8d768ceeaacdf1c8a0f6cdc8be0041e4ae4ed1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099078"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-previous-version-2018"></a>Worum handelt es sich bei der neuen Version von Azure Digital Twins? Wie unterscheidet sie sich von der vorherigen Version (2018)?

Die erste öffentliche Vorschau von Azure Digital Twins wurde im Oktober 2018 veröffentlicht. Wenngleich die wesentlichen Konzepte der ersten Version im neuen Dienst unverändert sind, wurden eine Vielzahl von Schnittstellen und Implementierungsdetails geändert, um den Dienst flexibler und zugänglicher zu machen. Grundlage für diese Änderungen war Feedback, das wir von Kunden erhalten haben.

> [!IMPORTANT]
> Aufgrund der erweiterten Funktionen des neuen Diensts wird der vorherige Azure Digital Twins-Dienst ab Ende 2020 nicht mehr verfügbar sein.

Wenn Sie die erste Version von Azure Digital Twins während der öffentlichen Vorschau verwendet haben, finden Sie in diesem Artikel wichtige Informationen und bewährte Methoden, um sich mit dem neuen Dienst vertraut zu machen und von den neuen Features zu profitieren.

## <a name="differences-by-topic"></a>Unterschiede nach Thema

In der nachfolgenden Übersicht finden Sie einen direkten Vergleich der Konzepte, die sich gegenüber der vorherigen Version in der neuen (aktuellen) Version des Diensts geändert haben.

| Thema | Vorherige Version | Neue Version |
| --- | --- | --- | --- |
| **Modellierung**<br>*Flexibler* | Grundlage der vorherigen Version waren Smart Spaces, sodass diese Version über ein integriertes Vokabular für Gebäude verfügt. | Die neue Azure Digital Twins-Version ist nicht an einen bestimmten Bereich gebunden. Sie können benutzerdefiniertes Vokabular und benutzerdefinierte Modelle für Ihre Lösung definieren, um eine größere Palette an Umgebungen darzustellen.<br><br>Weitere Informationen finden Sie unter [*Konzepte: Grundlegendes zu Zwillingsmodellen in Azure Digital Twins*](concepts-models.md). |
| **Topologie**<br>*Flexibler*| In der vorherigen Version wurde eine Baumdatenstruktur unterstützt, die auf Smart Spaces abgestimmt ist. Digitale Zwillinge wurden mit hierarchischen Beziehungen verbunden. | In der neuen Version können digitale Zwillinge in beliebigen Topologien verbunden und gemäß Ihren individuellen Anforderungen organisiert werden. Dadurch können Sie die komplexen Beziehungen der realen Umgebung flexibler darstellen.<br><br>Weitere Informationen finden Sie unter [*Konzepte: Grundlegendes zu digitalen Zwillingen und zum zugehörigen Zwillingsgraphen*](concepts-twins-graph.md). |
| **Compute**<br>*Umfangreicher, flexibler* | In der vorherigen Version wurde Logik für die Verarbeitung von Ereignissen und Telemetriedaten in benutzerdefinierten JavaScript-Funktionen (User-Defined Functions, UDFs) definiert. Die Debuggingmöglichkeiten waren bei UDFs eingeschränkt. | Die neue Version verfügt über ein offenes Computemodell: Sie geben benutzerdefinierte Logik an, indem Sie externe Computeressourcen wie [Azure Functions](../azure-functions/functions-overview.md) anfügen. Dadurch können Sie Ihre bevorzugte Programmiersprache auswählen, uneingeschränkt auf benutzerdefinierte Codebibliotheken zugreifen sowie Entwicklungs- und Debuggingressourcen des externen Diensts nutzen.<br><br>Weitere Informationen finden Sie unter [*Anleitungen: von Azure Functions-Apps für die Verarbeitung von Daten*](how-to-create-azure-function.md). |
| **Geräteverwaltung mit IoT Hub**<br>*Zugänglicher* | In der vorherigen Version wurden Geräte mit einer internen [IoT Hub](../iot-hub/about-iot-hub.md)-Instanz des Azure Digital Twins-Diensts verwaltet. Dieser integrierte Hub war für Entwickler nicht voll zugänglich. | In der neuen Version wird ein eigener IoT-Hub verwendet, indem eine unabhängig erstellte IoT Hub-Instanz angefügt wird (mitsamt aller Geräte, die diese Instanz bereits verwaltet). Dadurch erhalten Sie vollen Zugriff auf die Funktionen von IoT Hub, und Sie profitieren von einer vollständigen Kontrolle über die Geräteverwaltung.<br><br>Weitere Informationen finden Sie unter [*Anleitungen: Erfassen von IoT Hub Telemetriedaten in Azure Digital Twins*](how-to-ingest-iot-hub-data.md). |
| **Security**<br>*Standardisierter* | In der vorherigen Version waren Rollen vordefiniert, mit denen Sie den Zugriff auf Ihre Instanz verwalten konnten. | Die neue Version ermöglicht eine Integration in denselben Azure-Back-End-Dienst für die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../role-based-access-control/overview.md), die auch von anderen Azure-Diensten verwendet wird. Dadurch kann die Authentifizierung zwischen anderen Azure-Diensten in Ihrer Lösung (IoT Hub, Azure Functions, Event Grid usw.) vereinfacht werden.<br>Mit der rollenbasierten Zugriffssteuerung können Sie entweder vordefinierte Rollen verwenden oder benutzerdefinierte Rollen erstellen und konfigurieren.<br><br>Weitere Informationen finden Sie unter [*Konzepte: Schützen von Azure Digital Twins mit rollenbasierter Zugriffssteuerung*](concepts-security.md). |
| **Skalierbarkeit**<br>*Höher* | In der vorherigen Version waren die Skalierungsmöglichkeiten bei Geräten, Nachrichten, Diagrammen und Skalierungseinheiten eingeschränkt. Pro Abonnement wurde nur eine Instanz von Azure Digital Twins unterstützt.  | Die neue Version basiert auf einer neuen Architektur mit verbesserter Skalierbarkeit und mehr Computeleistung. Außerdem werden zehn Instanzen pro Region und Abonnement unterstützt.<br><br>Unter [*Referenz: Diensteinschränkungen*](reference-service-limits.md) finden Sie Einzelheiten zu den Einschränkungen in der aktuellen Version. |

## <a name="service-limits"></a>Diensteinschränkungen

Eine Liste mit den Einschränkungen für Azure Digital Twins finden Sie unter [*Referenz: Diensteinschränkungen*](reference-service-limits.md).

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie als Nächstes das erste Tutorial zu Azure Digital Twins durch, um sich näher mit der Funktionsweise vertraut zu machen:

[*Tutorial: Codieren einer Client-App*](tutorial-code.md)