---
title: Azure HPC Cache-Voraussetzungen
description: Voraussetzungen für die Verwendung von Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/05/2020
ms.author: v-erkel
ms.openlocfilehash: a31aee3f4548d3137fa1241aaa3a0f6171cf6895
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412509"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Voraussetzungen für Azure HPC Cache

Stellen Sie sicher, dass Ihre Umgebung diese Anforderungen erfüllt, bevor Sie das Azure-Portal zum Erstellen eines neuen Azure HPC Cache verwenden.

## <a name="video-overviews"></a>Videoübersichten

Schauen Sie sich diese Videos an, um eine schnelle Übersicht über die Komponenten des Systems zu erhalten und darüber, was sie benötigen, um zusammenzuarbeiten.

(Klicken Sie zum Ansehen auf das Videobild oder den Link).

* [Funktionsweise](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/): Erläutert, wie Azure HPC Cache mit Speicher und Clients interagiert.

  [![Videominiaturbild: Azure HPC Cache: Funktionsweise (klicken Sie, um die Videoseite zu besuchen)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Voraussetzungen](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/): Beschreibt die Anforderungen für NAS-Speicher, Azure Blob Storage, Netzwerkzugriff und Clientzugriff.

  [![Videominiaturbild: Azure HPC Cache: Voraussetzungen (klicken Sie, um die Videoseite zu besuchen)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Lesen Sie den Rest dieses Artikels für bestimmte Empfehlungen.

## <a name="azure-subscription"></a>Azure-Abonnement

Ein kostenpflichtiges Abonnement wird empfohlen.

## <a name="network-infrastructure"></a>Netzwerkinfrastruktur

Bevor Sie den Cache verwenden können, sollten Sie zwei netzwerkbezogene Voraussetzungen einrichten:

* Ein dediziertes Subnetz für die Azure HPC Cache-Instanz
* DNS-Unterstützung, sodass der Cache auf Speicher und andere Ressourcen zugreifen kann

### <a name="cache-subnet"></a>Cache-Subnetz

Der Azure HPC Cache benötigt ein dediziertes Subnetz mit diesen Eigenschaften:

* Im Subnetz müssen mindestens 64 IP-Adressen verfügbar sein.
* Das Subnetz darf keine weiteren VMs hosten, nicht einmal für verwandte Dienste wie Clientcomputer.
* Wenn Sie mehrere Azure HPC Cache-Instanzen verwenden, benötigt jede ein eigenes Subnetz.

Die bewährte Methode besteht im Erstellen eines neuen Subnetzes für die einzelnen Caches. Sie können im Rahmen der Cache-Erstellung ein neues virtuelles Netzwerk und Subnetz erstellen.

### <a name="dns-access"></a>DNS-Zugriff

Der Cache benötigt DNS für den Zugriff auf Ressourcen außerhalb seines virtuellen Netzwerks. Je nachdem, welche Ressourcen Sie verwenden, müssen Sie möglicherweise einen angepassten DNS-Server einrichten und die Weiterleitung zwischen diesem Server und Azure DNS-Servern konfigurieren:

* Um auf Azure Blob Storage-Endpunkte und andere interne Ressourcen zuzugreifen, benötigen Sie den Azure-basierten DNS-Server.
* Um auf lokalen Speicher zuzugreifen, müssen Sie einen benutzerdefinierten DNS-Server konfigurieren, der Ihre Speicherhostnamen auflösen kann. Diese Aktion müssen Sie ausführen, **bevor** Sie den Cache erstellen.

Wenn Sie nur Zugriff auf Blobspeicher benötigen, können Sie den von Azure bereitgestellten DNS-Standardserver für Ihren Cache verwenden. Wenn Sie hingegen Zugriff auf weitere Ressourcen benötigen, sollten Sie einen benutzerdefinierten DNS-Server erstellen und für die Weiterleitung aller Azure-spezifischen Auflösungsanforderungen an den Azure DNS-Server konfigurieren.

Um einen benutzerdefinierten DNS-Server verwenden zu können, müssen Sie die folgenden Einrichtungsschritte ausführen, bevor Sie den Cache erstellen:

* Erstellen Sie das virtuelle Netzwerk, das den Azure HPC Cache hosten soll.
* Erstellen Sie den DNS-Server.
* Fügen Sie den DNS-Server zum virtuellen Netzwerk hinzu.

  Führen Sie die folgenden Schritte im Azure-Portal aus, um den DNS-Server zum virtuellen Netzwerk hinzuzufügen:

  1. Öffnen Sie das virtuelle Netzwerk im Azure-Portal.
  1. Wählen Sie in der Randleiste im Menü **Einstellungen** die Option **DNS-Server** aus.
  1. Wählen Sie **Benutzerdefiniert** aus.
  1. Geben Sie die IP-Adresse des DNS-Server in das Feld ein.

Ein einfacher DNS-Server kann außerdem für den Lastenausgleich für Clientverbindungen zwischen allen verfügbaren Cache-Einbindungspunkten verwendet werden.

Weitere Informationen zu virtuellen Azure-Netzwerken und DNS-Serverkonfigurationen finden Sie unter [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="permissions"></a>Berechtigungen

Überprüfen Sie diese Voraussetzungen im Hinblick auf Berechtigungen, bevor Sie mit dem Erstellen des Caches beginnen.

* Die Cache-Instanz muss in der Lage sein, virtuelle Netzwerkschnittstellen (NICs) zu erstellen. Der Benutzer, der den Cache erstellt, muss über ausreichende Berechtigungen für das Abonnement verfügen, um NICs zu erstellen.

* Bei der Verwendung von Blobspeicher benötigt Azure HPC Cache die Autorisierung zum Zugriff auf Ihr Speicherkonto. Verwenden Sie rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um dem Cache Zugriff auf Ihren Blobspeicher zu erteilen. Zwei Rollen sind erforderlich: „Mitwirkender von Speicherkonto“ und „Mitwirkender an Storage-Blobdaten“.

  Befolgen Sie die Anweisungen unter [Hinzufügen von Speicherzielen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account), um die Rollen hinzuzufügen.

## <a name="storage-infrastructure"></a>Speicherinfrastruktur

Der Cache unterstützt Azure-Blobcontainer oder NFS-Hardwarespeicherexporte. Fügen Sie nach dem Erstellen des Caches Speicherziele hinzu.

Für jeden Speichertyp gelten bestimmte Voraussetzungen.

### <a name="blob-storage-requirements"></a>Blobspeicheranforderungen

Wenn Sie Azure-Blobspeicher in Kombination mit Ihrem Cache verwenden möchten, benötigen Sie ein kompatibles Speicherkonto und entweder einen leeren Blobcontainer oder einen Container, der mit Daten im Format von Azure HPC Cache aufgefüllt ist, wie unter [Verschieben von Daten in Azure Blob Storage](hpc-cache-ingest.md) beschrieben.

Erstellen Sie das Konto, bevor Sie versuchen, ein Speicherziel hinzufügen. Beim Hinzufügen des Ziels können Sie einen neuen Container erstellen.

Verwenden Sie diese Einstellungen, um ein kompatibles Speicherkonto zu erstellen:

* Leistung: **Standard**
* Kontoart: **StorageV2 (universell v2)**
* Replikation: **Lokal redundanter Speicher (LRS)**
* Zugriffsebene (Standard): **Heiße Ebene**

Es wird empfohlen, ein Speicherkonto am Standort Ihres Caches zu verwenden.

Außerdem müssen Sie der Cacheanwendung Zugriff auf Ihr Azure-Speicherkonto erteilt haben (wie weiter oben unter [Berechtigungen](#permissions) beschrieben). Befolgen Sie das Verfahren unter [Hinzufügen von Speicherzielen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account), um dem Cache die erforderlichen Zugriffsrollen zuzuweisen. Wenn Sie nicht der Besitzer des Speicherkontos sind, lassen Sie den Besitzer diesen Schritt ausführen.

### <a name="nfs-storage-requirements"></a>NFS-Speicheranforderungen
<!-- linked from configuration.md -->

Vergewissern Sie sich bei Verwendung eines NFS-Speichersystems (beispielsweise eines lokalen hardwarebasierten NAS-Systems), dass es die folgenden Anforderungen erfüllt. Arbeiten Sie bei der Überprüfung dieser Einstellungen ggf. mit den Netzwerkadministratoren oder den Firewall-Managern Ihres Speichersystems (oder Rechenzentrums) zusammen.

> [!NOTE]
> Wenn der Cache nicht im erforderlichen Umfang auf das NFS-Speichersystem zugreifen kann, ist die Speicherzielerstellung nicht erfolgreich.

Weitere Informationen finden Sie unter [Behandeln von Problemen mit der NAS-Konfiguration und dem NFS-Speicherziel](troubleshoot-nas.md).

* **Netzwerkkonnektivität:** Azure HPC Cache benötigt Netzwerkzugriff mit hoher Bandbreite zwischen dem Cachesubnetz und dem Rechenzentrum des NFS-Systems. [ExpressRoute](../expressroute/index.yml) oder ein ähnliches Zugriffsverfahren wird empfohlen. Bei Verwendung eines VPN muss TCP MSS ggf. mit 1350 verknüpft werden, um die Blockierung großer Pakete zu vermeiden. Weitere Informationen zur Problembehandlung bei VPN-Einstellungen finden Sie unter [Einschränkungen der VPN-Paketgröße](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions).

* **Portzugriff:** Der Cache benötigt Zugriff auf bestimmte TCP/UDP-Ports Ihres Speichersystems. Verschiedene Speichertypen haben unterschiedliche Portanforderungen.

  Gehen Sie wie folgt vor, um die Einstellungen Ihres Speichersystems zu überprüfen:

  * Führen Sie den Befehl `rpcinfo` für Ihr Speichersystem aus, um die erforderlichen Ports zu überprüfen. Der folgende Befehl führt die Ports auf und formatiert die relevanten Ergebnisse in einer Tabelle. (Ersetzen Sie den Platzhalter *<storage_IP>* durch die IP-Adresse Ihres Systems.)

    Dieser Befehl kann über einen beliebigen Linux-Client mit installierter NFS-Infrastruktur ausgeführt werden. Wenn Sie einen Client innerhalb des Clustersubnetzes verwenden, können Sie damit auch die Konnektivität zwischen dem Subnetz und dem Speichersystem überprüfen.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Stellen Sie sicher, dass alle von der ``rpcinfo``-Abfrage zurückgegebenen Ports uneingeschränkten Datenverkehr aus dem Azure HPC Cache-Subnetz zulassen.

  * Wenn Sie den Befehl `rpcinfo` nicht verwenden können, vergewissern Sie sich, dass an den folgenden häufig verwendeten Ports ein- und ausgehender Datenverkehr zugelassen wird:

    | Protocol | Port  | Dienst  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

    Bei einigen Systemen werden für diese Dienste verschiedene Portnummern verwendet. Schlagen Sie in der Dokumentation zu Ihrem Speichersystem nach, ob dies für Ihr System zutrifft.

  * Vergewissern Sie sich, dass die Firewalleinstellungen Datenverkehr an allen diesen erforderlichen Ports zulassen. Überprüfen Sie sowohl in Azure verwendete Firewalls als auch lokale Firewalls in Ihrem Rechenzentrum.

* **Verzeichniszugriff:** Aktivieren Sie den Befehl `showmount` im Speichersystem. Mit diesem Befehl überprüft Azure HPC Cache, ob Ihre Speicherzielkonfiguration auf einen gültigen Export verweist. Außerdem wird sichergestellt, dass nicht von mehreren Einbindungen auf dieselben Unterverzeichnisse zugegriffen wird (Risiko von Dateikonflikten).

  > [!NOTE]
  > **Aktivieren Sie `showmount` nicht**, wenn Ihr NFS-Speichersystem das ONTAP 9.2-Betriebssystem von NetApp verwendet. Erstellen Sie in diesem Fall ein Supportticket. Weitere Informationen finden Sie [hier](hpc-cache-support-ticket.md).

  Weitere Informationen zum Auflistungszugriff auf Verzeichnisse finden Sie im [Artikel zur Problembehandlung](troubleshoot-nas.md#enable-export-listing) für NFS-Speicherziele.

* **Root-Zugriff** (Lesen/Schreiben): Der Cache stellt als Benutzer mit der ID 0 eine Verbindung mit dem Back-End-System her. Überprüfen Sie die folgenden Einstellungen für Ihr Speichersystem:
  
  * `no_root_squash`aktivieren. Diese Option sorgt dafür, dass der Root-Remotebenutzer auf Dateien zugreifen kann, die sich im Besitz von Root befinden.

  * Vergewissern Sie sich, dass die Exportrichtlinien keine Einschränkungen für den Root-Zugriff aus dem Subnetz des Caches enthalten.

  * Wenn Ihr Speicher Exporte aufweist, bei denen es sich um Unterverzeichnisse eines anderen Exports handelt, stellen Sie sicher, dass der Cache über Root-Zugriff auf das niedrigste Segment des Pfads verfügt. Weitere Informationen finden Sie unter [Root-Zugriff auf Verzeichnispfade](troubleshoot-nas.md#allow-root-access-on-directory-paths) im Artikel zur Problembehandlung für NFS-Speicherziele.

* Der NFS-Back-End-Speicher muss eine kompatible Hardware-/Softwareplattform aufweisen. Ausführliche Informationen erhalten Sie vom Azure HPC Cache-Team.

## <a name="set-up-azure-cli-access-optional"></a>Einrichten des Azure CLI-Zugriffs (optional)

Wenn Sie Azure HPC Cache über die Azure-Befehlszeilenschnittstelle (Azure CLI) erstellen oder verwalten möchten, müssen Sie die CLI-Software und die Erweiterung „hpc-cache“ installieren. Befolgen Sie die Anweisungen unter [Einrichten der Azure CLI für Azure HPC Cache](az-cli-prerequisites.md).

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Azure HPC Cache-Instanz](hpc-cache-create.md) im Azure-Portal