---
title: Was ist OPC Twin? – Azure | Microsoft-Dokumentation
description: Übersicht über OPC-Zwilling
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 15deadad3b53b64c619933db76d28f012c85d6d1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730267"
---
# <a name="what-is-opc-twin"></a>Was ist OPC-Zwilling?

OPC Twin umfasst Microservices, die über Azure IoT Edge und IoT Hub eine Verbindung zwischen der Cloud und dem Fabriknetzwerk herstellen. OPC-Zwilling bietet Erkennungs- und Registrierungsfunktionen und ermöglicht die Fernsteuerung von Industriegeräten über Rest-APIs. OPC-Zwilling erfordert kein OPC UA SDK (Unified Architecture), ist unabhängig von der Programmiersprache und kann in einen serverlosen Workflow integriert werden. In diesem Artikel werden einige Anwendungsfälle für OPC-Zwilling beschrieben.

## <a name="discovery-and-control"></a>Ermittlung und Steuerung
OPC-Zwilling ermöglicht eine einfache Ermittlung und Registrierung.

### <a name="simple-discovery-and-registration"></a>Einfache Ermittlung und Registrierung
Mit OPC-Zwilling können Bediener das Fabriknetzwerk scannen, um die Ermittlung und Registrierung von OPC UA-Servern zu ermöglichen. Alternativ können OPC UA-Geräte von Bedienern auch manuell unter Verwendung einer bekannten Ermittlungs-URL registriert werden. Ein Bediener kann also beispielsweise remote einen Scanvorgang des Netzwerks initiieren und alle OPC UA-Server anzeigen, um eine Verbindung mit allen OPC UA-Geräten herzustellen, nachdem das IoT Edge-Gateway mit einem OPC-Zwilling-Modul im Fertigungsbereich installiert wurde. 

### <a name="simple-control"></a>Einfache Steuerung
Mit OPC-Zwilling können Bediener auf Ereignisse reagieren und ihre Computer im Fertigungsbereich über die Cloud neu konfigurieren (entweder automatisch oder manuell bei Bedarf). OPC-Zwilling bietet REST-APIs, mit denen Sie Dienste auf dem OPC UA-Server aufrufen, seinen Adressraums durchsuchen sowie Variablen lesen/schreiben und Methoden ausführen können. Ein Beispiel: Eine Fertigungslinie wird auf der Grundlage der Temperatur eines Heizkessels gesteuert. Der Temperatursensor veröffentlicht eine Änderung der Daten mithilfe von OPC Publisher. Der Bediener erhält die Warnung, dass die Temperatur den Schwellenwert erreicht hat. Die Fertigungslinie wird über OPC-Zwilling automatisch abgekühlt. Der Bediener wird über die Abkühlung informiert.

## <a name="authentication"></a>Authentication
OPC-Zwilling ermöglicht eine einfache Authentifizierung und Entwicklung.

### <a name="simple-authentication"></a>Einfache Authentifizierung 
OPC-Zwilling nutzt eine auf Azure Active Directory (AAD) basierende End-to-End-Authentifizierung und -Überwachung. So können Sie beispielsweise eine Anwendung auf der Grundlage von OPC-Zwilling erstellen, um zu ermitteln, welche Aktionen ein Bediener auf einem Computer ausgeführt hat. Aufseiten des Computers wird dazu die OPC UA-Überwachung verwendet. Aufseiten der Cloud wird ein unveränderliches Clientüberwachungsprotokoll gespeichert und eine AAD-Authentifizierung über die REST-API verwendet.

### <a name="simple-developer-experience"></a>Einfache Entwicklung 
OPC -Zwilling kann über REST-APIs mit Anwendungen in einer beliebigen Programmiersprache verwendet werden. Da Entwickler einen OPC UA-Client in eine Lösung integrieren, müssen sie nicht mit dem OPC UA SDK vertraut sein. OPC-Zwilling lässt sich nahtlos in zustands- und serverlose Architekturen integrieren. So kann beispielsweise ein Full-Stack-Webentwickler, der eine Anwendung für ein Alarm- und Ereignisdashboard entwickelt, die Logik für die Reaktion auf Ereignisse in JavaScript oder TypeScript schreiben und dabei OPC-Zwilling verwenden, ohne mit C, C# oder der Implementierung des vollständigen OPC UA-Stapels vertraut zu sein. 

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich hier über OPC-Zwilling und entsprechende Verwendungsmöglichkeiten informiert haben, können Sie mit dem nächsten Thema fortfahren:

> [!div class="nextstepaction"]
> [OPC Twin-Architektur](overview-opc-twin-architecture.md)