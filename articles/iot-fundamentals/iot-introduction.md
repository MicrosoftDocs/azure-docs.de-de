---
title: Einführung in Azure IoT (Internet der Dinge)
description: Einführung in die Grundlagen von Azure IoT und der IoT-Dienste, einschließlich Beispielen zur Veranschaulichung der Anwendung von IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: d2afd88500801f1e2b1e4da5a1e267bddfa69fc7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453015"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Was ist Azure IoT (Internet der Dinge)?

Azure IoT (Internet der Dinge) ist eine Sammlung von Clouddiensten, die von Microsoft verwaltet werden und Milliarden von IoT-Ressourcen verbinden, überwachen und steuern. Vereinfacht ausgedrückt besteht eine IoT-Lösung aus mindestens einem IoT-Gerät, das mit mindestens einem in der Cloud gehosteten Back-End-Dienst kommuniziert. 

## <a name="iot-devices"></a>IoT-Geräte

Ein IoT-Gerät besteht in der Regel aus einer Leiterplatte mit Sensoren, das per WLAN mit dem Internet verbunden ist. Beispiel:

* Ein Drucksensor einer Ölpumpe an einem entfernten Ort
* Temperatur- und Luftfeuchtigkeitssensoren in einer Klimaanlage
* Ein Beschleunigungsmesser in einem Fahrstuhl
* Präsenzmelder in einem Raum

Bei der Erstellung Ihrer Lösung können Sie aus einer Vielzahl von Geräten von verschiedenen Herstellern wählen. Eine Liste mit Geräten, die für die Verwendung mit Azure IoT Hub zertifiziert sind, finden Sie im [Azure Certified for IoT-Gerätekatalog](https://catalog.azureiotsolutions.com/alldevices). Für die Prototyperstellung können Sie Geräte wie [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) oder [Raspberry Pi](https://www.raspberrypi.org/) verwenden. Das DevKit-Gerät verfügt über integrierte Sensoren für Temperatur, Druck und Luftfeuchtigkeit sowie über ein Gyroskop, einen Beschleunigungsmesser und ein Magnetometer. An einen Raspberry Pi können verschiedenste Sensoren angeschlossen werden. 

Microsoft stellt Open-Source-basierte [Geräte-SDKs](../iot-hub/iot-hub-devguide-sdks.md) bereit, mit denen Sie die Apps erstellen können, die auf Ihren Geräten ausgeführt werden. Diese [SDKs vereinfachen und beschleunigen](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) die Entwicklung Ihrer IoT-Lösungen.

## <a name="communication"></a>Kommunikation

IoT-Geräte senden in der Regel Telemetriedaten von den Sensoren an Back-End-Dienste in der Cloud. Es sind jedoch auch andere Kommunikationsformen möglich. Ein Beispiel wäre etwa ein Back-End-Dienst, der Befehle an Ihre Geräte sendet. Im Anschluss finden Sie einige Beispiele für die Kommunikation zwischen Gerät und Cloud (Gerät-zu-Cloud bzw. Cloud-zu-Gerät):

* Ein Kühllaster sendet alle fünf Minuten Temperaturdaten an eine IoT Hub-Instanz. 

* Der Back-End-Dienst sendet einen Befehl an ein Gerät, um für die Problemdiagnose die Sendefrequenz von Telemetriedaten zu ändern. 

* Ein Gerät sendet Warnungen auf der Grundlage der Werte, die von den Sensoren ausgelesen wurden. Beispiel: Ein Gerät zur Überwachung eines diskontinuierlichen Rührkessels in einem Chemiewerk sendet eine Warnung, wenn die Temperatur einen bestimmten Wert überschreitet.

* Ihre Geräte senden Informationen, die menschlichen Operatoren auf einem Dashboard angezeigt werden. Beispiel: Im Kontrollraum einer Raffinerie werden Temperatur, Druck und Durchflussmenge der einzelnen Rohre angezeigt, um Operatoren die Überwachung der Anlage zu ermöglichen. 

Die [IoT-Geräte-SDKs](../iot-hub/iot-hub-devguide-sdks.md) und IoT Hub unterstützen gängige [Kommunikationsprotokolle](../iot-hub/iot-hub-devguide-protocols.md) wie HTTP, MQTT und AMQP.

Die Merkmale von IoT-Geräten unterscheiden sich von anderen Clients wie Browsern und mobilen Apps. Die Geräte-SDKs unterstützen Sie bei der Bewältigung der Herausforderungen im Zusammenhang mit der Herstellung sicherer und zuverlässiger Verbindungen zwischen Geräten und Ihrem Back-End-Dienst.  Für IoT-Geräte gilt beispielsweise Folgendes:

* Es handelt sich häufig um eingebettete Systeme ohne menschlichen Bediener (im Gegensatz zu einem Smartphone).
* Sie können an Remotestandorten bereitgestellt werden, an denen der physische Zugriff mit hohen Kosten verbunden ist.
* Sie sind unter Umständen nur über das Back-End der Lösung erreichbar.
* Sie verfügen ggf. über eingeschränkte Stromversorgungs- und Verarbeitungsressourcen.
* Sie verfügen ggf. über intermittierende, langsame oder teure Netzwerkverbindungen.
* Sie müssen unter Umständen proprietäre, benutzerdefinierte oder branchenspezifische Anwendungsprotokolle verwenden.

## <a name="back-end-services"></a>Back-End-Dienste 

In einer IoT-Lösung ermöglicht der Back-End-Dienst beispielsweise Folgendes:

* Bedarfsgesteuertes Empfangen von Telemetriedaten von Ihren Geräten und Bestimmen, wie diese Daten verarbeitet und gespeichert werden sollen
* Analysieren der Telemetriedaten entweder in Echtzeit oder später, um Erkenntnisse zu gewinnen
* Senden von Befehlen aus der Cloud an ein bestimmtes Gerät 
* Bereitstellen von Geräten und Steuern, welche Geräte eine Verbindung mit Ihrer Infrastruktur herstellen können
* Steuern des Zustands Ihrer Geräte und Überwachen ihrer Aktivitäten
* Verwalten der auf Ihren Geräten installierten Firmware

Beispiel: In einer Remoteüberwachungslösung für eine Ölpumpstation verwendet das Cloud-Back-End Telemetriedaten der Pumpen, um Anomalien zu erkennen. Wenn der Back-End-Dienst eine Anomalie erkennt, kann er automatisch einen Befehl an das Gerät senden, um das Problem zu beheben. Bei diesem Prozess wird eine automatisierte Feedbackschleife zwischen dem Gerät und der Cloud generiert, mit der die Effizienz der Lösung deutlich gesteigert wird.

## <a name="azure-iot-examples"></a>Azure IoT-Beispiele

Praxisbeispiele für die Verwendung von Azure IoT in Organisationen finden Sie in den [technischen Fallstudien von Microsoft zu IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

Ausführliche Informationen zur IoT-Architektur finden Sie unter [Microsoft Azure IoT Reference Architecture](/azure/architecture/reference-architectures/iot) (Microsoft Azure IoT-Referenzarchitektur).

## <a name="next-steps"></a>Nächste Schritte

Einige konkrete Anwendungsfälle und die entsprechenden Architekturen finden Sie in den [technischen Fallstudien zu Microsoft Azure IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Einige Beispielprojekte, die Sie mit einem IoT-DevKit testen können, finden Sie im [IoT DevKit-Projektkatalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Eine ausführlichere Erläuterung der verschiedenen Dienste sowie Informationen zu deren Verwendung finden Sie unter [Azure IoT-Dienste und -Technologien](iot-services-and-technologies.md).