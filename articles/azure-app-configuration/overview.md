---
title: Was ist Azure App Configuration?
description: Lesen Sie eine Übersicht über den Dienst „Azure App Configuration“. Hier werden die Gründe für die Verwendung von App Configuration erläutert, und Sie erfahren, wie Sie den Dienst verwenden können.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 9786ff20c1d3f467e217d5fe34518fcc9494863e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930108"
---
# <a name="what-is-azure-app-configuration"></a>Was ist Azure App Configuration?

Azure App Configuration ist ein Dienst zur zentralen Verwaltung von Anwendungseinstellungen und Featureflags. Moderne Programme, vor allem in einer Cloud ausgeführte Programme, verfügen normalerweise über viele verteilte Komponenten. Das Versehen dieser Komponenten mit Konfigurationseinstellungen kann bei einer Anwendungsbereitstellung zu Fehlern führen, deren Behebung schwierig ist. Mit App Configuration können Sie alle Einstellungen für Ihre Anwendung speichern und den Zugriff darauf an einem zentralen Ort schützen.

## <a name="why-use-app-configuration"></a>Gründe für die Verwendung von App Configuration

Cloudbasierte Anwendungen werden häufig auf mehreren virtuellen Computern oder in Containern in mehreren Regionen ausgeführt, und es werden mehrere externe Dienste verwendet. Das Erstellen einer stabilen und skalierbaren Anwendung in einer verteilten Umgebung stellt eine große Herausforderung dar.

Entwickler greifen als Hilfe bei der immer komplexer werdenden Erstellung von Anwendungen auf verschiedene Programmiermethodiken zurück. Die [Twelve-Factor-App](https://12factor.net/) enthält beispielsweise viele sorgfältig getestete Architekturmuster und bewährte Methoden für die Verwendung mit Cloudanwendungen. Eine wichtige Empfehlung dieses Leitfadens lautet, dass Konfiguration und Code getrennt werden sollten. Die Konfigurationseinstellungen einer Anwendung sollten getrennt von der ausführbaren Datei vorliegen und aus der Runtimeumgebung oder einer externen Quelle eingelesen werden.

App Configuration kann zwar mit allen Anwendungen genutzt werden, eignet sich aber beispielsweise besonders für folgende Arten von Anwendungen:

* Microservices basierend auf Azure Kubernetes Service, Azure Service Fabric oder andere Container-Apps, die in einer oder mehreren geografischen Regionen bereitgestellt werden
* Serverlose Apps, z. B. Azure Functions oder andere ereignisgesteuerte zustandslose Compute-Apps
* Continuous Deployment-Pipeline

Mit App Configuration kommen Sie in den Genuss der folgenden Vorteile:

* Vollständig verwalteter Dienst, der in wenigen Minuten eingerichtet werden kann
* Flexible Schlüsseldarstellungen und -zuordnungen
* Kennzeichnung (Tagging) mit Bezeichnungen
* Point-in-Time-Wiedergabe von Einstellungen
* Dedizierte Benutzeroberfläche für die Verwaltung von Featureflags
* Vergleich von zwei Sätzen von Konfigurationen in benutzerdefinierten Dimensionen
* Erweiterte Sicherheit durch Identitäten, die per Azure verwaltet werden
* Verschlüsselung vertraulicher Informationen im Ruhezustand und während der Übertragung
* Native Integration mit beliebten Frameworks

App Configuration ergänzt die Lösung [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), die zum Speichern von Anwendungsgeheimnissen verwendet wird. Mit App Configuration können die folgenden Szenarien einfacher implementiert werden:

* Zentralisieren von Verwaltung und Verteilung hierarchischer Konfigurationsdaten für unterschiedliche Umgebungen und geografische Regionen
* Dynamische Änderung von Anwendungseinstellungen ohne erneute Bereitstellung oder Neustart einer Anwendung
* Steuern der Featureverfügbarkeit in Echtzeit

## <a name="use-app-configuration"></a>Verwendung von App Configuration

Die einfachste Möglichkeit zum Hinzufügen eines App Configuration-Speichers zu Ihrer Anwendung ist eine von Microsoft bereitgestellte Clientbibliothek. Die folgenden Methoden sind verfügbar, um je nach gewählter Sprache und Framework eine Verbindung mit Ihrer Anwendung herzustellen.

| Programmiersprache und Framework | Herstellen der Verbindung |
|---|---|
| .NET Core und ASP.NET Core | App Configuration-Anbieter für .NET Core |
| .NET Framework und ASP.NET | App Configuration-Generator für .NET |
| Java Spring | App Configuration-Client für Spring Cloud |
| Andere | App Configuration-REST-API |

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstartanleitung zu ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Schnellstartanleitung zu .NET Core](./quickstart-dotnet-core-app.md)
* [Schnellstartanleitung zu .NET Framework](./quickstart-dotnet-app.md)
* [Schnellstart zu Azure Functions](./quickstart-azure-functions-csharp.md)
* [Schnellstartanleitung zu Java Spring](./quickstart-java-spring-app.md)
* [Schnellstartanleitung zu ASP.NET Core-Featureflags](./quickstart-feature-flag-aspnet-core.md)
* [Schnellstartanleitung zu Spring Boot-Featureflags](./quickstart-feature-flag-spring-boot.md)
