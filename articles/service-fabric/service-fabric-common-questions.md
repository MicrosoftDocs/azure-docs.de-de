---
title: Häufig gestellte Fragen zu Microsoft Azure Service Fabric | Microsoft-Dokumentation
description: Häufig gestellte Fragen zu Service Fabric und entsprechende Antworten
services: service-fabric
documentationcenter: .net
author: chackdan
manager: chackdan
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: 0bd8a7d403ad1fe0f7abb15356cc9c90ed6b3f02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66153834"
---
# <a name="commonly-asked-service-fabric-questions"></a>Häufig gestellte Fragen zu Service Fabric

Es gibt viele häufig gestellte Fragen zu den Funktionen und zur Verwendung von Service Fabric. In diesem Dokument werden viele dieser häufig gestellten Fragen und die zugehörigen Antworten behandelt.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Clustereinrichtung und -verwaltung

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Wie kann ich ein Rollback für mein Service Fabric-Clusterzertifikat ausführen?

Für das Rollback (Zurücksetzen) eines Upgrades für Ihre Anwendung ist vor dem Commit für die Änderung durch das Service Fabric-Clusterquorum eine Integritätsfehlererkennung erforderlich; für Änderungen mit erfolgtem Commit kann nur ein Rollforward ausgeführt werden. Möglicherweise muss ein Eskalationstechniker über die Kundensupportdienste Ihr Cluster wiederherstellen, wenn eine nicht überwachte wichtige Zertifikatsänderung eingeführt wurde.  Das [Service Fabric-Anwendungsupgrade](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) wendet [Anwendungsupgradeparameter](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master) an und verspricht ein Upgrade ohne Ausfallzeiten.  Entsprechend dem empfohlenen überwachten Modus für Anwendungsupgrades basiert der automatische Fortschritt der Domänenaktualisierung auf bestandenen Integritätsprüfungen. Ein Rollback erfolgt automatisch, wenn die Aktualisierung eines Standarddienstes fehlschlägt.
 
Wenn Ihr Cluster noch die klassische Eigenschaft „Zertifikatfingerabdruck“ in der Resource Manager-Vorlage nutzt, empfiehlt es sich, [„Zertifikatfingerabdruck“ in „Allgemeiner Name“](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn) für den Cluster zu ändern, um die modernen Verwaltungsfunktionen für geheime Schlüssel verwenden zu können.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Kann ich einen Cluster erstellen, der mehrere Azure-Regionen oder eigene Datencenter umfasst?

Ja. 

Mit der Service Fabric-Kernclusteringtechnologie können Computer an beliebigen Orten auf der ganzen Welt verknüpft werden, solange diese über ein Netzwerk miteinander verbunden sind. Die Erstellung und Ausführung eines solchen Clusters kann jedoch komplex sein.

Wenn Sie sich für dieses Szenario interessieren, wird empfohlen, entweder über die [Liste mit Service Fabric-Problemen auf GitHub ](https://github.com/azure/service-fabric-issues) den Kontakt herzustellen oder sich an einen Supportmitarbeiter zu wenden, um weitere Anleitungen zu erhalten. Das Service Fabric-Team arbeitet an der Bereitstellung zusätzlicher Anleitungen und Empfehlungen für dieses Szenario. 

Einige Aspekte, die zu berücksichtigen sind: 

1. Die Service Fabric-Clusterressource in Azure ist derzeit regional, ebenso wie die VM-Skalierungsgruppen, auf denen der Cluster beruht. Das bedeutet, dass Sie bei einem regionalen Ausfall den Cluster nicht mehr über Azure Resource Manager oder das Azure-Portal verwalten können. Dieser Fall kann eintreten, auch wenn der Cluster weiterhin ausgeführt wird und Sie direkt mit ihm interagieren können. Darüber hinaus bietet Azure heute nicht die Möglichkeit, ein einzelnes virtuelles Netzwerk einzurichten, das in alle Regionen verwendet werden kann. Das bedeutet, dass für einen Cluster mit mehreren Regionen in Azure entweder [öffentliche IP-Adressen für jeden virtuellen Computer in den VM Scale Sets-Instanzen](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) oder [Azure-VPN-Gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md) erforderlich sind. Diese Netzwerkoptionen haben unterschiedliche Auswirkungen auf Kosten, Leistung und – in gewissem Maße – auf das Anwendungsdesign. Daher ist vor der Bereitstellung einer solchen Umgebung eine sorgfältige Analyse und Planung erforderlich.
2. Wartung, Verwaltung und Überwachung dieser Computer können kompliziert werden, insbesondere, wenn sie sich über verschiedene _Arten_ von Umgebungen erstrecken (beispielsweise zwischen verschiedenen Cloudanbietern oder zwischen lokalen Ressourcen und Azure). Machen Sie sich daher unbedingt mit Upgrades, Überwachung, Verwaltung und Diagnose für Cluster und Anwendungen vertraut, bevor Sie Produktionsworkloads in einer solchen Umgebung ausführen. Wenn Sie bei der Behebung derartiger Probleme in Azure oder Ihren eigenen Datencentern bereits Erfahrung haben, können die gleichen Lösungsansätze wahrscheinlich auch beim Erstellen oder Ausführen des Service Fabric-Clusters angewendet werden. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Erhalten Service Fabric-Knoten automatisch Betriebssystemupdates?

Sie können das allgemein verfügbare Feature [Automatische Betriebssystemimageupgrades mit Azure-VM-Skalierungsgruppen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) nutzen.

Für Cluster, die NICHT in Azure ausgeführt werden, haben wir [eine Anwendung bereitgestellt](service-fabric-patch-orchestration-application.md), mit der die Betriebssysteme unter Ihren Service Fabric-Knoten gepatcht werden können.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Kann ich große VM-Skalierungsgruppen in meinem SF-Cluster verwenden? 

**Kurze Antwort**: Nein. 

**Lange Antwort**: Obwohl Sie dank großer VM-Skalierungsgruppen eine VM-Skalierungsgruppe mit bis zu 1000 VM-Instanzen skalieren können, werden dazu Platzierungsgruppen (PGs) verwendet. Fehlerdomänen (FDs) und Upgradedomänen (UDs) sind nur innerhalb einer Platzierungsgruppe konsistent. Service Fabric verwendet FDs und UDs für Platzierungsentscheidungen, die Ihre Dienstreplikate/Dienstinstanzen betreffen. Da die FDs und UDs nur innerhalb einer Platzierungsgruppe vergleichbar sind, kann SF sie nicht verwenden. Wenn beispielsweise VM1 in PG1 über eine Topologie FD=0 und VM9 in PG2 über eine Topologie FD=4 verfügt, bedeutet dies nicht, dass VM1 und VM2 sich in zwei verschiedenen Hardwareracks befinden, daher kann SF die FD-Werte in diesem Fall nicht für Platzierungsentscheidungen verwenden.

Es gibt derzeit andere Probleme mit großen VM-Skalierungsgruppen, z.B. den Mangel an Unterstützung von Ebene-4-Lastenausgleich. Weitere Informationen finden Sie unter [Näheres zu großen Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md).



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Was ist die Mindestgröße für einen Service Fabric-Cluster? Warum darf er nicht kleiner sein?

Die unterstützte Mindestgröße für einen Service Fabric-Cluster, auf dem Produktionsworkloads ausgeführt werden, beträgt fünf Knoten. Für Entwicklungszenarios unterstützen wir Cluster mit einem Knoten (optimiert für eine schnelle Entwicklung in Visual Studio) und Cluster mit fünf Knoten.

Ein Produktionscluster muss aus den folgenden drei Gründen mindestens 5 Knoten umfassen:
1. Selbst wenn keine Benutzerdienste ausgeführt werden, führt ein Service Fabric-Cluster verschiedene zustandsbehaftete Systemdienste aus, darunter Naming Service und Failover-Manager-Dienst. Diese Systemdienste sind für die Aufrechterhaltung des Clusterbetriebs unerlässlich.
2. Es wird immer ein Replikat eines Dienstes pro Knoten platziert, sodass die Clustergröße die Obergrenze für die Anzahl von Replikaten ist, die ein Dienst (eigentlich eine Partition) aufweisen kann.
3. Da bei einem Clusterupgrade mindestens ein Knoten heruntergefahren wird, sollte ein Puffer von mindestens einem Knoten vorhanden sein. Deshalb wird angestrebt, dass ein Produktionscluster mindestens zwei Knoten *zusätzlich* zum absoluten Mindestwert aufweist. Der absolute Mindestwert ist die Quorumgröße eines Systemdienstes, wie nachstehend erläutert wird.  

Es wird angestrebt, dass der Cluster auch bei einem gleichzeitigen Ausfall von zwei Knoten verfügbar bleibt. Damit ein Service Fabric-Cluster verfügbar ist, müssen die Systemdienste verfügbar sein. Zustandsbehaftete Systemdienste wie z.B. Naming Service und Failover-Manager-Dienst verfolgen, welche Dienste auf dem Cluster bereitgestellt wurden und wo sie zurzeit gehostet werden, und sind von starker Konsistenz abhängig. Diese starke Konsistenz ist wiederum von der Möglichkeit abhängig, ein *Quorum* für jedes Update am Zustand dieser Dienste abzurufen, wobei ein Quorum die strikte Mehrheit der Replikate (N/2 + 1) für einen bestimmten Dienst darstellt. Wenn also Resilienz gegenüber einem gleichzeitigen Verlust von zwei Knoten (also einem gleichzeitigen Verlust von zwei Replikaten eines Systemdienstes) erreicht werden soll, muss ClusterSize - QuorumSize >= 2 sein, wodurch sich eine Mindestgröße von 5 ergibt. Zeigen wir dies an einem Beispiel: Angenommen, der Cluster umfasst N Knoten, und es liegen N Replikate eines Systemdienstes vor – einer auf jedem Knoten. Die Quorumgröße für einen Systemdienst ist (N/2 + 1). Die obige Ungleichung lautet N - (N/2 + 1) >= 2. Es müssen zwei Fälle berücksichtigt werden: N ist gerade und N ist ungerade. Wenn N gerade ist, z.B. N = 2\*m, wobei gilt: m >= 1, lautet die Ungleichung 2\*m - (2\*m/2 + 1) >= 2 oder m >= 3. Der Mindestwert für N ist 6, und dies wird bei m = 3 erreicht. Wenn andererseits N ungerade ist, z.B. N = 2\*m+1, wobei gilt: m >= 1, lautet die Ungleichung 2\*m+1 - ( (2\*m+1)/2 + 1 ) >= 2 oder 2\*m+1 - (m+1) >= 2 oder m >= 2. Der Mindestwert für N ist 5, und dies wird bei m = 2 erreicht. Daher lautet unter allen Werten von N, die die Ungleichung ClusterSize - QuorumSize >= 2 erfüllen, der Mindestwert 5.

Beachten Sie, dass wir im obigen Argument davon ausgegangen sind, dass jeder Knoten eine Kopie eines Systemdienstes umfasst, sodass die Quorumgröße basierend auf der Anzahl von Knoten im Cluster berechnet wird. Durch die Änderung von *TargetReplicaSetSize* könnten wir jedoch die Quorumgröße unter (N/2+1) verringern. Dies könnte den Eindruck erwecken, dass ein Cluster verwendet werden kann, der weniger als 5 Knoten umfasst und dennoch 2 zusätzliche Knoten über der Quorumgröße aufweist. Ein Beispiel: Wenn in einem 4-Knoten-Cluster TargetReplicaSetSize auf 3 festgelegt wird, lautet die Quorumgröße basierend auf TargetReplicaSetSize (3/2 + 1) oder 2, also ergibt sich: ClusterSize - QuorumSize = 4-2 >= 2. Es kann jedoch nicht garantiert werden, dass der Systemdienst das Quorum erfüllt oder übererfüllt, wenn ein beliebiges Knotenpaar gleichzeitig ausfällt. Es könnte sein, dass die beiden ausgefallenen Knoten zwei Replikate gehostet haben, sodass es beim Systemdienst zu einem Quorumverlust kommt (weil nur noch eine einzige Kopie verbleibt) und der Dienst nicht mehr verfügbar ist.

Untersuchen wir vor diesem Hintergrund einige mögliche Clusterkonfigurationen:

**Ein Knoten**: Diese Option bietet keine Hochverfügbarkeit, da jeglicher Verlust dieses einzigen Knotens den Verlust des gesamten Clusters bedeutet.

**Zwei Knoten**: Ein Quorum für einen Dienst, der über zwei Knoten bereitgestellt wird (N = 2), ist 2 (2/2 + 1 = 2). Wenn ein einzelnes Replikat verloren geht, ist es unmöglich, ein Quorum zu erstellen. Da es zum Ausführen eines Dienstupgrades erforderlich ist, ein Replikat vorübergehend außer Betrieb zu nehmen, ist dies keine nützlich Konfiguration.

**Drei Knoten**: Mit drei Knoten (N = 3) liegt die Anforderung zum Erstellen eines Quorums noch immer bei zwei Knoten (3/2 + 1 = 2). Dies bedeutet, dass Sie einen einzelnen Knoten verlieren und das Quorum dennoch beibehalten können. Der gleichzeitige Ausfall von zwei Knoten führt jedoch dazu, dass es bei den Systemdiensten zu einem Quorumverlust kommt und der Cluster nicht mehr verfügbar ist.

**Vier Knoten**: Mit vier Knoten (N = 4) liegt die Anforderung zum Erstellen eines Quorums bei drei Knoten (4/2 + 1 = 3). Dies bedeutet, dass Sie einen einzelnen Knoten verlieren und das Quorum dennoch beibehalten können. Der gleichzeitige Ausfall von zwei Knoten führt jedoch dazu, dass es bei den Systemdiensten zu einem Quorumverlust kommt und der Cluster nicht mehr verfügbar ist.

**Fünf Knoten**: Mit fünf Knoten (N = 5) liegt die Anforderung zum Erstellen eines Quorums weiterhin bei drei Knoten (5/2 + 1 = 3). Dies bedeutet, dass Sie zwei Knoten gleichzeitig verlieren und das Quorum für die Systemdienste weiter beibehalten können.

Bei Produktionsworkloads müssen Sie für Resilienz gegenüber dem gleichzeitigen Ausfall von mindestens zwei Knoten (z.B. einer durch ein Clusterupgrade, ein weiterer aus anderen Gründen) sorgen, deshalb sind fünf Knoten erforderlich.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Kann ich meinen Cluster am Abend/Wochenende abschalten, um Kosten zu sparen?

Im Allgemeinen nicht. Service Fabric speichert den Zustand auf lokalen, kurzlebigen Festplatten. Dies bedeutet beim Verschieben des virtuellen Computers auf einen anderen Host, dass die Daten nicht mit verschoben werden. Im normalen Betrieb ist das kein Problem, da der neue Knoten von anderen Knoten auf den neuesten Stand gebracht wird. Wenn Sie jedoch alle Knoten beenden und später neu starten, besteht die erhebliche Möglichkeit, dass die meisten der Knoten auf neuen Hosts gestartet werden, sodass das System nicht wiederhergestellt werden kann.

Wenn Sie Cluster erstellen möchten, um Ihre Anwendung vor der Bereitstellung zu testen, empfiehlt es sich, diese Cluster dynamisch als Teil Ihrer [Continuous Integration/Continuous Deployment-Pipeline](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) zu erstellen.


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Wie aktualisiere ich mein Betriebssystem (z.B. von Windows Server 2012 auf Windows Server 2016)?

Wir arbeiten an einer optimierten Lösung, derzeit müssen Sie jedoch das Upgrade durchführen. Sie müssen das Betriebssystemimage nacheinander auf den einzelnen virtuellen Computern des Clusters aktualisieren. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kann ich die angefügten Datenträger in einem Clusterknotentyp (VM-Skalierungsgruppe) verschlüsseln?
Ja.  Weitere Informationen finden Sie unter [Erstellen eines Clusters mit angefügten Datenträgern](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [Verschlüsseln von Datenträgern (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) und [Verschlüsseln von Datenträgern (CLI)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kann ich virtuelle Computer mit niedriger Priorität in einem Clusterknotentyp verwenden (VM-Skalierungsgruppe)?
Nein. Virtuelle Computer mit niedriger Priorität werden nicht unterstützt. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Welche Verzeichnisse und Prozesse muss ich ausschließen, wenn ein Antivirenprogramm in meinem Cluster ausgeführt wird?

| **Vom Virenschutz ausgeschlossene Verzeichnisse** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (aus der Clusterkonfiguration) |
| FabricLogRoot (aus der Clusterkonfiguration) |

| **Vom Virenschutz ausgeschlossene Prozesse** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Wie kann sich meine Anwendung bei KeyVault authentifizieren, um geheime Schlüssel abzurufen?
Nachfolgend werden die Möglichkeiten für Ihre Anwendung zum Abrufen von Anmeldeinformationen für die Authentifizierung bei Key Vault aufgeführt:

A. Während des Erstellungs-/Packauftrags Ihrer Anwendungen können Sie ein Zertifikat in das Datenpaket Ihrer Service Fabric-App pullen und dieses für die Authentifizierung bei Key Vault verwenden.
B: Für MSI-fähige Hosts von VM-Skalierungsgruppen können Sie einen einfachen PowerShell-SetupEntryPoint für Ihre Service Fabric-App entwickeln, um [ein Zugriffstoken vom MSI-Endpunkt](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token) abzurufen und dann [Ihre geheimen Schlüssel aus KeyVault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret) abzurufen.

## <a name="application-design"></a>Anwendungsentwurf

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Was ist die beste Möglichkeit, Daten über Partitionen einer zuverlässigen Sammlung hinweg abzufragen?

Zuverlässige Sammlungen sind in der Regel [partitioniert](service-fabric-concepts-partitioning.md), um eine horizontale Hochskalierung für bessere Leistung und einen verbesserten Durchsatz zu ermöglichen. Das bedeutet, dass der Zustand für einen bestimmten Dienst über Dutzende oder Hunderte von Computern verteilt sein kann. Um Vorgänge über dieses vollständige Dataset auszuführen, stehen Ihnen einige Optionen zur Verfügung:

- Erstellen Sie einen Dienst, der alle Partitionen eines anderen Diensts abfragt, um die erforderlichen Daten abzurufen.
- Erstellen Sie einen Dienst, der Daten von allen Partitionen eines anderen Diensts empfangen kann.
- Verschieben Sie Daten aus den einzelnen Diensten regelmäßig mithilfe von Push in einen externen Speicher. Dieser Ansatz ist nur geeignet, wenn die von Ihnen durchgeführten Abfragen nicht Teil Ihrer Kerngeschäftslogik sind.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Welche Möglichkeit bietet sich an, um Daten akteurübergreifend abzufragen?

Akteure sind als unabhängige Einheiten von Zustand und Compute vorgesehen, daher wird davon abgeraten, zur Laufzeit umfassende Abfragen des Akteurzustands durchzuführen. Wenn Sie eine Abfrage über den vollständigen Satz von Akteurzuständen durchführen müssen, sollten Sie Folgendes in Betracht ziehen:

- Ersetzen Sie Ihre Akteurdienste durch zustandsbehaftete zuverlässige Dienste mit der Anzahl von Netzwerkanforderungen, um alle Daten von der Anzahl von Akteuren bis hin zur Anzahl der Partitionen in Ihrem Dienst zu erfassen.
- Entwerfen Sie Ihre Akteure so, dass sie in regelmäßigen Abständen ihren Zustand mithilfe von Push an einen externen Speicher übertragen, um eine einfachere Abfrage zu ermöglichen. Wie oben erwähnt, ist dieser Ansatz nur umsetzbar, wenn die von Ihnen durchgeführten Abfragen für das Laufzeitverhalten nicht erforderlich sind.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Wie viele Daten kann ich in einer zuverlässigen Sammlung speichern?

Zuverlässige Dienste werden in der Regel partitioniert, sodass die speicherbare Menge nur durch die Anzahl der Computer im Cluster und durch die Menge des verfügbaren Arbeitsspeichers auf diesen Computern begrenzt wird.

Nehmen Sie beispielsweise an, Sie hätten eine zuverlässige Sammlung in einem Dienst mit 100 Partitionen und 3 Replikaten, in der Objekte von durchschnittlich 1 KB gespeichert werden. Nehmen Sie außerdem an, Sie hätten einen Cluster mit 10 Computern mit jeweils 16 GB Arbeitsspeicher. Gehen wir der Einfachheit halber und für eine konservative Schätzung davon aus, dass das Betriebssystem und die Systemdienste, die Service Fabric-Runtime und Ihre Dienste 6 GB davon beanspruchen, sodass pro Computer 10 GB oder für den gesamten Cluster 100 GB übrig bleiben.

Denken Sie daran, dass jedes Objekt dreimal gespeichert werden muss (ein primäres Objekt und zwei Replikate). Demnach hätten Sie genügend Arbeitsspeicher für ca. 35 Millionen Objekte in Ihrer Sammlung, wenn Sie mit voller Kapazität arbeiten. Wir empfehlen jedoch, den gleichzeitigen Ausfall einer Fehlerdomäne und einer Upgradedomäne aufzufangen, was in etwa 1/3 der Kapazität ausmacht, und würden daher die Anzahl auf ungefähr 23 Millionen verringern.

Beachten Sie, dass bei dieser Berechnung zudem Folgendes vorausgesetzt wird:

- Die Daten sind einigermaßen gleichmäßig über die Partitionen verteilt, oder Sie melden Auslastungsmetriken an den Resource Manager des Clusters. Standardmäßig führt Service Fabric basierend auf der Replikatanzahl einen Lastenausgleich durch. Im vorherigen Beispiel würden 10 primäre Replikate und 20 sekundäre Replikate auf jedem Knoten im Cluster platziert. Dies funktioniert gut für Lasten, die gleichmäßig auf die Partitionen verteilt sind. Wenn die Last nicht gleichmäßig verteilt ist, müssen Sie die Last melden, damit der Resource Manager kleinere Replikate zusammenlegen und zulassen kann, dass größere Replikate auf einem einzelnen Knoten mehr Arbeitsspeicher beanspruchen.

- Der betreffende zuverlässige Dienst ist der einzige im Cluster, auf dem der Zustand gespeichert wird. Da Sie einem Cluster mehrere Dienste bereitstellen können, müssen Sie die Ressourcen berücksichtigen, die jeder Dienst zum Ausführen und Verwalten seines Zustands benötigt.

- Der Cluster selbst wird nicht vergrößert oder verkleinert. Wenn Sie weitere Computer hinzufügen, führt Service Fabric zum Nutzen der zusätzlichen Kapazität einen erneuten Ausgleich der Replikate durch, bis die Anzahl der Computer die Anzahl der Partitionen in Ihrem Dienst übersteigt, da ein einzelnes Replikat sich nicht auf mehrere Computer erstrecken kann. Wenn Sie im Gegensatz dazu die Größe des Clusters durch das Entfernen von Computern reduzieren, werden die Replikate enger gepackt und weisen eine geringere Gesamtkapazität auf.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Wie viele Daten können in einem Akteur gespeichert werden?

Wie bei zuverlässigen Diensten wird die Menge der Daten, die Sie in einem Akteurdienst speichern können, nur durch den insgesamt verfügbaren Speicherplatz und Arbeitsspeicher auf den Knoten in Ihrem Cluster begrenzt. Einzelne Akteure sind jedoch am effektivsten, wenn sie zum Kapseln einer kleinen Menge von Zustandslogik und der zugehörigen Geschäftslogik verwendet werden. Allgemein gilt, dass ein einzelner Akteur einen Zustand aufweisen sollte, der in KB gemessen werden kann.

## <a name="other-questions"></a>Weitere Fragen

### <a name="how-does-service-fabric-relate-to-containers"></a>Welcher Zusammenhang besteht zwischen Service Fabric und Containern?

Container bieten eine einfache Möglichkeit zum Paketieren von Diensten und ihren Abhängigkeiten, sodass sie konsistent in allen Umgebungen und in isolierter Weise auf einem einzelnen Computer ausgeführt werden können. Service Fabric bietet eine Möglichkeit zum Bereitstellen und Verwalten von Diensten, darunter [Dienste, die in einem Container paketiert wurden](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Planen Sie die Open Source-Bereitstellung von Service Fabric?

Wir haben Teile von Service Fabric ([Reliable Services-Framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [Reliable Actors-Framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET Core-Integrationsbibliotheken](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer) und die [Service Fabric-Befehlszeilenschnittstelle](https://github.com/Azure/service-fabric-cli)) als Open-Source-Code auf GitHub bereitgestellt und akzeptieren Communitybeiträge für diese Projekte. 

[Vor kurzem haben wir bekannt gegeben](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/), dass wir die Breitstellung der Service Fabric-Laufzeit als Open-Source-Code planen. Aktuell steht auf GitHub das [Service Fabric-Repository](https://github.com/Microsoft/service-fabric/) mit Erstellungs- und Testtools für Linux zur Verfügung. Sie können also das Repository klonen, Service Fabric für Linux erstellen, grundlegende Tests ausführen, Probleme melden und Pull Requests übermitteln. Wir arbeiten mit Hochdruck daran, auch die Windows-Buildumgebung zu migrieren – zusammen mit einer vollständigen CI-Umgebung.

Folgen Sie dem [Service Fabric-Blog](https://blogs.msdn.microsoft.com/azureservicefabric/), in dem Sie weitere Details finden, sobald sie angekündigt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [wesentlichen Service Fabric-Konzepten](service-fabric-technical-overview.md) und [Bewährte Methoden](service-fabric-best-practices-overview.md) [Service Fabric-Konzepte](service-fabric-technical-overview.md) und [Bewährte Methoden](service-fabric-best-practices-overview.md)
