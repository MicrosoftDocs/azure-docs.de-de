---
title: Konzeptionelle Erläuterung der Grundlagen des Sicherheitsmoduls für Azure RTOS
description: In diesem erfahren Sie die Grundlagen zu den Konzepten und Workflows des Sicherheitsmoduls für Azure RTOS.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 8f521bd593369509fd520831f90ce9c601227f09
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340049"
---
# <a name="security-module-for-azure-rtos-preview"></a>Sicherheitsmodul für Azure RTOS (Vorschau)

Verwenden Sie diesen Artikel, um ein besseres Verständnis des Sicherheitsmoduls für Azure RTOS zu erlangen, einschließlich Funktionen und Vorteilen sowie Links zu relevanten Konfigurations- und Referenzressourcen. 

## <a name="azure-rtos-iot-security-module"></a>IoT-Sicherheitsmodul für Azure RTOS

Das Sicherheitsmodul für Azure RTOS bietet eine umfassende Sicherheitslösung für Azure RTOS-Geräte als Teil des NetX Duo-Angebots. Innerhalb des NetX Duo-Angebots wird Azure RTOS in das Azure IoT-Sicherheitsmodul integriert ausgeliefert und bietet nach seiner Aktivierung Abdeckung für häufige Bedrohungen auf Ihren Echtzeit-Betriebssystemgeräten. 

Das Sicherheitsmodul für Azure RTOS wird im Hintergrund ausgeführt und bietet eine nahtlose Benutzererfahrung, während Sicherheitsmeldungen mit den eindeutigen Verbindungen der einzelnen Kunden an deren IoT Hub gesendet werden. Das Sicherheitsmodul für Azure RTOS ist standardmäßig aktiviert.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo eist ein erweiterter, TCP/IP-Netzwerkstapel auf Branchenniveau, der speziell für tief eingebettete Echtzeit- und IoT-Anwendungen entwickelt wurde. Azure RTOS NetX Duo ist ein Doppelnetzwerkstack für IPv4 und IPv6, der einen umfangreichen Satz von Protokollen bereitstellt, einschließlich Sicherheit und Cloud. Erfahren Sie mehr über [Azure RTOS NetX Duo](/azure/rtos/netx-duo/)-Lösungen.

Das Modul bietet die folgenden Funktionen:

- **Erkennung bösartiger Netzwerkaktivitäten**
- **Baselines für Geräteverhalten auf Grundlage von benutzerdefinierten Warnungen**
- **Verbesserung der Gerätesicherheit**

## <a name="security-module-for-azure-rtos-architecture"></a>Architektur des Sicherheitsmoduls für Azure RTOS

Das Sicherheitsmodul für Azure RTOS wird von der Azure IoT-Middlewareplattform initialisiert und verwendet IoT Hub-Clients, um Sicherheitstelemetriedaten an den Hub zu senden.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Statusdiagramm und Informationsfluss des Azure IoT-Sicherheitsmoduls":::

Das Sicherheitsmodul für Azure RTOS überwacht die folgende Geräteaktivitäten und -informationen mithilfe dreier Collectors:
- Gerätenetzwerkaktivitäten **TCP**, **UDP** und **ICM**
- Systeminformationen als **ThreadX**- und **NetX Duo**-Versionen
- Taktereignisse

Jeder Collector ist mit einer Prioritätsgruppe verknüpft, und jede Prioritätsgruppe besitzt ein eigenes Intervall mit den möglichen Werten **Niedrig**, **Mittel** und **Hoch**. Die Intervalle beeinflussen das Zeitintervall, in dem die Daten erfasst und gesendet werden.

Jedes Zeitintervall ist konfigurierbar, und die IoT-Connectors können aktiviert und deaktiviert werden, um [Ihre Lösung noch weiter anzupassen](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Unterstützte Sicherheitswarnungen und -empfehlungen

Das Sicherheitsmodul für Azure RTOS unterstützt spezifische Sicherheitswarnungen und -empfehlungen. Stellen Sie sicher, dass Sie die [relevanten Warnungs- und Empfehlungswerte für Ihren Dienst überprüfen und anpassen](concept-rtos-security-alerts-recommendations.md), nachdem Sie die Erstkonfiguration abgeschlossen haben.

## <a name="ready-to-begin"></a>Bereit für den Einstieg?

Das Sicherheitsmodul für Azure RTOS steht als kostenloser Download für Ihre IoT-Geräte zur Verfügung. Der Defender für IoT-Clouddienst ist mit einer 30-tägigen Testversion pro Azure-Abonnement verfügbar. [Laden Sie das Sicherheitsmodul jetzt herunter](https://github.com/azure-rtos/azure-iot-preview/releases), um fangen wir an. 

## <a name="next-steps"></a>Nächste Schritte

- Erste Schritte mit dem Sicherheitsmodul für Azure RTOS: [Voraussetzungen und Einrichtung](quickstart-azure-rtos-security-module.md).
- Weitere Informationen zum Sicherheitsmodul für Azure RTOS: [Unterstützung für Sicherheitswarnungen und -empfehlungen](concept-rtos-security-alerts-recommendations.md). 
- Verwenden Sie die [Referenz-API](azure-rtos-security-module-api.md) des Sicherheitsmoduls für Azure RTOS.