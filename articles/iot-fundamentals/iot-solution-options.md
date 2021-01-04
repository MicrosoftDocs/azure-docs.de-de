---
title: Optionen für Azure IoT-Lösungen (Internet of Things, Internet der Dinge)
description: Leitfaden zur Entscheidung zwischen Plattformdiensten und der verwalteten App-Plattform beim Erstellen einer IoT-Lösung. Bei der Variante mit Plattformdiensten werden Dienste wie IoT Hub und Digital Twins als Bausteine verwendet. Bei der Variante mit der verwalteten App-Plattform wird IoT Central verwendet, um einen schnellen Einstieg zu ermöglichen.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 02/05/2020
ms.author: dobett
ms.openlocfilehash: 7ff8193e87d138878d95e783d144d445436e7a08
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452996"
---
# <a name="what-is-the-right-iot-solution-for-your-business"></a>Welche IoT-Lösung ist für Ihr Unternehmen am besten geeignet?

Wenn Sie eine IoT-Lösung für Ihr Unternehmen erstellen, wird in der Regel entweder ein Ansatz mit *Plattformdiensten* oder ein Ansatz mit *verwalteter App-Plattform* verfolgt.

Plattformdienste stellen die Bausteine für angepasste und flexible IoT-Anwendungen bereit. Sie haben mehr Auswahl- und Programmieroptionen beim Verbinden von Geräten sowie beim Erfassen, Speichern und Analysieren Ihrer Daten. Azure IoT-Plattformdienste enthalten Azure IoT Hub and Azure Digital Twins.

Im Vergleich zu Plattformdiensten ermöglicht eine verwaltete App-Plattform einen schnelleren Einstieg in die App-Erstellung, da bei dieser Variante weniger Entscheidungen erforderlich sind, um Ergebnisse zu erzielen. Die verwaltete App-Plattform kümmert sich um die meisten Elemente Ihrer Lösung, sodass Sie sich auf die Einbringung von Branchenwissen sowie auf das Skalieren und Verbinden von Geräten konzentrieren können. Azure IoT Central ist eine verwaltete App-Plattform.

Berücksichtigen Sie bei der Entscheidung zwischen den beiden Ansätzen folgende Aspekte:

- Wie möchten Sie Ihre Lösung verwalten?
- Welche Anpassungs- und Steuerungsmöglichkeiten benötigen Sie für Ihre Lösung?
- Welche Preisstruktur bevorzugen Sie?

## <a name="management"></a>Verwaltung

Wofür möchten Sie Ihre Zeit und Ressourcen bei der Systemverwaltung aufwenden? 

- Verwenden Sie die Plattformdienste, um die vollständige Kontrolle über die zugrunde liegenden Dienste Ihrer Lösung zu haben. Dies ermöglicht beispielsweise Folgendes:

    - Verwalten der Skalierung und des Schutzes von Diensten gemäß Ihren Anforderungen
    - Nutzen von internem Know-how oder des Know-hows von Partnern beim Onboarding von Geräten und bei der Dienstbereitstellung

- Entscheiden Sie sich für die verwaltete App-Plattform, um eine Plattform zu verwenden, die Ihnen die Skalierung, den Schutz und die Verwaltung Ihrer IoT-Anwendungen und -Geräte abnimmt.

## <a name="control"></a>Control

Welche Elemente Ihrer Lösung möchten Sie anpassen?

- Verwenden Sie die Plattformdienste, wenn Sie umfassende Anpassungsmöglichkeiten und die vollständige Kontrolle über die Lösungsarchitektur benötigen.

- Entscheiden Sie sich für die verwaltete App-Plattform, wenn Sie Aspekte wie Branding, Dashboards, Benutzerrollen, Geräte und Telemetrie anpassen, sich aber nicht um die Verwaltung des zugrunde liegenden IoT-Systems kümmern möchten.

## <a name="pricing"></a>Preise

Welche Preisstruktur passt am besten zu Ihren Anforderungen?

- Verwenden Sie die Plattformdienste, wenn Sie Dienste im Detail optimieren und Ihre Gesamtkosten steuern möchten.

- Entscheiden Sie sich für die verwaltete App-Plattform, wenn Sie eine einfache, planbare Preisstruktur bevorzugen.

## <a name="summary"></a>Zusammenfassung

Die Plattformdienste eignen sich für Unternehmen, die sich mit Cloudlösungen und Geräten auskennen und folgende Anforderungen haben:

- Möglichkeit zur Optimierung der Dienste in der Lösung
- Hohes Maß an Kontrolle über die Dienste in der Lösung
- Umfassende Anpassungsmöglichkeiten für die Lösung

Die verwaltete App-Plattform eignet sich für Unternehmen mit folgenden Anforderungen:

- Vermeidung eines hohen Ressourcenaufwands für Systemdesign, Entwicklung und Verwaltung
- Planbare Preisstruktur
- Überschaubare Anpassungsmöglichkeiten

## <a name="next-steps"></a>Nächste Schritte

Eine ausführlichere Erläuterung der verschiedenen Dienste und Plattformen sowie Informationen zu deren Verwendung finden Sie unter [Azure IoT-Dienste und -Technologien](iot-services-and-technologies.md).

Weitere Informationen zu den Schlüsselattributen erfolgreicher IoT-Lösungen finden Sie im Whitepaper [Acht Attribute erfolgreicher IoT-Lösungen](https://aka.ms/8attributes).

Ausführliche Informationen zur IoT-Architektur finden Sie unter [Microsoft Azure IoT Reference Architecture](/azure/architecture/reference-architectures/iot) (Microsoft Azure IoT-Referenzarchitektur).