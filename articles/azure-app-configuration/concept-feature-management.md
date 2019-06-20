---
title: Featureverwaltung in Azure App Configuration | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht darüber, wie Azure App Configuration zum bedarfsbasierten Aktivieren und Deaktivieren von Anwendungsfeatures verwendet werden kann.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 46f39e87e4e4cf115cbc1fceeabf0dab38fade28
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393358"
---
# <a name="feature-management-overview"></a>Übersicht über die Featureverwaltung

Traditionell erfordert die Auslieferung eines neuen Anwendungsfeatures eine vollständige erneute Bereitstellung der Anwendung selbst. Zum Testen eines Features müssen Sie Ihre Anwendung wahrscheinlich mehrmals bereitstellen, um zu steuern, wann oder wem das Feature angezeigt wird.

Die Featureverwaltung ist ein modernes Verfahren der Softwareentwicklung, das die Freigabe von Features von der Codebereitstellung entkoppelt und schnelle bedarfsbasierte Änderungen an der Featureverfügbarkeit ermöglicht. Dabei wird eine Technik namens *Featureflag* (auch als *Feature Toggle*, *Featureschalter* usw. bezeichnet) verwendet, um den Lebenszyklus eines Features dynamisch zu verwalten.

Die Featureverwaltung hilft Entwicklern bei der Lösung der folgenden Probleme:

* **Verwaltung von Codebranches**: Featureflags werden häufig verwendet, um neue Anwendungsfunktionen zu umschließen, die sich in der Entwicklung befinden. Solche Funktionalität ist standardmäßig ausgeblendet. Sie können das Feature sicher ausliefern, auch wenn es noch nicht vollendet ist, da es in Produktionsumgebungen inaktiv bleibt. Mit diesem Ansatz, der als *Dark Deployment* bezeichnet wird, können Sie Ihren gesamten Code am Ende jedes Entwicklungszyklus freigeben. Sie müssen keine Codebranches mehr über mehrere Zyklen hinweg verwalten, wenn die Fertigstellung eines Features mehrere Zyklen in Anspruch nimmt.
* **Testen in der Produktion**: Sie können Featureflags verwenden, um Vorabzugriff auf neue Features in der Produktion zu ermöglichen. Sie können diesen Zugriff beispielsweise auf Ihre Teammitglieder oder interne Betatester beschränken. Diese Benutzer erhalten die originalgetreue Produktionsoberfläche anstelle einer Simulations- oder Teiloberfläche in einer Testumgebung.
* **Erstellen von Flights**: Sie können Featureflags auch verwenden, um neue Funktionen schrittweise für Endbenutzer einzuführen. Sie können zunächst einen kleinen Prozentsatz Ihrer Benutzer als Zielgruppe verwenden und diesen Prozentsatz im Laufe der Zeit schrittweise erhöhen, nachdem Sie mehr Vertrauen in die Implementierung gewonnen haben.
* **Schalter zum sofortigen Beenden**: Featureflags bieten ein inhärentes Sicherheitsnetz für die Freigabe neuer Features. Damit können Sie Anwendungsfeatures ganz einfach aktivieren und deaktivieren. Bei Bedarf können Sie ein Feature schnell deaktivieren, ohne die gesamte Anwendung erneut erstellen und bereitstellen zu müssen.
* **Selektive Aktivierung**: Die meisten Featureflags existieren zwar nur, bis ihre zugehörigen Funktionalitäten erfolgreich freigegeben wurden, einige können jedoch lange vorhanden sein. Mithilfe von Featureflags können Sie Benutzer gruppieren und jeder Benutzergruppe einen bestimmten Satz von Features zur Verfügung stellen. Ein Beispiel: Sie verfügen über ein Feature, das nur in einem bestimmten Webbrowser funktioniert. Sie können ein Featureflag so definieren, dass es nur von Benutzern des entsprechenden Browsers gesehen und genutzt werden kann. Mit diesem Ansatz können Sie die Liste der unterstützten Browser später problemlos erweitern, ohne den Code ändern zu müssen.

## <a name="basic-concepts"></a>Grundlegende Konzepte

Im Folgenden werden einige neue Begriffe im Zusammenhang mit der Featureverwaltung erläutert:

* **Featureflag**: Ein Featureflag ist eine Variable mit dem Binärzustand *ein* oder *aus*. Das Featureflag weist auch einen zugehörigen Codeblock auf. Der Zustand des Featureflags wird ausgelöst, unabhängig davon, ob der Codeblock ausgeführt wird oder nicht.
* **Feature-Manager**: Ein Feature-Manager ist ein Anwendungspaket, das den Lebenszyklus aller Featureflags in einer Anwendung verwaltet. Ein Feature-Manager bietet in der Regel zusätzliche Funktionen, wie z.B. die Zwischenspeicherung von Featureflags und die Aktualisierung ihrer Zustände.
* **Filter**: Ein Filter ist eine Regel für das Auswerten des Featureflag-Zustands. Eine Benutzergruppe, ein Geräte- oder Browsertyp, ein geografischer Standort und ein Zeitfenster sind Beispiele dafür, was ein Filter darstellen kann.

Um eine effektive Implementierung der Featureverwaltung zu erreichen, müssen mindestens zwei Komponenten zusammenarbeiten:

* Eine Anwendung, die Featureflags nutzt.
* Ein separates Repository, das die Featureflags und ihre aktuellen Zustände speichert.

Die Interaktion zwischen diesen Komponenten wird in den folgenden Beispielen veranschaulicht.

## <a name="feature-flag-usage-in-code"></a>Verwendung von Featureflags in Code

Das Grundmuster für die Implementierung von Featureflags in einer Anwendung ist einfach. Sie können sich ein Featureflag als boolesche Zustandsvariable vorstellen, die mit einer `if`-Bedingungsanweisung in Ihrem Code verwendet wird:

```csharp
if (featureFlag) {
    // Run the following code
}
```

In diesem Fall gilt: Wenn `featureFlag` auf `True` festgelegt ist, wird der eingeschlossene Codeblock ausgeführt, andernfalls wird er übersprungen. Sie können den Wert von `featureFlag` statisch festlegen, wie im folgenden Codebeispiel gezeigt:

```csharp
bool featureFlag = true;
```

Sie können den Zustand des Flags auch basierend auf bestimmten Regeln auswerten:

```csharp
bool featureFlag = isBetaUser();
```

Ein etwas komplizierteres Featureflagmuster beinhaltet auch eine `else`-Anweisung:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Sie können dieses Verhalten jedoch auch im grundlegenden Muster erneut generieren. Unter [Verwenden von Featureflags in einer ASP.NET Core-App](./use-feature-flags-dotnet-core.md) werden die Vorteile der Standardisierung auf ein einfaches Codemuster aufgezeigt. Beispiel:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Featureflag-Repository

Sie müssen alle in einer Anwendung genutzten Featureflags extern verwalten, um sie effektiv nutzen zu können. So können Sie den Zustand von Featureflags ändern, ohne die Anwendung selbst ändern und neu bereitstellen zu müssen.

Azure App Configuration ist als zentrales Repository für Featureflags konzipiert. Sie können damit verschiedene Arten von Featureflags definieren und deren Zustände schnell und sicher ändern. Dann können Sie die App Configuration-Bibliotheken für verschiedene Programmiersprachen-Frameworks verwenden, um in Ihrer Anwendung problemlos auf diese Featureflags zuzugreifen.

In [Verwenden von Featureflags in einer ASP.NET Core-App](./use-feature-flags-dotnet-core.md) wird veranschaulicht, wie der .NET Core App Configuration-Anbieter und die Bibliotheken der Featureverwaltung zusammen zur Implementierung von Featureflags für Ihre ASP.NET-Webanwendung verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen von Featureflags zu einer ASP.NET Core-Web-App](./quickstart-feature-flag-aspnet-core.md)  
