---
title: Nächste Schritte bei der Service Fabric-Projekterstellung
description: Erfahren Sie mehr über das Anwendungsprojekt, das Sie gerade in Visual Studio erstellt haben.  Erfahren Sie, wie Sie mithilfe von Tutorials Dienste erstellen können, und erfahren Sie mehr über die Entwicklung von Diensten für Service Fabric.
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4d162918644727d4c79ad606f1ed34816f543d81
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044378"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Ihre Service Fabric-Anwendung und nächste Schritte
Ihre Azure Service Fabric-Anwendung wurde erstellt. Dieser Artikel enthält eine Reihe von Ressourcen, einige weitere Informationen, die möglicherweise von Interesse sind, und die möglichen [nächsten Schritte](#next-steps).

Neue Benutzer finden [Tutorials, exemplarische Vorgehensweisen und Beispiele](#get-started-with-tutorials-walk-throughs-and-samples) möglicherweise hilfreich. Es kann auch hilfreich sein, die [Struktur des erstellten Anwendungsprojekts](#the-application-project) zu überprüfen. Außerdem sind Beschreibungen von [Programmiermodellen](#learn-more-about-the-programming-models), [Dienstkommunikation](#learn-about-service-communication), [Anwendungssicherheit](#learn-about-configuring-application-security) und [Anwendungslebenszyklus](#learn-about-the-application-lifecycle) von Service Fabric enthalten.

Erfahrenere Benutzer finden den Abschnitt zu [bewährten Methoden](#learn-about-best-practices) in Service Fabric sicher nützlich, weil sie dort lernen, die Plattform- und Strukturanwendungen mit maximaler Effizienz zu nutzen.

Nutzen Sie für Fragen, Feedback oder Meldung eines Problems die Informationen im [entsprechenden Abschnitt](#have-questions-or-feedback--need-to-report-an-issue).

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Erste Schritte mit Tutorials, exemplarischen Vorgehensweisen und Beispielen
Wollen Sie loslegen?  

Arbeiten Sie sich durch das Tutorial zur .NET-Anwendung. Erfahren Sie, wie Sie [eine App](service-fabric-tutorial-create-dotnet-app.md) mit einem ASP.NET Core-Front-End und einem zustandsbehafteten Back-End erstellen, [die Anwendung](service-fabric-tutorial-deploy-app-to-party-cluster.md) auf einen Cluster bereitstellen, [CI/CD konfigurieren](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) und die [Überwachung und Diagnose](service-fabric-tutorial-monitoring-aspnet.md) einrichten.

Oder probieren Sie eine der folgenden exemplarischen Vorgehensweisen aus, und erstellen Sie Ihre(n) erste(n)...
- [Reliable Services-Dienst in C# unter Windows](service-fabric-reliable-services-quick-start.md) 
- [Reliable Actors-Dienst in C# unter Windows](service-fabric-reliable-actors-get-started.md) 
- [Ausführbaren Gastdienst unter Windows](quickstart-guest-app.md) 
- [Windows-Containeranwendung](service-fabric-get-started-containers.md) 

Vielleicht sind Sie auch daran interessiert, unsere [Beispielanwendungen](/samples/browse/?products=azure) auszuprobieren.

## <a name="the-application-project"></a>Das Anwendungsprojekt
Jede neue Anwendung umfasst ein Anwendungsprojekt. Es gibt möglicherweise je nach gewähltem Diensttyp ein oder zwei zusätzliche Projekte.

Das Anwendungsprojekt besteht aus Folgendem:

* Ein Satz von Verweisen auf die Dienste, aus denen die Anwendung besteht.
* Drei Veröffentlichungsprofile („1-Node Local“, „5-Node Local“ und „Cloud“), die Sie zum Verwalten der Voreinstellungen für die Arbeit mit unterschiedlichen Umgebungen verwenden können – z.B. für den Clusterendpunkt und eine Einstellung, ob standardmäßig Upgradebereitstellungen ausgeführt werden sollen.
* Drei Anwendungsparameterdateien (wie oben), mit denen Sie umgebungsspezifische Anwendungskonfigurationen verwalten können, wie z.B. die Anzahl der Partitionen, die für einen Dienst erstellt werden sollen. Erfahren Sie, wie Sie [Ihre Anwendung für mehrere Umgebungen konfigurieren](service-fabric-manage-multiple-environment-app-configuration.md).
* einem Bereitstellungsskript, mit dem Sie Ihre Anwendung über die Befehlszeile oder als Teil einer automatisierten fortlaufenden Integrations- und Bereitstellungspipeline bereitstellen können Erfahren Sie mehr über das [Bereitstellen von Anwendungen mithilfe von PowerShell](service-fabric-deploy-remove-applications.md).
* das Anwendungsmanifest, das die Anwendung beschreibt Das Manifest finden Sie im Ordner „ApplicationPackageRoot“. Erfahren Sie mehr über [Anwendungs- und Dienstmanifeste](service-fabric-application-model.md).

## <a name="learn-more-about-the-programming-models"></a>Weitere Informationen zu den Programmiermodellen
Service Fabric bietet verschiedene Methoden zum Schreiben und Verwalten von Diensten.  Hier folgen eine Übersicht und konzeptionelle Informationen zu [zustandslosen und zustandsbehafteten Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [Containern](service-fabric-containers-overview.md), [ausführbaren Gastdateien](service-fabric-guest-executables-introduction.md) und [zustandslosen und zustandsbehafteten ASP.NET Core-Diensten](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Weitere Informationen zur Dienstkommunikation
Eine Service Fabric-Anwendung besteht aus verschiedenen Diensten, die jeweils eine bestimmte Aufgabe ausführen. Diese Dienste können miteinander kommunizieren und es kann Clientanwendungen außerhalb des Clusters geben, die sich mit den Diensten verbinden und mit ihnen kommunizieren. Erfahren Sie, wie die [Kommunikation mit und zwischen Ihren Diensten](service-fabric-connect-and-communicate-with-services.md) in Service Fabric eingerichtet wird. 

## <a name="learn-about-configuring-application-security"></a>Weitere Informationen zum Konfigurieren der Anwendungssicherheit
Sie können Anwendungen sichern, die im Cluster unter verschiedenen Benutzerkonten ausgeführt werden. Mit Service Fabric werden auch die Ressourcen gesichert, die von Anwendungen zum Zeitpunkt der Bereitstellung in den Benutzerkonten genutzt werden, z.B. Dateien, Verzeichnisse und Zertifikate. So lässt sich erreichen, dass ausgeführte Anwendungen auch in einer gemeinsamen gehosteten Umgebung sicher voneinander abgegrenzt sind.  Erfahren Sie, wie Sie [Sicherheitsrichtlinien für Ihre Anwendung konfigurieren](service-fabric-application-runas-security.md).

Ihre Anwendung kann vertrauliche Informationen (z. B. Speicherverbindungszeichenfolgen, Kennwörter oder andere Werte, die nicht als Nur-Text verarbeitet werden sollen) enthalten. Erfahren Sie, wie Sie [geheime Schlüssel in der Anwendung verwalten](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Weitere Informationen zum Anwendungslebenszyklus
Ähnlich wie auf anderen Plattformen durchläuft eine Service Fabric-Anwendung normalerweise die folgenden Phasen: Entwurf, Entwicklung, Test, Bereitstellung, Upgrade, Wartung und Deinstallation. [Dieser Artikel](service-fabric-application-lifecycle.md) bietet eine Übersicht über die APIs und wie sie von den verschiedenen Rollen während der Phasen des Service Fabric-Anwendungslebenszyklus verwendet werden.

## <a name="learn-about-best-practices"></a>Erfahren Sie mehr über bewährte Methoden
Es gibt eine Reihe von Artikeln zu Service Fabric, in denen [bewährten Methoden](./service-fabric-best-practices-security.md) beschrieben werden. Nutzen Sie diese Informationen, um sicherzustellen, dass Ihr Cluster und Ihre Anwendung so gut wie möglich ausgeführt werden.
Folgende Themen werden behandelt:
* [Security](./service-fabric-best-practices-security.md)
* [Netzwerk](./service-fabric-best-practices-networking.md)
* [Computeplanung und -skalierung](./service-fabric-best-practices-capacity-scaling.md)
* [Infrastructure-as-Code](./service-fabric-best-practices-infrastructure-as-code.md)
* [Überwachung und Diagnose](./service-fabric-best-practices-monitoring.md)
* [Anwendungsentwurf](./service-fabric-best-practices-applications.md)

Ebenfalls enthalten ist eine [Prüfliste für die Produktionsbereitschaft](./service-fabric-production-readiness-checklist.md), in der alle bewährten Methoden in einem leicht verwendbaren Format integriert werden.

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Haben Sie Fragen oder Feedback?  Möchten Sie ein Problem melden?
Lesen Sie die [häufig gestellte Fragen](service-fabric-common-questions.md) und finden Sie Antworten zu den Funktionen und zur Verwendung von Service Fabric.

[Leitfäden zur Problembehandlung](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) können hilfreich sein, um häufige Probleme in Service Fabric-Clustern zu diagnostizieren und zu lösen.

[Supportoptionen](service-fabric-support.md) listet Foren unter StackOverflow und MSDN auf, in denen Fragen gestellt werden, sowie Optionen für das Melden von Problemen, das Erhalten von Support und das Übermitteln von Produktfeedback.


## <a name="next-steps"></a>Nächste Schritte
- [Erstellen eines Windows-Clusters in Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- Visualisieren Ihres Clusters, einschließlich der bereitgestellten Anwendungen und des physischen Layouts, mit [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
- [Versionierung und Upgrade Ihrer Dienste](service-fabric-application-upgrade-tutorial.md)