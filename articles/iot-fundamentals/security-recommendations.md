---
title: Sicherheitsempfehlungen für Azure IoT | Microsoft-Dokumentation
description: In diesem Artikel werden zusätzliche Schritte zur Gewährleistung der Sicherheit Ihrer Azure IoT Hub-Lösung zusammengefasst.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: a1de3a71253b1a82b4423bff279fbf3f7e378da4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457616"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Sicherheitsempfehlungen für die Bereitstellung von Azure Internet of Things (IoT)

Dieser Artikel enthält Sicherheitsempfehlungen für das Internet der Dinge. Die Umsetzung dieser Empfehlungen erleichtert es Ihnen, Ihre Sicherheitspflichten zu erfüllen, die in unserem Modell der gemeinsamen Verantwortung beschrieben werden. Weitere Informationen dazu, wie Microsoft seiner Verantwortung als Dienstanbieter nachkommt, finden Sie unter [Gemeinsame Verantwortung für das Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Einige der in diesem Artikel enthaltenen Empfehlungen können automatisch durch Azure Security Center überwacht werden. Azure Security Center ist die erste Verteidigungslinie zum Schutz Ihrer Ressourcen in Azure. Der Dienst analysiert regelmäßig den Sicherheitsstatus Ihrer Azure-Ressourcen, um potenzielle Sicherheitsrisiken zu erkennen. Anschließend erhalten Sie Empfehlungen dazu, wie damit umgegangen werden kann.

- Weitere Informationen zu Empfehlungen von Azure Security Center finden Sie unter [Sicherheitsempfehlungen in Azure Security Center](../security-center/security-center-recommendations.md).
- Weitere Informationen zu Azure Security Center finden Sie unter [Was ist Azure Security Center?](../security-center/security-center-introduction.md)

## <a name="general"></a>Allgemein

| Empfehlung | Kommentare | Von ASC unterstützt |
|-|----|--|
| Bleiben Sie auf dem neusten Stand | Verwenden Sie aktuelle Versionen der unterstützten Plattformen, Programmiersprachen, Protokolle und Frameworks. | - |
| Schützen von Authentifizierungsschlüsseln | Bewahren Sie die Geräte-IDs und ihre Authentifizierungsschlüssel nach der Bereitstellung physisch geschützt auf. Dadurch wird vermieden, dass sich ein böswilliges Gerät als registriertes Gerät ausgibt. | - |
| Verwenden von Geräte-SDKs nach Möglichkeit | Geräte-SDKs implementieren eine Vielzahl von Sicherheitsmerkmalen, wie z.B. Verschlüsselung, Authentifizierung usw., um Sie bei der Entwicklung einer zuverlässigen und sicheren Geräteanwendung zu unterstützen. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md). | - |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung 

| Empfehlung | Kommentare | Von ASC unterstützt |
|-|----|--|
| Definieren der Zugriffssteuerung für den Hub | [Verstehen und definieren Sie die Art des Zugriffs](iot-security-deployment.md#securing-the-cloud), die jede Komponente in Ihrer IoT Hub-Lösung basierend auf der Funktionalität haben wird. Die zulässigen Berechtigungen sind *Registry Read*, *RegistryReadWrite*, *ServiceConnect* und *DeviceConnect*. Standardmäßige [SAS-Richtlinien in Ihrem IoT-Hub](../iot-hub/iot-hub-devguide-security.md#access-control-and-permissions) können auch dazu beitragen, die Berechtigungen jeder Komponente basierend auf ihrer Rolle zu definieren. | - |
| Definieren der Zugriffssteuerung für Back-End-Dienste | Daten, die von Ihrer IoT-Hub-Lösung erfasst werden, können von anderen Azure-Diensten wie [Cosmos DB](../cosmos-db/index.yml), [Stream Analytics](../stream-analytics/index.yml), [App Service](../app-service/index.yml), [Logik-Apps](../logic-apps/index.yml) und [Blob Storage](../storage/blobs/storage-blobs-introduction.md) genutzt werden. Stellen Sie sicher, dass Sie die für diese Dienste dokumentierten Zugriffsberechtigungen verstehen und erteilen. | - |

## <a name="data-protection"></a>Schutz von Daten

| Empfehlung | Kommentare | Von ASC unterstützt |
|-|----|--|
| Sichere Geräteauthentifizierung | Stellen Sie eine sichere Kommunikation zwischen Ihren Geräten und Ihrem IoT-Hub sicher, indem Sie entweder [einen eindeutigen Identitätsschlüssel oder ein Sicherheitstoken](iot-security-deployment.md#iot-hub-security-tokens) oder für jedes Gerät [ein auf dem Gerät befindliches X.509-Zertifikat](iot-security-deployment.md#x509-certificate-based-device-authentication) verwenden. Wählen Sie die geeignete Methode, um [Sicherheitstoken basierend auf dem gewählten Protokoll (MQTT, AMQP oder HTTPS)](../iot-hub/iot-hub-devguide-security.md) einzusetzen. | - |
| Sichere Kommunikation zwischen Geräten | IoT Hub schützt die Verbindung mit den Geräten gemäß dem TLS-Standard (Transport Layer Security) und unterstützt die Versionen 1.2 und 1.0. Verwenden Sie [TLS 1.2](https://tools.ietf.org/html/rfc5246), um maximale Sicherheit zu gewährleisten. | - |
| Sichern der Dienstkommunikation | IoT Hub bietet Endpunkte für die Verbindung mit Back-End-Diensten wie [Azure Storage](../storage/index.yml) oder [Event Hubs](../event-hubs/index.yml), wobei nur das TLS-Protokoll verwendet und kein Endpunkt auf einem unverschlüsselten Kanal offengelegt wird. Sobald diese Daten diese Back-End-Dienste zur Speicherung oder Analyse erreichen, stellen Sie sicher, dass Sie geeignete Sicherheits- und Verschlüsselungsmethoden für den jeweiligen Dienst verwenden und sensible Informationen im Back-End schützen. | - |

## <a name="networking"></a>Netzwerk

| Empfehlung | Kommentare | Von ASC unterstützt |
|-|----|--|
| Schützen des Zugriffs auf Ihre Geräte | Beschränken Sie Hardwareanschlüsse in Ihren Geräten auf ein absolutes Minimum, um unerwünschten Zugriff zu vermeiden. Entwickeln Sie darüber hinaus Mechanismen, um physische Manipulationen am Gerät zu verhindern oder zu erkennen. Weitere Informationen finden Sie unter [Bewährte Methoden für die IoT-Sicherheit](iot-security-best-practices.md). | - |
| Entwickeln sicherer Hardware | Integrieren Sie Sicherheitsmerkmale wie verschlüsselte Speicherung oder Trusted Platform Module (TPM), um Geräte und Infrastruktur sicherer zu gestalten. Halten Sie das Gerätebetriebssystem und Treiber auf dem neuesten Stand, und installieren Sie, sofern der Platz es zulässt, Antiviren- und Antischadsoftware. Unter [IoT-Sicherheitsarchitektur](iot-security-architecture.md) erfahren Sie, wie dies dazu beitragen kann, mehrere Sicherheitsbedrohungen zu entschärfen. | - |

## <a name="monitoring"></a>Überwachung

| Empfehlung | Kommentare | Von ASC unterstützt |
|-|----|--|
| Überwachen des nicht autorisierten Zugriffs auf Ihre Geräte |  Überwachen Sie mithilfe der Protokollierungsfunktion Ihres Gerätebetriebssystems etwaige Sicherheitsverletzungen oder physische Manipulationen am Gerät oder an seinen Anschlüssen. | - |
| Überwachen Ihrer IoT-Lösung in der Cloud | Überwachen Sie die allgemeine Integrität Ihrer IoT Hub-Lösung mithilfe der [Metriken in Azure Monitor](../iot-hub/monitor-iot-hub.md). | - |
| Einrichten der Diagnose | Beobachten Sie Ihren Betrieb genau, indem Sie Ereignisse in Ihrer Lösung protokollieren und dann die Diagnoseprotokolle an Azure Monitor senden, um einen Überblick über die Leistung zu erhalten. Weitere Informationen finden Sie unter [Überwachen und Diagnostizieren von Problemen in Ihrem IoT-Hub](../iot-hub/monitor-iot-hub.md). | - |

## <a name="next-steps"></a>Nächste Schritte

Für weitergehende Szenarien mit Azure IoT müssen Sie möglicherweise zusätzliche Sicherheitsanforderungen berücksichtigen. Weitere Anleitungen finden Sie unter [IoT-Sicherheitsarchitektur](iot-security-architecture.md).