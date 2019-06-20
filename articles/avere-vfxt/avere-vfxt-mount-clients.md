---
title: Einbinden von Avere vFXT – Azure
description: Einbinden von Clients mit Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 41065b4ac6bc486e204c2bfd72b78ba8722270c4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409364"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Einbinden des Avere vFXT-Clusters  

Führen Sie die folgenden Schritte aus, um Clientcomputer mit Ihrem vFXT-Cluster zu verbinden.

1. Entscheiden Sie, wie Sie den Clientdatenverkehr auf Ihre Clusterknoten aufteilen. Weitere Informationen finden Sie nachfolgend unter [Verteilen der Clientauslastung](#balance-client-load). 
1. Identifizieren Sie die einzubindende IP-Adresse und den Verbindungspfad.
1. Führen Sie den Befehl [mount](#mount-command-arguments) mit entsprechenden Argumenten aus.

## <a name="balance-client-load"></a>Verteilen der Clientauslastung

Um die Clientanforderungen auf alle Knoten im Cluster zu verteilen, sollten Sie Clients auf die gesamte Bandbreite der clientseitigen IP-Adressen einbinden. Es gibt mehrere einfache Möglichkeiten, diese Aufgabe zu automatisieren.

> [!TIP] 
> Andere Lastenausgleichsmethoden können für große oder komplizierte Systeme geeignet sein. ([Öffnen Sie ein Supportticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt), um Hilfe zu erhalten.)
> 
> Wenn Sie es vorziehen, einen DNS-Server für den automatischen serverseitigen Lastenausgleich zu verwenden, müssen Sie Ihren eigenen DNS-Server in Azure einrichten und verwalten. In diesem Fall können Sie gemäß diesem Dokument ein Roundrobin-DNS für den vFXT-Cluster konfigurieren: [DNS-Konfiguration des Avere-Clusters](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Beispiel eines Skripts zur ausgeglichenen Clienteinbindung

Dieses Codebeispiel verwendet Client-IP-Adressen als zufälliges Element, um Clients an alle verfügbaren IP-Adressen des vFXT-Clusters zu verteilen.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default 
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

Die obige Funktion ist Teil des Batchbeispiels, das auf der Seite [Avere vFXT-Beispiele](https://github.com/Azure/Avere#tutorials) verfügbar ist.

## <a name="create-the-mount-command"></a>Erstellen des mount-Befehls 

> [!NOTE]
> Wenn Sie beim Erstellen Ihres Avere vFXT-Clusters keinen neuen Blobcontainer erstellt haben, führen Sie die Schritte unter [Konfigurieren von Speicher](avere-vfxt-add-storage.md) aus, bevor Sie versuchen, Clients einzubinden.

Von Ihrem Client aus ordnet der Befehl ``mount`` den virtuellen Server (vserver) im vFXT-Cluster einem Pfad des lokalen Dateisystems zu. Das Format ist ``mount <vFXT path> <local path> {options}``.

Der mount-Befehl umfasst drei Elemente: 

* vFXT-Pfad – (eine Kombination aus IP-Adresse und Namespaceverbindungspfad, die unten beschrieben wird)
* Lokaler Pfad – Der Pfad auf dem Client 
* mount-Befehlsoptionen – (unter [Argumente des mount-Befehls](#mount-command-arguments) aufgeführt)

### <a name="junction-and-ip"></a>Verbindung und IP-Adresse

Der vserver-Pfad ist eine Kombination aus seiner *IP-Adresse* und dem Pfad zu einer *Namespaceverbindung*. Die Namespaceverbindung ist ein virtueller Pfad, der beim Hinzufügen des Speichersystems definiert wurde.

Wenn Ihr Cluster mit Blob-Speicher erstellt wurde, ist der Nampacepfad `/msazure`.

Beispiel: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Wenn Sie nach der Erstellung des Clusters Speicher hinzugefügt haben, entspricht der Namespaceverbindungspfad dem Wert, den Sie bei der Erstellung der Verbindung unter **Namespacepfad** festgelegt haben. Wenn Sie z. B. ``/avere/files`` als Namespacepfad verwendet haben, würden Ihre Clients „*IP_address*:/avere/files“ an ihrem lokalen Bereitstellungspunkt einbinden.

![Dialogfeld „Neue Verbindung hinzufügen“ mit „/avere/files“ im Feld für den Namespacepfad](media/avere-vfxt-create-junction-example.png)


Die IP-Adresse ist eine der clientseitigen IP-Adressen, die für den vserver definiert sind. Sie finden den Bereich der clientseitigen IP-Adressen an zwei Stellen in der Avere-Systemsteuerung:

* Tabelle **VServers** (Registerkarte „Dashboard“) – 

  ![Registerkarte „Dashboard“ der Avere Systemsteuerung mit der Registerkarte „VServer“, die in der Datentabelle unterhalb der Grafik ausgewählt wurde, und dem eingekreisten Abschnitt „IP-Adresse“](media/avere-vfxt-ip-addresses-dashboard.png)

* Einstellungsseite **Clientseitiges Netzwerk** – 

  ![Einstellungen > VServer > Konfigurationsseite für clientseitiges Netzwerk mit einem Kreis um den Abschnitt „Adressbereich“ der Tabelle für einen bestimmten VServer](media/avere-vfxt-ip-addresses-settings.png)

Fügen Sie zusätzlich zu den Pfaden die unten beschriebenen [Argumente des mount-Befehls](#mount-command-arguments) hinzu, wenn Sie die einzelnen Clients einbinden.

### <a name="mount-command-arguments"></a>Argumente des mount-Befehls

Um eine problemlose Clienteinbindung sicherzustellen, übergeben Sie diese Einstellungen und Argumente in Ihrem mount-Befehl: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Erforderliche Einstellungen | |
--- | --- 
``hard`` | Softwareseitige Bereitstellungen für den vFXT-Cluster sind Anwendungsfehlern und möglichen Datenverlusten zugeordnet. 
``proto=netid`` | Diese Option unterstützt eine angemessene Behandlung von NFS-Netzwerkfehlern.
``mountproto=netid`` | Diese Option unterstützt die angemessene Behandlung von Netzwerkfehlern für Einbindungsvorgänge.
``retry=n`` | Legen Sie ``retry=30`` fest, um vorübergehende Einbindungsfehler zu vermeiden. (Bei Einbindungen im Vordergrund wird ein anderer Wert empfohlen.)

| Bevorzugte Einstellungen  | |
--- | --- 
``nointr``            | Die Option „nointr“ wird für Clients mit Legacy-Kernel (vor April 2008) bevorzugt, die diese Option unterstützen. Beachten Sie, dass die Option „intr“ die Standardeinstellung ist.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Clients eingebunden haben, können Sie sie verwenden, um den Back-End-Datenspeicher (Kernspeichereinheit) zu füllen. Weitere Informationen zu zusätzlichen Einrichtungsaufgaben finden Sie in den folgenden Dokumenten:

* [Verschieben von Daten in die Kernspeichereinheit des Clusters](avere-vfxt-data-ingest.md): Verwenden mehrerer Clients und Threads zum effizienten Hochladen Ihrer Daten.
* [Anpassen der Clusteroptimierung](avere-vfxt-tuning.md): Anpassen der Clustereinstellungen gemäß der Workload.
* [Verwalten des Clusters](avere-vfxt-manage-cluster.md): Starten oder Beenden des Clusters und Verwalten von Knoten.
