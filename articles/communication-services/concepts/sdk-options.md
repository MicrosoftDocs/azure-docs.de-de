---
title: Clientbibliotheken und REST-APIs für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über die Clientbibliotheken und REST-APIs für Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: f59ebfdb1942c7eb9be6c2cf09e1ed677c3f22e3
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507675"
---
# <a name="client-libraries-and-rest-apis"></a>Clientbibliotheken und REST-APIs

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services-Funktionen sind vom Prinzip her in sechs Bereiche unterteilt. Für eine Bereiche gibt es Clientbibliotheken, die vollständig Open-Source sind. Die Clientbibliothek „Calling“ verwendet proprietäre Netzwerkschnittstellen und ist derzeit Closed-Source. Die Bibliothek „Chat“ weist eine Closed-Source-Abhängigkeit auf. Beispiele und weitere technische Details für Clientbibliotheken werden im [GitHub-Repository Azure Communication Services](https://github.com/Azure/communication) veröffentlicht.

## <a name="client-libraries"></a>Clientbibliotheken

| Assembly               | Protokolle             |Open-Source im Vergleich zu Closed-Source| Namespaces                          | Funktionen                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Öffnen            | Azure.ResourceManager.Communication | Dient zum Bereitstellen und Verwalten von Communication Services-Ressourcen             |
| Allgemein                 | REST | Öffnen               | Azure.Communication.Common          | Stellt Basistypen für andere Clientbibliotheken bereit |
| Verwaltung         | REST | Öffnen               | Azure.Communication.Administration  | Dient zum Verwalten von Benutzern, Zugriffstoken und Telefonnummern sowie zum Zuteilen von standardkonformen STUN- und TURN-Servern |
| Chat                   | REST mit proprietärer Signalisierung | Open-Source mit Closed-Source-Signalisierungspaket    | Azure.Communication.Chat            | Dient zum Hinzufügen von textbasiertem Chat in Echtzeit zu Ihren Anwendungen  |
| SMS                    | REST | Öffnen              | Azure.Communication.SMS             | Dient zum Senden und Empfangen von SMS-Nachrichten |
| Aufrufen                | Proprietärer Transport | Geschlossen |Azure.Communication.Calling         | Ermöglicht das Nutzen von Sprache, Video, Bildschirmübertragung und anderen Möglichkeiten der Datenkommunikationen in Echtzeit          |

Beachten Sie, dass sich die Azure Resource Manager-, Administration- und SMS-Clientbibliotheken auf die Dienstintegration konzentrieren, und in vielen Fällen treten Sicherheitsprobleme auf, wenn Sie diese Funktionen in Endbenutzeranwendungen integrieren. Die Common- und Chat-Clientbibliothek sind für Dienst- und Clientanwendungen geeignet. Die Calling-Clientbibliothek ist für Clientanwendungen konzipiert. Eine Clientbibliothek, die sich auf Dienstszenarien konzentriert, ist in der Entwicklung.

### <a name="languages-and-publishing-locations"></a>Sprachen und Veröffentlichungsspeicherorte

Veröffentlichungsspeicherorte für einzelne Clientbibliothekspakete finden Sie nachstehend. 

| Bereich           | JavaScript | .NET | Python | Java SE | iOS | Android | Andere                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Go über GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Allgemein         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | –      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases/tag/1.0.0-beta.1)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Verwaltung | [npm](https://www.npmjs.com/package/@azure/communication-administration)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Administration)    | [PyPi](https://pypi.org/project/azure-communication-administration/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-administration)   | -              | -              | -                            |
| Chat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Aufrufen        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases/tag/v1.0.0-beta.2)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Referenzdokumentation     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](https://docs.microsoft.com/objectivec/communication-services/calling/)      | [docs](https://docs.microsoft.com/java/api/com.azure.communication.calling?view=communication-services-java-android)            | -                              |
## <a name="rest-apis"></a>REST-APIs

Communication Services-APIs sind unter [docs.microsoft.com](/rest/api/azure/) neben anderen Azure-REST-APIs dokumentiert. Diese Dokumentation erläutert die Strukturierung Ihrer HTTP-Nachrichten und bietet eine Anleitung zur Verwendung von Postman. Diese Dokumentation wird auch im Swagger-Format auf [GitHub](https://github.com/Azure/azure-rest-api-specs) angeboten.

## <a name="additional-support-details"></a>Weitere Details zur Unterstützung

### <a name="ios-and-android-support-details"></a>Details zur Unterstützung von iOS und Android

- Communication Services-Clientbibliotheken für iOS sind für iOS ab Version 13 und Xcode ab Version 11 vorgesehen.
- Java-Clientbibliotheken für Android sind für die Android-API ab Ebene 21 und für Android Studio ab Version 4.0 vorgesehen.

### <a name="net-support-details"></a>Details zur Unterstützung von .NET

Mit Ausnahme der Bibliothek „Calling“ sind Communication Services-Pakete für die .NET Standard-Version 2.0 vorgesehen, die die nachstehend aufgeführten Plattformen unterstützt.

Unterstützung über .NET Framework 4.6.1
- Windows 10, 8.1, 8 und 7
- Windows Server 2012 R2, 2012 und 2008 R2 SP1

Unterstützung über .NET Core 2.0:
- Windows 10 (ab 1607), 7 SP1+, 8.1
- Windows Server 2008 R2 SP1+
- Mac OS X ab 10.12
- Mehrere Versionen/Distributionen von Linux
- UWP 10.0.16299 (RS3) September 2017
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

## <a name="api-stability-expectations"></a>Erwartungen an die API-Stabilität 

> [!IMPORTANT]
> Dieser Abschnitt enthält Anleitungen zu REST-APIs und Clientbibliotheken, die als **stabil** gekennzeichnet sind. APIs, die als Vorabversion, Vorschau oder Beta gekennzeichnet sind, können ohne Vorankündigung geändert oder als **veraltet eingestuft** werden. Azure Communication Services ist derzeit in der Phase **Public Preview**. APIs sind entsprechend markiert.

In Zukunft werden wir möglicherweise Versionen der Communication Services-Clientbibliotheken zurückziehen und möglicherweise Änderungen an unseren REST-APIs und veröffentlichten Clientbibliotheken vornehmen. Für Azure Communication Services gelten *allgemein* zwei Richtlinien zur Unterstützung des Zurückziehens von Dienstversionen:

- Sie werden mindestens drei Jahre vorher benachrichtigt, ehe Sie aufgrund einer Änderung einer Communication Services-Schnittstelle Code ändern müssen. Bei allen dokumentierten REST- und Clientbibliotheks-APIs wird in der Regel eine mindestens dreijährige Vorwarnzeit eingehalten, bevor Schnittstellen ausgemustert werden.
- Sie werden mindestens ein Jahr vorher benachrichtigt, ehe Sie Assemblys von Clientbibliotheken auf die neueste Nebenversion aktualisieren müssen. Diese erforderlichen Aktualisierungen sollten keine Codeänderungen erfordern, da sie zur gleichen Hauptversion gehören. Dies gilt insbesondere für die Bibliotheken „Calling“ und „Chat“, die über Echtzeitkomponenten verfügen, bei denen häufig Sicherheits- und Leistungsaktualisierungen erforderlich sind. Wir empfehlen Ihnen nachdrücklich, Ihre Communication Services-Clientbibliotheken auf dem neuesten Stand zu halten.

### <a name="api-and-client-library-decommissioning-examples"></a>Beispiele der Außerbetriebnahme von APIs und Clientbibliotheken

**Sie haben Version 24 der REST-API „SMS“ in Ihre Anwendung integriert. Azure Communication veröffentlicht Version 25.**

Sie erhalten eine Warnfrist von 3 Jahren, ehe diese APIs ihren Dienst einstellen, und Sie gezwungen sind, auf Version 25 zu aktualisieren. Dieses Update erfordert möglicherweise eine Codeänderung.

**Sie haben die Version 2.02 der Clientbibliothek „Calling“ in Ihre Anwendung integriert. Azure Communication veröffentlicht Version 2.05.**

Möglicherweise müssen Sie innerhalb von 12 Monaten nach Veröffentlichung von Version 2.05 ein Update auf diese Version der Clientbibliothek „Calling“ vornehmen. Dabei sollte es sich um einen einfachen Austausch des Artefakts handeln, ohne dass eine Codeänderung erforderlich ist, da Version 2.05 zur Hauptversion 2 gehört und keine Breaking Changes aufweist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Übersichten über Clientbibliotheken:

- [Übersicht über die Clientbibliothek „Calling“](../concepts/voice-video-calling/calling-sdk-features.md)
- [Übersicht über die Clientbibliothek „Chat“](../concepts/chat/sdk-features.md)
- [Übersicht über die Clientbibliothek „SMS“](../concepts/telephony-sms/sdk-features.md)

Für den Einstieg in Azure Communication Services:

- [Erstellen von Azure Communication-Ressourcen](../quickstarts/create-communication-resource.md)
- Generieren von [Benutzerzugrifftoken](../quickstarts/access-tokens.md)
