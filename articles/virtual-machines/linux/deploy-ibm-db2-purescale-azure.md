---
title: Bereitstellen von IBM DB2 pureScale in Azure
description: Erfahren Sie, wie Sie eine Beispielarchitektur bereitstellen, die vor Kurzem zum Migrieren eines Unternehmens von der IBM DB2-Umgebung unter z/OS zu IBM DB2 pureScale in Azure verwendet wurde.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: fba6b5308b380b374611c09747302dbf8305dd9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60716046"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Bereitstellen von IBM DB2 pureScale in Azure

In diesem Artikel wird beschrieben, wie Sie eine [Beispielarchitektur](ibm-db2-purescale-azure.md) bereitstellen, mit der ein Unternehmenskunde vor Kurzem von seiner IBM DB2-Umgebung unter z/OS zu IBM DB2 pureScale in Azure migriert ist.

Informationen zu den Schritten für die Migration finden Sie in den Installationsskripts im [DB2onAzure](https://aka.ms/db2onazure)-Repository auf GitHub. Diese Skripts basieren auf der Architektur für eine typische OLTP-Workload (Onlinetransaktionsverarbeitung) mittlerer Größe.

## <a name="get-started"></a>Erste Schritte

Zum Bereitstellen dieser Architektur laden Sie das Skript „deploy.sh“ herunter und führen es aus. Sie finden dieses Skript im [DB2onAzure](https://aka.ms/db2onazure)-Repository auf GitHub.

Das Repository enthält auch Skripts zum Einrichten eines Grafana-Dashboards. Sie können mit dem Dashboard Prometheus abfragen, das in DB2 enthaltene Open-Source-Überwachungs- und -Warnsystem.

> [!NOTE]
> Das Skript „deploy.sh“ auf dem Client erstellt private SSH-Schlüssel und übergibt diese per HTTPS an die Bereitstellungsvorlage. Aus Sicherheitsgründen wird empfohlen, [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) zum Speichern von Geheimnissen, Schlüsseln und Kennwörtern zu verwenden.

## <a name="how-the-deployment-script-works"></a>Funktionsweise des Bereitstellungsskripts

Das Skript „deploy.sh“ erstellt und konfiguriert die Azure-Ressourcen für diese Architektur. Das Skript fordert Sie auf, das Azure-Abonnement und virtuelle Computer anzugeben, die in der Zielumgebung verwendet werden, und führt dann die folgenden Vorgänge aus:

-   Einrichten der Ressourcengruppe, des virtuellen Netzwerks und der Subnetze in Azure für die Installation

-   Einrichten der Netzwerksicherheitsgruppen und von SSH für die Umgebung

-   Einrichten der NICs auf den virtuellen GlusterFS- und DB2 pureScale-Computern

-   Erstellen der virtuellen Computer für den GlusterFS-Speicher

-   Erstellen des virtuellen Jumpbox-Computers

-   Erstellen der virtuellen DB2 pureScale-Computer

-   Erstellen des virtuellen Zeugencomputers, den DB2 pureScale pingt

-   Erstellen eines virtuellen Windows-Computers zu Testzwecken, jedoch ohne jegliche Installation auf diesem Computer

Als Nächstes richten die Bereitstellungsskripts ein virtuelles Storage Area Network (vSAN) mit iSCSI für den freigegebenen Speicher in Azure ein. In diesem Beispiel stellt iSCSI eine Verbindung mit GlusterFS her. Diese Lösung bietet Ihnen auch die Möglichkeit, die iSCSI-Ziele als einzelnen Windows-Knoten zu installieren. iSCSI stellt eine freigegebene Blockspeicherschnittstelle über TCP/IP bereit, die es dem DB2 pureScale-Setupvorgang ermöglicht, eine Geräteschnittstelle zum Herstellen einer Verbindung mit dem freigegebenen Speicher zu verwenden. Grundlegende Informationen zu GlusterFS finden Sie im Thema [Architecture: Types of volumes](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) (Architektur: Volumetypen) in der Dokumentation zu Gluster.

Die Bereitstellungsskripts führen die folgenden allgemeinen Schritte aus:

1.  Einrichten eines freigegebenen Speicherclusters in Azure mithilfe von GlusterFS. Dieser Schritt umfasst mindestens zwei Linux-Knoten. Ausführliche Informationen zum Setup finden Sie im Abschnitt zum Thema [Einrichten von Red Hat Gluster Storage in Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) in der Dokumentation zu Red Hat Gluster.

2.  Einrichten einer iSCSI Direct-Schnittstelle auf Linux-Zielservern für GlusterFS. Ausführliche Informationen zum Setup finden Sie unter [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) im Administratorhandbuch zu GlusterFS.

3.  Einrichten des iSCSI-Initiators auf den virtuellen Linux-Computern. Der Initiator greift mit einem iSCSI-Ziel auf den GlusterFS-Cluster zu. Ausführliche Informationen zum Setup finden Sie unter [How To Configure An iSCSI Target And Initiator In Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) (Konfigurieren von iSCSI-Ziel und -Initiator unter Linux) in der RootUsers-Dokumentation.

4.  Installieren von GlusterFS als Speicherebene für die iSCSI-Schnittstelle.

Nach dem Erstellen des iSCSI-Geräts durch die Skripts wird im letzten Schritt DB2 pureScale installiert. Im Rahmen des DB2 pureScale-Setups wird [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (ehemals GPFS) kompiliert und auf dem GlusterFS-Cluster installiert. Dieses gruppierte Dateisystem ermöglicht DB2 pureScale das Freigeben von Daten zwischen virtuellen Computern, auf denen die DB2 pureScale-Engine ausgeführt wird. Weitere Informationen finden Sie in der Dokumentation zu [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) auf der IBM-Website.

## <a name="db2-purescale-response-file"></a>DB2 pureScale-Antwortdatei

Das GitHub-Repository enthält „DB2server.rsp“, eine Antwortdatei (RSP), mit der Sie ein automatisiertes Skript für die Installation von DB2 pureScale generieren können. In der folgenden Tabelle sind die DB2 pureScale-Optionen aufgeführt, die von der Antwortdatei für das Setup verwendet werden. Sie können die Antwortdatei entsprechend den Anforderungen Ihrer Umgebung anpassen.

> [!NOTE]
> Eine Beispielantwortdatei („DB2server.rsp“) befindet sich im [DB2onAzure](https://aka.ms/db2onazure)-Repository auf GitHub. Wenn Sie diese Datei verwenden, müssen Sie sie bearbeiten, bevor sie in Ihrer Umgebung funktionieren kann.

| Anzeigename               | Feld                                        | Wert                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Willkommen                   |                                              | Neue Installation                                                                                           |
| Produkt auswählen          |                                              | DB2 Version 11.1.3.3. Server-Editionen mit DB2 pureScale                                              |
| Konfiguration             | Verzeichnis                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Installationstyp auswählen                 | Typisch                                                                                               |
|                           | Ich akzeptiere die IBM Bedingungen                     | Aktiviert                                                                                               |
| Instanzeigner            | Vorhandener Benutzer für Instanz, Benutzername        | DB2sdin1                                                                                              |
| Abgeschirmter Benutzer               | Vorhandener Benutzer, Benutzername                     | DB2sdfe1                                                                                              |
| Clusterdateisystem       | Einheitenpfad gemeinsam genutzter Plattenpartition            | /dev/dm-2                                                                                             |
|                           | Mountpunkt                                  | /DB2sd\_1804a                                                                                         |
|                           | Gemeinsam genutzte Platte für Daten                         | /dev/dm-1                                                                                             |
|                           | Mountpunkt (Daten)                           | /DB2fs/datafs1                                                                                        |
|                           | Gemeinsam genutzte Platte für Protokoll                          | /dev/dm-0                                                                                             |
|                           | Mountpunkt (Protokoll)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2-Clusterdienste-Tiebreaker. Einheitenpfad | /dev/dm-3                                                                                             |
| Hostliste                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | Bevorzugte primäre CF                         | cf1                                                                                                   |
|                           | Bevorzugte sekundäre CF                       | cf2                                                                                                   |
| Antwortdatei und Zusammenfassung | erste Option                                 | Installieren von DB2 Server Edition mit dem IBM DB2 pureScale-Feature und Speichern der Einstellungen in einer Antwortdatei |
|                           | Antwortdateiname                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Anmerkungen zu dieser Bereitstellung

- Die Werte für „/dev-dm0“, „/dev-dm1“, „/dev-dm2“ und „/dev-dm3“ können sich nach einem Neustart auf dem virtuellen Computer, auf dem das Setup ausgeführt wird (d0 im automatisierten Skript), ändern. Um die richtigen Werte zu finden, können Sie den folgenden Befehl ausgeben, bevor Sie die Antwortdatei auf dem Server, auf dem das Setup ausgeführt wird, fertigstellen:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Die Setupskripts verwenden Aliase für die iSCSI-Datenträger, sodass die tatsächlichen Namen leicht gefunden werden können.

- Wenn das Setupskript auf d0 ausgeführt wird, können die Werte für **„/dev/dm-\*** “ auf d1, cf0 und cf1 unterschiedlich sein. Der Unterschied bei den Werten hat keine Auswirkung auf das DB2 pureScale-Setup.

## <a name="troubleshooting-and-known-issues"></a>Problembehandlung und bekannte Probleme

Das GitHub-Repository enthält eine Wissensdatenbank, die von den Autoren verwaltet wird. Dort sind mögliche Probleme aufgeführt sowie Lösungen, die Sie ausprobieren können. Beispielsweise können unter den folgenden Umständen bekannte Probleme auftreten:

-   Versuch, die Gateway-IP-Adresse zu erreichen

-   Kompilieren der General Public License (GPL)

-   Fehler beim Sicherheitshandshake zwischen Hosts

-   Erkennen eines vorhandenen Dateisystems durch das DB2-Installationsprogramm

-   Manuelles Installieren von IBM Spectrum Scale

-   Installieren von DB2 pureScale bei bereits erstelltem IBM Spectrum Scale

-   Entfernen von DB2 pureScale und IBM Spectrum Scale

Weitere Informationen zu diesen und anderen bekannten Problemen finden Sie in der Datei „kb.md“ im [DB2onAzure](https://aka.ms/DB2onAzure)-Repository.

## <a name="next-steps"></a>Nächste Schritte

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Erstellen der erforderlichen Benutzer für eine Installation des DB2 pureScale Features](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt – Befehl zum Erstellen einer Instanz](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale Clusters Data Solution](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Platform Modernization Alliance: IBM DB2 on Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx) (Platform Modernization Alliance: DB2 unter Azure)

-   [Virtuelles Azure-Rechenzentrum: Lift and Shift-Leitfaden](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
