---
title: Grundlegendes zur Azure Security Center for IoT-Lösungsarchitektur (Vorschauversion) | Microsoft-Dokumentation
description: Lernen Sie den Informationsfluss im Azure Security Center for IoT-Dienst kennen.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 3c775c8cb99b056c7f468c028a80dd0b27ae6438
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200695"
---
# <a name="azure-security-center-for-iot-architecture"></a>Azure Security Center für IoT-Architektur

In diesem Artikel wird die funktionale Systemarchitektur der Lösung „Azure Security Center (ASC) für IoT“ erläutert. 

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="asc-for-iot-components"></a>Azure Security Center for IoT-Komponenten

Azure Security Center for IoT besteht aus den folgenden Komponenten:
- Geräte-Agents
- SDK zum Senden von Sicherheitsmeldungen
- IoT Hub-Integration
- Analytics-Pipeline
 
### <a name="asc-for-iot-workflow"></a>Azure Security Center for IoT-Workflow

Mit Azure Security Center for IoT-Geräte-Agents können Sie problemlos Sicherheitsereignis-Rohdaten Ihrer Geräte erfassen. Sicherheitsereignis-Rohdaten können IP-Verbindungen, die Prozesserstellung, Benutzeranmeldungen und andere sicherheitsrelevante Informationen enthalten. Azure Security Center for IoT-Geräte-Agents verarbeiten auch die Ereignisaggregation, um hohe Netzwerkdurchsätze zu vermeiden. Die Agents sind in hohem Maße anpassbar, sodass Sie sie für bestimmte Aufgaben wie das Senden ausschließlich wichtiger Informationen mit dem dringlichsten SLA oder für das Aggregieren umfangreicher Sicherheitsinformationen und Kontextdaten zu größeren Segmenten verwenden und auf diese Weise höhere Servicekosten vermeiden können.
 
Geräte-Agents und andere Anwendungen verwenden das **Azure Security Center SDK zum Senden von Sicherheitsmeldungen**, um Sicherheitsinformationen an Azure IoT Hub zu senden. IoT Hub übernimmt diese Informationen und leitet sie an den Azure Security Center for IoT-Dienst weiter.

Sobald der Azure Security Center for IoT-Dienst aktiviert ist, versendet IoT Hub zusätzlich zu den weitergeleiteten Daten alle seine internen Daten für die Analyse durch Azure Security Center for IoT. Diese Daten umfassen Cloudvorgangsprotokolle von Geräten, Geräte-Identitäten und die Hub-Konfiguration. Alle diese Informationen tragen dazu bei, die Azure Security Center for IoT-Analysepipeline zu erstellen.
 
Die Azure Security Center for IoT-Analysepipeline erhält ferner zusätzliche Threat Intelligence-Streams aus verschiedenen Quellen innerhalb von Microsoft sowie von Microsoft-Partnern. Die gesamte Azure Security Center for IoT-Analysepipeline arbeitet mit sämtlichen für den Dienst erstellten Kundenkonfigurationen (wie benutzerdefinierte Warnungen und die Verwendung des SDK zum Versenden von Sicherheitsmeldungen).
 
Bei der Verwendung der Analysepipeline kombiniert Azure Security Center for IoT alle Informationsdatenströme, um direkt umsetzbare Empfehlungen und Warnungen zu generieren. Die Pipeline enthält sowohl benutzerdefinierte Regeln, die von Sicherheitsanalytikern und Experten erstellt wurden, als auch nach Abweichungen vom standardmäßigen Geräteverhalten suchende Machine Learning-Modelle und Risikoanalysen.
 
Azure Security Center for IoT-Empfehlungen und -Warnungen (Analysepipeline-Ausgabe) werden in den Log Analytics-Arbeitsbereich des einzelnen Kunden geschrieben. Durch die Einbeziehung der Ereignisrohdaten im Arbeitsbereich sowie der Warnungen und Empfehlungen werden eingehende Untersuchungen und Abfragen möglich, die die genauen Details der festgestellten verdächtigen Aktivitäten verwenden.  

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die grundlegende Architektur und den Workflow der Azure Security Center for IoT-Lösung kennengelernt. Weitere Informationen zu den Voraussetzungen sowie zum Einstieg in Ihre Sicherheitslösung in IoT Hub und zu deren Aktivierung finden Sie in den folgenden Artikeln:

- [ASC für IoT-Voraussetzungen](service-prerequisites.md)
- [Erste Schritte](getting-started.md)
- [Konfigurieren Ihrer IoT-Lösung](quickstart-configure-your-solution.md)
- [Enable security in IoT Hub (Aktivieren der Sicherheit in IoT Hub)](quickstart-onboard-iot-hub.md)
- [ASC for IoT FAQ (Azure Security Center for IoT – häufig gestellte Fragen)](resources-frequently-asked-questions.md)
- [ASC for IoT security alerts (Azure Security Center for IoT-Sicherheitswarnungen)](concept-security-alerts.md)

