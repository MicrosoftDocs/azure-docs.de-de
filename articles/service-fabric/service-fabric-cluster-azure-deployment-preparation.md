---
title: Planen der Bereitstellung eines Azure Service Fabric-Clusters
description: Erfahren Sie in diesem Artikel, wie Sie die Bereitstellung von Service Fabric-Clusters für eine Produktionsumgebung in Azure planen und vorbereiten.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9de59811397eb47809c6d71f608e43beae5bfadb
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109622"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planen und Vorbereiten der Clusterbereitstellung

Die Planung und Vorbereitung einer Clusterbereitstellung für eine Produktionsumgebung ist äußerst wichtig.  Hierbei müssen zahlreiche Faktoren berücksichtigt werden.  Dieser Artikel führt Sie durch die Schritte zur Vorbereitung Ihrer Cluster-Bereitstellung.

## <a name="read-the-best-practices-information"></a>Durchlesen der Informationen zu den bewährten Methoden
Für die erfolgreiche Verwaltung von Azure Service Fabric-Anwendungen und -Clustern empfehlen wir Ihnen dringend, bestimmte Vorgänge auszuführen, um die Zuverlässigkeit Ihrer Produktionsumgebung zu optimieren.  Weitere Informationen finden Sie im Artikel [Bewährte Methoden für Azure Service Fabric-Anwendungen und -Cluster](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Auswählen des Betriebssystems für den Cluster
Service Fabric ermöglicht die Erstellung von Service Fabric-Clustern auf VMs oder Computern mit Windows Server oder Linux.  Vor dem Bereitstellen Ihres Clusters müssen Sie das Betriebssystem auswählen:  Windows oder Linux.  Auf allen Knoten (bzw. virtuellen Computern) im Cluster wird dasselbe Betriebssystem ausgeführt. Es ist nicht möglich, virtuelle Windows- und Linux-Computer im selben Cluster zu kombinieren.

## <a name="capacity-planning"></a>Kapazitätsplanung
Die Kapazitätsplanung ist ein wichtiger Schritt bei jeder Produktionsbereitstellung. Nachfolgend sind einige Aspekte aufgeführt, die dabei berücksichtigt werden müssen.

* Die anfängliche Anzahl von Knotentypen für Ihren Cluster 
* Die Eigenschaften der einzelnen Knotentypen (Größe, Instanzenzahl, primärer Knotentyp, Internetzugriff, Anzahl der VMs usw.)
* Die Zuverlässigkeits- und Dauerhaftigkeitsmerkmale des Clusters

### <a name="select-the-initial-number-of-node-types"></a>Auswählen der anfänglichen Anzahl von Knotentypen
Zuerst müssen Sie ermitteln, für welche Zwecke der von Ihnen erstellte Cluster verwendet werden soll. Welche Arten von Anwendungen sollen in diesem Cluster bereitgestellt werden? Weist Ihre Anwendung mehrere Dienste auf, und müssen einige dieser Dienste öffentlich sein oder über Internetzugriff verfügen? Haben Ihre Dienste (aus denen sich Ihre Anwendung zusammensetzt) unterschiedliche Infrastrukturanforderungen, z. B. höhere RAM-Anforderungen oder längere CPU-Zyklen? Ein Service Fabric-Cluster kann aus mehreren Knotentypen bestehen: einem primären Knotentyp und einem oder mehreren nicht primären Knotentypen. Jeder Knotentyp wird einer VM-Skalierungsgruppe zugeordnet. Jeden Knotentyp kann dann unabhängig zentral hoch- oder herunterskaliert werden, bei jedem Typ können unterschiedliche Portgruppen geöffnet sein, und die Typen können verschiedene Kapazitätsmetriken aufweisen. Es können [Knoteneigenschaften und Platzierungseinschränkungen][placementconstraints] festgelegt werden, um bestimmte Dienste auf bestimmte Knotentypen zu beschränken.  Weitere Informationen finden Sie unter [Kapazitätsplanung für Service Fabric-Cluster](service-fabric-cluster-capacity.md).

### <a name="select-node-properties-for-each-node-type"></a>Auswählen der Knoteneigenschaften für die einzelnen Knotentypen
Knotentypen definieren die VM-SKU, die Anzahl und die Eigenschaften der virtuellen Computer in der zugehörigen Skalierungsgruppe.

Die Mindestgröße der VMs für jeden Knotentyp hängt von der [Dauerhaftigkeitsstufe][durability] ab, die Sie für den Knotentyp auswählen.

Die Mindestanzahl der VMs für den primären Knotentyp hängt von der von Ihnen ausgewählten [Zuverlässigkeitsstufe][reliability] ab.

Beachten Sie die Mindestempfehlungen für [primäre Knotentypen](service-fabric-cluster-capacity.md#primary-node-type), [zustandsbehaftete Workloads für nicht primäre Knotentypen](service-fabric-cluster-capacity.md#stateful-workloads) und [zustandslose Workloads für nicht primäre Knotentypen](service-fabric-cluster-capacity.md#stateless-workloads).

Eine die Mindestanzahl von Knoten überschreitende Anzahl sollte auf der Anzahl der Replikate der Anwendung/Dienste basieren, die mit diesem Knotentyp ausgeführt werden sollen.  Die [Kapazitätsplanung für Service Fabric-Anwendungen](service-fabric-capacity-planning.md) hilft Ihnen, die Ressourcen abzuschätzen, die Sie für die Ausführung Ihrer Anwendungen benötigen. Sie können den Cluster später jederzeit vergrößern oder verkleinern, um ihn an eine sich ändernde Workload von Anwendungen anzupassen. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Verwenden kurzlebiger Betriebssystemdatenträger für VM-Skalierungsgruppen

Bei *kurzlebigen Betriebssystemdatenträgern* handelt es sich um Speicher, der auf dem lokalen virtuellen Computer erstellt und nicht in der externen Azure Storage-Instanz gespeichert wird. Sie werden für alle Service Fabric-Knotentypen (primär und sekundär) empfohlen, da kurzlebige Betriebssystemdatenträger im Vergleich zu herkömmlichen persistenten Betriebssystemdatenträgern Folgendes ermöglichen:

* Reduzieren der Lese-/Schreibwartezeit auf dem Betriebssystemdatenträger
* Schnellere Vorgänge zum Zurücksetzen/Durchführen eines Reimagings für die Knotenverwaltung
* Senken der Gesamtkosten (Die Datenträger sind kostenlos, und es fallen keine zusätzlichen Speicherkosten an.)

Kurzlebige Betriebssystemdatenträger sind kein spezifisches Feature von Service Fabric, sondern ein Feature der *VM-Skalierungsgruppen* von Azure, die Service Fabric-Knotentypen zugeordnet sind. Wenn Sie sie mit Service Fabric verwenden, ist in der Azure Resource Manager-Vorlage Ihres Clusters Folgendes erforderlich:

1. Stellen Sie sicher, das die Knotentypen [unterstützte Azure-VM-Größen](../virtual-machines/ephemeral-os-disks.md) für kurzlebige Betriebssystemdatenträger angeben und dass der Cache der VM-Größe für die Größe des Betriebssystemdatenträgers ausreichend dimensioniert ist (siehe *Hinweis* weiter unten). Beispiel:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Wählen Sie unbedingt eine VM-Größe mit einer Cachegröße aus, die mindestens der Größe des Betriebssystemdatenträgers des virtuellen Computers selbst entspricht. Andernfalls tritt bei der Azure-Bereitstellung unter Umständen ein Fehler auf (auch wenn sie zunächst angenommen wurde).

2. Geben Sie mindestens Version `2018-06-01` für VM-Skalierungsgruppen (`vmssApiVersion`) an:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Geben Sie im Abschnitt „VM-Skalierungsgruppe“ der Bereitstellungsvorlage die Option `Local` für `diffDiskSettings` an:

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> Benutzeranwendungen sollten auf dem Betriebssystemdatenträger keine Abhängigkeit, keine Datei und kein Artefakt aufweisen, da der Betriebssystemdatenträger im Falle eines Betriebssystemupgrades verloren gehen würde.

> [!NOTE]
> Vorhandene nicht kurzlebige VMSS können nicht direkt aktualisiert werden, um kurzlebige Datenträger zu verwenden.
> Zum Migrieren müssen Benutzer einen neuen nodeType mit kurzlebigen Datenträgern [hinzufügen](./virtual-machine-scale-set-scale-node-type-scale-out.md), die Workloads in den neuen nodeType verschieben und den vorhandenen nodeType [entfernen](./service-fabric-how-to-remove-node-type.md).
>

Weitere Informationen und Konfigurationsoptionen finden Sie unter [Kurzlebige Betriebssystemdatenträger für virtuelle Azure-Computer](../virtual-machines/ephemeral-os-disks.md). 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Auswählen der Dauerhaftigkeits- und der Zuverlässigkeitsstufe für den Cluster
Über die Dauerhaftigkeitsstufe wird dem System angezeigt, über welche Berechtigungen Ihre VMs für die zugrunde liegende Azure-Infrastruktur verfügen. Auf dem primären Knotentyp kann Service Fabric mit dieser Berechtigung Infrastrukturanforderungen auf VM-Ebene anhalten (z. B. einen VM-Neustart, ein VM-Reimaging oder eine VM-Migration), die sich auf die Quorumanforderungen für die Systemdienste und Ihre zustandsbehafteten Dienste auswirken. Auf den nicht primären Knotentypen kann Service Fabric mit dieser Berechtigung Infrastrukturanforderungen auf VM-Ebene (z.B. einen VM-Neustart, ein VM-Reimaging und eine VM-Migration) anhalten, die sich auf die Quorumanforderungen für Ihre zustandsbehafteten Dienste auswirken.  Ausführungen zu den Vorteilen der verschiedenen Stufen sowie Empfehlungen dazu, welche Stufe wann verwendet werden sollte, finden Sie unter [Die Dauerhaftigkeitsmerkmale des Clusters][durability].

Über die Zuverlässigkeitsstufe wird die Anzahl von Replikaten der Systemdienste festgelegt, die in diesem Cluster auf dem primären Knotentyp ausgeführt werden sollen. Je mehr Replikate vorhanden sind, desto größer ist die Zuverlässigkeit der Systemdienste in Ihrem Cluster.  Ausführungen zu den Vorteilen der verschiedenen Stufen sowie Empfehlungen dazu, welche Stufe wann verwendet werden sollte, finden Sie unter [Die Zuverlässigkeitsmerkmale des Clusters][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Reverseproxy aktivieren und/oder DNS
Dienste, die innerhalb eines Clusters miteinander verbunden sind, können im Allgemeinen direkt auf die Endpunkte anderer Dienste zugreifen, da sich die Knoten in einem Cluster im gleichen Netzwerk befinden. Um die Verbindung zwischen Diensten zu vereinfachen, bietet Service Fabric zusätzliche Dienste: Einen [DNS-Dienst](service-fabric-dnsservice.md) und einen [Reverseproxydienst](service-fabric-reverseproxy.md).  Beide Dienste können bei der Bereitstellung eines Clusters aktiviert werden.

Da viele Dienste, insbesondere Containerdienste, unter Umständen bereits über einen URL-Namen verfügen, ist es praktisch, diesen über das standardmäßige DNS-Protokoll (anstatt über das Naming Service-Protokoll) auflösen zu können, besonders in Szenarios mit Lift Shift-Anwendungen. Genau dies übernimmt der DNS-Dienst. Mit dem DNS-Dienst können Sie einem Dienstnamen DNS-Namen zuordnen und so Endpunkt-IP-Adressen auflösen.

Der Reverseproxy adressiert Dienste im Cluster, die HTTP-Endpunkte (einschließlich HTTPS) verfügbar machen. Der Reverseproxy vereinfacht das Aufrufen anderer Dienste enorm, indem er ein spezifisches URI-Format bereitstellt.  Der Reverseproxy verarbeitet auch die Schritte Auflösen, Verbinden und Wiederholen, die ein Dienst benötigt, um mit einem anderen Dienst zu kommunizieren.

## <a name="prepare-for-disaster-recovery"></a>Vorbereiten der Notfallwiederherstellung
Zur Gewährleistung von hoher Verfügbarkeit muss unter anderem sichergestellt werden, dass Dienste verschiedenste Arten von Ausfällen überstehen können. Dies ist besonders wichtig bei Ausfällen, die überraschend auftreten oder sich Ihrer Kontrolle entziehen. Im Artikel [Notfallwiederherstellung in Azure Service Fabric](service-fabric-disaster-recovery.md) werden einige allgemeine Fehlermodi beschrieben, die sich als äußerst problematisch erweisen können, wenn sie nicht angemessen im Modell berücksichtigt und behandelt werden. Darüber hinaus enthält der Artikel Informationen zu Abhilfen und Maßnahmen, die Sie ergreifen können, wenn dennoch ein Notfall eintritt.

## <a name="production-readiness-checklist"></a>Prüfliste für die Produktionsbereitschaft
Sind Ihre Anwendung und Ihr Cluster bereit für den Produktionsdatenverkehr? Gehen Sie die [Prüfliste für die Produktionsbereitschaft](service-fabric-production-readiness-checklist.md) durch, bevor Sie Ihren Cluster in einer Produktionsumgebung bereitstellen. Sorgen Sie dafür, dass Ihre Anwendung und Ihr Cluster störungsfrei laufen, indem Sie die Elemente dieser Checkliste durcharbeiten. Es wird dringend empfohlen, vor dem Wechsel in die Produktionsphase alle diese Elemente zu prüfen.

## <a name="next-steps"></a>Nächste Schritte
* [Create a Service Fabric cluster running Windows (Erstellen eines Service Fabric-Clusters unter Windows)](service-fabric-best-practices-overview.md)
* [Create a Service Fabric cluster running Linux (Erstellen eines Service Fabric-Clusters unter Linux)](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster