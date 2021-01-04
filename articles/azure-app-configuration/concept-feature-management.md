---
title: Informationen zur Featureverwaltung mithilfe von Azure App Configuration
description: Aktivieren und Deaktivieren von Features mithilfe von Azure App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: cfd676092bcaede58909a3ec1eefeabb4c80f86b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930227"
---
# <a name="feature-management-overview"></a>Übersicht über die Featureverwaltung

Traditionell erfordert die Auslieferung eines neuen Anwendungsfeatures eine vollständige erneute Bereitstellung der Anwendung selbst. Zum Testen eines Features sind häufig mehrere Bereitstellungen der Anwendung erforderlich.  Jede Bereitstellung kann das Feature ändern oder verschiedenen Kunden zu Testzwecken verfügbar machen.  

Die Featureverwaltung ist ein modernes Verfahren der Softwareentwicklung, das die Freigabe von Features von der Codebereitstellung entkoppelt und schnelle bedarfsbasierte Änderungen an der Featureverfügbarkeit ermöglicht. Dabei wird eine Technik namens *Featureflag* (auch als *Feature Toggle*, *Featureschalter* usw. bezeichnet) verwendet, um den Lebenszyklus eines Features dynamisch zu verwalten.

Die Featureverwaltung hilft Entwicklern bei der Behandlung der folgenden Probleme:

* **Verwaltung von Codebranches**: Verwenden Sie Featureflags, um neue Anwendungsfunktionen zu umschließen, die sich derzeit in der Entwicklung befinden. Solche Funktionalität ist standardmäßig ausgeblendet. Sie können das Feature sicher ausliefern, auch wenn es noch nicht vollendet ist, da es in Produktionsumgebungen inaktiv bleibt. Mit diesem Ansatz, der als *dunkle Bereitstellung* bezeichnet wird, können Sie Ihren gesamten Code am Ende jedes Entwicklungszyklus freigeben. Sie müssen Codebranches nicht mehr über mehrere Entwicklungszyklen hinweg beibehalten, da ein bestimmtes Feature zum Abschließen mehrere Zyklen benötigt.
* **Testen in der Produktion**: Verwenden Sie Featureflags, um Vorabzugriff auf neue Features in der Produktion zu ermöglichen. Sie können den Zugriff beispielsweise auf Teammitglieder oder interne Betatester beschränken. Diese Benutzer erhalten die originalgetreue Produktionsoberfläche anstelle einer Simulations- oder Teiloberfläche in einer Testumgebung.
* **Erstellen von Flights**: Verwenden Sie Featureflags, um neue Funktionen nach und nach für Endbenutzer einzuführen. Sie können zunächst einen kleinen Anteil Ihrer Benutzer als Zielgruppe verwenden und diesen Anteil im Lauf der Zeit nach und nach erhöhen.
* **Schalter zum sofortigen Beenden**: Featureflags bieten ein inhärentes Sicherheitsnetz für die Freigabe neuer Features. Sie können Anwendungsfeatures aktivieren und deaktivieren, ohne Code erneut bereitzustellen. Bei Bedarf können Sie ein Feature schnell deaktivieren, ohne die gesamte Anwendung erneut erstellen und bereitstellen zu müssen.
* **Selektive Aktivierung**: Mithilfe von Featureflags können Sie Benutzer gruppieren und jeder Benutzergruppe einen bestimmten Satz von Features zur Verfügung stellen. Möglicherweise verfügen Sie über ein Feature, das nur in einem bestimmten Webbrowser funktioniert. Sie können ein Featureflag so definieren, dass es nur von Benutzern des entsprechenden Browsers gesehen und genutzt werden kann. Mit diesem Ansatz können Sie die Liste der unterstützten Browser später problemlos erweitern, ohne den Code ändern zu müssen.

## <a name="basic-concepts"></a>Grundlegende Konzepte

Im Folgenden werden einige neue Begriffe im Zusammenhang mit der Featureverwaltung erläutert:

* **Featureflag**: Ein Featureflag ist eine Variable mit dem Binärzustand *ein* oder *aus*. Das Featureflag weist auch einen zugehörigen Codeblock auf. Der Zustand des Featureflags wird ausgelöst, wenn der Codeblock ausgeführt wird.
* **Feature-Manager**: Ein Feature-Manager ist ein Anwendungspaket, das den Lebenszyklus aller Featureflags in einer Anwendung verwaltet. Der Feature-Manager bietet auch zusätzliche Funktionen wie Zwischenspeicherung von Featureflags und Aktualisierung ihrer Zustände.
* **Filter**: Ein Filter ist eine Regel für das Auswerten des Featureflag-Zustands. Mögliche Filter sind etwa Benutzergruppen, Geräte- oder Browsertypen, geografische Standorte und Zeitfenster.

Um eine effektive Implementierung der Featureverwaltung zu erreichen, müssen mindestens zwei Komponenten zusammenarbeiten:

* Eine Anwendung, die Featureflags nutzt.
* Ein separates Repository, das die Featureflags und ihre aktuellen Zustände speichert.

## <a name="using-feature-flags-in-your-code"></a>Verwenden von Featureflags in Ihrem Code

Das Grundmuster für die Implementierung von Featureflags in einer Anwendung ist einfach. Ein Featureflag ist eine boolesche Zustandsvariable, die eine Bedingungsanweisung in Ihrem Code steuert:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Sie können den Wert von `featureFlag` statisch festlegen.

```csharp
bool featureFlag = true;
```

Sie können den Zustand des Flags basierend auf bestimmten Regeln auswerten:

```csharp
bool featureFlag = isBetaUser();
```

Sie können die Bedingung erweitern, um das Anwendungsverhalten für beide Zustände festzulegen:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Featureflag-Repository

Sie müssen alle in einer Anwendung genutzten Featureflags extern verwalten, um sie effektiv nutzen zu können. Dadurch können Sie den Zustand von Featureflags ändern, ohne die Anwendung selbst ändern und neu bereitstellen zu müssen.

Azure App Configuration bietet ein zentrales Repository für Featureflags. Sie können damit verschiedene Arten von Featureflags definieren und deren Zustände schnell und sicher ändern. Dann können Sie die App Configuration-Bibliotheken für verschiedene Programmiersprachen-Frameworks verwenden, um in Ihrer Anwendung problemlos auf diese Featureflags zuzugreifen.

In [Verwenden von Featureflags in einer ASP.NET Core-App](./use-feature-flags-dotnet-core.md) wird veranschaulicht, wie der .NET Core App Configuration-Anbieter und die Bibliotheken der Featureverwaltung zusammen zur Implementierung von Featureflags für Ihre ASP.NET-Webanwendung verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen von Featureflags zu einer ASP.NET Core-Web-App](./quickstart-feature-flag-aspnet-core.md)  
