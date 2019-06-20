---
title: Handbuch für Entwickler zu den ersten Schritten in Azure | Microsoft-Dokumentation
description: Dieses Thema enthält wichtige Informationen für Entwickler, die mit der Microsoft Azure-Plattform für Ihre Entwicklungsbedürfnisse beginnen möchten.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: 99e043adeac9a43432fb1eba85527b561c477354
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64570552"
---
# <a name="get-started-guide-for-azure-developers"></a>Anleitung mit den ersten Schritten für Azure-Entwickler

## <a name="what-is-azure"></a>Was ist Azure?

Azure ist eine umfassende Cloudplattform, die Ihre vorhandenen Anwendungen hosten, die Entwicklung neuer Anwendungen optimieren und auch lokale Anwendungen verbessern kann. Azure integriert die Clouddienste, die Sie zum Entwickeln, Testen, Bereitstellen und Verwalten Ihrer Anwendungen benötigen – und gleichzeitig wird die Effizienz des Cloud Computing genutzt.

Durch Hosten Ihrer Anwendungen in Azure können Sie klein anfangen und Ihre Anwendungen einfach horizontal hochskalieren, wenn die Kundennachfrage steigt. Azure bietet zudem die Zuverlässigkeit, die für Anwendungen mit hoher Verfügbarkeit benötigt wird, einschließlich Failover zwischen unterschiedlichen Regionen. Mit dem [Azure-Portal](https://portal.azure.com) können Sie alle Ihre Azure-Dienste einfach verwalten. Sie können Ihre Dienste auch programmgesteuert mithilfe dienstspezifischer APIs und Vorlagen verwalten.

**Zielgruppe dieses Artikels**: Dieses Handbuch ist eine Einführung in die Azure-Plattform für Anwendungsentwickler. Es bietet die Unterstützung und Anleitung, die Sie benötigen, um mit dem Erstellen neuer Anwendungen in Azure oder dem Migrieren vorhandener Anwendungen zu Azure zu beginnen.

## <a name="where-do-i-start"></a>Wo beginne ich?

Mit der Vielzahl von Diensten, die Azure bietet, kann es eine schwierige Aufgabe sein, herauszufinden, welche Dienste Sie für die Unterstützung Ihrer Lösungsarchitektur benötigen. In diesem Abschnitt werden die Azure-Dienste hervorgehoben, die von Entwicklern am häufigsten genutzt werden. Eine Liste aller Azure-Dienste finden Sie in der [Azure-Dokumentation](../../index.md).

Zunächst müssen Sie sich entscheiden, wie Sie Ihre Anwendung in Azure hosten wollen. Müssen Sie Ihre gesamte Infrastruktur als virtuellen Computer (VM) verwalten? Können Sie die Verwaltungseinrichtungen der Plattform verwenden, die von Azure bereitgestellt werden? Benötigen Sie möglicherweise ein serverloses Framework, um nur die Codeausführung zu hosten?

Ihre Anwendung benötigt Cloudspeicher, für den Azure mehrere Optionen bereitstellt. Sie können die Unternehmensauthentifizierung von Azure nutzen. Es gibt auch Tools für die cloudbasierte Entwicklung und Überwachung, und die meisten Dienste zum Hosen bieten DevOps-Integration.

Sehen wir uns nun einige bestimmte Dienste an, die Sie für Ihre Anwendungen untersuchen sollten.

### <a name="application-hosting"></a>Anwendungshosting

Azure stellt mehrere cloudbasierte Computerangebote bereit, mit denen Sie Ihre Anwendung ausführen können. Sie müssen sich also keine Sorgen über Infrastrukturdetails machen. Sie können Ihre Ressourcen bequem zentral oder horizontal hochskalieren, sobald Ihre Anwendungsnutzung steigt.

Azure bietet Dienste, die Ihre Anforderungen für Anwendungsentwicklung und Hosting unterstützen. Mit den Infrastructure-as-a-Service-Angeboten (IaaS) von Azure können Sie das Hosting Ihrer Anwendungen vollständig selbst steuern. Die Platform-as-a-Service-Angebote (PaaS) von Azure bieten Ihnen vollständig verwaltete Dienste, die Sie zur Unterstützung Ihrer Apps benötigen. Es gibt sogar die Möglichkeit zum serverlosen Hosten in Azure, wo Sie nur Ihren Code schreiben müssen.

![Optionen zum Anwendungshosting in Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Wenn Sie den schnellsten Weg zum Veröffentlichen Ihrer webbasierten Projekte wählen möchten, sehen Sie sich Azure App Service an. App Service erleichtert das Erweitern Ihrer Web-Apps zur Unterstützung Ihrer mobilen Clients und das Veröffentlichen einfach genutzter REST-APIs. Diese Plattform bietet die Authentifizierung durch soziale Netzwerke, auf Datenverkehr basierte automatische Skalierung, Tests in der Produktionsumgebung sowie kontinuierliche und containerbasierte Bereitstellung.

Sie können Web-Apps, Back-Ends für mobile Apps und API-Apps erstellen.

Da sich alle drei App-Typen die App Service-Runtime teilen, können Sie eine Website hosten, mobile Clients unterstützen und Ihre APIs in Azure verfügbar machen, und das alles vom gleichen Projekt oder der gleichen Lösung aus. Weitere Informationen zu App Service erhalten Sie unter [Was ist Azure Web-Apps?](../../app-service/overview.md).

App Service wurde mit Blick auf DevOps entworfen. App Service unterstützt verschiedene Tools für die Veröffentlichung und Continuous Integration-Bereitstellungen, einschließlich GitHub-Webhooks, Jenkins, Azure DevOps, TeamCity und andere.

Sie können Ihre vorhandenen Anwendungen zu App Service migrieren, indem Sie das [Onlinetool für die Migration](https://www.migratetoazure.net/) verwenden.

> **Einsatzgebiete**: Verwenden Sie App Service, wenn Sie vorhandene Webanwendungen zu Azure migrieren, und wenn Sie eine vollständig verwaltete Hostingplattform für Ihre Web-Apps benötigen. Sie können App Service auch verwenden, wenn Sie mobile Clients unterstützen oder REST-APIs mit Ihrer App verfügbar machen müssen.
> 
> **Erste Schritte**: App Service erleichtert die Erstellung und Bereitstellung Ihrer ersten [Web-App](../../app-service/app-service-web-get-started-dotnet.md), [mobilen App](../../app-service-mobile/app-service-mobile-ios-get-started.md) oder [API-App](../../app-service/app-service-web-tutorial-rest-api.md).
> 
> **Jetzt testen**: Mit App Service können Sie eine kurzlebige App bereitstellen, um die Plattform zu testen, ohne sich für ein Azure-Konto registrieren zu müssen. Testen Sie die Plattform und [erstellen Sie Ihre Azure App Service-App](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Als Infrastructure-as-a-Service-Anbieter (IaaS) ermöglicht Azure es Ihnen, Ihre Anwendung auf Windows- oder Linux-VMs bereitzustellen oder zu migrieren. Zusammen mit Azure Virtual Network unterstützt Azure Virtual Machines die Bereitstellung von Windows- oder Linux-VMs in Azure. Mit virtuellen Computern verfügen Sie über die vollständige Kontrolle über die Konfiguration des Computers. Wenn Sie VMs verwenden, sind Sie für die gesamte Serversoftwareinstallation, -konfiguration, -wartung und für Patches für das Betriebssystem verantwortlich.

Aufgrund dieses Maß an Kontrolle, über das Sie mit den VMs verfügen, können Sie eine Vielzahl von Serverworkloads auf Azure ausführen, die in kein PaaS-Modell passen. Diese Workloads enthalten Datenbankserver, Windows Server Active Directory und Microsoft SharePoint. Weitere Informationen finden Sie in der Dokumentation für Virtual Machines für [Linux](/azure/virtual-machines/linux/) oder [Windows](/azure/virtual-machines/windows/).

> **Einsatzgebiete**: Verwenden Sie virtuelle Computer, wenn Sie über die volle Kontrolle über Ihre Anwendungsinfrastruktur verfügen möchten oder lokale Anwendungsworkflows zu Azure migrieren möchten, ohne Änderungen vorzunehmen.
> 
> **Erste Schritte**: Erstellen Sie eine [Linux-VM](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) oder eine [Windows-VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) im Azure-Portal.

#### <a name="azure-functions-serverless"></a>Azure Functions (serverlos)

Sie müssen sich keine Sorgen über das Erstellen und Verwalten einer Anwendung oder der Infrastruktur machen, um Ihren Code auszuführen. Was wäre, wenn Sie einfach Ihren Code schreiben könnten und als Reaktion auf Ereignisse oder nach einem Zeitplan ausführen könnten?  [Azure Functions](../../azure-functions/functions-overview.md) ist ein serverloses Angebot, mit dem Sie nur den benötigten Code schreiben. Mit Azure Functions wird die Codeausführung von HTTP-Anforderungen, Webhooks, Clouddienstereignissen oder einem Zeitplan ausgelöst. Sie können in der Entwicklungssprache Ihrer Wahl programmieren, z.B. in C\#, F\#, Node.js, Python oder PHP. Über die nutzungsbasierte Abrechnung zahlen Sie nur für den Zeitraum, in dem Ihr Code ausgeführt wird, und Azure skaliert flexibel nach Bedarf.

> **Einsatzgebiete**: Verwenden Sie Azure Functions, wenn Sie über Code verfügen, der von anderen Azure-Diensten, von webbasierten-Ereignissen oder nach einem Zeitplan ausgelöst wird. Sie können Functions auch verwenden, wenn Sie den Mehraufwand eines vollständigen gehosteten Projekts nicht brauchen oder wenn nur für den Zeitraum zahlen wollen, in dem Ihr Code ausgeführt wird. Weitere Informationen finden Sie in der [Übersicht über Azure Functions](../../azure-functions/functions-overview.md).
> 
> **Erste Schritte**: Befolgen Sie das Schnellstart-Tutorial für Functions, um [Ihre erste Funktion](../../azure-functions/functions-create-first-azure-function.md) im Portal zu erstellen.
> 
> **Jetzt testen**: Mit Azure Functions können Sie Ihren Code ausführen, ohne sich für ein Azure-Konto registrieren zu müssen. Probieren Sie es jetzt aus, und [erstellen Sie Ihre erste Azure-Funktion](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric ist eine Plattform für verteilte Systeme, die das Erstellen, Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices vereinfacht. Die Plattform bietet eine ausgereifte Laufzeit für das Erstellen verteilter, skalierbarer zustandsloser und zustandsbehafteter Microservices sowie umfassende Anwendungsverwaltungsfunktionen zum Organisieren, Bereitstellen, Überwachen, Upgraden/Patchen und Löschen bereitgestellter Anwendungen. Apps, die in einem gemeinsam genutzten Pool von Computern ausgeführt werden, können klein anfangen und bei Bedarf auf Hunderte oder Tausende VMs skaliert werden.

Service Fabric unterstützt WebAPI mit Open Web Interface für .NET (OWIN) und ASP.NET Core. Es bietet SDKs, mit denen sich Dienste unter Linux sowohl in .NET Core als auch in Java erstellen lassen. Weitere Informationen zu Service Fabric finden Sie in der [Service Fabric-Dokumentation](https://docs.microsoft.com/azure/service-fabric/).

> **Einsatzgebiete:** Service Fabric ist eine gute Wahl, wenn Sie eine Anwendung erstellen oder eine vorhandenen Anwendung neu erstellen, um eine Microservice-Architektur zu verwenden. Verwenden Sie Service Fabric, wenn Sie mehr Kontrolle über oder direkten Zugriff auf die zugrunde liegende Infrastruktur benötigen.
> 
> **Erste Schritte:** [Erstellen Ihrer ersten Azure Service Fabric-Anwendung](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Verbessern Ihrer Anwendungen mit Azure-Diensten

Zusätzlich zum Anwendungshosting bietet Azure Dienstangebote, die die Funktionalität, Entwicklung und Wartung Ihrer Anwendungen jeweils in der Cloud und lokal optimieren können.

#### <a name="hosted-storage-and-data-access"></a>Gehosteter Speicher und Datenzugriff

Die meisten Anwendungen müssen Daten speichern, es ist also egal, wie Sie Ihre Anwendung in Azure hosten möchten. Denken Sie nur an mindestens einen der folgenden Speicher- und Datendienste.

- **Azure Cosmos DB**: Ein global verteilter Multimodell-Datenbankdienst zur elastischen Skalierung von Durchsatz und Speicher in verschiedenen geografischen Regionen mit einer umfassenden SLA. 
  > **Einsatzgebiete:** Wenn Ihre Anwendung Dokument-, Tabellen- oder Diagrammdatenbanken einschließlich MongoDB-Datenbanken benötigt, mit mehreren klar definierten Konsistenzmodellen. 
  > 
  > **Erste Schritte**: [Erstellen einer Azure Cosmos DB-Web-App](../../cosmos-db/create-sql-api-dotnet.md). Wenn Sie ein MongoDB-Entwickler sind, lesen Sie [Azure Cosmos DB: Erstellen einer Web-App mit einer MongoDB-API mit .NET und dem Azure-Portal](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: Bietet dauerhaften, hoch verfügbaren Speicher für Blobs, Warteschlangen, Dateien und andere Arten von nicht relationalen Daten. Storage stellt die Speichergrundlage für VMs bereit.

  > **Einsatzgebiete**: Wenn Ihre App nicht relationale Daten speichert, wie Schlüssel-Wert-Paare (Tabellen), Blobs, Dateifreigaben oder Meldungen (Warteschlangen).
  > 
  > **Erste Schritte**: Wählen Sie aus den folgenden Speichertypen aus: [Blobs](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [Tabellen](../../cosmos-db/table-storage-how-to-use-dotnet.md), [Warteschlangen](../../storage/queues/storage-dotnet-how-to-use-queues.md) oder [Dateien](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL-Datenbank**: Eine auf Azure basierende Version der Microsoft SQL Server-Engine zum Speichern relationaler Tabellendaten in der Cloud. SQL-Datenbank bietet vorhersagbare Leistung, Skalierbarkeit ohne Downtime, Geschäftskontinuität, und Datenschutz.

  > **Einsatzgebiete**: Wenn Ihre Anwendung Datenspeicherung mit referentieller Integrität, Transaktionsunterstützung und Unterstützung für TSQL-Abfragen erfordert.
  > 
  > **Erste Schritte**: [Erstellen einer SQL-Datenbank in Minuten mit dem Azure-Portal](../../sql-database/sql-database-get-started.md).


Sie können [Azure Data Factory](../../data-factory/introduction.md) verwenden, um vorhandene lokale Daten in Azure zu verschieben. Wenn Sie nicht dazu bereit sind, Daten in die Cloud zu verschieben, können Sie mit [Hybridverbindungen](../../biztalk-services/integration-hybrid-connection-overview.md) in BizTalk Services eine Verbindung von Ihrer von App Service gehosteten App mit lokalen Ressourcen herstellen. Sie können von Ihren lokalen Anwendungen aus auch eine Verbindung mit Azure-Daten und Speicherdiensten herstellen.

#### <a name="docker-support"></a>Docker-Unterstützung

Mit Docker-Containern, einer Form der Betriebssystemvirtualisierung, können Sie Anwendungen auf effizientere und vorhersagbare Weise bereitstellen. Eine Anwendung im Container arbeitet in der Produktion auf die gleiche Weise wie in Ihren Entwicklungs- und Testsystemen. Sie können Container mit Standard-Docker-Tools verwalten. Sie können Ihre vorhandenen Kenntnisse und beliebte Open Source-Tools verwenden, um containerbasierte Anwendungen in Azure bereitzustellen und zu verwalten.

Azure bietet mehrere Methoden zum Verwenden von Containern in Ihren Anwendungen.

- **Azure Docker-VM-Erweiterung**: Hiermit können Sie Ihre VM mit Docker-Tools so konfigurieren, dass sie als Docker-Host fungiert.

  > **Einsatzgebiete**: Wenn Sie konsistente Containerbereitstellungen für Ihre Anwendungen auf einer VM erstellen möchten, oder wenn Sie [Docker Compose](https://docs.docker.com/compose/overview/) verwenden möchten.
  > 
  > **Erste Schritte**: [Erstellen einer Docker-Umgebung in Azure mit der Docker-VM-Erweiterung](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Azure Container Service**: Hiermit können Sie einen Cluster mit virtuellen Computern, die für die Ausführung von Anwendungen in Containern vorkonfiguriert sind, erstellen, konfigurieren und verwalten. Weitere Informationen zu Container Service finden Sie unter [Einführung in Azure Container Service](../../container-service/container-service-intro.md).

  > **Einsatzgebiete**: Wenn Sie produktionsreife, skalierbare Umgebungen erstellen müssen, die zusätzliche Planungs- und Verwaltungstools bereitstellen, oder wenn Sie einen Docker Swarm-Cluster bereitstellen.
  > 
  > **Erste Schritte**: [Bereitstellen eines Container Service-Clusters](../../container-service/dcos-swarm/container-service-deployment.md).

- **Docker-Computer**: Hiermit können Sie eine Docker-Engine auf virtuellen Hosts installieren und verwalten, indem Sie Docker-Computerbefehle verwenden.

  >**Einsatzgebiete**: Wenn Sie schnell einen Prototyp für eine App erstellen müssen, indem Sie einen einzelnen Docker-Host erstellen.

- **Benutzerdefiniertes Docker-Image für App Service**: Hiermit können Sie Docker-Container von einer Containerregistrierung oder einen Kundencontainer verwenden, wenn Sie eine Web-App unter Linux bereitstellen.

  > **Einsatzgebiete**: Wenn Sie eine Web-App unter Linux für ein Docker-Image bereitstellen.
  > 
  > **Erste Schritte**: [Verwenden eines benutzerdefinierten Docker-Image für App Service unter Linuxx](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Es ist wichtig, nicht nur zu wissen, wer Ihre Anwendungen verwendet, sondern auch den unberechtigten Zugriff auf Ihre Ressourcen zu verhindern. Azure bietet mehrere Möglichkeiten zum Authentifizieren Ihrer App-Clients.

- **Azure Active Directory (Azure AD)** : Der mehrinstanzenfähige, cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft. Sie können das einmalige Anmelden (SSO) zu Ihren Anwendungen hinzufügen, indem Sie in Azure AD integrieren. Sie können auf Verzeichniseigenschaften zugreifen, indem Sie direkt die Azure AD Graph-API oder die Microsoft Graph-API verwenden. Sie können mit Azure AD-Unterstützung für das OAuth2.0-Autorisierungsframework und Open ID Connect integrieren, indem Sie native HTTP/REST-Endpunkte und die Azure AD-Authentifizierungsbibliotheken für mehrere Plattformen verwenden.

  > **Einsatzgebiete**: Wenn Sie SSO bereitstellen, mit graphbasierten Daten arbeiten oder domänenbasierte Benutzer authentifizieren möchten.
  > 
  > **Erste Schritte**: Weitere Informationen finden Sie im [Entwicklerhandbuch zu Azure Active Directory](../../active-directory/develop/v1-overview.md).

- **App Service-Authentifizierung**: Wenn Sie App Service zum Hosten Ihrer App auswählen, erhalten Sie auch integrierte Authentifizierungsunterstützung für Azure AD, zusammen mit Identitätsanbietern aus sozialen Netzwerken wie Facebook, Google, Microsoft und Twitter.

  > **Einsatzgebiete**: Wenn Sie die Authentifizierung in einer App Service-App mithilfe von Azure AD, Identitätsanbietern aus sozialen Netzwerken oder beidem aktivieren möchten.
  > 
  > **Erste Schritte**: Weitere Informationen zur Authentifizierung in App Service finden Sie unter [Authentifizierung und Autorisierung in Azure App Service](../../app-service/overview-authentication-authorization.md).

Weitere Informationen zu bewährten Sicherheitsmethoden in Azure finden Sie unter [Sicherheit in Azure – bewährte Methoden und Muster](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Überwachung

Wenn Ihre Anwendung in Azure eingerichtet ist und ausgeführt wird, müssen Sie dazu in der Lage sein, die Leistung zu überwachen, nach Problemen Ausschau zu halten und anzuzeigen, wie Kunden Ihre App verwenden. Azure bietet mehrere Überwachungsoptionen.

-   **Visual Studio Application Insights**: Ein in Azure gehosteter erweiterbarer Analytics-Dienst, der in Visual Studio integrierbar ist, um Ihre Live-Web-Anwendungen zu überwachen. Dies stellt für Sie die Daten bereit, die Sie benötigen, um die Leistung und Benutzerfreundlichkeit Ihrer Apps kontinuierlich zu verbessern, unabhängig davon, ob diese in Azure gehostet werden oder nicht.

    >**Erste Schritte**: Befolgen Sie die Anweisungen unter [Application Insights – Tutorial](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: Ein Dienst, der Ihnen dabei hilft, die Metriken und Protokolle, die von Ihrer Azure-Infrastruktur und Ihren Azure-Ressourcen erstellt werden, zu visualisieren, Abfragen dafür durchzuführen, weiterzuleiten, zu archivieren und darauf zu reagieren. Monitor stellt die Datenansichten bereit, die Ihnen im Azure-Portal angezeigt werden, und stellt eine einzelne Quelle zum Überwachen von Azure-Ressourcen dar.
 
    >**Erste Schritte**: [Erste Schritte mit Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps-Integration

Unabhängig davon, ob Azure VMs bereitstellt oder Ihre Web-Apps mit Continuous Integration veröffentlicht, ist Azure in die meisten der gängigen DevOps-Tools integrierbar. Dank der Unterstützung für Tools wie Jenkins, GitHub, Puppet, Chef, TeamCity, Ansible, Azure DevOps u.a. können Sie mit den Tools arbeiten, über die Sie bereits verfügen, und die bisherige Benutzerfreundlichkeit maximieren.

> **Jetzt testen:** [Testen Sie mehrere der DevOps-Integrationen](https://azure.microsoft.com/try/devops/).
> 
> **Erste Schritte**: DevOps-Optionen für eine App Service-App können Sie hier anzeigen: [Kontinuierliche Bereitstellung in Azure App Service](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Azure-Regionen

Azure ist eine globale Cloudplattform, die in vielen Regionen auf der ganzen Welt allgemein verfügbar ist. Wenn Sie einen Dienst, eine Anwendung oder eine VM in Azure bereitstellen, müssen Sie eine Region auswählen, die ein bestimmtes Rechenzentrum darstellt, in dem Ihre Anwendung ausgeführt wird oder in dem Ihre Daten gespeichert werden. Diese Regionen entsprechen bestimmten Standorten, die auf der Seite [Azure-Regionen](https://azure.microsoft.com/regions/) veröffentlicht sind.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Auswählen der besten Region für Ihre Anwendung und Daten

Einer der Vorteile der Verwendung von Azure ist, dass Sie Ihre Anwendungen in verschiedenen Rechenzentren auf der ganzen Welt bereitstellen können. Die Region, die Sie auswählen, kann sich auf die Leistung Ihrer Anwendung auswirken. Beispielsweise ist es besser, eine Region auszuwählen, die näher an den meisten Ihrer Kunden liegt, um die Wartezeit bei Netzwerkanforderungen zu reduzieren. Möglicherweise möchten Sie Ihre Region auch so auswählen, dass sie die rechtlichen Anforderungen für den Vertrieb Ihrer App in bestimmten Ländern/Regionen erfüllen. Es wird immer empfohlen, Anwendungsdaten im gleichen Rechenzentrum oder in einem Rechenzentrum zu speichern, das so nah wie möglich an dem Rechenzentrum liegt, das Ihre Anwendung hostet.

### <a name="multi-region-apps"></a>Apps in mehreren Regionen

Es ist zwar unwahrscheinlich aber nicht unmöglich, dass ein gesamtes Rechenzentrum aufgrund eines Ereignisses, z.B. eine Naturkatastrophe oder ein Ausfall des Internets, offline geschaltet wird. Es wird empfohlen, wichtige Geschäftsanwendungen in mehreren Rechenzentren zu hosten, um maximale Verfügbarkeit zu gewährleisten. Die Verwendung von mehreren Regionen kann zudem die Wartezeit für globale Benutzer verringern und bietet zusätzliche Flexibilität beim Aktualisieren von Anwendungen.

Einige Dienste, wie VM und App Services, verwenden [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md), um die Unterstützung in mehreren Regionen mit Failover zwischen Regionen zu ermöglichen, um Unternehmensanwendungen mit hoher Verfügbarkeit zu unterstützen. Ein Beispiel finden Sie unter [Azure-Referenzarchitektur: Ausführen einer Webanwendung in mehreren Regionen](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Einsatzgebiete**: Wenn Sie über Unternehmensanwendungen und Anwendungen mit hoher Verfügbarkeit verfügen, die vom Failover und der Replikation profitieren.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Wie verwalte ich meine Anwendungen und Projekte?

Azure bietet für Sie eine Reihe von Möglichkeiten zum Erstellen und Verwalten Ihrer Azure-Ressourcen, -Anwendungen und -Projekte – sowohl programmgesteuert als auch im [Azure-Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Befehlszeilenschnittstellen und PowerShell

Azure bietet zwei Möglichkeiten zum Verwalten Ihrer Anwendungen und Dienste über die Befehlszeile, indem Sie Bash, Terminal, die Eingabeaufforderung oder ein Befehlszeilentool Ihrer Wahl verwenden. Üblicherweise können Sie über die Befehlszeile die gleichen Tasks ausführen wie im Azure-Portal – z.B. das Erstellen und Konfigurieren von virtuellen Computern, virtuellen Netzwerken, Web-Apps und anderen Diensten.

-   [Azure-Befehlszeilenschnittstelle (CLI)](../../xplat-cli-install.md): Hiermit können Sie eine Verbindung mit einem Azure-Abonnement herstellen und verschiedene Tasks für Azure-Ressourcen über die Befehlszeile programmieren.

-   [Azure PowerShell:](../../powershell-install-configure.md) Bietet eine Reihe von Modulen mit Cmdlets, die es Ihnen ermöglichen, Azure-Ressourcen zu verwalten, indem Sie Windows PowerShell verwenden.

### <a name="azure-portal"></a>Azure-Portal

Das Azure-Portal ist eine webbasierte Anwendung, die Sie zum Erstellen, Verwalten und Entfernen von Azure-Ressourcen und -Diensten verwenden können. Das Azure-Portal finden Sie unter <https://portal.azure.com>. Es umfasst ein anpassbares Dashboard, Tools zum Verwalten von Azure-Ressourcen und Zugang zu Abonnementeinstellungen und Abrechnungsinformationen. Weitere Informationen finden Sie unter [Azure-Portal – Übersicht](../../azure-portal-overview.md).

### <a name="rest-apis"></a>REST-APIs

Azure basiert auf einem Satz von REST-APIs, die die Benutzeroberfläche des Azure-Portals unterstützen. Die meisten dieser REST-APIs werden ebenfalls unterstützt, um Ihnen zu ermöglichen, Ihre Azure-Ressourcen und -Anwendungen über jedes internetfähige Gerät programmgesteuert bereitzustellen und zu verwalten. Die komplette REST-API-Dokumentation finden Sie unter der [Azure REST SDK reference (Azure REST SDK-Referenz)](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>APIs

Zusätzlich zu REST-APIs können Sie mithilfe von vielen Azure-Diensten auch programmgesteuert Ressourcen von Ihren Anwendungen verwalten, indem Sie plattformspezifische Azure SDKs verwenden, einschließlich SDKs für die folgenden Entwicklungsplattformen:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

Dienste wie [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) und [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) stellen clientseitige SDKs bereit, um Ihnen den Zugriff auf Dienste von Web- und Mobile Client-Apps aus zu ermöglichen.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Das Ausführen Ihrer App in Azure umfasst wahrscheinlich das Arbeiten mit mehreren Azure-Diensten, die alle dem gleichen Lebenszyklus folgen und als eine logische Einheit betrachtet werden können. Eine Web-App könnte beispielsweise die Dienste Web-Apps, SQL-Datenbank, Storage, Azure Cache für Redis und Azure Content Delivery Network verwenden. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) ermöglicht es Ihnen, mit den Ressourcen in Ihrer Anwendung als Gruppe zu arbeiten. Sie können alle Ressourcen in einem einzigen koordinierten Vorgang bereitstellen, aktualisieren oder löschen.

Zusätzlich zur logischen Gruppierung und dem Verwalten von zugehörigen Ressourcen umfasst Azure Resource Manager Bereitstellungsfunktionen, mit deren Hilfe Sie die Bereitstellung und die Konfiguration zugehöriger Ressourcen anpassen können. Beispielsweise können Sie mithilfe von Resource Manager eine Anwendung bereitstellen und konfigurieren, die aus mehreren virtuellen Computern, einem Lastenausgleich und einer Azure SQL-Datenbank als einzelne Einheit besteht.

Sie entwickeln diese Bereitstellungen, indem Sie eine Azure Resource Manager-Vorlage verwenden, einem JSON-formatierten Dokument. Mit Vorlagen können Sie eine Bereitstellung definieren und Ihre Anwendungen verwalten, indem Sie deklarative Vorlagen anstelle von Skripts verwenden. Ihre Vorlagen können für unterschiedliche Umgebungen geeignet sein, z.B. Testing, Staging und Produktion. Beispielsweise können Sie durch die Verwendung von Vorlagen eine Schaltfläche zu einem GitHub-Repository hinzufügen, die den Code im Repository für eine Reihe von Azure-Diensten mit einem einzigen Klick bereitstellt.

> **Einsatzgebiete**: Verwenden Sie Resource Manager-Vorlagen, wenn Sie eine vorlagenbasierte Bereitstellung für Ihre App wünschen, die Sie programmgesteuert verwalten können, indem Sie REST APIs, die Azure-Befehlszeilenschnittstelle und Azure PowerShell verwenden.
> 
> **Erste Schritte**: Erste Schritte zum Verwenden von Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Grundlegendes zu Konten, Abonnements und Abrechnung

Als Entwickler tauchen wir gern direkt in den Code ein und möchten so schnell wie möglich damit beginnen, unsere Anwendungen zum Ausführen zu bringen. Wir möchten Sie allerdings dazu ermutigen, so einfach wie möglich mit Azure zu beginnen. Um Sie dabei zu unterstützen, bietet Azure eine [kostenlose Testversion](https://azure.microsoft.com/free/). Für einige Dienste wie [Azure App Service](https://tryappservice.azure.com/) ist sogar eine Funktion zum kostenlosen Testen verfügbar, für die Sie nicht einmal ein Konto erstellen müssen. Auch wenn es noch so viel Spaß macht, in den Code einzutauchen und Ihre Anwendung bei Azure bereitzustellen, ist es auch wichtig, dass Sie sich etwas Zeit nehmen, um zu verstehen, wie Azure im Hinblick auf Benutzerkonten, Abonnements und die Abrechnung funktioniert.

### <a name="what-is-an-azure-account"></a>Was ist ein Azure-Konto?

Um ein Azure-Abonnement erstellen zu können oder damit arbeiten zu können, müssen Sie über ein Azure-Konto verfügen. Ein Azure-Konto ist einfach eine Identität in Azure AD oder in einem Verzeichnis wie einer Geschäfts- oder Schulorganisation, die von Azure AD als vertrauenswürdig angesehen wird. Wenn Sie nicht einer solchen Organisation angehören, können Sie immer ein Abonnement erstellen, indem Sie Ihr Microsoft-Konto verwenden, das von Azure AD als vertrauenswürdig angesehen wird. Weitere Informationen zum Integrieren von lokalem Windows Server Active Directory in Azure AD finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Jedes Azure-Abonnement weist eine Vertrauensstellung mit einer Azure AD-Instanz auf. Dies bedeutet, dass es diesem Verzeichnis bei der Authentifizierung von Benutzern, Diensten und Geräten vertraut. Mehrere Abonnements können dem gleichen Verzeichnis vertrauen, ein Abonnement vertraut jedoch nur einem Verzeichnis. Weitere Information finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Zusätzlich zum Definieren individueller Azure-Kontoidentitäten, auch *Benutzer* genannt, können Sie in Azure AD auch *Gruppen* definieren. Das Erstellen von Benutzergruppen ist eine gute Methode zum Verwalten des Zugriffs auf Ressourcen in einem Abonnement mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC). Informationen zum Erstellen von Gruppen finden Sie unter [Erstellen einer Gruppe und Hinzufügen von Mitgliedern in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Sie können Gruppen auch [mithilfe von PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md) erstellen und verwalten.

### <a name="manage-your-subscriptions"></a>Verwalten Ihrer Abonnements

Ein Abonnement ist eine logische Gruppierung von Azure-Diensten, die mit einem Azure-Konto verknüpft ist. Ein einzelnes Azure-Konto kann mehrere Abonnements enthalten. Die Abrechnung für Azure-Dienste erfolgt auf Abonnementbasis. Eine Liste der verfügbaren Abonnementangebote nach Typ finden Sie unter [Microsoft Azure-Angebotsdetails](https://azure.microsoft.com/support/legal/offer-details/). Azure-Abonnements verfügen über einen Kontoadministrator, der die vollständige Kontrolle über das Abonnement hat, und einen Dienstadministrator, der die Kontrolle über alle Dienste des Abonnements hat. Informationen über Administratoren für klassische Abonnements finden Sie unter [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](../../billing/billing-add-change-azure-subscription-administrator.md). Zusätzlich zu Administratoren kann einzelnen Konten über die [rollenbasierte Zugriffssteuerung (RBAC)](../../role-based-access-control/overview.md) detaillierte Kontrolle über Azure-Ressourcen gewährt werden.

#### <a name="resource-groups"></a>Ressourcengruppen

Wenn Sie neue Azure-Dienste bereitstellen, erfolgt die in einem angegebenen Abonnement. Einzelne Azure-Dienste, die auch Ressourcen genannt werden, werden im Kontext einer Ressourcengruppe erstellt. Ressourcengruppen vereinfachen das Bereitstellen und Verwalten der Ressourcen Ihrer Anwendung. Eine Ressourcengruppe sollte alle Ressourcen für Ihre Anwendung enthalten, mit der Sie als Einheit arbeiten möchten. Sie können Ressourcen zwischen Ressourcengruppen und sogar in verschiedene Abonnements verschieben. Informationen zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../resource-group-move-resources.md).

Der Azure-Ressourcen-Explorer eignet sich hervorragend zum Visualisieren der Ressourcen, die Sie bereits in Ihrem Abonnement erstellt haben. Weitere Informationen finden Sie unter [Verwenden des Azure-Ressourcen-Explorer zum Anzeigen und Ändern von Ressourcen](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Gewähren von Zugriff auf Ressourcen

Wenn Sie den Zugriff auf Azure-Ressourcen gewähren, ist es empfehlenswert, Benutzern so wenig Berechtigungen wie möglich zum Ausführen einer bestimmten Aufgabe bereitzustellen.

- **Rollenbasierte Zugriffssteuerung (RBAC)** : In Azure können Sie Benutzerkonten (Prinzipalen) in einem angegebenen Bereich Zugriff gewähren: Abonnement, Ressourcengruppe oder einzelne Ressourcen. Mit RBAC können Sie eine Reihe von Ressourcen in einer Ressourcengruppe bereitstellen und einem bestimmten Benutzer oder einer bestimmten Gruppe Berechtigungen erteilen. Sie können auch den Zugriff auf nur die Ressourcen beschränken, die zur Zielressourcengruppe gehören. Sie können auch den Zugriff auf eine einzelne Ressource, z.B. einen virtuellen Computer oder ein virtuelles Netzwerk, gewähren. Um Zugriff zu gewähren, weisen Sie dem Benutzer, der Gruppe oder dem Dienstprinzipal eine Rolle zu. Es existieren viele vordefinierte Rollen, und Sie können auch Ihre eigenen benutzerdefinierten Rollen definieren. Weitere Informationen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)?](../../role-based-access-control/overview.md)

  > **Einsatzgebiete**: Wenn Sie eine differenzierte Zugriffsverwaltung für Benutzer und Gruppen benötigen, oder wenn Sie einen Benutzer zum Besitzer eines Abonnements machen müssen.
  > 
  > **Erste Schritte**: Weitere Informationen finden Sie unter [Verwalten des Zugriffs mithilfe der RBAC und des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).

- **Dienstprinzipalobjekte**: Zusätzlich zum Bereitstellen des Zugriffs für Benutzerprinzipale und Gruppen können Sie einem Dienstprinzipal den gleichen Zugriff gewähren.

  > **Einsatzgebiete**: Wenn Sie programmgesteuert Azure-Ressourcen verwalten oder den Zugriff für Anwendungen gewähren. Weitere Informationen finden Sie unter [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>`Tags`

Mit Azure Resource Manager können Sie einzelnen Ressourcen benutzerdefinierte Tags zuweisen. Tags, die Schlüssel-Wert-Paare sind, können hilfreich sein, wenn Sie Ressourcen für die Abrechnung oder die Überwachung organisieren müssen. Tags bieten Ihnen eine Möglichkeit, Ressourcen über mehrere Ressourcengruppen hinweg nachzuverfolgen. Sie können Tags im Portal, in der Azure Resource Manager-Vorlage oder programmgesteuert zuweisen, indem Sie die REST-API, die Azure-Befehlszeilenschnittstelle oder PowerShell verwenden. Sie können jeder Ressource mehrere Tags zuweisen. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Ressourcen](../../resource-group-using-tags.md).

### <a name="billing"></a>Abrechnung

Beim Übergang von lokalem Computing zu in der Cloud gehosteten Diensten sind die Nachverfolgung und das Schätzen der Dienstnutzung sowie der zugehörigen Kosten wichtige Aspekte. Es ist wichtig, schätzen zu können, welche Kosten für das Ausführen neuer Ressourcen monatlich anfallen. Sie müssen auch in der Lage sein, zu projizieren, wie die Abrechnung für einen gegebenen Monat basierend auf den aktuellen Ausgaben aussieht.

#### <a name="get-resource-usage-data"></a>Abrufen von Ressourcennutzungsdaten

Azure bietet eine Reihe von Abrechnungs-REST-APIs, die den Zugriff auf den Ressourcenverbrauch und Metadateninformationen für Azure-Abonnements gewähren. Durch diese Abrechnungs-APIs können Sie Azure-Kosten besser vorhersagen und verwalten. Sie können Ausgaben in stündlichen Schritten nachverfolgen und analysieren, Ausgabenwarnungen erstellen und die zukünftige Abrechnung basierend auf aktuellen Nutzungstrends vorhersagen.

>**Erste Schritte**: Weitere Informationen zur Verwendung der Abrechnungs-APIs finden Sie unter [Verwenden der Azure-Abrechnungs-APIs, um programmgesteuerte Einblicke in die Nutzung Ihrer Azure-Ressourcen zu erlangen](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Vorhersagen künftiger Kosten

Obwohl es schwierig ist, Kosten im Voraus zu schätzen, verfügt Azure über einen [Preisrechner](https://azure.microsoft.com/pricing/calculator/), den Sie verwenden können, wenn Sie die Kosten bereitgestellter Ressourcen schätzen. Sie können auch das Blatt "Rechnung" in das Portal und die Abrechnung REST-APIs verwenden, zum Schätzen der künftiger Kosten basierend auf dem aktuellen Verbrauchs.

>**Erste Schritte**: Weitere Informationen finden Sie unter [Verwenden der Azure-Abrechnungs-APIs, um programmgesteuerte Einblicke in die Nutzung Ihrer Azure-Ressourcen zu erlangen](../../billing-usage-rate-card-overview.md).
