---
title: Avere vFXT DNS – Azure
description: Konfigurieren eines DNS-Servers für den Roundrobin-Lastenausgleich mit Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009023"
---
# <a name="avere-cluster-dns-configuration"></a>DNS-Konfiguration des Avere-Clusters

In diesem Abschnitt werden die Grundlagen der Konfiguration eines DNS-Systems für den Lastenausgleich Ihres Avere vFXT-Clusters erläutert.

Dieses Dokument *enthält keine* Anweisungen zur Einrichtung und Verwaltung eines DNS-Servers in der Azure-Umgebung.

Anstatt Roundrobin-DNS für den Lastenausgleich in einem vFXT-Cluster in Azure zu verwenden, sollten Sie IP-Adressen mithilfe manueller Methoden gleichmäßig zwischen den Clients zuzuweisen, wenn sie eingebunden sind. Mehrere Methoden sind unter [Einbinden des Avere-Clusters](avere-vfxt-mount-clients.md) beschrieben.

Beachten Sie diese Faktoren bei der Entscheidung, ob Sie einen DNS-Server verwenden:

* Wenn nur über NFS-Clients auf Ihr System zugegriffen wird, ist die Verwendung von DNS nicht erforderlich. Alle Netzwerkadressen können über numerische IP-Adressen angegeben werden.

* Wenn Ihr System den SMB-Zugriff (CIFS) unterstützt, ist DNS erforderlich, da Sie eine DNS-Domäne für den Active Directory-Server angeben müssen.

* Wenn Sie die Kerberos-Authentifizierung verwenden möchten, ist DNS erforderlich.

## <a name="load-balancing"></a>Lastenausgleich

Um die Gesamtlast zu verteilen, konfigurieren Sie Ihre DNS-Domäne so, dass sie die Roundrobin-Lastverteilung für clientseitige IP-Adressen verwendet.

## <a name="configuration-details"></a>Konfigurationsdetails

Wenn Clients auf den Cluster zugreifen, gleicht RRDNS ihre Anforderungen automatisch zwischen allen verfügbaren Schnittstellen aus.

Konfigurieren Sie Ihren DNS-Server so, dass er clientseitige Clusteradressen wie in der folgenden Abbildung dargestellt verarbeitet, um optimale Leistungen zu erhalten.

Auf der linken Seite wird ein virtueller Clusterserver und in der Mitte und auf der rechten Seite werden IP-Adressen angezeigt. Konfigurieren Sie die einzelnen Clientzugriffspunkte mit A-Einträgen und Zeigern, wie in der Abbildung gezeigt.

![Diagramm zum Avere-Cluster mit Roundrobin-DNS](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Jede clientseitige IP-Adresse muss einen eindeutigen Namen für die interne Verwendung durch den Cluster aufweisen. (In diesem Diagramm werden die Client-IPs aus Gründen der Übersichtlichkeit mit „vs1-client-IP-*“ bezeichnet, aber in der Produktionsumgebung sollten Sie eine prägnantere Bezeichnung verwenden, z. B. „client*“.)

Clients binden den Cluster über den VServer-Namen als Serverargument ein.

Ändern Sie die Datei ``named.conf`` Ihres DNS-Servers, um die Reihenfolge für Abfragen an Ihren VServer festzulegen. Diese Option stellt sicher, dass alle verfügbaren Werte durchlaufen werden. Fügen Sie eine Anweisung wie die folgende hinzu:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Die folgenden ``nsupdate``-Befehle sind ein Beispiel für die ordnungsgemäße Konfiguration von DNS:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Cluster-DNS-Einstellungen

Geben Sie auf der Einstellungsseite **Cluster** > **Verwaltungsnetzwerk** den DNS-Server an, den der vFXT-Cluster verwendet. Zu den Einstellungen auf dieser Seite zählen die folgenden:

* DNS-Serveradresse
* DNS-Domänenname
* DNS-Suchdomänen

Weitere Informationen zur Verwendung dieser Seite finden Sie in der Anleitung zur Konfiguration des Avere-Clusters unter [DNS-Einstellungen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>).
