---
title: Unterschiede zur Originalversion
titleSuffix: Azure Digital Twins
description: Informationen zu den Änderungen in der neuen Version von Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072936"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Worum handelt es sich bei der neuen Version von Azure Digital Twins? Inwiefern unterscheidet sie sich von der ursprünglichen Version (2018)?

Die erste öffentliche Vorschau von Azure Digital Twins wurde im Oktober 2018 veröffentlicht. Wenngleich die wesentlichen Konzepte der ursprünglichen Version im aktuellen Dienst unverändert sind, wurden eine Vielzahl von Schnittstellen und Implementierungsdetails geändert, um den Dienst flexibler und zugänglicher zu machen. Grundlage für diese Änderungen war Feedback, das wir von Kunden erhalten haben.

> [!IMPORTANT]
> Im Hinblick auf die erweiterten Funktionen des neuen Diensts wurde der ursprüngliche Azure Digital Twins-Dienst eingestellt. Ab Januar 2021 sind seine APIs und die zugehörigen Daten nicht mehr verfügbar.

Wenn Sie die erste Version von Azure Digital Twins während der Public Preview verwendet haben, finden Sie in diesem Artikel wichtige Informationen und bewährte Methoden, um sich mit dem aktuellen Dienst vertraut zu machen und von den neuen Features zu profitieren.

## <a name="differences-by-topic"></a>Unterschiede nach Thema

In der nachfolgenden Übersicht finden Sie einen direkten Vergleich der Konzepte, die sich gegenüber der ursprünglichen Version in der aktuellen Version des Diensts geändert haben.

| Thema | Originalversion | Aktuelle Version |
| --- | --- | --- | --- |
| **Modellierung**<br>*Flexibler* | Die Grundlage des ursprünglichen Release waren Smart Spaces, sodass diese Version über ein integriertes Vokabular für Gebäude verfügt. | Die aktuelle Azure Digital Twins-Version ist nicht an einen bestimmten Bereich gebunden. Sie können benutzerdefiniertes Vokabular und benutzerdefinierte Modelle für Ihre Lösung definieren, um eine größere Palette an Umgebungen darzustellen.<br><br>Weitere Informationen finden Sie unter [*Konzepte: Grundlegendes zu Zwillingsmodellen in Azure Digital Twins*](concepts-models.md). |
| **Topologie**<br>*Flexibler*| Im ursprünglichen Release wurde eine Baumdatenstruktur unterstützt, die auf Smart Spaces abgestimmt ist. Digitale Zwillinge wurden mit hierarchischen Beziehungen verbunden. | Im aktuellen Release können digitale Zwillinge in beliebigen Topologien verbunden und gemäß Ihren individuellen Anforderungen organisiert werden. Dadurch können Sie die komplexen Beziehungen der realen Umgebung flexibler darstellen.<br><br>Weitere Informationen finden Sie unter [*Konzepte: Grundlegendes zu digitalen Zwillingen und zum zugehörigen Zwillingsgraphen*](concepts-twins-graph.md). |
| **Compute**<br>*Umfangreicher, flexibler* | Im ursprünglichen Release wurde Logik für die Verarbeitung von Ereignissen und Telemetriedaten in benutzerdefinierten JavaScript-Funktionen (User-Defined Functions, UDFs) definiert. Die Debuggingmöglichkeiten waren bei UDFs eingeschränkt. | Das aktuelle Release verfügt über ein offenes Computemodell: Sie geben benutzerdefinierte Logik an, indem Sie externe Computeressourcen wie [Azure Functions](../azure-functions/functions-overview.md) anfügen. Dadurch können Sie Ihre bevorzugte Programmiersprache auswählen, uneingeschränkt auf benutzerdefinierte Codebibliotheken zugreifen sowie Entwicklungs- und Debuggingressourcen des externen Diensts nutzen.<br><br>Weitere Informationen finden Sie unter [*Anleitungen: von Azure Functions-Apps für die Verarbeitung von Daten*](how-to-create-azure-function.md). |
| **Geräteverwaltung mit IoT Hub**<br>*Zugänglicher* | Im ursprünglichen Release wurden Geräte mit einer internen [IoT Hub](../iot-hub/about-iot-hub.md)-Instanz des Azure Digital Twins-Diensts verwaltet. Dieser integrierte Hub war für Entwickler nicht voll zugänglich. | Im aktuellen Release wird ein eigener IoT-Hub verwendet, indem eine unabhängig erstellte IoT Hub-Instanz angefügt wird (mitsamt aller Geräte, die diese Instanz bereits verwaltet). Dadurch erhalten Sie vollen Zugriff auf die Funktionen von IoT Hub, und Sie profitieren von einer vollständigen Kontrolle über die Geräteverwaltung.<br><br>Weitere Informationen finden Sie unter [*Anleitungen: Erfassen von IoT Hub Telemetriedaten in Azure Digital Twins*](how-to-ingest-iot-hub-data.md). |
| **Security**<br>*Standardisierter* | Im ursprünglichen Release waren Rollen vordefiniert, mit denen Sie den Zugriff auf Ihre Instanz verwalten konnten. | Das aktuelle Release ermöglicht eine Integration mit demselben Back-End-Dienst für die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../role-based-access-control/overview.md), die auch von anderen Azure-Diensten verwendet wird. Dadurch kann die Authentifizierung zwischen anderen Azure-Diensten in Ihrer Lösung (IoT Hub, Azure Functions, Event Grid usw.) vereinfacht werden.<br>Mit der rollenbasierten Zugriffssteuerung können Sie entweder vordefinierte Rollen verwenden oder benutzerdefinierte Rollen erstellen und konfigurieren.<br><br>Weitere Informationen finden Sie unter [*Konzepte: Schützen von Azure Digital Twins mit rollenbasierter Zugriffssteuerung*](concepts-security.md). |
| **Skalierbarkeit**<br>*Höher* | Im ursprünglichen Release waren die Skalierungsmöglichkeiten bei Geräten, Nachrichten, Diagrammen und Skalierungseinheiten eingeschränkt. Pro Abonnement wurde nur eine Instanz von Azure Digital Twins unterstützt.  | Das aktuelle Release basiert auf einer neuen Architektur mit verbesserter Skalierbarkeit und mehr Computeleistung. Außerdem werden zehn Instanzen pro Region und Abonnement unterstützt.<br><br>Informationen zu den Einschränkungen im aktuellen Release finden Sie unter [*Diensteinschränkungen*](reference-service-limits.md). |

## <a name="service-limits"></a>Diensteinschränkungen

Eine Liste der Einschränkungen für Azure Digital Twins finden Sie unter [*Diensteinschränkungen*](reference-service-limits.md).

## <a name="next-steps"></a>Nächste Schritte

* Arbeiten Sie mit dem aktuellen Release in der Schnellstartanleitung: [*Schnellstart: Erkunden eines Beispielszenarios*](quickstart-adt-explorer.md).

* Informationen zu den wichtigsten Konzepten finden Sie im Artikel [*Konzepte: Grundlegendes zu Zwillingsmodellen in Azure Digital Twins*](concepts-models.md).