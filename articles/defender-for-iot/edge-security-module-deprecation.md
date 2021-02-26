---
title: Funktionsunterstützung und -einstellung
titleSuffix: Azure Defender for IoT
description: Defender für IoT unterstützt C, C# und Edge bis zum 1. März 2022 weiter.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 2779a73d3a5f77e3a3b144309baf6d2788565443
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809634"
---
# <a name="feature-support-and-retirement"></a>Funktionsunterstützung und -einstellung

In diesem Artikel werden die Features von Azure Defender für IoT und die Unterstützung der unterschiedlichen Funktionen in Defender für IoT beschrieben.

## <a name="defender-for-iot-c-c-and-edge-security-module-deprecation"></a>Einstellung der Defender für IoT-Sicherheitsmodule für C, C# und Edge

Der neue Micro-Agent ersetzt das aktuelle C-, C#- und Edge-Sicherheitsmodul.  

In den Micro-Agent sind die Kenntnisse und Erfahrungen aus der Entwicklung der bestehenden Sicherheitsmodule und das Feedback von Kunden und Partnern eingeflossen. Er weist vier wichtige Verbesserungen auf: 

- **Umfassender Sicherheitswert:**  Der neue Agent wird auf Hostebene ausgeführt und bietet damit mehr Einblick in die zugrunde liegenden Vorgänge des Geräts und eine bessere Sicherheitsabdeckung.

- **Verbesserte Geräteleistung und geringerer Speicherbedarf:**  Erreicht wird dies durch einen geringen Speicherbedarf bei RAM und ROM und einen niedrigen CPU-Verbrauch.  

- **Plug & Play:**  Der neue Micro-Agent weist keine Abhängigkeiten auf Kernelebene mehr auf, und alle erforderlichen Softwareabhängigkeiten werden als Teil des Pakets bereitgestellt. Der Micro-Agent unterstützt die allgemeine CPU-Architektur.

- **Einfach bereitzustellen:**  Der Micro-Agent unterstützt verschiedene Verteilungsmodelle über den Quellcode und als binäres Paket. 

### <a name="timeline"></a>Zeitachse 

Defender für IoT unterstützt C, C# und Edge bis zum 1. März 2022 weiter. 

## <a name="micro-agent-preview-support"></a>Unterstützung der Vorschauversion des Micro-Agents

Während der Vorschauphase können beim Micro-Agent ohne vorherige Ankündigung Breaking Changes vorgenommen werden.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über [Sensor- und Verwaltungskonsolen-APIs für Defender für IoT](references-work-with-defender-for-iot-apis.md).