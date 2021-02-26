---
title: Übersicht
description: Es wird beschrieben, wie Azure App Service Sie beim Entwickeln und Hosten von Webanwendungen unterstützt.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 668988ae34c2f97f3baca3f360c13e3ec3e30731
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586344"
---
# <a name="app-service-overview"></a>App Service: Übersicht

*Azure App Service* ist ein HTTP-basierter Dienst zum Hosten von Webanwendungen, REST-APIs und mobilen Back-Ends. Sie können in Ihrer bevorzugten Sprache entwickeln, z.B. .NET, .NET Core, Java, Ruby, Node.js, PHP oder Python. Anwendungen können mühelos in Windows- und [Linux](#app-service-on-linux)-basierten Umgebungen ausgeführt und skaliert werden.

App Service ergänzt Ihre Anwendung nicht nur um die Leistungsfähigkeit von Microsoft Azure, z.B. Sicherheit, Lastenausgleich, automatische Skalierung und automatisierte Verwaltung. Sie können auch die Vorteile ihrer DevOps-Funktionen nutzen, z. B. Continuous Deployment über Azure DevOps, GitHub, Docker Hub und andere Quellen, Paketverwaltung, Stagingumgebung, benutzerdefinierte Domäne und TLS-/SSL-Zertifikate. 

Mit App Service zahlen Sie nur für die Azure-Computeressourcen, die Sie verwenden. Die Computeressourcen, die Sie verwenden, richten sich nach dem _App Service-Plan_, nach dem Sie Ihre Apps ausführen. Weitere Informationen hierzu finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Gründe für die Verwendung von App Service

Hier sind einige wichtige Features von App Service aufgeführt:

* **Mehrere Sprachen und Frameworks**: App Service bietet erstklassige Unterstützung für ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP oder Python. Sie können [PowerShell und weitere Skripts oder ausführbare Dateien](webjobs-create.md) auch als Hintergrunddienst ausführen.
* **Verwaltete Produktionsumgebung:** App Service [patcht und wartet die Betriebssystem- und Sprachframeworks](overview-patch-os-runtime.md) automatisch für Sie. Konzentrieren Sie sich auf das Schreiben erstklassiger Apps, Azure kümmert sich um die Plattform.
* **Containerisierung und Docker**: Dockerisieren Sie Ihre App, und hosten Sie einen benutzerdefinierten Windows- oder Linux-Container in App Service. Führen Sie mit Docker Compose Apps mit mehreren Containern aus. Übertragen Sie Ihr Docker-Know-how direkt auf App Service.
* **DevOps-Optimierung**: Richten Sie mit Azure DevOps, GitHub, BitBucket, Docker Hub oder Azure Container Registry die [fortlaufende Integration und Bereitstellung](deploy-continuous-deployment.md) ein. Stufen Sie Updates über [Test- und Stagingumgebungen](deploy-staging-slots.md)herauf. Verwalten Sie Ihre Apps in App Service mithilfe von [Azure PowerShell](/powershell/azure/) oder der [plattformübergreifenden Befehlszeilenschnittstelle (Command-Line Interface, CLI)](/cli/azure/install-azure-cli).
* **Globale Skalierung mit Hochverfügbarkeit** – Skalieren Sie manuell oder automatisch [zentral](manage-scale-up.md) oder [horizontal](../azure-monitor/autoscale/autoscale-get-started.md) hoch. Hosten Sie Ihre Apps überall in der globalen Rechenzentrumsinfrastruktur von Microsoft. Die App Service-[SLA](https://azure.microsoft.com/support/legal/sla/app-service/) garantiert Hochverfügbarkeit.
* **Verbindungen mit SaaS-Plattformen und lokalen Daten**: Wählen Sie aus über 50 [Connectors](../connectors/apis-list.md) für Unternehmenssysteme (z.B. SAP), SaaS-Dienste (z.B. Salesforce) sowie Internetdienste (z.B. Facebook). Greifen Sie über [Hybridverbindungen](app-service-hybrid-connections.md) und [Azure Virtual Networks](web-sites-integrate-with-vnet.md) auf lokale Daten zu.
* **Sicherheit und Compliance** – App Service ist [ISO-, SOC- und PCI-konform](https://www.microsoft.com/en-us/trustcenter). Authentifizieren Sie Benutzer per [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) oder [Microsoft-Konto](configure-authentication-provider-microsoft.md). Erstellen Sie [IP-Adresseinschränkungen](app-service-ip-restrictions.md) und [verwalten Sie Dienstidentitäten](overview-managed-identity.md).
* **Anwendungsvorlagen**: Wählen Sie im [Azure Marketplace](https://azure.microsoft.com/marketplace/) aus zahlreichen Anwendungsvorlagen wie WordPress, Joomla und Drupal.
* **Visual Studio- und Visual Studio Code-Integration:** Dedizierte Tools in Visual Studio und Visual Studio Code optimieren das Erstellen, Bereitstellen und Debuggen.
* **API und mobile Features**: App Service bietet sofort einsetzbare CORS-Unterstützung für RESTful-API-Szenarien und vereinfacht Szenarien mit mobilen Apps durch Aktivierung von Authentifizierung, Offlinedatensynchronisierung, Pushbenachrichtigungen und mehr.
* **Serverloser Code**: Führen Sie einen Codeausschnitt oder ein Skript bei Bedarf aus, ohne explizit eine Infrastruktur bereitstellen oder verwalten zu müssen, und zahlen Sie nur für die Rechenzeit, die für Ihren Code tatsächlich erforderlich ist (siehe [Dokumentation zu Azure Functions](../azure-functions/index.yml)).

Neben App Service bietet Azure noch andere Dienste an, die zum Hosten von Websites und Webanwendungen verwendet werden können. Für die meisten Szenarien ist App Service aber die beste Wahl.  Informationen zur Microservicearchitektur finden Sie in der [Azure Spring Cloud-Dokumentation](../spring-cloud/index.yml) oder [Azure Service Fabric-Dokumentation](https://azure.microsoft.com/documentation/services/service-fabric).  Wenn Sie eine bessere Kontrolle über die virtuellen Computer benötigen, auf denen Ihr Code ausgeführt wird, können Sie [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) einsetzen. Weitere Informationen zur Wahl zwischen diesen Azure-Diensten finden Sie unter [Azure App Service, Azure Cloud Services, Azure Virtual Machines und Azure Service Fabric im Vergleich](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>App Service unter Linux

Von App Service können Web-Apps für unterstützte Anwendungsstapel auch nativ unter Linux gehostet werden. Darüber hinaus können benutzerdefinierte Linux-Container ausgeführt werden. (Diese werden auch als Web-App für Container bezeichnet.)

### <a name="built-in-languages-and-frameworks"></a>Integrierte Sprachen und Frameworks

Von App Service für Linux wird eine Reihe von sprachspezifischen integrierten Images unterstützt. Stellen Sie einfach Ihren Code bereit. Unterstützte Sprachen: Node.js, Java (JRE 8 und JRE 11), PHP, Python, .NET Core und Ruby. Führen Sie [`az webapp list-runtimes --linux`](/cli/azure/webapp#az-webapp-list-runtimes) aus, um die neuesten Sprachen und unterstützten Versionen anzuzeigen. Sollte die für Ihre Anwendung erforderliche Runtime in den integrierten Images nicht unterstützt werden, können Sie sie mit einem benutzerdefinierten Container bereitstellen.

Veraltete Runtimes werden in regelmäßigen Abständen von den Blättern zum Erstellen und Konfigurieren von Web-Apps im Portal entfernt. Diese Runtimes werden im Portal ausgeblendet, wenn sie von der für die Wartung zuständigen Organisation als veraltet eingestuft werden oder wenn bedeutende Sicherheitsrisiken dafür ermittelt wurden. Diese Optionen werden ausgeblendet, damit Kunden zu den neuesten Runtimes gelangen, mit denen sie am besten arbeiten können. 

Wenn eine veraltete Runtime im Portal ausgeblendet wird, werden alle vorhandenen Websites, die diese Version verwenden, weiterhin ausgeführt. Wird eine Runtime vollständig von der App Service-Plattform entfernt, erhalten die Azure-Abonnementbesitzer vor dem Entfernen eine E-Mail-Benachrichtigung.

Wenn Sie eine weitere Web-App mit einer veralteten Runtimeversion erstellen müssen, die nicht mehr im Portal angezeigt wird, finden Sie in den Anleitungen zur Sprachkonfiguration Anweisungen dazu, wie Sie die Runtimeversion Ihrer Website erhalten. Sie können die Azure CLI verwenden, um eine andere Website mit derselben Runtime zu erstellen. Alternativ dazu können Sie im Portal auf dem Blatt „Web-App“ die Schaltfläche **Vorlage exportieren** verwenden, um eine ARM-Vorlage der Website zu exportieren. Sie können diese Vorlage wiederverwenden, um eine neue Website mit derselben Runtime und Konfiguration bereitzustellen.

### <a name="limitations"></a>Einschränkungen

- App Service für Linux wird im Tarif [Shared](https://azure.microsoft.com/pricing/details/app-service/plans/) nicht unterstützt. 
- Windows- und Linux-Apps können nicht im gleichen App Service-Plan miteinander kombiniert werden.  
- In der Vergangenheit konnten Windows- und Linux-Apps nicht in der gleichen Ressourcengruppe miteinander kombiniert werden. Allerdings unterstützen alle Ressourcengruppen, die am oder nach dem 21. Januar 2021 erstellt wurden, dieses Szenario. Für Ressourcengruppen, die vor dem 21. Januar 2021 erstellt wurden, wird die Möglichkeit zum Hinzufügen gemischter Plattformbereitstellungen in Azure-Regionen (einschließlich nationaler Cloudregionen) in Kürze eingeführt.
- Im Azure-Portal werden nur Features angezeigt, die aktuell für Linux-Apps geeignet sind. Wenn neue Features hinzukommen, werden sie im Portal aktiviert.
- Bei der Bereitstellung in integrierten Images wird Ihrem Code und Ihren Inhalten ein Azure Storage-basiertes Speichervolume für Webinhalte zugeordnet. Die Datenträgerwartezeit dieses Volumes ist höher und variabler als die Wartezeit des Containerdateisystems. Für Apps, die intensiven Lesezugriff auf Inhaltsdateien erfordern, ist unter Umständen die benutzerdefinierte Containeroption von Vorteil, da die Dateien hierbei im Containerdateisystem platziert werden und nicht auf dem Inhaltsvolume.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie Ihre erste Web-App.

> [!div class="nextstepaction"]
> [ASP.NET Core (unter Windows oder Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (unter Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (unter Windows oder Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (unter Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (unter Windows oder Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (unter Windows oder Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (unter Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (unter Windows oder Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Benutzerdefinierter Container (Windows oder Linux)](tutorial-custom-container.md)
