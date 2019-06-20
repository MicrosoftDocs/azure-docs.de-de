---
title: Leitfaden zu den ersten Schritten für Azure-IT-Operatoren | Microsoft Docs
description: Leitfaden zu den ersten Schritten für Azure-IT-Operatoren
services: ''
documentationcenter: ''
author: themichaelbender-ms
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: ''
ms.topic: overview
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 08/24/2018
ms.author: mibender
ms.openlocfilehash: 1f8972c89ecb0fc7d8cc7ceb9bfdeab9e905e862
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570607"
---
# <a name="get-started-for-azure-it-operators"></a>Erste Schritte für Azure-IT-Operatoren

Dieser Leitfaden enthält eine Einführung in die wichtigsten Konzepte im Zusammenhang mit der Bereitstellung und Verwaltung einer Microsoft Azure-Infrastruktur. Wenn Sie mit Cloud Computing oder Azure selbst noch nicht vertraut sind, ermöglicht dieser Leitfaden Ihnen den schnellen Einstieg in Konzeptdetails, Bereitstellungsvorgänge und die Verwaltung. In vielen Abschnitten wird ein Vorgang beschrieben, z.B. die Bereitstellung eines virtuellen Computers, und anschließend ein Link zu weiterführenden technischen Details angegeben.

## <a name="cloud-computing-overview"></a>Übersicht über Cloud Computing

Cloud Computing stellt eine moderne Alternative zum herkömmlichen lokalen Datencenter dar. Anbieter von öffentlichen Clouds sorgen für die Bereitstellung und Verwaltung der gesamten Computing-Infrastruktur und der zugrunde liegenden Verwaltungssoftware. Bei diesen Anbietern sind viele unterschiedliche Clouddienste erhältlich. Ein Clouddienst kann hierbei ein virtueller Computer, ein Webserver oder eine in der Cloud gehostete Datenbank-Engine sein. Als Kunde eines Cloudanbieters leasen Sie diese Clouddienste je nach Bedarf. Auf diese Weise wandeln Sie die Investitionsaufwendungen für die Hardwarewartung in betriebliche Gesamtaufwendungen um. Außerdem bietet ein Clouddienst die folgenden Vorteile:

- Schnelle Bereitstellung von großen Compute-Umgebungen

- Schnelle Außerbetriebnahme von Systemen, die nicht mehr erforderlich sind

- Einfache Bereitstellung von Systemen, die normalerweise sehr komplex sind, z.B. Lastenausgleichsmodule

- Nutzung von flexibler Computekapazität oder Skalierbarkeit bei Bedarf

- Höhere Kosteneffizienz für Computing-Umgebungen

- Zugriff von jedem Ort aus per webbasiertem Portal oder programmgesteuerter Automatisierung

- Cloudbasierte Dienste zur Erfüllung der meisten Compute- und Anwendungsanforderungen

Mit der lokalen Infrastruktur haben Sie die vollständige Kontrolle über die bereitgestellte Hardware und Software. Bisher hat dies bei Entscheidungen zur Hardwarebeschaffung häufig dazu geführt, dass der Schwerpunkt auf dem zentralen Hochskalieren lag. Ein Beispiel hierfür ist der Kauf eines Servers mit mehr Kernen, um Leistungsspitzen abdecken zu können. Der Nachteil ist, dass diese Infrastruktur nicht ausgelastet werden kann, wenn der Bedarf weniger hoch ist. Mit Azure haben Sie die Möglichkeit, nur die Infrastruktur bereitzustellen, die Sie wirklich benötigen, und Sie können jederzeit Anpassungen vornehmen. Der Schwerpunkt liegt also auf dem horizontalen Hochskalieren, indem zusätzliche Computeknoten bereitgestellt werden, um bestimmte Leistungsanforderungen zu erfüllen. Das horizontale Hochskalieren von Clouddiensten ist kostengünstiger als das zentrale Hochskalieren mit teurer Hardware.

Microsoft verfügt bereits über viele Azure-Datencenter weltweit, und der Bau von weiteren Datencentern ist geplant. Außerdem erhöht Microsoft die Anzahl von unabhängigen Clouds in Regionen wie China und Deutschland. Nur die größten globalen Unternehmen können auf diese Weise Datencenter bereitstellen, sodass die Nutzung von Azure es für Unternehmen jeder Größe leicht macht, ihre Dienste in der Nähe ihrer Kunden bereitzustellen.

Für kleine Unternehmen ist ein kostengünstiger Einstieg in Azure möglich, und es kann dann schnell skaliert werden, wenn die Anforderungen an die Computeleistung zunehmen. Größere Vorabinvestitionen in die Infrastruktur werden verhindert, und Sie können die Architektur Ihrer Systeme je nach Bedarf flexibel errichten und anpassen. Die Nutzung von Cloud Computing passt perfekt zur Dynamik und zur Fail-Fast-Fähigkeit beim Wachstum von Startup-Unternehmen.

Weitere Informationen zu den verfügbaren Azure-Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Cloud Computing-Modell

Azure verwendet ein Cloud Computing-Modell basierend auf Dienstkategorien, die für Kunden bereitgestellt werden. Die drei Dienstkategorien sind Infrastructure-as-a-Service (IaaS), Platform-as-a-Service (PaaS) und Software-as-a-Service (SaaS). Die Anbieter teilen sich die Verantwortung für die Komponenten im Computing-Stapel in jeder dieser Kategorien ganz oder teilweise. Werfen wir einen Blick auf die einzelnen Kategorien für Cloud Computing.
![Cloud Computing-Stapel im Vergleich](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastructure-as-a-Service

Ein IaaS-Cloudanbieter führt alle physischen Computeressourcen und die erforderliche Software aus und verwaltet sie, um die Computervirtualisierung zu ermöglichen. Ein Kunde dieses Diensts stellt seine virtuellen Computer in diesen gehosteten Datencentern bereit. Die virtuellen Computer sind zwar in einem dezentralen Datencenter angeordnet, aber der IaaS-Kunde besitzt die Kontrolle über die Konfiguration und Verwaltung des Betriebssystems. Die zugrunde liegende Infrastruktur überlässt er dem Cloudanbieter.

Azure umfasst mehrere IaaS-Lösungen, z.B. virtuelle Computer, VM-Skalierungsgruppen und die dazugehörige Netzwerkinfrastruktur. Virtuelle Computer sind eine beliebte Wahl für die anfängliche Migration von Diensten zu Azure, weil sie ein „Lift & Shift“-Migrationsmodell ermöglichen. Sie können eine VM wie die Infrastruktur konfigurieren, mit der Ihre Dienste derzeit in Ihrem Datencenter ausgeführt werden, und die Software anschließend zur neuen VM migrieren. Unter Umständen müssen Sie Konfigurationsupdates vornehmen, z.B. URLs zu anderen Diensten oder Speicher, aber mit diesem Ansatz können Sie viele Anwendungen migrieren.

VM-Skalierungsgruppen werden basierend auf virtuellen Azure-Computern erstellt und stellen eine einfache Möglichkeit zum Bereitstellen von Clustern mit identischen VMs dar. VM-Skalierungsgruppen unterstützen auch die automatische Skalierung, damit neue VMs automatisch bereitgestellt werden können, wenn dies erforderlich ist. Aus diesem Grund sind VM-Skalierungsgruppen eine ideale Plattform zum Hosten von Microservice-Computeclustern der höheren Ebene, z.B. Azure Service Fabric und Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platform-as-a-Service

Bei PaaS stellen Sie Ihre Anwendung in einer Umgebung des Clouddienstanbieters bereit. Der Anbieter übernimmt die gesamte Infrastrukturverwaltung, damit Sie sich auf die Anwendungsentwicklung und die Datenverwaltung konzentrieren können.

Azure umfasst mehrere PaaS-Computeangebote, z.B. das Web-Apps-Feature von Azure App Service und Azure Cloud Services (Web- und Workerrollen). In beiden Fällen können Entwickler ihre Anwendung auf unterschiedliche Arten bereitstellen, ohne dass sie sich mit den zugrunde liegenden Details auskennen müssen. Entwickler müssen keine virtuellen Computer (VMs) erstellen, nicht jeweils Remotedesktopprotokoll (RDP) für die Anmeldung verwenden und auch die Anwendung nicht installieren. Sie klicken nur auf eine Schaltfläche (bzw. in die Nähe). Mit den Tools von Microsoft werden dann die VMs bereitgestellt, und anschließend wird die Anwendung darauf bereitgestellt und installiert.

#### <a name="saas-software-as-a-service"></a>SaaS: Software-as-a-Service

Bei SaaS handelt es sich um Software, die zentral gehostet und verwaltet wird. Normalerweise basiert sie auf einer mehrinstanzenfähigen Architektur, bei der nur eine Version der Anwendung für alle Kunden verwendet wird. Die Software kann horizontal auf mehrere Instanzen hochskaliert werden, um für alle Standorte die beste Leistung zu erzielen. Software vom Typ „SaaS“ wird normalerweise per Monats- oder Jahresabonnement lizenziert. Software vom Typ „SaaS“ wird normalerweise per Monats- oder Jahresabonnement lizenziert. SaaS-Softwareanbieter sind für alle Komponenten des Softwarestapels verantwortlich. Sie verwalten also ausschließlich die bereitgestellten Dienste.

Microsoft Office 365 ist ein gutes Beispiel für ein SaaS-Angebot. Abonnenten zahlen eine monatliche oder jährliche Abonnementgebühr und können Microsoft Exchange, Microsoft OneDrive und die restlichen Anwendungen der Microsoft Office Suite als Dienst nutzen. Die Abonnenten erhalten immer die aktuelle Version, und die Verwaltung des Exchange-Servers ist inbegriffen. Verglichen mit der jährlichen Office-Installation und -Aktualisierung ist dies kostengünstiger und weniger aufwändig.

## <a name="azure-services"></a>Azure-Dienste

Azure bietet über seine Cloud Computing-Plattform viele Dienste an. Dies sind die folgenden Dienste:

### <a name="compute-services"></a>Compute Services

Dienste zum Hosten und Ausführen der Anwendungsworkload:

- Azure Virtual Machines – sowohl Linux als auch Windows

- App Services (Web-Apps, Mobile Apps, Logic Apps, API-Apps und Funktionen-Apps)

- Azure Batch (für umfangreiche parallele Compute- und Batchcomputeaufträge)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Datendienste

Dienste zum Speichern und Verwalten von Daten:

- Azure Storage (umfasst Azure-Dienste für Blob, Warteschlange, Tabelle und Datei)

- Azure SQL-Datenbank

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Anwendungsdienste

Dienste zum Erstellen und Betreiben von Anwendungen:

- Azure Active Directory (Azure AD)

- Azure Service Bus zum Verbinden von verteilten Systemen

- Azure HDInsight für die Big Data-Verarbeitung

- Azure Scheduler

- Azure Media Services

### <a name="network-services"></a>Netzwerkdienste

Dienste für Netzwerke sowohl in Azure als auch zwischen Azure und lokalen Datencentern:

- Virtuelles Azure-Netzwerk

- Azure ExpressRoute

- Von Azure bereitgestelltes DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Eine ausführliche Dokumentation zu Azure-Diensten finden Sie in der [Dokumentation zu Azure-Diensten](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Wichtige Azure-Begriffe

### <a name="datacenters-and-regions"></a>Datencenter und Regionen

Azure ist eine globale Cloudplattform, die in vielen Regionen auf der ganzen Welt allgemein verfügbar ist. Beim Bereitstellen eines Diensts, einer Anwendung oder einer VM in Azure werden Sie aufgefordert, eine Region auszuwählen. Die ausgewählte Region steht für ein bestimmtes Datencenter, in dem Ihre Anwendung ausgeführt wird. Weitere Informationen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/).

Einer der Vorteile der Verwendung von Azure ist, dass Sie Ihre Anwendungen in verschiedenen Datencentern auf der ganzen Welt bereitstellen können. Die Region, die Sie auswählen, kann sich auf die Leistung Ihrer Anwendung auswirken. Es ist optimal, eine Region auszuwählen, die für den Großteil Ihrer Kunden in der Nähe liegt, um die Wartezeit bei Netzwerkanforderungen zu reduzieren. Es kann auch ratsam sein, die Region so auszuwählen, dass sie die rechtlichen Anforderungen für den Vertrieb Ihrer App in bestimmten Ländern/Regionen erfüllt.

### <a name="azure-portal"></a>Azure-Portal

Das Azure-Portal ist eine webbasierte Anwendung, die zum Erstellen, Verwalten und Entfernen von Azure-Ressourcen und -Diensten verwendet werden kann. Das Azure-Portal finden Sie unter [portal.azure.com](https://portal.azure.com). Es enthält ein anpassbares Dashboard und Tools zum Verwalten der Azure-Ressourcen. Darüber hinaus werden Informationen zur Abrechnung für das Abonnement bereitgestellt. Weitere Informationen finden Sie unter [Übersicht über das Microsoft Azure-Portal](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) und [Verwalten von Azure-Ressourcen über das Portal](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Ressourcen

Bei Azure-Ressourcen handelt es sich um individuelle Compute-, Netzwerk-, Daten- oder App-Hosting-Dienste, die für ein Azure-Abonnement bereitgestellt wurden. Häufig verwendete Ressourcen sind virtuelle Computer, Speicherkonten oder SQL-Datenbanken. Azure-Dienste bestehen oft aus mehreren zusammengehörigen Azure-Ressourcen. Ein virtueller Azure-Computer kann beispielsweise Folgendes enthalten: VM, Speicherkonto, Netzwerkadapter und öffentliche IP-Adresse. Diese Ressourcen können einzeln oder als Gruppe erstellt, verwaltet und gelöscht werden. Azure-Ressourcen werden später in diesem Handbuch ausführlicher behandelt.

### <a name="resource-groups"></a>Ressourcengruppen

Eine Azure-Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Die Ressourcengruppe kann alle Ressourcen für die Lösung oder nur die Ressourcen enthalten, die Sie als Gruppe verwalten möchten. Azure-Ressourcengruppen werden später in diesem Handbuch ausführlicher behandelt.

### <a name="resource-manager-templates"></a>Resource Manager-Vorlagen

Eine Azure Resource Manager-Vorlage ist eine JSON-Datei (JavaScript Object Notation), mit der eine oder mehrere Ressourcen zum Bereitstellen einer Ressourcengruppe definiert werden. Außerdem werden damit die Abhängigkeiten zwischen bereitgestellten Ressourcen definiert. Resource Manager-Vorlagen werden später in diesem Handbuch ausführlicher behandelt.

### <a name="automation"></a>Automation

Zusätzlich zum Erstellen, Verwalten und Löschen von Ressourcen mit dem Azure-Portal können Sie diese Aktivitäten automatisieren, indem Sie PowerShell oder die Azure-Befehlszeilenschnittstelle (CLI) verwenden.

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell umfasst eine Gruppe von Modulen, mit denen Cmdlets für die Verwaltung von Azure bereitgestellt werden. Sie können die Cmdlets zum Erstellen, Verwalten und Entfernen von Azure-Diensten verwenden. Mit den Cmdlets können Sie einheitliche und wiederholbare Bereitstellungen ohne Benutzereingriff erzielen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>Azure-Befehlszeilenschnittstelle

Die Azure-Befehlszeilenschnittstelle ist ein Tool, das Sie zum Erstellen, Verwalten und Entfernen von Azure-Ressourcen über die Befehlszeile verwenden können. Die Azure CLI ist für Linux, Mac OS X und Windows verfügbar. Weitere Informationen und technische Details finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>REST-APIs

Azure basiert auf einem Satz von REST-APIs, die die Benutzeroberfläche des Azure-Portals unterstützen. Die meisten dieser REST-APIs werden ebenfalls unterstützt, um es Ihnen zu ermöglichen, Ihre Azure-Ressourcen und -Apps über jedes internetfähige Gerät programmgesteuert bereitzustellen und zu verwalten. Weitere Informationen finden Sie unter [Azure REST API Reference](https://docs.microsoft.com/rest/api/index) (Azure-REST-API – Referenz).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Administratoren können auf Azure PowerShell und die Azure CLI über eine browserbasierte Benutzeroberfläche namens Azure Cloud Shell zugreifen. Diese interaktive Schnittstelle bietet ein flexibles Tool für Linux- und Windows-Administratoren, um die Befehlszeilenschnittstelle ihrer Wahl (Bash oder PowerShell) zu verwenden. Auf Azure Cloud Shell kann über das Portal, als eigenständige Webschnittstelle unter [shell.azure.com](https://shell.azure.com) oder von mehreren anderen Zugangspunkten aus zugegriffen werden. Weitere Informationen finden Sie in der [Übersicht über Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Azure-Abonnements

Ein Abonnement ist eine logische Gruppierung von Azure-Diensten, die mit einem Azure-Konto verknüpft ist. Ein einzelnes Azure-Konto kann mehrere Abonnements enthalten. Die Abrechnung für Azure-Dienste erfolgt auf Abonnementbasis. Azure-Abonnements verfügen über einen Kontoadministrator, der die vollständige Kontrolle über das Abonnement hat, und einen Dienstadministrator, der die Kontrolle über alle Dienste des Abonnements hat. Informationen über Administratoren für klassische Abonnements finden Sie unter [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](../../billing/billing-add-change-azure-subscription-administrator.md). Zusätzlich zu Administratoren kann einzelnen Konten über die [rollenbasierte Zugriffssteuerung (RBAC)](../../role-based-access-control/overview.md) detaillierte Kontrolle über Azure-Ressourcen gewährt werden.

### <a name="select-and-enable-an-azure-subscription"></a>Auswählen und Aktivieren eines Azure-Abonnements

Bevor Sie Azure-Dienste verwenden können, benötigen Sie ein Abonnement. Es sind mehrere Abonnementypen verfügbar.

**Kostenlose Konten:** Der Link zur Registrierung für ein kostenloses Konto befindet sich auf der [Azure-Website](https://azure.microsoft.com/). Sie erhalten damit eine Gutschrift für 30 Tage, damit Sie in Azure beliebige Kombinationen von Ressourcen ausprobieren können. Wenn Sie Ihren Gutschriftbetrag überschreiten, wird die Nutzung Ihres Kontos beendet. Am Ende des Testzeitraums werden Ihre Dienste außer Betrieb genommen und funktionieren nicht mehr. Sie können jederzeit ein Upgrade auf ein Abonnement mit nutzungsbasierter Bezahlung durchführen.

**MSDN-Abonnements:** Wenn Sie über ein MSDN-Abonnement verfügen, erhalten Sie jeden Monat einen bestimmten Betrag als Azure-Gutschrift. Falls Sie beispielsweise das Abonnement „Microsoft Visual Studio Enterprise mit MSDN“ nutzen, erhalten Sie pro Monat eine Azure-Gutschrift in Höhe von \$150 US-Dollar.

Wenn der Gutschriftbetrag überschritten wird, wird Ihr Dienst bis zum Beginn des nächsten Monats deaktiviert. Sie können das Ausgabenlimit deaktivieren und die Daten für eine Kreditkarte angeben, die zum Begleichen der zusätzlichen Kosten verwendet werden soll. Für einige dieser Kosten gelten für MSDN-Konten Rabatte. Beispielsweise zahlen Sie den Linux-Preis für VMs, auf denen Windows Server ausgeführt wird, und es fallen keine weiteren Kosten für Microsoft-Server an, z.B. Microsoft SQL Server. Daher sind MSDN-Konten ideal für Entwicklungs- und Testszenarien geeignet.

**BizSpark-Konten:** Das Microsoft BizSpark-Programm bietet zahlreiche Vorteile für Startup-Unternehmen. Einer dieser Vorteile ist der Zugriff auf die gesamte Microsoft-Software für Entwicklungs- und Testumgebungen für bis zu fünf MSDN-Konten. Sie erhalten für jedes dieser fünf MSDN-Konten eine Azure-Gutschrift in Höhe von 150 US-Dollar und zahlen für einige Azure-Dienste einen reduzierten Preis, z.B. Virtual Machines.

**Nutzungsbasierte Bezahlung:** Mit diesem Abonnement zahlen Sie nur für die tatsächliche Nutzung, indem Sie dem Konto eine Kredit- oder Debitkarte zuordnen. Wenn Sie für ein Unternehmen arbeiten, können Sie auch eine Genehmigung für die Zahlung auf Rechnung erhalten.

**Enterprise Agreements:** Mit einem Enterprise Agreement sagen Sie die Nutzung einer bestimmten Anzahl von Diensten in Azure für das nächste Jahr verbindlich zu und zahlen den entsprechenden Betrag im Voraus. Dieser verbindliche Betrag wird im Laufe des Jahres verbraucht. Falls Sie den zugesagten Betrag überschreiten, können Sie die Differenz im Nachhinein begleichen. Je nach Betrag der Zusage erhalten Sie für die Dienste in Azure einen Rabatt.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Gewähren des administrativen Zugriffs auf ein Azure-Abonnement

Die rollenbasierte Zugriffssteuerung verfügt über mehrere integrierte Rollen, die Sie zum Zuweisen von Berechtigungen verwenden können. Um einen Benutzer zum Administrator eines Azure-Abonnements zu machen, weisen Sie ihm die Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner) im Abonnementbereich zu. Durch die Rolle „Besitzer“ erhält der Benutzer vollständigen Zugriff auf alle Ressourcen im Abonnement, einschließlich des Rechts, den Zugriff an andere Personen zu delegieren.

Weitere Informationen finden Sie unter [Verwalten des Zugriffs mithilfe von RBAC und des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Anzeigen von Abrechnungsinformationen im Azure-Portal

Eine wichtige Komponente der Verwendung von Azure ist die Anzeige von Abrechnungsinformationen. Das Azure-Portal ermöglicht ausführliche Einblicke in Azure-Abrechnungsinformationen.

Weitere Informationen finden Sie unter [Herunterladen von Azure-Rechnungen und täglichen Nutzungsdaten](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Abrufen von Abrechnungsinformationen aus Abrechnungs-APIs

Zusätzlich zum Anzeigen der Abrechnung im Portal können Sie auf die Abrechnungsinformationen zugreifen, indem Sie über die Azure-Abrechnungs-REST-APIs ein Skript oder Programm verwenden:

- Sie können die Azure-Nutzungs-API zum Abrufen Ihrer Nutzungsdaten verwenden. Sie können die Abrechnungs- bzw. Nutzungsinformationen optimieren, indem Sie zusammengehörende Azure-Ressourcen entsprechend markieren. Beispielsweise können Sie alle Ressourcen einer Ressourcengruppe mit einem Abteilungsnamen oder Projektnamen markieren und dann die spezifischen Kosten für diese Markierung nachverfolgen.

- Sie können die Azure-Gebührenkarten-API verwenden, um alle verfügbaren Ressourcen zusammen mit den Metadaten und Preisinformationen zu diesen einzelnen Ressourcen aufzulisten.

Weitere Informationen finden Sie unter [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Vorhersagen von Kosten mit dem Preisrechner

Die Preise für die einzelnen Dienste in Azure sind unterschiedlich. Viele Azure-Dienste verfügen über die Tarife „Basic“, „Standard“ und „Premium“. Normalerweise hat jeder Tarif mehrere Preis- und Leistungsebenen. Mit dem [Online-Preisrechner](https://azure.microsoft.com/pricing/calculator) können Sie Preisschätzungen erstellen. Der Preisrechner ermöglicht Ihnen das flexible Schätzen von Kosten für eine einzelne Ressource oder eine Gruppe von Ressourcen.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Der Azure Resource Manager ist ein Mechanismus für die Bereitstellung, Verwaltung und Organisation von Azure-Ressourcen. Mit dem Resource Manager können Sie viele einzelne Ressourcen zusammen in einer Ressourcengruppe anordnen.

Außerdem enthält der Resource Manager Bereitstellungsfunktionen, die eine anpassbare Bereitstellung und Konfiguration von verwandten Ressourcen ermöglichen. Beispielsweise können Sie mithilfe von Resource Manager eine Anwendung bereitstellen, die aus mehreren virtuellen Computern, einem Lastenausgleich und einer SQL-Datenbank als einzelne Einheit besteht. Sie entwickeln diese Bereitstellungen mithilfe einer Resource Manager-Vorlage.

Der Ressourcen-Manager bietet mehrere Vorteile:

- Sie können alle Ressourcen für Ihre Lösung als Gruppe bereitstellen, verwalten und überwachen, anstatt diese Ressourcen einzeln zu verarbeiten.

- Sie können die Lösung während des gesamten Entwicklungslebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden.

- Sie können zum Verwalten Ihrer Infrastruktur anstelle von Skripts auch deklarative Vorlagen verwenden.

- Sie können die Abhängigkeiten zwischen Ressourcen definieren, sodass diese in der richtigen Reihenfolge bereitgestellt werden.

- Sie können die Zugriffssteuerung auf alle Dienste in der Ressourcengruppe anwenden, da die rollenbasierte Zugriffssteuerung (RBAC) standardmäßig in die Verwaltungsplattform integriert ist.

- Sie können Tags auf Ressourcen anwenden, um alle Ressourcen in Ihrem Abonnement logisch zu organisieren.

- Indem Sie die Kosten für eine Gruppe mit Ressourcen anzeigen, für die das gleiche Tag verwendet wird, erhalten Sie die Abrechnungsinformationen für Ihre Organisation.

### <a name="tips-for-creating-resource-groups"></a>Tipps zum Erstellen von Ressourcengruppen

Beachten Sie diese Tipps, wenn Sie Entscheidungen zu Ihren Ressourcengruppen treffen:

- Alle Ressourcen einer Ressourcengruppe sollten über den gleichen Lebenszyklus verfügen.

- Sie können eine Ressource nur jeweils einer Gruppe zuweisen.

- Sie können eine Ressource jederzeit einer Ressourcengruppe hinzufügen bzw. sie daraus entfernen. Jede Ressource muss einer Ressourcengruppe angehören. Dies bedeutet also Folgendes: Wenn Sie eine Ressource aus einer Gruppe entfernen, müssen Sie sie einer anderen Gruppe hinzufügen.

- Sie können die meisten Arten von Ressourcen jederzeit in eine andere Ressourcengruppe verschieben.

- Die Ressourcen einer Ressourcengruppe können sich in unterschiedlichen Regionen befinden.

- Sie können eine Ressourcengruppe verwenden, um den Zugriff für die darin enthaltenen Ressourcen zu steuern.

### <a name="building-resource-manager-templates"></a>Erstellen von Resource Manager-Vorlagen

Mit Resource Manager-Vorlagen werden die Ressourcen und Ressourcenkonfigurationen deklarativ definiert, die in einer einzelnen Ressourcengruppe bereitgestellt werden. Sie können Resource Manager-Vorlagen zum Orchestrieren von komplexen Bereitstellungen verwenden, ohne dass eine aufwändige Skripterstellung oder manuelle Konfiguration erforderlich ist. Nachdem Sie eine Vorlage bereitgestellt haben, können Sie sie mehrfach bereitstellen – jeweils mit einem identischen Ergebnis.

Eine Resource Manager-Vorlage besteht aus vier Abschnitten:

- **Parameter:** Eingaben für die Bereitstellung. Parameterwerte können vom Benutzer oder automatisiert angegeben werden. Ein Beispielparameter ist ein Administratorbenutzername mit dem dazugehörigen Kennwort für eine Windows-VM. Die Parameterwerte werden nach dem Angeben für die gesamte Bereitstellung verwendet.

- **Variablen:** Enthalten Werte, die für die gesamte Bereitstellung verwendet werden. Ein Variablenwert wird im Gegensatz zu Parametern nicht während der Bereitstellung angegeben. Stattdessen wird er hartcodiert oder dynamisch generiert.

- **Ressourcen:** In diesem Abschnitt der Vorlage werden die bereitzustellenden Ressourcen (virtuelle Computer, Speicherkonten, virtuelle Netzwerke und Ähnliches) definiert.

- **Ausgabe**: Nach Abschluss einer Bereitstellung kann Resource Manager Daten zurückgeben (beispielsweise dynamisch generierte Verbindungszeichenfolgen).

Die folgenden Mechanismen sind für die Automatisierung der Bereitstellung verfügbar:

- **Funktionen:** In Resource Manager-Vorlagen können mehrere Funktionen verwendet werden. Hierzu gehören Vorgänge wie das Konvertieren einer Zeichenfolge in Kleinbuchstaben, das Bereitstellen von mehreren Instanzen einer definierten Ressource und das dynamische Zurückgeben der Zielressourcengruppe. Resource Manager-Funktionen sind für das Erstellen von dynamischen Bereitstellungen hilfreich.

- **Ressourcenabhängigkeiten:** Wenn Sie mehrere Ressourcen bereitstellen, sind einige Ressourcen von anderen Ressourcen abhängig. Zur Erleichterung der Bereitstellung können Sie eine Abhängigkeitsdeklaration verwenden, sodass abhängige Ressourcen vor anderen Ressourcen bereitgestellt werden.

- **Vorlagenlinks:** In einer Resource Manager-Vorlage können Sie einen Link zu einer anderen Vorlage einrichten. Auf diese Weise können Sie die Bereitstellung in eine Gruppe von zielgerichteten, zweckgebundenen Vorlagen unterteilen.

Sie können Resource Manager-Vorlagen in jedem beliebigen Text-Editor erstellen. Das Azure SDK für Visual Studio enthält aber Tools, die hierfür hilfreich sind. Mit Visual Studio können Sie der Vorlage Ressourcen über einen Assistenten hinzufügen und die Vorlage dann direkt aus Visual Studio bereitstellen und debuggen. Weitere Informationen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](../../resource-group-authoring-templates.md).

Zu guter Letzt können Sie vorhandene Ressourcengruppen über das Azure-Portal in eine wiederverwendbare Vorlage konvertieren. Dies kann hilfreich sein, wenn Sie aus einer vorhandenen Ressourcengruppe eine Vorlage erstellen möchten, die bereitgestellt werden kann, oder auch wenn Sie nur den zugrunde liegenden JSON-Code untersuchen möchten. Wählen Sie zum Exportieren einer Ressourcengruppe in den Einstellungen der Ressourcengruppe die Schaltfläche **Automatisierungsskript**.

## <a name="security-of-azure-resources-rbac"></a>Sicherheit von Azure-Ressourcen (RBAC)

Sie können den Umfang für den betriebsbedingten Zugriff auf Benutzerkonten festlegen: Abonnement, Ressourcengruppe oder Einzelressource. Dies bedeutet, dass Sie einen Satz von Ressourcen in einer Ressourcengruppe bereitstellen können, z.B. einen virtuellen Computer und alle dazugehörigen Ressourcen, und einem bestimmten Benutzer oder einer Gruppe Berechtigungen gewähren können. Mit diesem Ansatz wird der Zugriff auf die Ressourcen beschränkt, die zur Zielressourcengruppe gehören. Sie können auch den Zugriff auf eine einzelne Ressource gewähren, z.B. einen virtuellen Computer oder ein virtuelles Netzwerk.

Um Zugriff zu gewähren, weisen Sie dem Benutzer oder der Benutzergruppe eine Rolle zu. Es gibt viele vordefinierte Rollen. Sie können auch eigene benutzerdefinierte Rollen definieren.

Im Folgenden werden einige Beispiele für [in Azure integrierte Rollen](../../role-based-access-control/built-in-roles.md) aufgeführt:

- **Besitzer:** Ein Benutzer mit dieser Rolle kann alles verwalten (unter anderem auch den Zugriff).

- **Leser:** Eine Benutzer mit dieser Rolle kann Ressourcen jeglicher Art (mit Ausnahme von Geheimnissen) lesen, aber keine Änderungen vornehmen.

- **Mitwirkender für virtuelle Computer:** Ein Benutzer mit dieser Rolle kann virtuelle Computer verwalten, aber nicht das virtuelle Netzwerk, mit dem sie verbunden sind, oder das Speicherkonto, in dem sich die VHD-Datei befindet.

- **SQL-DB-Mitwirkender:** Ein Benutzer mit dieser Rolle kann SQL-Datenbanken verwalten, aber nicht ihre sicherheitsbezogenen Richtlinien.

- **SQL-Sicherheits-Manager:** Ein Benutzer mit dieser Rolle kann die sicherheitsbezogenen Richtlinien von SQL-Servern und -Datenbanken verwalten.

- **Speicherkontomitwirkender:** Ein Benutzer mit dieser Rolle kann Speicherkonten verwalten, aber nicht den Zugriff auf die Speicherkonten.

Weitere Informationen finden Sie unter [Verwalten des Zugriffs mithilfe von RBAC und des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines ist einer den zentralen IaaS-Dienste in Azure. Azure Virtual Machines unterstützt die Bereitstellung von virtuellen Windows- oder Linux-Computern in einem Microsoft Azure-Datencenter. Mit Azure Virtual Machines haben Sie die vollständige Kontrolle über die VM-Konfiguration und sind für die gesamte Softwareinstallation, -konfiguration und -wartung verantwortlich.

Beim Bereitstellen einer Azure-VM können Sie ein Image vom Azure Marketplace wählen oder ein eigenes generalisiertes Image verwenden. Dieses Image wird verwendet, um das Betriebssystem und die erste Konfiguration anzuwenden. Während der Bereitstellung übernimmt der Resource Manager einige Konfigurationseinstellungen, z.B. Zuweisung des Computernamens, Administratoranmeldeinformationen und Netzwerkkonfiguration. Sie können Azure-VM-Erweiterungen einsetzen, um Konfigurationen weiter zu automatisieren, z.B. Softwareinstallation, Virenschutzkonfiguration und Überwachungslösungen.

Sie können virtuelle Computer in vielen unterschiedlichen Größen erstellen. Die Größe eines virtuellen Computers bestimmt die Ressourcenzuteilung, z.B. Verarbeitung, Arbeitsspeicher und Speicherkapazität. In einigen Fällen sind Features wie RDMA-fähige Netzwerkadapter und SSD-Datenträger nur für bestimmte VM-Größen verfügbar. Eine vollständige Liste mit den VM-Größen und Funktionen finden Sie unter „Größen für virtuelle Computer in Azure“ für [Windows](../../virtual-machines/windows/sizes.md) und [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Anwendungsfälle

Da virtuelle Azure-Computer eine vollständige Kontrolle der Konfiguration ermöglichen, sind sie für viele Serverworkloads, für die kein PaaS-Modell verwendet werden kann, ideal geeignet. Serverworkloads wie Datenbankserver (SQL Server, Oracle oder MongoDB), Windows Server Active Directory, Microsoft SharePoint und viele andere mehr können so auf der Microsoft Azure-Plattform ausgeführt werden. Bei Bedarf können Sie diese Workloads aus einem lokalen Datencenter in eine oder mehrere Azure-Regionen verlagern, ohne dass ein hoher Aufwand für die Neukonfiguration anfällt.

### <a name="deployment-of-virtual-machines"></a>Bereitstellung von virtuellen Computern

Sie können virtuelle Azure-Computer bereitstellen, indem Sie das Azure-Portal, die Automatisierung mit dem Azure PowerShell-Modul oder die Automatisierung per plattformübergreifender CLI verwenden.

#### <a name="portal"></a>Portal

Für die Bereitstellung eines virtuellen Computers mit dem Azure-Portal sind nur ein aktives Azure-Abonnement und Zugriff auf einen Webbrowser erforderlich. Sie können viele verschiedene Betriebssystemimages mit unterschiedlichen Konfigurationen wählen. Alle Speicher- und Netzwerkanforderungen werden während der Bereitstellung konfiguriert. Weitere Informationen finden Sie unter „Erstellen einer Linux-VM mit dem Azure-Portal“ für [Windows](../../virtual-machines/windows/quick-create-portal.md) und [Linux](../../virtual-machines/linux/quick-create-portal.md).

Zusätzlich zur Bereitstellung eines virtuellen Computers mit dem Azure-Portal können Sie auch eine Azure Resource Manager-Vorlage über das Portal bereitstellen. Auf diese Weise werden alle Ressourcen so bereitgestellt und konfiguriert, wie sie in der Vorlage definiert sind. Weitere Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Die Bereitstellung eines virtuellen Azure-Computers mit PowerShell ermöglicht die vollständige Automatisierung der Bereitstellung aller dazugehörigen VM-Ressourcen, z.B. Speicher und Netzwerk. Weitere Informationen finden Sie unter [Erstellen einer Windows-VM mit dem Resource Manager und PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Zusätzlich zum individuellen Bereitstellen von Azure-Computeressourcen können Sie das Azure PowerShell-Modul verwenden, um eine Azure Resource Manager-Vorlage bereitzustellen. Weitere Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Befehlszeilenschnittstelle (CLI)

Wie beim PowerShell-Modul auch, ermöglicht die Azure-Befehlszeilenschnittstelle die Automatisierung der Bereitstellung und kann unter den Betriebssystemen Windows, OS X oder Linux verwendet werden. Wenn Sie den Azure CLI-Befehl **vm quick-create** verwenden, werden alle dazugehörigen VM-Ressourcen (einschließlich Speicher und Netzwerk) und der virtuelle Computer selbst bereitgestellt. Weitere Informationen finden Sie unter [Erstellen einer Linux-VM mit der Azure CLI](../../virtual-machines/linux/quick-create-cli.md).

Sie können die Azure CLI auch nutzen, um eine Azure Resource Manager-Vorlage bereitzustellen. Informationen darüber finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Zugriff und Sicherheit für virtuelle Computer

Für den Zugriff auf einen virtuellen Computer über das Internet muss für die zugeordnete Netzwerkschnittstelle bzw. das Lastenausgleichsmodul, falls zutreffend, eine öffentliche IP-Adresse konfiguriert sein. Die öffentliche IP-Adresse enthält einen DNS-Namen, der in den virtuellen Computer bzw. das Lastenausgleichsmodul aufgelöst wird. Weitere Informationen finden Sie unter [IP-Adressen in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Sie verwalten den Zugriff auf den virtuellen Computer über die öffentliche IP-Adresse, indem Sie eine NSG-Ressource (Netzwerksicherheitsgruppe) verwenden. Eine NSG verhält sich wie eine Firewall und kann Datenverkehr über die Netzwerkschnittstelle oder ein Subnetz für eine Gruppe von definierten Ports zulassen oder verweigern. Zum Erstellen einer Remotedesktopsitzung mit einer Azure-VM müssen Sie die NSG beispielsweise so konfigurieren, dass eingehender Datenverkehr über Port 3389 zugelassen ist. Weitere Informationen finden Sie unter [Öffnen von Ports für einen virtuellen Computer in Azure mithilfe des Azure-Portals](../../virtual-machines/windows/nsg-quickstart-portal.md).

Schließlich sollten Sie die Sicherheit für einen virtuellen Azure-Computer wie bei der Verwaltung aller Computersysteme über das Betriebssystem gewährleisten, indem Sie Sicherheitsanmeldeinformationen und Softwarefirewalls verwenden.

## <a name="azure-storage"></a>Azure Storage

Azure Storage ist ein von Microsoft verwalteter Dienst zur Bereitstellung von dauerhaftem, skalierbarem und redundantem Speicher. Sie können ein Azure-Speicherkonto einer beliebigen Ressourcengruppe als Ressource hinzufügen, indem Sie eine Ressourcenbereitstellungsmethode Ihrer Wahl verwenden. Azure umfasst vier Speichertypen: Blob Storage, File Storage, Table Storage und Queue Storage. Beim Bereitstellen eines Speicherkontos sind zwei Kontotypen verfügbar: allgemein und Blobspeicher. Mit einem allgemeinen Speicherkonto haben Sie Zugriff auf alle vier Speichertypen. Blob-Speicherkonten ähneln allgemeinen Speicherkonten, aber sie enthalten spezielle Blobs mit „heißen“ und „kalten“ Zugriffsebenen. Weitere Informationen zu Blobspeicher finden Sie unter [Azure Blob Storage](../../storage/blobs/storage-blob-storage-tiers.md).

Azure-Speicherkonten können mit unterschiedlichen Redundanzebenen konfiguriert werden:

- **Lokal redundanter Speicher** bietet Hochverfügbarkeit, indem sichergestellt wird, dass synchron drei Kopien aller Daten erstellt werden, bevor ein Schreibvorgang als erfolgreich angesehen wird. Diese Kopien werden in einer einzelnen Einrichtung in einer einzelnen Region gespeichert. Die Replikate befinden sich in separaten Fehler- und Upgradedomänen. Dies bedeutet Folgendes: Die Daten sind auch dann verfügbar, wenn ein Speicherknoten, der Ihre Daten enthält, ausfällt oder zur Durchführung eines Updates in den Offlinezustand versetzt wird.

- **Georedundanter Speicher** erstellt drei synchrone Kopien der Daten in der primären Region, um für Hochverfügbarkeit zu sorgen, und anschließend asynchron drei Replikate in einer verknüpften Region für die Notfallwiederherstellung.

- Bei **georedundantem Speicher mit Lesezugriff** handelt es sich um georedundanten Speicher, der zusätzlich über die Möglichkeit verfügt, dass Daten in der sekundären Region gelesen werden. Hierdurch ist der Speicher für Teilnotfallwiederherstellungen geeignet. Wenn ein Problem mit der primären Region besteht, können Sie Ihre Anwendung so ändern, dass sie über schreibgeschützten Zugriff auf die verknüpfte Region verfügt.

### <a name="use-cases"></a>Anwendungsfälle

Für jeden Speichertyp gilt ein anderer Anwendungsfall.

#### <a name="blob-storage"></a>Blob Storage

Der Ausdruck *Blob* ist ein Akronym für *Binary Large Object*. Blobs sind unstrukturierte Dateien, also beispielsweise die Dateien, die Sie auf Ihrem Computer speichern. In Blob Storage können alle Arten von Text- oder Binärdaten gespeichert werden, z. B. ein Dokument, eine Mediendatei oder ein Installer einer Anwendung. Der Blobspeicher wird auch als Objektspeicher bezeichnet. Azure Blob Storage umfasst auch Azure Virtual Machines-Datenträger für Daten.

Azure Storage unterstützt drei Arten von Blobs:

- **Blockblobs** werden für gewöhnliche Dateien mit einer Größe von bis zu 195 GB verwendet (4 MB mal 50.000 Blöcke). Der wichtigste Anwendungsfall für Blockblobs ist die Speicherung von Dateien, die von Anfang bis Ende gelesen werden, z.B. Mediendateien oder Imagedateien für Websites. Sie werden als Blockblobs bezeichnet, da Dateien, die größer als 64 MB sind, als kleine Blöcke hochgeladen werden müssen. Diese Blöcke werden dann im endgültigen Blob konsolidiert (Commit-Vorgang).

- **Seitenblobs** werden für Random-Access-Dateien mit einer Größe von bis zu 1 TB verwendet. Seitenblobs werden hauptsächlich als Hintergrundspeicher für die VHDs verwendet, die dauerhafte Datenträger für Azure Virtual Machines, den IaaS-Computedienst von Azure, bereitstellen. Sie werden als Seitenblobs bezeichnet, da sie zufälligen Lese-/Schreibzugriff auf 512-Byte-Seiten ermöglichen.

- **Anfügeblobs** bestehen wie Blockblobs auch aus Blöcken, aber sie sind für Anfügevorgänge optimiert. Diese werden häufig eingesetzt, um Informationen aus mindestens einer Quelle in demselben Blob zu protokollieren. Beispielsweise können Sie die gesamte Protokollierung der Ablaufverfolgung für eine Anwendung, die auf mehreren VMs ausgeführt wird, in dasselbe Anfügeblob schreiben. Ein einzelnes Anfügeblob kann eine maximale Größe von 195 GB haben.

Weitere Informationen finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Azure File Storage ist ein Dienst, bei dem Dateifreigaben in der Cloud mit dem standardmäßigen SMB-Protokoll (Server Message Block) bereitgestellt werden können. Der Dienst unterstützt sowohl SMB 2.1 als auch SMB 3.0. Mit Azure File Storage können Sie Anwendungen, für die Dateifreigaben benötigt werden, schnell und ohne teures Umschreiben zu Azure migrieren. Anwendungen, die auf virtuellen Azure-Computern, in Clouddiensten oder auf lokalen Clients ausgeführt werden, können eine Dateifreigabe in der Cloud bereitstellen. Dies ähnelt dem Bereitstellen einer typischen SMB-Freigabe durch eine Desktopanwendung. Die File Storage-Freigaben können dann von beliebig vielen Anwendungskomponenten gleichzeitig eingebunden und genutzt werden.

Da es sich bei File Storage-Freigaben um gewöhnliche SMB-Dateifreigaben handelt, können Anwendungen in Azure über die E/A-APIs des Dateisystems auf die Daten der Freigabe zugreifen. Entwickler können daher ihren vorhandenen Code und bereits erlernte Fertigkeiten für die Migration vorhandener Anwendungen verwenden. IT-Fachkräfte können PowerShell-Cmdlets verwenden, um Dateispeicher-Freigaben im Rahmen der Administration von Azure-Anwendungen zu erstellen, bereitzustellen und zu verwalten.

Weitere Informationen finden Sie unter [Erste Schritte mit Azure File Storage unter Windows](../../storage/files/storage-how-to-use-files-windows.md) und [Verwenden des Azure-Dateispeichers unter Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Table Storage

Azure Table Storage ist ein Dienst, bei dem strukturierte NoSQL-Daten in der Cloud gespeichert werden. Bei Table Storage handelt es sich um einen Schlüssel-/Attributspeicher mit einem schemalosen Design. Aufgrund der Schemalosigkeit von Table Storage ist es einfach, Ihre Daten an die Entwicklung Ihrer Anwendungen anzupassen. Der Datenzugriff ist für alle Arten von Anwendungen schnell und kostengünstig. Table Storage ist in der Regel erheblich günstiger als herkömmliche SQL-Lösungen für ähnliche Datenmengen.

Mit Table Storage können Sie flexible Datasets wie Benutzerdaten für Webanwendungen, Adressbücher, Geräteinformationen und jegliche Art von Metadaten speichern, die Ihr Dienst erfordert. Sie können in einer Tabelle eine beliebige Anzahl von Entitäten speichern. Ein Speicherkonto kann eine beliebige Anzahl von Tabellen enthalten (bis zur Kapazitätsgrenze des Speicherkontos).

Weitere Informationen finden Sie unter [Erste Schritte mit Azure Table Storage](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Azure Queue Storage ermöglicht Cloud-Messaging zwischen Anwendungskomponenten. Bei der Entwicklung skalierbarer Anwendungen werden häufig einzelne Anwendungskomponenten entkoppelt, um eine unabhängige Skalierung zu ermöglichen. Queue Storage bietet asynchrones Messaging für die Kommunikation zwischen Anwendungskomponenten, egal ob diese in der Cloud, auf dem Desktop, auf einem lokalen Server oder einem mobilen Gerät ausgeführt werden. Queue Storage unterstützt auch die Verwaltung asynchroner Aufgaben und den Aufbau von Prozessworkflows.

Weitere Informationen finden Sie unter [Erste Schritte mit Azure Queue Storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Bereitstellen eines Speicherkontos

Es gibt mehrere Optionen zum Bereitstellen eines Speicherkontos.

#### <a name="portal"></a>Portal

Für die Bereitstellung eines Speicherkontos mit dem Azure-Portal sind nur ein aktives Azure-Abonnement und Zugriff auf einen Webbrowser erforderlich. Sie können ein neues Speicherkonto in einer neuen oder vorhandenen Ressourcengruppe bereitstellen. Nachdem Sie das Speicherkonto erstellt haben, können Sie mit dem Portal einen Blobcontainer oder eine Dateifreigabe erstellen. Sie können Table Storage- und Queue Storage-Entitäten programmgesteuert erstellen. Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos](../../storage/common/storage-quickstart-create-account.md).

Zusätzlich zur Bereitstellung eines Speicherkontos mit dem Azure-Portal können Sie auch eine Azure Resource Manager-Vorlage über das Portal bereitstellen. Hierbei werden alle Ressourcen gemäß der Definition in der Vorlage, einschließlich aller Speicherkonten, bereitgestellt und konfiguriert. Weitere Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Die Bereitstellung eines Azure-Speicherkontos mithilfe von PowerShell ermöglicht eine vollständige Automatisierung der Bereitstellung von Speicherkonten. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Zusätzlich zum individuellen Bereitstellen von Azure-Ressourcen können Sie das Azure PowerShell-Modul verwenden, um eine Azure Resource Manager-Vorlage bereitzustellen. Weitere Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Befehlszeilenschnittstelle (CLI)

Wie beim PowerShell-Modul auch, ermöglicht die Azure-Befehlszeilenschnittstelle die Automatisierung der Bereitstellung und kann unter den Betriebssystemen Windows, OS X oder Linux verwendet werden. Sie können den Azure CLI-Befehl **storage account create** verwenden, um ein Speicherkonto zu erstellen. Weitere Informationen finden Sie unter [Verwenden der Azure CLI 2.0 mit Azure Storage](../../storage/common/storage-azure-cli.md).

Sie können die Azure CLI auch nutzen, um eine Azure Resource Manager-Vorlage bereitzustellen. Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Zugriff und Sicherheit für Azure Storage

Der Zugriff auf Azure Storage ist auf verschiedene Arten möglich, z.B. über das Azure-Portal, während der Erstellung und des Betriebs der VM und über Speicherclientbibliotheken.

#### <a name="virtual-machine-disks"></a>VM-Datenträger

Beim Bereitstellen eines virtuellen Computers müssen Sie auch ein Speicherkonto erstellen, unter dem der Betriebssystemdatenträger des virtuellen Computers und alle weiteren Datenträger angeordnet sind. Sie können ein vorhandenes Speicherkonto auswählen oder ein neues erstellen. Da die maximale Größe eines Blobs 1.024 GB beträgt, kann ein einzelner VM-Datenträger eine maximale Größe von 1.023 GB haben. Zum Konfigurieren eines größeren Datenträgers können Sie für den virtuellen Computer mehrere Datenträger für Daten bereitstellen und zusammen als logischen Einzeldatenträger in einem Pool anordnen. Weitere Informationen finden Sie im Artikel zur Verwaltung von Azure-Datenträgern für [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) und [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Speichertools

Auf Azure Storage-Konten kann mit vielen unterschiedlichen Storage-Explorern zugegriffen werden, z.B. Visual Studio Cloud-Explorer. Mit diesen Tools können Sie Speicherkonten und Daten durchsuchen. Weitere Informationen und eine Liste mit verfügbaren Storage-Explorern finden Sie unter [Azure Storage-Clienttools](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Storage-API

Auf Storage-Ressourcen kann über jede Sprache zugegriffen werden, die für HTTP/HTTPS-Anforderungen geeignet ist. Zusätzlich bietet Azure Storage Programmierbibliotheken für einige beliebte Sprachen. Diese Bibliotheken vereinfachen die Arbeit mit Azure Storage und behandeln bestimmte Details wie synchrone und asynchrone Aufrufe, die Zusammenfassung von Vorgängen, die Ausnahmeverwaltung und automatische Wiederholungen. Weitere Informationen finden Sie unter [Referenz zur REST-API von Azure Storage Services](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Speicherzugriffsschlüssel

Jedes Speicherkonto verfügt über zwei Authentifizierungsschlüssel: einen primären und einen sekundären Schlüssel. Beide können für Speicherzugriffsvorgänge verwendet werden. Diese Speicherschlüssel werden zum Schützen eines Speicherkontos genutzt und sind für den programmgesteuerten Zugriff auf Daten erforderlich. Es sind zwei Schlüssel vorhanden, um bei Bedarf einen Rollover der Schlüssel zur Erhöhung der Sicherheit zu ermöglichen. Es ist sehr wichtig, diese Schlüssel sicher aufzubewahren, da sie zusammen mit dem Kontonamen den unbegrenzten Zugriff auf alle Daten im Speicherkonto ermöglichen.

#### <a name="shared-access-signatures"></a>Shared Access Signatures

Wenn Sie anderen Benutzern kontrollierten Zugriff auf Ihre Speicherressourcen gewähren müssen, können Sie eine Shared Access Signature erstellen. Eine Shared Access Signature ist ein Token, das an eine URL angefügt werden kann und den delegierten Zugriff auf eine Speicherressource ermöglicht. Jeder Benutzer, der über dieses Token verfügt, kann während der Gültigkeitsdauer mit festgelegten Berechtigungen auf die Ressource zugreifen, auf die das Token verweist. Weitere Informationen finden Sie unter [Verwenden von Shared Access Signatures](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Virtuelles Azure-Netzwerk

Virtuelle Netzwerke sind erforderlich, um die Kommunikation zwischen virtuellen Computern zu unterstützen. Sie können Subnetze, benutzerdefinierte IP-Adressen, DNS-Einstellungen, Sicherheitsfilter und den Lastenausgleich definieren. Azure unterstützt verschiedene Anwendungsfälle: rein cloudbasierte Netzwerke oder hybride virtuelle Netzwerke.

### <a name="cloud-only-virtual-networks"></a>Rein cloudbasierte virtuelle Netzwerke

Ein virtuelles Azure-Netzwerk ist standardmäßig nur für Ressourcen zugänglich, die in Azure gespeichert sind. Ressourcen, die mit demselben virtuellen Netzwerk verbunden sind, können miteinander kommunizieren. Sie können VM-Netzwerkschnittstellen und Lastenausgleichsmodule einer öffentlichen IP-Adresse zuordnen, um den virtuellen Computer über das Internet zugänglich zu machen. Sie können den Zugriff auf die öffentlich verfügbar gemachten Ressourcen schützen, indem Sie eine Netzwerksicherheitsgruppe verwenden.

![Virtuelles Azure-Netzwerk für eine zweischichtige Webanwendung](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybride virtuelle Netzwerke

Sie können ein lokales Netzwerk mit einem virtuellen Azure-Netzwerk verbinden, indem Sie ExpressRoute oder eine Site-to-Site-VPN-Verbindung nutzen. In dieser Konfiguration stellt das virtuelle Azure-Netzwerk im Grunde eine cloudbasierte Erweiterung des lokalen Netzwerks dar.
![Hybrides virtuelles Netzwerk mit VPN](https://docs.microsoft.com/azure/architecture/reference-architectures/_images/blueprints/hybrid-network-vpn.png)

Da das virtuelle Azure-Netzwerk mit Ihrem lokalen Netzwerk verbunden ist, muss für standortübergreifende virtuelle Netzwerke ein eindeutiger Teil des Adressraums verwendet werden, der von Ihrer Organisation genutzt wird. Ebenso wie verschiedenen Unternehmensstandorten ein spezifisches IP-Subnetz zugewiesen wird, wird Azure bei Erweiterung Ihres Netzwerks zu einem weiteren Standort.
Es gibt mehrere Optionen zum Bereitstellen eines virtuellen Netzwerks.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Befehlszeilenschnittstelle (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager-Vorlagen

> **Verwendung:** Wenn Sie mit virtuellen Computern in Azure arbeiten, verwenden Sie auch virtuelle Netzwerke. Dies ermöglicht eine Unterteilung Ihrer VMs in öffentliche und private Subnetze, ähnlich wie bei lokalen Rechenzentren.
> 
> **Erste Schritte:** Für die Bereitstellung eines virtuellen Azure-Netzwerks mit dem Azure-Portal sind nur ein aktives Azure-Abonnement und Zugriff auf einen Webbrowser erforderlich. Sie können ein neues virtuelles Netzwerk in einer neuen oder vorhandenen Ressourcengruppe bereitstellen. Wenn Sie im Portal einen neuen virtuellen Computer erstellen, können Sie ein vorhandenes virtuelles Netzwerk auswählen oder ein neues erstellen. Informationen zum Einstieg finden Sie unter [Erstellen eines virtuellen Netzwerks über das Azure-Portal](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Zugriff und Sicherheit für virtuelle Netzwerke

Sie können virtuelle Azure-Netzwerke mit einer Netzwerksicherheitsgruppe schützen. NSGs enthalten eine Zugriffssteuerungsliste (Access Control List, ACL) zum Zulassen oder Verweigern von eingehendem Netzwerkdatenverkehr für Ihre VM-Instanzen in einem virtuellen Netzwerk. Sie können NSGs entweder Subnetzen oder einzelnen VM-Instanzen innerhalb dieses Subnetzes zuordnen. Wenn Sie eine NSG einem Subnetz zuordnen, gelten die ACL-Regeln für alle VM-Instanzen in diesem Subnetz. Außerdem können Sie den Datenverkehr weiter auf eine einzelne VM beschränken, indem Sie der VM direkt eine NSG zuordnen. Weitere Informationen finden Sie unter [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines virtuellen Windows-Computers](../../virtual-machines/windows/quick-create-portal.md)
- [Erstellen eines virtuellen Linux-Computers](../../virtual-machines/linux/quick-create-portal.md)
