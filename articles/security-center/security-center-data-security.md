---
title: Azure Security Center-Datensicherheit | Microsoft Docs
description: In diesem Dokument wird erläutert, wie Daten in Azure Security Center verwaltet und geschützt werden.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 0c21c916d152188031212b47704dfb41d3f64a82
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596285"
---
# <a name="azure-security-center-data-security"></a>Azure Security Center-Datensicherheit

Azure Security Center erfasst und verarbeitet sicherheitsbezogene Daten (einschließlich Konfigurationsinformationen, Metadaten, Ereignisprotokolle und Ähnliches), um Kunden bei der Vermeidung, Erkennung und Behandlung von Bedrohungen zu unterstützen. Microsoft hält strenge Compliance- und Sicherheitsrichtlinien ein – angefangen bei der Codierung bis hin zum Betreiben von Diensten.

In diesem Artikel wird erläutert, wie Daten in Security Center verwaltet und geschützt werden.

## <a name="data-sources"></a>Datenquellen
Security Center analysiert Daten aus den folgenden Quellen, um über den Sicherheitsstatus zu informieren, Sicherheitslücken zu identifizieren, Gegenmaßnahmen zu empfehlen und aktive Bedrohungen zu erkennen:

- **Azure-Dienste**: Verwendet Informationen zur Konfiguration von Azure-Diensten, die Sie bereitgestellt haben. Hierzu wird mit dem Ressourcenanbieter des Diensts kommuniziert.
- **Netzwerkdatenverkehr:** Verwendet Metadatenstichproben des Netzwerkdatenverkehrs aus der Infrastruktur von Microsoft wie etwa Quelle/Ziel, IP/Port, Paketgröße und Netzwerkprotokoll.
- **Partnerlösungen:** Verwendet Sicherheitswarnungen von integrierten Partnerlösungen (beispielsweise Firewalls und Antischadsoftwarelösungen).
- **Ihre Computer:** Verwendet Konfigurationsdetails und Informationen zu sicherheitsrelevanten Ereignissen, beispielsweise Ereignis- und -Überwachungsprotokolle unter Windows und Syslog-Nachrichten von Ihren Computern.


## <a name="data-protection"></a>Schutz von Daten

### <a name="data-segregation"></a>Trennung von Daten
Daten werden für jede Komponente des Diensts logisch getrennt verwaltet. Sämtliche Daten werden nach Organisation gekennzeichnet. Dieser Kennzeichnung wird während des gesamten Datenlebenszyklus beibehalten und auf jeder Ebene des Diensts erzwungen.

### <a name="data-access"></a>Datenzugriff
Zur Bereitstellung von Sicherheitsempfehlungen sowie bei der Untersuchung potenzieller Sicherheitsrisiken greifen Mitarbeiter von Microsoft unter Umständen auf Informationen zu, die von Azure-Diensten erfasst oder analysiert wurden. Hierzu zählen etwa Prozesserstellungsereignisse und andere Artefakte. Diese können ggf. Kundendaten oder personenbezogene Daten von Ihren Computern enthalten. 

Wir halten uns an den [Microsoft Online Services-Nachtrag zum Schutz von Daten](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880). Darin ist festgelegt, dass Microsoft keine Kundendaten oder daraus abgeleiteten Informationen zu Werbezwecken oder anderen kommerziellen Zwecken verwendet. Wir verwenden Kundendaten nur, wenn dies für die Bereitstellung Ihrer Azure-Dienste erforderlich ist. Dies gilt auch für Zwecke, die mit der Bereitstellung dieser Dienste kompatibel sind. Alle Rechte an den Kundendaten verbleiben bei Ihnen.

### <a name="data-use"></a>Datennutzung
Microsoft nutzt mandantenübergreifende Muster und Informationen zu Bedrohungen (Threat Intelligence), um die Funktionen für Prävention und Erkennung zu verbessern. Dies erfolgt in Übereinstimmung mit den in unserer [Datenschutzerklärung](https://privacy.microsoft.com/privacystatement) beschriebenen Datenschutzzusagen.

## <a name="manage-data-collection-from-machines"></a>Verwalten der Datensammlung von Computern
Wenn Sie Security Center in Azure aktivieren, wird die Datensammlung für alle Ihre Azure-Abonnements aktiviert. Sie können die Datensammlung für Ihre Abonnements in Security Center aktivieren. Wenn die Datensammlung aktiviert ist, stellt Security Center den Log Analytics-Agent auf allen vorhandenen unterstützten virtuellen Azure-Computern sowie auf allen neuen virtuellen Computern bereit, die erstellt werden.

Der Log Analytics-Agent sucht in ETW-Ablaufverfolgungen ([Event Tracing for Windows, Ereignisablaufverfolgung für Windows](/windows/win32/etw/event-tracing-portal)) nach verschiedenen sicherheitsbezogenen Konfigurationen und Ereignissen. Außerdem löst das Betriebssystem während der Ausführung des Computers Ereignisprotokollereignisse aus. Beispiele für Daten dieser Art: Betriebssystemtyp und -version, Betriebssystemprotokolle (Windows-Ereignisprotokolle), ausgeführte Prozesse, Computername, IP-Adressen, angemeldeter Benutzer und Mandanten-ID. Der Log Analytics-Agent liest Ereignisprotokolleinträge und ETW-Ablaufverfolgungen und kopiert diese zur Analyse in Ihren Arbeitsbereich bzw. in Ihre Arbeitsbereiche. Der Log Analytics-Agent ermöglicht auch Prozesserstellungsereignisse und die Befehlszeilenüberwachung.

Wenn Sie nicht Azure Defender verwenden, können Sie die Datensammlung von virtuellen Computern auch in der Sicherheitsrichtlinie deaktivieren. Die Datensammlung ist für Abonnements erforderlich, die durch Azure Defender geschützt werden. Momentaufnahmen von VM-Datenträgern sowie die Artefaktsammlung sind auch bei deaktivierter Datensammlung aktiviert.

Sie können den Arbeitsbereich und die Region angeben, in dem bzw. der die von Ihren Computern gesammelten Daten gespeichert werden. Standardmäßig werden auf Ihren Computern gesammelte Daten im nächstgelegenen Arbeitsbereich gespeichert, wie in der folgenden Tabelle gezeigt:

| Geografischer Raum VM                                      | Geografischer Raum Arbeitsbereich  |
|---------------------------------------------|----------------|
| USA, Brasilien, Südafrika         | USA  |
| Canada                                      | Canada         |
| Europa (außer Vereinigtes Königreich)           | Europa         |
| United Kingdom                              | United Kingdom |
| Asien (außer Indien, Japan, Korea, China) | Asien-Pazifik   |
| Korea                                       | Asien-Pazifik   |
| Indien                                       | Indien          |
| Japan                                       | Japan          |
| China                                       | China          |
| Australien                                   | Australien      |
|                                             |                |

> [!NOTE]
> **Azure Defender für Storage** speichert Artefakte dem Standort der verknüpften Azure-Ressource entsprechend regional. Weitere Informationen finden Sie unter [Einführung in Azure Defender für Storage](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Nutzung der Daten

Kunden können auf Daten im Zusammenhang mit Security Center über die folgenden Datenströme zugreifen:


| STREAM                                                                                | Datentypen                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md)                       | Alle Sicherheitswarnungen, von Security Center genehmigte [Just-In-Time](security-center-just-in-time.md)-Zugriffsanforderungen und alle von [adaptiven Anwendungssteuerungen](security-center-adaptive-application.md) generierten Warnungen|
| [Azure Monitor-Protokolle](../azure-monitor/data-platform.md)                      | Alle Sicherheitswarnungen                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | Sicherheitswarnungen, Sicherheitsempfehlungen, Ergebnisse der Sicherheitsrisikobewertung, sichere Bewertungsinformationen, Status von Konformitätsprüfungen und mehr                                                                       |
| [Azure Security Center-REST-API](/rest/api/securitycenter/) | Sicherheitswarnungen, Sicherheitsempfehlungen und vieles mehr                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Daten in Azure Security Center verwaltet und geschützt werden. 

Weitere Informationen zu Azure Security Center finden Sie unter [Was ist Azure Security Center?](security-center-introduction.md).