---
title: 'Häufig gestellte Fragen: Avere vFXT für Azure'
description: Häufig gestellte Fragen zu Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: v-erkell
ms.openlocfilehash: 47a4b38d39c52992b51284776ec34cb9491020e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595411"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT für Azure: Häufig gestellte Fragen

In diesem Artikel werden Fragen beantwortet, die Ihnen bei der Entscheidung helfen können, ob Avere vFXT for Azure für Ihre Anforderungen geeignet ist. Er enthält grundlegende Informationen zu Avere vFXT und erläutert, wie die Zusammenarbeit mit anderen Azure-Komponenten und mit Produkten von Drittanbietern funktioniert. 

## <a name="general"></a>Allgemein 

### <a name="what-is-avere-vfxt-for-azure"></a>Was ist Avere vFXT für Azure?

Avere vFXT for Azure ist ein leistungsstarkes Dateisystem, das aktive Daten in Azure Compute zwischenspeichert, um kritische Workloads effizient zu verarbeiten.

### <a name="is-avere-vfxt-a-storage-solution"></a>Ist Avere vFXT eine Speicherlösung?

Nein. Avere vFXT ist ein *Dateisystemcache*, der an Speicherumgebungen wie EMC oder NetApp NAS oder einen Azure-Blobcontainer angefügt werden kann. Avere vFXT optimiert Datenanforderungen von Clients und speichert die bereitgestellten Daten zwischen, um die Leistung bedarfsgerecht und im Lauf der Zeit zu verbessern. Avere vFXT speichert selbst keine Daten. Es verfügt über keine Informationen hinsichtlich der Menge der gespeicherten Daten.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Ist Avere vFXT eine Tieringlösung?

Avere vFXT ordnet Daten nicht automatisch zwischen heißen und kalten Speicherebenen ein.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Woher weiß ich, ob eine Umgebung für Avere vFXT geeignet ist?

Am besten stellen Sie sich hierzu folgende Frage: „Kann die Workload zwischengespeichert werden?“ Das entspricht der Frage, ob die Workload ein hohes Verhältnis zwischen Lese- und Schreibvorgängen hat. Beispiel: ein Verhältnis von 80/20 oder 70/30 für Lese- und Schreibvorgänge.

Erwägen Sie Avere vFXT für Azure, wenn Sie über eine dateibasierte Analysepipeline verfügen, die über eine große Anzahl von virtuellen Azure-Computern ausgeführt wird, und wenn sie mindestens eine der folgenden Bedingungen erfüllt:

* Die Gesamtleistung ist aufgrund der langen Dateizugriffszeiten (je nach Anforderung Dutzende Millisekunden oder Sekunden) langsam oder inkonsistent. Diese Wartezeit ist für den Endkunden nicht akzeptabel.

* Die für die Verarbeitung erforderlichen Daten befinden sich am anderen Ende einer WAN-Umgebung, und das dauerhafte Verschieben dieser Daten ist unpraktisch. Die Daten können sich in einer anderen Azure-Region oder in einem vom Kunden betriebenen Rechenzentrum befinden.

* Eine beträchtliche Anzahl von Clients fordert die Daten an, z. B. in einem HPC-Cluster (High Performance Computing). Die große Anzahl gleichzeitiger Anforderungen kann die Wartezeit erhöhen.

* Der Kunde möchte seine aktuelle Pipeline unverändert auf virtuellen Azure-Computern ausführen und benötigt für Skalierbarkeit eine POSIX-basierte Lösung für freigegebenen Speicher (oder Cache). Mithilfe von Avere vFXT for Azure müssen Sie die Arbeitspipeline nicht neu strukturieren, um native Aufrufe für Azure Blob Storage durchzuführen.

* Ihre HPC-Anwendung basiert auf NFSv3-Clients. (Unter bestimmten Umständen können SMB 2.1-Clients verwendet werden, die Leistung ist jedoch begrenzt.)

Im folgenden Diagramm wird die Antwort auf diese Frage vereinfacht. Je weiter Ihr Workflow der Situation oben rechts entspricht, desto wahrscheinlicher ist es, dass die Avere-Cachinglösung für Ihre Umgebung geeignet ist.

![Diagramm, das zeigt, dass Workloads mit vielen Lesevorgängen bei Tausenden von Clients besser für Avere vFXT geeignet sind](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Bei welcher Anzahl von Kunden ist die Avere vFXT-Lösung am sinnvollsten?

Die Avere vFXT-Cachelösung ist für die Verarbeitung von Hunderten, Tausenden oder Zehntausenden von Computekernen ausgelegt. Wenn Sie über einige Computer mit geringer Workload verfügen, ist Avere vFXT nicht die richtige Lösung.

Typische Avere vFXT-Kunden führen anspruchsvolle Workloads ab etwa 1.000 CPU-Kernen aus. Diese Umgebungen können bis zu 50.000 Kerne oder auch mehr umfassen. Da Avere vFXT skalierbar ist, können Sie Knoten hinzufügen, die diese Workloads unterstützen, wenn sie zunehmen und mehr Durchsatz oder mehr IOPS erfordern.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Wie viele Daten können in einer Avere vFXT-Umgebung gespeichert werden?

Avere vFXT ist ein Cache. Es werden keine Daten spezifisch gespeichert. Es verwendet eine Kombination aus RAM und SSDs für die Zwischenspeicherung der Daten. Die Daten werden dauerhaft in einem Back-End-Speichersystem (z.B. einem NetApp NAS-System oder einem Blobcontainer) gespeichert. Das Avere vFXT-System verfügt nicht über Informationen hinsichtlich der Menge der gespeicherten Daten. Avere vFXT speichert lediglich die Teilmenge der Daten zwischen, die von Clients angefordert werden.  

### <a name="what-regions-are-supported"></a>Welche Regionen werden unterstützt?

Avere vFXT for Azure wird mit Ausnahme von Regionen mit Datenhoheit (China, Deutschland) in allen Regionen unterstützt. Stellen Sie sicher, dass die zu verwendende Region die große Anzahl von Computekernen und die für die Erstellung des Avere vFXT-Clusters erforderlichen VM-Instanzen unterstützt.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Wie erhalte ich Hilfe bei der Verwendung von Avere vFXT?

Eine spezialisierte Supportgruppe bietet Hilfe zu Avere vFXT for Azure. Befolgen Sie die Anweisungen unter [Abrufen von Hilfe zu Ihrem System](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt), um ein Supportticket über das Azure-Portal zu öffnen. 

### <a name="is-avere-vfxt-highly-available"></a>Ist Avere vFXT hochverfügbar?

Ja, Avere vFXT wird ausschließlich als Hochverfügbarkeitslösung betrieben.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Unterstützt Avere vFXT für Azure auch andere Clouddienste?

Ja, Kunden können mit dem Avere vFXT-Cluster mehrere Cloudanbieter verwenden. Es unterstützt Standardbuckets von AWS S3 und Google Cloud Services sowie Azure-Blobcontainer. 

> [!NOTE] 
> Für die Nutzung von Avere vFXT in AWS oder Google Cloud fällt eine Softwaregebühr an, jedoch nicht bei Azure.

## <a name="technical-compute"></a>Technisch: Compute

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Können Sie beschreiben, wie eine Avere vFXT-Umgebung „aussieht“?

Avere vFXT ist eine gruppierte Appliance, die sich aus mehreren virtuellen Azure-Computern zusammensetzt. Eine Python-Bibliothek übernimmt die Erstellung, Löschung und Modifikation von Clustern. Weitere Informationen finden Sie unter [Was ist Avere vFXT für Azure?](avere-vfxt-overview.md). 

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Auf welcher Art von virtuellen Azure-Computern wird Avere vFXT ausgeführt?  

Avere vFXT for Azure-Cluster verwenden virtuelle Computer mit Microsoft Azure E32s_v3. 

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.

-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Kann die Avere vFXT-Umgebung skaliert werden?

Der Avere vFXT-Cluster kann zwischen drei und 24 virtuelle Computerknoten umfassen. Wenden Sie sich an den technischen Support von Azure, um Hilfe bei der Planung zu erhalten, wenn Sie den Meinung sind, dass Sie einen Cluster mit mehr als neun Knoten benötigen. Die größere Anzahl von Knoten erfordert eine größere Bereitstellungsarchitektur.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Kann die Avere vFXT-Umgebung automatisch skaliert werden?

Nein. Sie können den Cluster vergrößern oder verkleinern, aber das Hinzufügen oder Entfernen von Clusterknoten ist ein manueller Schritt.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Können Avere vFXT-Cluster als VM-Skalierungsgruppe ausgeführt werden?

Die Bereitstellung von VM-Skalierungsgruppen wird von Avere vFXT nicht unterstützt. Mehrere integrierte Mechanismen zur Unterstützung der Verfügbarkeit sind nur für unteilbare virtuelle Computer konzipiert, die an einem Cluster teilnehmen.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Können Avere vFXT-Cluster auf virtuellen Computern mit niedriger Priorität ausgeführt werden?

Nein, das System erfordert eine zugrunde liegende stabile Gruppe von virtuellen Computern.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Können Avere vFXT-Cluster in Containern ausgeführt werden?

Nein, Avere vFXT muss als unabhängige Anwendung bereitgestellt werden.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Werden die virtuellen Computer mit Avere vFXT auf mein Computekontingent angerechnet?

Ja. Stellen Sie sicher, dass Sie über ein ausreichendes Kontingent in der Region verfügen, um den Cluster zu unterstützen.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Ist es möglich, die Avere vFXT-Clustercomputer in verschiedenen Verfügbarkeitszonen auszuführen?

Nein. Das in Avere vFXT verwendete Hochverfügbarkeitsmodell unterstützt derzeit keine einzelnen vFXT-Clustermitglieder, die sich in verschiedenen Verfügbarkeitszonen befinden.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Können virtuelle Avere vFXT-Computer geklont werden?

Nein, Sie müssen das unterstützte Python-Skript verwenden, um Knoten im Avere vFXT-Cluster hinzuzufügen oder zu entfernen. Weitere Informationen finden Sie unter [Verwalten des Avere vFXT-Clusters](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Gibt es eine „VM“-Version der Software, die ich in meiner eigenen lokalen Umgebung ausführen kann?

Nein, das System wird als gruppierte Appliance angeboten und auf bestimmten virtuellen Computertypen getestet. Durch diese Einschränkung können Kunden einfacher die Erstellung eines Systems vermeiden, das die hohen Leistungsanforderungen eines typischen Avere vFXT-Workflows nicht unterstützen kann. 

## <a name="technical-disks"></a>Technisch: Datenträger

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Welche Art von Datenträgern werden für die virtuellen Azure-Computer unterstützt?

Avere vFXT für Azure kann SSD Premium-Konfigurationen mit 1 TB oder 4 TB verwenden. Die SSD Premium-Konfiguration kann als mehrere verwaltete Datenträger bereitgestellt werden.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Unterstützt der Cluster nicht verwaltete Datenträger?

Nein, der Cluster erfordert verwaltete Datenträger.

### <a name="does-the-system-support-local-attached-ssds"></a>Unterstützt das System lokale (angefügte) SSDs?

Avere vFXT für Azure unterstützt derzeit keine lokalen SSDs. Die für Avere vFXT verwendeten Datenträger müssen heruntergefahren und neu gestartet werden können, aber lokal angefügte SSDs in dieser Konfiguration können nur beendet werden.

### <a name="does-the-system-support-ultra-ssds"></a>Unterstützt das System Ultra-SSDs?

Nein, das System unterstützt nur SSD Premium-Konfigurationen.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Kann ich meine Premium-SSDs trennen und später erneut anfügen, um den Inhalt des Caches in der Zwischenzeit zu erhalten?

Das Trennen und erneute Anfügen von SSDs wird nicht unterstützt. Metadaten oder Dateiinhalte der Quelle könnten sich zwischen den Anwendungen geändert haben. Dies führt möglicherweise zu Problemen bei der Datenintegrität führen.

### <a name="does-the-system-encrypt-the-cache"></a>Verschlüsselt das System den Cache?

Die Daten werden über die Datenträger verteilt, aber nicht verschlüsselt. Die Datenträger selbst können jedoch verschlüsselt werden. Weitere Informationen finden Sie unter [Absichern und Verwenden von Richtlinien auf virtuellen Computern in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Technisch: Netzwerk

### <a name="what-network-is-recommended"></a>Welches Netzwerk wird empfohlen?

Wenn Sie lokalen Speicher mit Avere vFXT verwenden, sollten Sie über eine Netzwerkverbindung mit mindestens 1 GBit/s verfügen. Wenn Sie über eine geringe Datenmenge verfügen und bereit sind, Daten vor der Ausführung von Aufträgen in die Cloud zu kopieren, ist eine VPN-Verbindung möglicherweise ausreichend. 

> [!TIP] 
> Je langsamer die Netzwerkverbindung ist, desto langsamer sind die ersten kalten Lesevorgänge. Langsame Lesevorgänge erhöhen die Latenz der Arbeitspipeline. 

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Kann ich Avere vFXT in einem anderen virtuellen Netzwerk als meinem Computecluster ausführen?

Ja, Sie können Ihr Avere vFXT-System in einem anderen virtuellen Netzwerk erstellen. Details finden Sie unter [Planen des Avere vFXT-Systems](avere-vfxt-deploy-plan.md).

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Ist für Avere vFXT ein eigenes Subnetz erforderlich?

Ja. Avere vFXT wird strikt als Hochverfügbarkeitscluster (High Availability, HA) ausgeführt und erfordert mehrere IP-Adressen für den Betrieb. Wenn sich der Cluster in einem eigenen Subnetz befindet, vermeiden Sie das Risiko von IP-Adresskonflikten, die zu Problemen bei der Installation und dem normalen Betrieb führen können. Das Subnetz des Clusters kann sich innerhalb des bestehenden virtuellen Netzwerks befinden, solange sich keine IP-Adressen überschneiden.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Kann Avere vFXT über InfiniBand ausgeführt werden?

Nein, Avere vFXT verwendet ausschließlich Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Wie kann ich über Avere vFXT auf meine lokale NAS-Umgebung zugreifen?

Die Avere vFXT-Umgebung entspricht insofern anderen virtuellen Azure-Computern, als sie auch einen gerouteten Zugriff über ein Netzwerkgateway oder VPN zum Rechenzentrum des Kunden (und zurück) erfordert. Erwägen Sie die Verwendung von Azure ExpressRoute-Verbindungen, sofern diese in Ihrer Umgebung verfügbar sind.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Welche Bandbreitenanforderungen bestehen für Avere vFXT?

Die gesamte Bandbreitenanforderung hängt von zwei Faktoren ab: 

* Die Menge der Daten, die von der Quelle angefordert werden. 
* Die Toleranz des Clientsystems gegenüber Latenzen beim erstmaligen Laden von Daten.  

Für latenzempfindliche Umgebungen sollten Sie eine Glasfaserlösung mit einer Verbindungsgeschwindigkeit von mindestens 1 GBit/s verwenden. Verwenden Sie ExpressRoute, sofern es verfügbar ist.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Kann Avere vFXT mit öffentlichen IP-Adressen ausgeführt werden?

Nein, Avere vFXT ist für den Betrieb in einer Netzwerkumgebung vorgesehen, die durch bewährte Methoden geschützt ist.  

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Kann ich den Zugriff auf das Internet über das virtuelle Netzwerk meines Clusters einschränken? 

Im Allgemeinen können Sie bei Bedarf zusätzliche Sicherheit in Ihrem virtuellen Netzwerk konfigurieren, aber einige Einschränkungen können den Betrieb des Clusters beeinträchtigen.

Wenn Sie z. B. den ausgehenden Internetzugriff über Ihr virtuelles Netzwerk einschränken, führt dies zu Problemen für den Cluster, es sei denn, Sie fügen ebenfalls eine Regel hinzu, die den Zugriff auf „AzureCloud“ explizit gestattet. Diese Situation ist in der [ergänzenden Dokumentation auf GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md) beschrieben.

Wenn Sie Hilfe bei der angepassten Sicherheit benötigen, wenden Sie sich an den Support wie unter [Abrufen von Hilfe zu Ihrem System](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) beschrieben.

## <a name="technical-back-end-storage-core-filers"></a>Technisch: Back-End-Speicher (Kernspeichereinheiten)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Wie viele Kernspeichereinheiten unterstützt eine einzelne Avere vFXT-Umgebung?

Ein Avere vFXT-Cluster unterstützt bis zu 20 Kernspeichereinheiten. 

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Wie werden Daten in der Avere vFXT-Umgebung gespeichert?

Avere vFXT ist kein Speicher. Es handelt sich um einen Cache, der Daten von mehreren Speicherzielen, den sogenannten Kernspeichereinheiten, liest und schreibt. Die in Avere vFXT auf SSD Premium-Datenträgern gespeicherten Daten sind kurzlebig und werden schließlich in die Back-End-Kernspeichereinheit übertragen.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Welche Kernspeichereinheiten werden von Avere vFXT unterstützt?

Im Allgemeinen werden von Avere vFXT für Azure die folgenden Systeme als Kernspeichereinheiten unterstützt: 

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 und 8.1) 
* NetApp ONTAP (Clustered Mode 9.4, 9.3, 9.2, 9.1P1, 8.0 bis 8.3) und (7-Mode 7.*, 8.0 und 8.3) 

  > [!NOTE] 
  > Azure NetApp Files wird derzeit nicht unterstützt. 

* Azure-Blobcontainer (nur lokal redundanter Speicher) 
* AWS S3-Buckets 
* Google Cloud-Buckets

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Warum unterstützt Avere vFXT nicht alle NFS-Kernspeichereinheiten?

Obwohl alle NFS-Plattformen dieselben IETF-Standards erfüllen, hat in der Praxis jede Implementierung ihre eigenen Besonderheiten. Diese Details beeinflussen die Interaktion zwischen Avere vFXT und dem Speichersystem. Die unterstützten Systeme sind die am häufigsten verwendeten Plattformen auf dem Markt.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Unterstützt Avere vFXT die Speicherung privater Objekte (z.B. SwiftStack)?

Avere vFXT unterstützt keine Speicherung von privaten Objekten.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Wie kann ich Support für ein bestimmtes Speicherprodukt erhalten?

Der Support richtet sich nach der Nachfrage in der Praxis. Wenn es ausreichend umsatzabhängige Anforderungen gibt, eine NAS-Lösung zu unterstützen, wird diese in Betracht gezogen. Stellen Sie Anforderungen über den Azure-Support.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Kann Azure-BLOB-Speicher als Kernspeichereinheit verwendet werden?

Ja, Avere vFXT for Azure kann einen Blockblobcontainer als Cloud-Kernspeichereinheit verwenden.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Welche Anforderungen werden an Speicherkonten für eine Blob-Kernspeichereinheit gestellt?

Ihr Speicherkonto muss ein GPv2-Konto (General Purpose v2) sein, das nur für lokal redundanten Speicher konfiguriert ist. Georedundanter Speicher und zonenredundanten Speicher werden nicht unterstützt.

### <a name="can-i-use-archive-blob-storage"></a>Kann ich Archiv-Blob Storage verwenden?

Nein. Die Vereinbarung zum Servicelevel (SLA) für Archivspeicher ist nicht kompatibel mit den Echtzeitanforderungen des Avere vFXT-Systems an den Verzeichnis- und Dateizugriff. 

### <a name="can-i-use-cool-blob-storage"></a>Kann ich kalten Blob Storage verwenden?

Sie können die kalte Ebene verwenden, aber beachten Sie, dass die Verarbeitungsrate viel höher sein wird. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Wie verschlüssele ich den Blobcontainer?

Sie können die Blobverschlüsselung entweder in Azure (bevorzugt) oder auf Ebene der Avere vFXT-Kernspeichereinheit konfigurieren.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Kann ich meinen eigenen Verschlüsselungsschlüssel für eine Blobkernspeichereinheit verwenden?

Standardmäßig werden die Daten über von Microsoft verwaltete Schlüssel für Azure Blob, Table und Queue Storage sowie Azure Files verschlüsselt. Sie können Ihren eigenen Schlüssel für die Verschlüsselung für Blob Storage und Azure Files einsetzen. Wenn Sie sich für die Avere vFXT-Verschlüsselung entscheiden, müssen Sie den von Avere generierten Schlüssel verwenden und ihn lokal speichern. 

## <a name="purchasing"></a>Erwerb

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Wie erhalte ich Lizenzen für Avere vFXT für Azure?

Eine Lizenz für Avere vFXT für Azure zu erhalten, ist über den Azure Marketplace ganz einfach. Registrieren Sie sich für ein Azure-Konto, und befolgen Sie dann die Anweisungen in [Bereitstellen des Avere vFXT-Clusters](avere-vfxt-deploy.md), um einen Avere vFXT-Cluster zu erstellen. 

### <a name="how-much-does-avere-vfxt-cost"></a>Wie viel kostet Avere vFXT?

In Azure gibt es keine zusätzliche Lizenzgebühr für die Nutzung von Avere vFXT-Clustern. Kunden sind für die Speicherung und andere Azure-Verbrauchsgebühren verantwortlich.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Können virtuelle Avere vFXT-Computer mit niedriger Priorität ausgeführt werden?

Nein, Avere vFXT-Cluster erfordern einen immer aktivierten Dienst. Die Cluster können ausgeschaltet werden, wenn sie nicht erforderlich sind. 

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie für die ersten Schritte mit Avere vFXT for Azure diese Artikel, um zu erfahren, wie Sie Ihr eigenes System planen und bereitstellen können:

* [Planen des Avere vFXT-Systems](avere-vfxt-deploy-plan.md)
* [Übersicht über die Bereitstellung](avere-vfxt-deploy-overview.md)
* [Vorbereiten eines Avere vFXT-Clusters](avere-vfxt-prereqs.md)
* [Bereitstellen des Avere vFXT-Clusters](avere-vfxt-deploy.md)

Weitere Informationen zu Funktionen und Anwendungsfällen für Avere vFXT finden Sie unter [Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
