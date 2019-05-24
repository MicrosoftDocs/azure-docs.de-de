---
title: Unterstützungsrichtlinien für Azure Kubernetes Service (AKS)
description: Hier erhalten Sie Informationen zu AKS-Unterstützungsrichtlinien (Azure Kubernetes Service), zur gemeinsamen Verantwortung und zu Features, die in der Vorschauversion (bzw. Alpha- oder Betaversion) vorliegen.
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 0d2c080be727d2ae13d6d9e5274f17cadffbe640
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786459"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Unterstützungsrichtlinien für Azure Kubernetes Service

Dieser Artikel enthält Details zu den Richtlinien und Einschränkungen für den technischen Support von Azure Kubernetes Service (AKS). Der Artikel beschreibt auch die Workerknotenverwaltung, verwaltete Steuerungsebenenkomponenten, Open-Source-Komponenten von Drittanbietern sowie die Sicherheits- oder Patchverwaltung.

## <a name="service-updates-and-releases"></a>Dienstupdates und -versionen

* Versionsinformationen finden Sie unter [AKS-Versionshinweise](https://github.com/Azure/AKS/releases).
* Informationen zu Features in der Vorschauversion finden Sie unter [AKS-Previewfunktionen und zugehörige Projekte](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Verwaltete Features in AKS

IaaS-Cloudkomponenten (Infrastructure-as-a-Service) der Basisinfrastruktur, z. B. Compute- oder Netzwerkkomponenten, bieten Benutzern Zugriff auf Steuerelemente und Anpassungsoptionen auf niedriger Ebene. Im Gegensatz dazu bietet AKS eine betriebsbereite Kubernetes-Bereitstellung, die den Kunden die üblichen erforderlichen Konfigurationen und Funktionen bietet. AKS-Kunden verfügen nur über begrenzte Möglichkeiten zur Anpassung, Bereitstellung und für andere Optionen. Diese Kunden müssen sich nicht direkt um Kubernetes-Cluster kümmern oder diese verwalten.

Mit AKS erhält der Kunde eine vollständig verwaltete *Steuerungsebene*. Die Steuerungsebene enthält alle Komponenten und Dienste, die der Kunde benötigt, um die Kubernetes-Cluster zu betreiben und den Endbenutzern zur Verfügung zu stellen. Alle Kubernetes-Komponenten werden von Microsoft verwaltet und betrieben.

Microsoft verwaltet und überwacht die folgenden Komponenten über den Steuerungsbereich:

* Kubelet- oder Kubernetes-API-Server
* Etcd oder ein kompatibler Schlüssel-Wert-Speicher, der Servicequalität (QoS, Quality of Service), Skalierbarkeit und Laufzeit bietet
* DNS-Dienste (z. B. kube-dns oder CoreDNS)
* Kubernetes-Proxy oder -Netzwerk

AKS ist keine vollständig verwaltete Clusterlösung. Einige Komponenten, z. B. Workerknoten, weisen eine *gemeinsame Verantwortung* auf, wobei Benutzer bei der Verwaltung des AKS-Clusters helfen müssen. Benutzereingaben sind z. B. erforderlich, um einen Sicherheitspatch für das Betriebssystem (OS) eines Workerknotens anzuwenden.

Die Dienste sind in dem Sinne *verwaltet*, dass Microsoft und das AKS-Team die Dienste bereitstellen, betreiben und für deren Verfügbarkeit und Funktionalität verantwortlich sind. Kunden können diese verwalteten Komponenten nicht ändern. Microsoft beschränkt die Anpassung, um eine konsistente und skalierbare Benutzererfahrung zu gewährleisten. Eine vollständig anpassbare Lösung finden Sie unter [AKS-Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> AKS-Workerknoten werden im Azure Portal als reguläre Azure IaaS-Ressourcen angezeigt. Diese virtuellen Computer werden jedoch in einer benutzerdefinierten Azure-Ressourcengruppe bereitgestellt (mit dem Präfix MC\\*). AKS-Workerknoten können geändert werden. Sie können z. B. mit Secure Shell (SSH) AKS-Workerknoten wie normale virtuelle Computer ändern (Sie können jedoch nicht das Basisimage des Betriebssystems ändern und Änderungen bleiben möglicherweise bei Updates oder Neustarts nicht erhalten), und Sie können andere Azure-Ressourcen an AKS-Workerknoten anfügen. Aber wenn Sie Änderungen bei der *Out-of-Band-Verwaltung und -Anpassung* vornehmen, kann der AKS-Cluster möglicherweise nicht mehr unterstützt werden. Vermeiden Sie Änderungen bei Workerknoten, es sei denn, Sie werden vom Microsoft-Support zum Vornehmen von Änderungen angewiesen.

## <a name="shared-responsibility"></a>Gemeinsame Verantwortung

Wenn ein Cluster erstellt wird, definiert der Kunde die Kubernetes-Workerknoten, die von AKS erstellt werden. Auf diesen Knoten werden Kundenworkloads ausgeführt. Kunden sind die Besitzer der Workerknoten und können sie anzeigen und ändern.

Da Clusterknoten von Kunden privaten Code ausführen und vertrauliche Daten speichern, kann der Microsoft-Support nur eingeschränkt darauf zugreifen. Der Microsoft-Support kann sich ohne ausdrückliche Genehmigung oder Hilfe des Kunden nicht bei diesen Knoten anmelden, Befehle ausführen oder Protokolle anzeigen.

Da Workerknoten vertrauliche Daten enthalten, legt Microsoft großen Wert darauf, die Verwaltung im Hintergrund einzuschränken. In vielen Fällen wird Ihr Workload weiterhin ausgeführt, auch wenn die Kubernetes-Masterknoten, etcd und andere von Microsoft verwaltete Komponenten ausfallen. Sorglos geänderte Workerknoten können zu Daten- und Workloadverlusten führen und dafür sorgen, dass der Cluster nicht mehr unterstützt werden kann.

## <a name="aks-support-coverage"></a>AKS-Supportabdeckung

Microsoft bietet technischen Support für Folgendes:

* Konnektivität mit allen Kubernetes-Komponenten, die vom Kubernetes-Dienst bereitgestellt und unterstützt werden (beispielsweise der API-Server).
* Verwaltung, Betriebszeit, QoS und Vorgänge der Kubernetes-Steuerungsebenendienste (beispielsweise Kubernetes-Masterknoten, API-Server, etcd und kube-dns).
* Etcd: Der Support beinhaltet automatisierte, transparente Sicherungen sämtlicher etcd-Daten im 30-Minuten-Takt für die Planung der Notfallwiederherstellung und die Wiederherstellung des Clusterzustands. Diese Backups sind nicht direkt für Kunden oder Benutzer verfügbar. Sie stellen die Zuverlässigkeit und Konsistenz der Daten sicher.
* Alle Integrationspunkte im Azure-Cloudanbietertreiber für Kubernetes. Dazu gehören Integrationen in andere Azure-Dienste wie Lastenausgleichsmodule, persistente Volumes oder Netzwerke (Kubernetes und Azure CNI).
* Fragen oder Probleme zur Anpassung von Komponenten der Steuerungsebene wie Kubernetes-API-Server, etcd und kube-dns.
* Probleme bei Netzwerken, z. B. Azure CNI, Kubenet oder andere Probleme in Verbindung mit Netzwerkzugriff und -funktionalität. Probleme können DNS-Auflösung, Paketverluste, Routing usw. umfassen. Microsoft unterstützt verschiedene Netzwerkszenarien:
  * Kubenet (Standard) und erweiterte Netzwerke (Azure CNI) innerhalb des Clusters und der zugehörigen Komponenten
  * Konnektivität mit anderen Azure-Diensten und -Anwendungen
  * Eingangscontroller und Eingangs- oder Lastenausgleichskonfigurationen
  * Netzwerkleistung und Wartezeit

Microsoft bietet keinen technischen Support für Folgendes:

* Fragen zur Verwendung von Kubernetes. Der Microsoft-Support bietet z. B. keine Empfehlungen zur Erstellung benutzerdefinierter Eingangscontroller, zur Verwendung von Anwendungsworkloads oder zur Anwendung von Open-Source-Softwarepaketen oder -Tools bzw. zu Softwarepaketen oder Tools von Drittanbietern.
  > [!NOTE]
  > Der Microsoft-Support kann Sie hinsichtlich der AKS-Clusterfunktionalität, der Anpassung und der Optimierung beraten (z. B. Kubernetes-Betriebsprobleme und -verfahren).
* Open-Source-Projekte von Drittanbietern, die nicht im Rahmen der Kubernetes-Steuerungsebene bereitgestellt oder mit AKS-Clustern bereitgestellt wurden. Diese Projekte können Istio, Helm, Envoy und andere einbeziehen.
  > [!NOTE]
  > Microsoft kann den bestmöglichen Support für Open-Source-Projekte von Drittanbietern wie Helm und Kured bereitstellen. Wenn das Open-Source-Tool eines Drittanbieters mit den vom Kubernetes Azure-Cloudanbieter stammenden oder anderen AKS-spezifischen Fehlern integriert wird, unterstützt Microsoft Beispiele und Anwendungen aus der Microsoft-Dokumentation.
* Closed-Source-Software von Drittanbietern. Diese Software kann z. B. Tools für Sicherheitsscans und Netzwerkgeräte oder -software umfassen.
* Probleme mit Multi-Cloud- oder Multi-Vendor-Erweiterungen. Microsoft unterstützt z. B. keine Probleme bei der Ausführung einer vereinten Multipublic-Cloudanbieterlösung.
* Andere als die in der [AKS-Dokumentation](https://docs.microsoft.com/azure/aks/) aufgeführten Netzwerkanpassungen.
  > [!NOTE]
  > Microsoft unterstützt Probleme und Fehler im Zusammenhang mit Netzwerksicherheitsgruppen (NSGs). Der Microsoft-Support kann z. B. Fragen zu einem NSG-Fehler beim Aktualisieren oder zum unerwarteten Verhalten einer NSG oder eines Lastenausgleichs beantworten.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS-Supportabdeckung für Workerknoten

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Zuständigkeiten von Microsoft für AKS-Workerknoten

Microsoft und die Kunden teilen sich die Verantwortung für die Kubernetes-Workerknoten, für die Folgendes gilt:

* Das Basisbetriebssystem-Image verfügt über die erforderlichen Ergänzungen (wie etwa Überwachungs- und Netzwerk-Agents)
* Die Workerknoten erhalten Betriebssystempatches automatisch.
* Problemen mit Komponenten der Kubernetes-Steuerungsebene, die auf den Workerknoten ausgeführt werden, werden automatisch behoben. Dies umfasst folgende Komponenten:
  * Kube-proxy
  * Netzwerktunnel zur Bereitstellung von Kommunikationspfaden zu Kubernetes-Masterkomponenten
  * Kubelet
  * Docker- oder Moby-Daemon

> [!NOTE]
> Wenn eine Komponente der Steuerungsebene auf einem Workerknoten ausgefallen ist, muss das AKS-Team unter Umständen den gesamten Workerknoten neu starten. Aufgrund des eingeschränkten Zugriffs auf die aktiven Workloads und Daten des Kunden startet das AKS-Team einen Workerknoten nur dann neu, wenn der Kunde das Problem entsprechend eskaliert. Soweit möglich, ist das AKS-Team bestrebt zu verhindern, dass ein erforderlicher Neustart die Anwendung beeinträchtigt.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Zuständigkeiten des Kunden für AKS-Workerknoten

Microsoft startet Workerknoten nicht automatisch neu, um Patches auf Betriebssystemebene anzuwenden. Obwohl Betriebssystempatches an die Workerknoten verteilt werden, ist der *Kunde* für den Neustart der Workerknoten verantwortlich, um die Änderungen anzuwenden. Freigegebene Bibliotheken, Daemons wie SSHD (Solid State Hybrid Drive) und andere Komponenten auf System- oder Betriebssystemebene werden automatisch gepatcht.

Die Kunden sind für die Durchführung von Kubernetes-Upgrades verantwortlich. Sie können Upgrades über die Azure-Systemsteuerung oder die Azure CLI durchführen. Dies gilt für Updates zur Verbesserung der Sicherheit oder Funktionalität von Kubernetes.

> [!NOTE]
> Da es sich bei AKS um einen *verwalteten Dienst* handelt, besteht sein Endziel darin, jegliche Verantwortung für Patches, Updates und Protokollerfassung zu beseitigen, um die Dienstverwaltung umfassender und effektiver zu gestalten. Da die Kapazität des Diensts für die End-to-End-Verwaltung steigt, könnten zukünftige Versionen einige Funktionen auslassen (z. B. Neustart von Knoten und automatisches Patchen).

### <a name="security-issues-and-patching"></a>Sicherheitsprobleme und -patches

Wenn ein Sicherheitsproblem in einer oder mehreren Komponenten von AKS auftritt, wird das AKS-Team alle betroffenen Cluster patchen, um das Problem zu beheben. Alternativ wird das Team den Benutzern eine Anleitung für ein Upgrade bereitstellen.

Für Workerknoten, die von einem Sicherheitsproblem betroffen sind, wendet das AKS-Team einen Patch ohne Downtime (sofern verfügbar) an und informiert die Benutzer über die Änderung.

Wenn ein Sicherheitspatch einen Neustart des Workerknotens erfordert, wird Microsoft die Kunden über diese Anforderung informieren. Der Kunde ist für den Neustart oder die Aktualisierung verantwortlich, um das Clusterpatch abzurufen. Wenn Benutzer die Patches nicht gemäß den AKS-Anweisungen anwenden, ist ihr Cluster weiterhin anfällig für das Sicherheitsproblem.

### <a name="node-maintenance-and-access"></a>Knotenwartung und -zugriff

Workerknoten unterliegen einer gemeinsamen Verantwortung und befinden sich im Besitz von Kunden. Aus diesem Grund haben Kunden die Möglichkeit, sich bei ihren Workerknoten anzumelden und potenziell schädliche Änderungen wie Kernelupdates und das Installieren oder Entfernen von Paketen vorzunehmen.

Wenn Kunden schädliche Änderungen vornehmen oder verursachen, dass Komponenten der Steuerebene offline gehen oder nicht mehr funktionieren, erkennt AKS diesen Fehler und stellt den Workerknoten automatisch im vorherigen funktionierenden Zustand wieder her.

Obwohl sich Kunden bei Workerknoten anmelden und diese ändern können, ist dies nicht empfehlenswert, da Änderungen dazu führen können, dass ein Cluster nicht mehr unterstützt wird.

## <a name="network-ports-access-and-nsgs"></a>Netzwerkports, Zugriff und NSGs

Als verwalteter Dienst gelten für AKS bestimmte Netzwerk- und Konnektivitätsanforderungen. Diese Anforderungen sind weniger flexibel als bei normalen IaaS-Komponenten. In AKS können Vorgänge wie das Anpassen von NSG-Regeln, das Blockieren eines bestimmten Ports (z. B. durch Firewallregeln, die den ausgehenden Port 443 blockieren) und Whitelist-URLs dazu führen, dass Ihr Cluster nicht mehr unterstützt wird.

> [!NOTE]
> Derzeit gestattet AKS es Ihnen nicht, den Ausgang Ihres Clusters vollständig zu sperren (z. B. explizite Whitelist für Domänen oder Ports). Die Liste der URLs und Ports kann ohne vorherige Ankündigung geändert werden. Sie können die aktualisierte Liste erhalten, indem Sie ein Azure-Supportticket erstellen. Die Liste gilt nur für Kunden, die bereit sind zu akzeptieren, dass ihre Clusterverfügbarkeit *jederzeit* beeinträchtigt werden kann.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nicht unterstützte Kubernetes-Features der Alpha- und Betaversion

AKS bietet nur Support für stabile Features im Rahmen des Upstream-Kubernetes-Projekts. Sofern nicht anderweitig dokumentiert, unterstützt AKS keine Features der Alpha- und Betaversion, die im Upstream-Kubernetes-Projekt verfügbar sind.

In zwei Szenarien können Features der Alpha- und Betaversion eingeführt werden, bevor sie allgemein verfügbar sind:

* Kunden wurden bei einem Treffen mit den Produkt-, Support- oder -Entwicklungsteams von AKS darum gebeten, diese neuen Features auszuprobieren.
* Diese Features wurden [durch ein Featureflag aktiviert](https://github.com/Azure/AKS/blob/master/previews.md). Kunden müssen sich explizit für die Nutzung dieser Features entscheiden.

## <a name="preview-features-or-feature-flags"></a>Previewfunktionen oder Featureflags

Für Features und Funktionen, die ausführliche Tests und Benutzerfeedback erfordern, veröffentlicht Microsoft neue Previewfunktionen oder Features hinter einem Featureflag. Betrachten Sie diese Features als Features einer Vorab- oder Betaversion.

Previewfunktionen oder Features mit Featureflag sind nicht für die Produktionsumgebung vorgesehen. Kontinuierliche Änderungen an APIs und am Verhalten, Fehlerbehebungen und andere Änderungen können zu instabilen Clustern und Ausfallzeiten führen.

Features in der öffentlichen Vorschauversion fallen unter den „bestmöglichen“ Support, da sich diese Features in der Vorschauversion befinden und nicht für die Produktionsumgebung bestimmt sind und von den technischen AKS-Supportteams nur während der Geschäftszeiten unterstützt werden. Weitere Informationen finden Sie unter:

* [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Previewfunktionen werden auf der *Azure-Abonnementebene* aktiviert. Installieren Sie keine Previewfunktionen in einem Produktionsabonnement. In einem Produktionsabonnement können Previewfunktionen zu einer Änderung des API-Standardverhaltens führen und den regulären Betrieb beeinträchtigen.

## <a name="upstream-bugs-and-issues"></a>Upstream-Fehler und -Probleme

Angesichts des hohen Tempos bei der Entwicklung im Upstream-Kubernetes-Projekt können immer wieder Fehler auftreten. Einige dieser Fehler können innerhalb des AKS-Systems nicht gepatcht oder umgangen werden. Stattdessen erfordern Fehlerbehebungen größere Patches für Upstream-Projekte (z. B. Kubernetes, Betriebssysteme für Knoten oder Worker sowie Kernel). Bei Komponenten, die sich im Besitz von Microsoft befinden (z. B. der Azure-Cloudanbieter), kümmern sich die AKS-/Azure-Mitarbeiter um die Upstream-Behebung des Problems in der Community.

Sollte ein technisches Supportproblem auf einen oder mehrere Upstream-Fehler zurückzuführen sein, führen die Support- und Entwicklungsteams von AKS folgende Schritte aus:

* Identifizieren und Verknüpfen aller Upstream-Fehler mit unterstützenden Details, die Aufschluss darüber geben, warum sich dadurch Auswirkungen für Ihren Cluster und/oder Ihre Workload ergeben. Kunden erhalten Links zu den erforderlichen Repositorys, damit sie die Probleme beobachten und feststellen können, wann eine neue Version die Fehlerbehebung ermöglicht.
* Bereitstellen möglicher Problemumgehungen oder Gegenmaßnahmen. Wenn das Problem behoben werden kann, wird ein [Bekanntes Problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) im AKS-Repository erfasst. Bei der Erfassung des „Bekannten Problems“ wird Folgendes erläutert:
  * Das Problem, einschließlich Links zu upstream-Fehlern.
  * Die Problemumgehung und Details zu einem Upgrade oder einer anderen Persistenz der Lösung.
  * Grober Zeitplan für die Einbeziehung des Problems auf der Grundlage des Upstream-Releaserhythmus.
