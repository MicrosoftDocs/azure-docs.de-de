---
title: Knoten und Pools in Azure Batch
description: Erfahren Sie mehr über Computeknoten und Pools und deren Verwendung in einem Azure Batch-Workflow aus Entwicklersicht.
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: 880a956a2d839483c59578afad1b62146799578a
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95243068"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Knoten und Pools in Azure Batch

In einem Azure Batch-Workflow ist ein *Computeknoten* (bzw. *Knoten*) ein virtueller Computer, der einen Teil der Workload Ihrer Anwendung verarbeitet. Ein *Pool* ist eine Sammlung von Knoten, auf denen Ihre Anwendung ausgeführt wird. In diesem Artikel erfahren Sie mehr über Knoten und Pools sowie Aspekte bei deren Erstellung und Nutzung in einem Azure Batch-Workflow.

## <a name="nodes"></a>Nodes

Ein Computeknoten ist eine Azure- oder Clouddienst-VM, die für die Verarbeitung eines Teils der Anwendungsworkload fest zugeordnet ist. Die Größe eines Knotens bestimmt die Anzahl von CPU-Kernen, die Speicherkapazität und die lokale Dateisystemgröße, die dem Knoten zugeordnet werden.

Pools mit Windows- oder Linux-Knoten können mithilfe von Azure Cloud Services, auf der Grundlage von Images aus dem [Azure Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) oder auf der Grundlage eigener benutzerdefinierter Images erstellt werden.

Knoten können beliebige ausführbare Dateien oder Skripts ausführen, die von der Betriebssystemumgebung des Knotens unterstützt werden. Zu den ausführbaren Dateien oder Skripts zählen \*.exe-, \*.cmd-, \*.bat-Dateien und PowerShell-Skripts (für Windows) sowie Binärdateien, Shell- und Python-Skripts (für Linux).

Alle Computeknoten in Batch enthalten außerdem Folgendes:

- Eine standardmäßige [Ordnerstruktur](files-and-directories.md) und die dazugehörigen [Umgebungsvariablen](jobs-and-tasks.md), die zur Referenzierung durch Tasks verfügbar sind.
- **Firewalleinstellungeneinstellungen** .
- [Remotezugriff](error-handling.md#connect-to-compute-nodes) auf Windows-Knoten (Remotedesktopprotokoll, RDP) und Linux-Knoten (Secure Shell, SSH), es sei denn, Sie [erstellen einen Pool mit deaktiviertem Remotezugriff](pool-endpoint-configuration.md)

Standardmäßig können Knoten miteinander kommunizieren, sie können jedoch nicht mit virtuellen Computern kommunizieren, die nicht Teil desselben Pools sind. Um die sichere Kommunikation zwischen Knoten und anderen VMs oder einem lokalen Netzwerk zu ermöglichen, können Sie den Pool in einem [Subnetz eines virtuellen Azure-Netzwerks (VNet)](batch-virtual-network.md) bereitstellen. Sie können dann über öffentliche IP-Adressen auf Ihre Knoten zugreifen. Diese öffentlichen IP-Adressen werden von Batch erstellt und können sich im Laufe der Lebensdauer des Pools ändern. Sie können auch einen [Pool mit statischen öffentlichen IP-Adressen erstellen](create-pool-public-ip.md), die von Ihnen gesteuert werden, sodass sichergestellt ist, dass sie sich nicht unerwartet ändern.

## <a name="pools"></a>Pools

Ein Pool ist eine Sammlung von Knoten, auf denen Ihre Anwendung ausgeführt wird.

Azure Batch-Pools basieren auf der Azure-Computeplattform. Sie bieten Funktionen für umfangreiche Zuordnungen, für die Anwendungsinstallation, die Datenverteilung und die Systemüberwachung sowie für die flexible Anpassung ([Skalierung](#automatic-scaling-policy)) der Computeknotenanzahl innerhalb eines Pools.

Jedem Knoten, der einem Pool hinzugefügt wird, werden ein eindeutiger Name und eine IP-Adresse zugewiesen. Beim Entfernen eines Knotens aus einem Pool gehen alle Änderungen am Betriebssystem oder an den Dateien verloren, und sein Name und die IP-Adresse werden zur späteren Verwendung freigegeben. Wenn ein Knoten einen Pool verlässt, endet seine Lebensdauer.

Ein Pool kann nur von dem Batch-Konto verwendet werden, unter dem er erstellt wurde. In einem Batch-Konto können mehrere Pools erstellt werden, um die Ressourcenanforderungen der Anwendungen zu erfüllen, die ausgeführt werden sollen.

Der Pool kann manuell oder [automatisch vom Batch-Dienst](#autopools) erstellt werden, wenn Sie die zu erledigenden Aufgaben angeben. Wenn Sie einen Pool erstellen, können Sie die folgenden Attribute angeben:

- [Betriebssystem und Version von Knoten](#operating-system-and-version)
- [Knotentyp und Zielanzahl der Knoten](#node-type-and-target)
- [Knotengröße](#node-size)
- [Richtlinie für automatische Skalierung](#automatic-scaling-policy)
- [Richtlinie zur Taskplanung](#task-scheduling-policy)
- [Kommunikationsstatus](#communication-status)
- [Starttasks](#start-tasks)
- [Anwendungspakete](#application-packages)
- [Konfiguration von VNET (virtuelles Netzwerk) und Firewall](#virtual-network-vnet-and-firewall-configuration)
- [Gültigkeitsdauer](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Batch-Konten verfügen über ein Standardkontingent, das die Anzahl von Kernen in einem Batch-Konto begrenzt. Die Anzahl der Kerne entspricht der Anzahl von Serverknoten. Die Standardkontingente und eine Anleitung zum [Erhöhen des Kontingents](batch-quota-limit.md#increase-a-quota) finden Sie unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md). Wenn der Pool die Zielanzahl der Knoten nicht erreicht, könnte das Kontingent für die Kerne die Ursache sein.

## <a name="operating-system-and-version"></a>Betriebssystem und -version

Wenn Sie einen Batch-Pool erstellen, können Sie die Konfiguration der Azure-VM und den Typ des Betriebssystems angeben, das auf jedem Computeknoten im Pool ausgeführt werden soll.

## <a name="configurations"></a>Configurations

In Batch sind zwei Arten von Poolkonfigurationen verfügbar.

### <a name="virtual-machine-configuration"></a>VM-Konfiguration

Die **Konfiguration „Virtueller Computer“** gibt an, dass der Pool aus virtuellen Azure-Computern besteht. Diese virtuellen Computer können aus Linux- oder Windows-Images erstellt werden.

Der [Batch-Knoten-Agent](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) ist ein Programm, das auf jedem Knoten im Pool ausgeführt wird. Er stellt die Befehls- und Steuerungsschnittstelle zwischen dem Knoten und dem Batch-Dienst dar. Es gibt verschiedene Implementierungen des Knoten-Agents (SKUs) für verschiedene Betriebssysteme. Beim Erstellen eines Pools auf Basis der Konfiguration „Virtueller Computer“ müssen Sie nicht nur die Knotengröße und die Quelle der Images für deren Erstellen angeben, sondern auch die **VM-Imagereferenz** und die **Knoten-Agent-SKU** von Batch, die auf den Knoten installiert werden soll. Weitere Informationen zum Angeben dieser Pooleigenschaften finden Sie unter [Bereitstellen von Linux-Computeknoten in Azure Batch-Pools](batch-linux-nodes.md). Sie können optional leere Datenträger an virtuelle Poolcomputer anfügen, die auf der Grundlage von Marketplace-Images erstellt wurden, oder Datenträger in benutzerdefinierte Images einschließen, die zum Erstellen der virtuellen Computer verwendet werden. Wenn Sie Datenträger einbeziehen, müssen Sie sie innerhalb eines virtuellen Computers einbinden und formatieren, um sie zu verwenden.

### <a name="cloud-services-configuration"></a>Konfiguration „Cloud Services“

Die **Konfiguration „Cloud Services“** gibt an, dass der Pool aus Azure Cloud Services-Knoten besteht. Cloud Services stellt nur Windows-Computeknoten bereit.

Die verfügbaren Betriebssysteme für Pools zur Konfiguration der Clouddienste sind unter [Azure-Gastbetriebssystemversionen und SDK-Kompatibilitätsmatrix](../cloud-services/cloud-services-guestos-update-matrix.md) aufgeführt, und die verfügbaren Größen für Computeknoten sind unter [Größen für Clouddienste](../cloud-services/cloud-services-sizes-specs.md) aufgelistet. Beim Erstellen eines Pools, der Cloud Services-Knoten enthält, geben Sie die Knotengröße und *Betriebssystemfamilie* an (die bestimmt, welche Versionen von .NET mit dem Betriebssystem installiert werden). Cloud Services wird in Azure schneller bereitgestellt als virtuelle Computer mit Windows. Wenn Sie Pools von Windows-Serverknoten benötigen, werden Sie möglicherweise feststellen, dass die Konfiguration „Cloud Services“ im Hinblick auf die Bereitstellungszeit einen Leistungsvorteil bietet.

Genau wie bei Workerrollen innerhalb von Cloud Services können Sie eine *Betriebssystemversion* angeben. (Weitere Informationen zu Workerrollen finden Sie unter [Übersicht über Cloud Services](../cloud-services/cloud-services-choose-me.md).) Es empfiehlt sich auch bei der *Betriebssystemversion* die Angabe von `Latest (*)`, damit die Knoten automatisch per Upgrade aktualisiert werden und für neue Versionen kein Zusatzaufwand entsteht. Mit der Wahl einer bestimmten Betriebssystemversion wird in erster Linie die Anwendungskompatibilität sichergestellt. Hierzu wird die Überprüfung der Abwärtskompatibilität vor der Versionsaktualisierung ermöglicht. Nach der Überprüfung können die *Betriebssystemversion* für den Pool aktualisiert und das neue Betriebssystemimage installiert werden. Alle laufenden Tasks werden unterbrochen und erneut in die Warteschlange gestellt.

### <a name="node-agent-skus"></a>Knoten-Agent-SKUs

Wenn Sie einen Pool erstellen, müssen Sie je nach dem Betriebssystem des Basisimages Ihrer VHD die entsprechende **nodeAgentSkuId** auswählen. Eine Zuordnung der verfügbaren Knoten-Agent-SKU-IDs zu ihren Betriebssystemimages erhalten Sie durch den Aufruf des Vorgangs unter [List Supported Node Agent SKUs](/rest/api/batchservice/list-supported-node-agent-skus) (Auflisten der unterstützten Knoten-Agent-SKUs).

### <a name="custom-images-for-virtual-machine-pools"></a>Benutzerdefinierte Images für Pools virtueller Computer

Weitere Informationen zum Erstellen eines Pools mit benutzerdefinierten Images finden Sie unter [Verwenden von Shared Image Gallery zum Erstellen eines benutzerdefinierten Pools](batch-sig-images.md).

Alternativ können Sie einen benutzerdefinierten Pool mit virtuellen Computern erstellen, indem Sie eine Ressource mit [verwaltetem Image](batch-custom-images.md) verwenden. Informationen zum Vorbereiten benutzerdefinierter Linux-Images von Azure-VMs finden Sie unter [Generalisieren und Erfassen eines virtuellen Linux-Computers](../virtual-machines/linux/capture-image.md). Informationen zum Vorbereiten benutzerdefinierter Windows-Images von virtuellen Azure-Computern finden Sie unter [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](../virtual-machines/windows/capture-image-resource.md).

### <a name="container-support-in-virtual-machine-pools"></a>Containerunterstützung in VM-Pools

Wenn Sie mithilfe der Batch-APIs einen VM-Konfigurationspool erstellen, können Sie den Pool zum Ausführen von Aufgaben in Docker-Containern einrichten. Derzeit müssen Sie den Pool unter Verwendung eines Images erstellen, das Docker-Container unterstützt. Verwenden Sie das Image „Windows Server 2016 Datacenter mit Containern“ aus dem Azure Marketplace, oder stellen Sie ein benutzerdefiniertes VM-Image mit Docker Community Edition oder Enterprise Edition und allen erforderlichen Treibern bereit. Die Pooleinstellungen müssen eine [Containerkonfiguration](/rest/api/batchservice/pool/add) enthalten, die bei der Poolerstellung Containerimages auf die virtuellen Computer kopiert. Für den Pool ausgeführte Aufgaben können dann auf die Containerimages und auf die Containerausführungsoptionen verweisen.

Weitere Informationen finden Sie unter [Ausführen von Docker-Containeranwendungen in Azure Batch](batch-docker-container-workloads.md).

## <a name="node-type-and-target"></a>Typ und Zielanzahl der Knoten

Wenn Sie einen Pool erstellen, können Sie die gewünschten Typen von Knoten und die Zielanzahl für jeden einzelnen angeben. Es gibt zwei Knotentypen:

- **Dedizierte Knoten.** Dedizierte Serverknoten sind für Ihre Workloads reserviert. Sie sind teurer als Knoten mit niedriger Priorität, aber sie werden garantiert nie zurückgestellt.
- **Knoten mit niedriger Priorität.** Knoten mit niedriger Priorität nutzen überschüssige Kapazität in Azure, um Batch-Workloads auszuführen. Knoten mit niedriger Priorität sind pro Stunde kostengünstiger als dedizierte Knoten und erlauben Workloads, die eine erhebliche Computeleistung erfordern. Weitere Informationen finden Sie unter [Use low-priority VMs with Batch (Verwenden von VMs mit niedriger Priorität mit Batch)](batch-low-pri-vms.md).

Knoten mit niedriger Priorität können zurückgestellt werden, wenn Azure nicht über genügend überschüssige Kapazität verfügt. Wenn ein Knoten beim Ausführen von Aufgaben zurückgestellt wird, werden die Aufgaben in eine Warteschlange gestellt und erneut ausgeführt, wenn wieder ein Serverknoten verfügbar ist. Knoten mit niedriger Priorität eignen sich gut für Workloads, bei denen der Zeitpunkt des Auftragsabschlusses flexibel ist und die Arbeit über viele Knoten verteilt wird. Bevor Sie sich entscheiden, Knoten mit niedriger Priorität für Ihr Szenario zu verwenden, sollten Sie sich vergewissern, dass sämtliche Arbeit, die aufgrund einer vorzeitigen Entfernung verloren geht, gering und einfach zu wiederholen ist.

Sie können sowohl Serverknoten mit niedriger Priorität als auch dedizierte Serverknoten im selben Pool verwenden. Jeder Knotentyp verfügt über eine eigene Zieleinstellung, für die Sie die gewünschte Anzahl von Knoten angeben können.

Die Anzahl der Serverknoten wird als *Ziel* bezeichnet, da Ihr Pool in einigen Fällen unter Umständen nicht die gewünschte Anzahl von Knoten erreicht. Ein Pool kann das Ziel zum Beispiel nicht erreichen, wenn er zuvor das [Kernkontingent](batch-quota-limit.md) Ihres Batch-Kontos erreicht. Der Pool kann das Ziel auch nicht erreichen, wenn Sie eine Formel für automatische Skalierung auf den Pool angewendet haben, die die maximale Anzahl von Knoten beschränkt.

Informationen zu den Preisen für beide dedizierte Knoten und Knoten mit niedriger Priorität finden Sie unter [Batch – Preise](https://azure.microsoft.com/pricing/details/batch/).

## <a name="node-size"></a>Knotengröße

Wenn Sie einen Azure Batch-Pool erstellen, können Sie aus fast allen in Azure verfügbaren VM-Familien und -Größen wählen. Azure bietet verschiedene VM-Größen für unterschiedliche Workloads, u.a. spezialisierte [HPC-](../virtual-machines/sizes-hpc.md)- oder [GPU-fähige](../virtual-machines/sizes-gpu.md) VM-Größen. 

Weitere Informationen finden Sie unter [Choose a VM size for compute nodes in an Azure Batch pool](batch-pool-vm-sizes.md) (Auswählen einer VM-Größe für Computeknoten in einem Azure Batch-Pool).

## <a name="automatic-scaling-policy"></a>Richtlinie für automatische Skalierung

Bei dynamischen Workloads können Sie eine Richtlinie für automatische Skalierung auf einen Pool anwenden. Der Batch-Dienst wertet Ihre Formel regelmäßig aus und passt die Anzahl der Knoten innerhalb des Pools dynamisch an die aktuelle Workload und den Ressourcenverbrauch Ihres Computeszenarios an. So können Sie die Gesamtkosten für die Ausführung Ihrer Anwendung senken, indem Sie nur die erforderlichen Ressourcen verwenden und nicht benötigte Ressourcen freigeben.

Sie aktivieren die automatische Skalierung, indem Sie eine [Formel für die automatische Skalierung](batch-automatic-scaling.md#autoscale-formulas) erstellen und diese Formel einem Pool zuordnen. Der Batch-Dienst verwendet die Formel, um die vorgegebene Anzahl von Knoten im Pool für das nächste Skalierungsintervall (welches Sie konfigurieren können) zu bestimmen. Sie können für einen Pool die Einstellungen für die automatische Skalierung angeben, wenn Sie diesen erstellen, oder Sie können die Skalierung für einen Pool später aktivieren. Außerdem können Sie die Skalierungseinstellungen für einen Pool aktualisieren, für den die Skalierung aktiviert ist.

Beispielsweise kann es für einen Auftrag erforderlich sein, eine große Anzahl von Tasks zu übermitteln, die ausgeführt werden sollen. Sie können dem Pool eine Skalierungsformel zuweisen, die die Anzahl von Knoten im Pool auf der Grundlage der aktuellen Anzahl von Tasks in der Warteschlange sowie der Abschlussrate der Tasks im Auftrag anpasst. Der Batch-Dienst wertet die Formel in regelmäßigen Abständen aus und ändert die Größe des Pools auf Grundlage von Workload und anderen Formeleinstellungen. Der Dienst fügt bei Bedarf Knoten hinzu, wenn eine große Anzahl von Tasks in der Warteschlange vorhanden ist und entfernt Knoten, wenn keine Tasks in der Warteschlange sind oder ausgeführt werden.

Eine Skalierungsformel kann auf den folgenden Metriken basieren:

- **Zeitmetriken** : Basieren auf Statistiken, die alle fünf Minuten für die angegebene Anzahl von Stunden erfasst werden.
- **Ressourcenmetriken** : Basieren auf CPU-Auslastung, Bandbreitenauslastung, Speicherauslastung und Knotenanzahl.
- **Taskmetriken**: Basieren auf dem Taskstatus – beispielsweise *Aktiv* (in der Warteschlange), *Wird ausgeführt* oder *Abgeschlossen*.

Wenn die Anzahl von Computeknoten in einem Pool durch die automatische Skalierung verringert wird, müssen Sie sich überlegen, wie Sie Tasks behandeln, die zum Zeitpunkt des Verringerungsvorgangs ausgeführt werden. Hierfür verfügt Batch über eine [*Option zum Aufheben der Knotenzuordnung*](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption), die Sie in Ihre Formeln einfügen können. Sie können beispielsweise angeben, dass ausgeführte Tasks sofort beendet und dann für die Ausführung auf einem anderen Knoten erneut in die Warteschlange eingereiht werden oder dass die Fertigstellung abgewartet werden soll, bevor der Knoten aus dem Pool entfernt wird. Beachten Sie, dass das Festlegen der Optionen zum Aufheben der Knotenzuordnung auf `taskcompletion` oder `retaineddata` Vorgänge zum Ändern der Poolgröße verhindert, bis alle Aufgaben abgeschlossen sind bzw. alle Aufbewahrungszeiträume von Tasks abgelaufen sind.

Weitere Informationen zur automatischen Skalierung einer Anwendung finden Sie unter [Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool](batch-automatic-scaling.md).

> [!TIP]
> Legen Sie die vorgegebene Anzahl von Knoten am Ende eines Auftrags auf Null fest, und lassen Sie das Abschließen ausgeführter Tasks zu, um die Auslastung von Computeressourcen zu maximieren.

## <a name="task-scheduling-policy"></a>Richtlinie zur Taskplanung

Die Konfiguration der [maximalen Tasks pro Knoten](batch-parallel-node-tasks.md) bestimmt die maximale Anzahl von Tasks, die parallel auf den einzelnen Computeknoten im Pool ausgeführt werden können.

In der Standardkonfiguration wird auf einem Knoten immer nur ein einziger Task ausgeführt. Es sind aber auch Szenarien denkbar, in denen die gleichzeitige Ausführung von zwei oder mehr Tasks auf einem Knoten von Vorteil ist. Das [Beispielszenario](batch-parallel-node-tasks.md#example-scenario) im Artikel zu [parallelen Knotenaufgaben](batch-parallel-node-tasks.md) veranschaulicht, wie Sie von mehreren Tasks pro Knoten profitieren können.

Sie können auch einen *Fülltyp* angeben. Dieser legt fest, ob Batch die Tasks gleichmäßig auf alle Knoten eines Pools verteilt oder jeden Knoten mit der maximalen Anzahl von Tasks auffüllt, bevor Tasks einem weiteren Knoten zugewiesen werden.

## <a name="communication-status"></a>Kommunikationsstatus

In den meisten Szenarien werden Tasks unabhängig voneinander ausgeführt und müssen nicht miteinander kommunizieren. Es gibt aber einige Anwendungen, bei denen Tasks kommunizieren müssen, z.B. [MPI-Szenarien](batch-mpi.md).

Sie können einen Pool zum Zulassen der **Kommunikation zwischen Knoten** konfigurieren, sodass Knoten in einem Pool zur Laufzeit miteinander kommunizieren können. Wenn die Kommunikation zwischen Knoten aktiviert ist, können Knoten in Pools vom Typ „Clouddienstkonfiguration“ miteinander über Ports kommunizieren, die über 1100 liegen. In Pools vom Typ „Konfiguration des virtuellen Computers“ bestehen keine Portbeschränkungen für Datenverkehr.

Die Aktivierung der Kommunikation zwischen Knoten wirkt sich auch auf die Anordnung der Knoten in Clustern aus. Aufgrund von Bereitstellungsbeschränkungen wird unter Umständen die maximale Anzahl von Knoten in einem Pool begrenzt. Wenn für Ihre Anwendung keine Kommunikation zwischen Knoten erforderlich ist, kann der Batch-Dienst potenziell eine große Anzahl von Knoten in vielen verschiedenen Clustern und Datencentern für den Pool reservieren, um mehr Leistung für die parallele Verarbeitung bereitzustellen.

## <a name="start-tasks"></a>Starttasks

Nach Wunsch können Sie einen [Starttask](jobs-and-tasks.md#start-task) hinzufügen, der auf jedem Knoten ausgeführt wird, wenn dieser dem Pool hinzugefügt wird, sowie bei jedem Neustart oder Reimaging eines Knotens. Der Starttask eignet sich besonders gut zum Vorbereiten von Computeknoten für die Ausführung von Tasks, z.B. das Installieren der Anwendungen, die von den Tasks auf den Computeknoten ausgeführt werden.

## <a name="application-packages"></a>Anwendungspakete

Sie können Anwendungspakete für die Bereitstellung auf den Computeknoten im Pool festlegen. Anwendungspakete bieten eine vereinfachte Bereitstellung und Versionsverwaltung der Anwendungen, die von Ihren Tasks ausgeführt werden. Die Anwendungspakete, die Sie für einen Pool angeben, werden auf jedem Knoten installiert, der dem Pool hinzugefügt wird, sowie wenn der Knoten neu gestartet oder ein Reimaging durchgeführt wird.

Weitere Informationen zum Bereitstellen von Anwendungen für Batch-Knoten mithilfe von Anwendungspaketen finden Sie unter [Anwendungsbereitstellung mit Azure Batch-Anwendungspaketen](batch-application-packages.md).

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Virtuelles Netzwerk (VNET) und Firewallkonfiguration

Wenn Sie in Batch einen Computeknotenpool bereitstellen, können Sie den Pool einem Subnetz eines [virtuellen Netzwerks (VNET)](../virtual-network/virtual-networks-overview.md) zuordnen. Zur Verwendung eines Azure VNETs muss die Batch-Client-API die Azure Active Directory-Authentifizierung verwenden. Die Azure Batch-Unterstützung für Azure AD ist unter [Authentifizieren von Lösungen des Azure Batch-Diensts mit Active Directory](batch-aad-auth.md) dokumentiert.

### <a name="vnet-requirements"></a>VNET-Anforderungen

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Weitere Informationen zum Einrichten eines Batch-Pools in einem VNet finden Sie unter [Create an Azure Batch pool in a virtual network](batch-virtual-network.md) (Erstellen eines Azure Batch-Pools in einem virtuellen Netzwerk).

> [!TIP]
> Um sicherzustellen, dass sich die öffentlichen IP-Adressen für den Zugriff auf Knoten nicht ändern, können Sie einen [Pool mit festgelegten öffentlichen IP-Adressen erstellen](create-pool-public-ip.md), die von Ihnen gesteuert werden.

## <a name="pool-and-compute-node-lifetime"></a>Gültigkeitsdauer für Pools und Computeknoten

Beim Entwerfen Ihrer Azure Batch-Lösung müssen Sie angeben, wie und wann Pools erstellt werden und wie lange Computeknoten innerhalb dieser Pools verfügbar bleiben sollen.

An einem Ende des Spektrums können Sie einen Pool für jeden Auftrag erstellen, den Sie senden, und den Pool dann löschen, sobald die Ausführung seiner Tasks abgeschlossen ist. Auf diese Weise wird die Effektivität der Nutzung erhöht, da die Knoten nur bei Bedarf zugeordnet und heruntergefahren werden, wenn sie sich im Leerlauf befinden. Dies bedeutet, dass der Auftrag bis zur Zuteilung der Knoten warten muss. Dabei ist aber wichtig zu erwähnen, dass für die Tasks die Ausführung geplant wird, sobald die Knoten einzeln zugeteilt wurden und der Starttask abgeschlossen ist. Batch wartet mit dem Zuweisen von Tasks zu den Knoten *nicht*, bis alle Knoten in einem Pool verfügbar sind. Dadurch ist eine maximale Auslastung aller verfügbaren Knoten gewährleistet.

Falls dagegen der sofortige Start von Aufträgen höchste Priorität hat, können Sie bereits vorab einen Pool erstellen und die zugehörigen Knoten vor der Auftragsübermittlung verfügbar machen. Dieses Szenario ermöglicht zwar den sofortigen Start von Tasks, führt aber unter Umständen auch dazu, dass sich Knoten im Leerlauf befinden, während sie auf die Zuteilung warten.

Zur Bewältigung einer variablen, kontinuierlichen Auslastung wird in der Regel ein kombinierter Ansatz verfolgt. Sie können über einen Pool verfügen, an den mehrere Aufträge übermittelt werden, und die Anzahl von Knoten mittels Skalierung flexibel an die jeweilige Auftragslast anpassen. Dies kann reaktiv auf der Grundlage der aktuellen Auslastung oder proaktiv erfolgen, sofern die Auslastung vorausgesagt werden kann. Weitere Informationen finden Sie unter [Richtlinie für automatische Skalierung](#automatic-scaling-policy).

## <a name="autopools"></a>Automatische Pools

Ein [automatischer Pool](/rest/api/batchservice/job/add#autopoolspecification) ist ein Pool, der vom Batch-Dienst erstellt wird, wenn ein Auftrag übermittelt wird. Er wird also nicht vor den Pool ausgeführten Aufträgen erstellt. Der Batch-Dienst verwaltet die Lebensdauer eines automatischen Pools gemäß den angegebenen Eigenschaften. In den meisten Fällen sind diese Pools auch so festgelegt, dass sie nach Abschluss ihrer Aufträge automatisch gelöscht werden.

## <a name="security-with-certificates"></a>Sicherheit mit Zertifikaten

Zertifikate müssen in der Regel beim Ver- und Entschlüsseln vertraulicher Informationen für Tasks verwendet werden. Ein Beispiel wäre etwa der Schlüssel für ein [Azure-Speicherkonto](accounts.md#azure-storage-accounts). Hierzu können Sie Zertifikate auf Knoten installieren. Verschlüsselte geheime Schlüssel werden über Befehlszeilenparameter an Tasks übergeben oder in einer der Taskressourcen eingebettet. Zum Entschlüsseln können dann installierte Zertifikate verwendet werden.

Zum Hinzufügen eines Zertifikats zu einem Batch-Konto können Sie den Vorgang [Hinzufügen eines Zertifikats](/rest/api/batchservice/certificate/add) (Batch REST) oder die Methode [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) (Batch .NET) verwenden. Sie können das Zertifikat dann einem neuen oder vorhandenen Pool zuordnen.

Wenn ein Zertifikat einem Pool zugeordnet ist, installiert der Batch-Dienst das Zertifikat auf jedem Knoten im Pool. Der Batch-Dienst installiert die entsprechenden Zertifikate beim Start des Knotens, bevor Tasks gestartet werden (einschließlich [Starttask](jobs-and-tasks.md#start-task) und [Auftrags-Manager-Task](jobs-and-tasks.md#job-manager-task)).

Wenn Sie einem bestehenden Pool ein Zertifikat hinzufügen, müssen Sie dessen Computeknoten neu starten, damit das Zertifikat auf die Knoten angewendet werden kann.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zu [Aufträgen und Tasks](jobs-and-tasks.md).
