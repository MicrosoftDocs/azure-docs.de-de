---
title: IBM DB2 pureScale in Azure
description: In diesem Artikel wird eine Architektur zum Ausführen einer IBM DB2 pureScale-Umgebung in Azure gezeigt.
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
ms.openlocfilehash: 1622de0cccdbc8fee0681e209e756b30da292d3c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60543008"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale in Azure

Die IBM DB2 pureScale-Umgebung bietet einen Datenbankcluster für Azure mit hoher Verfügbarkeit und Skalierbarkeit unter Linux-Betriebssystemen. In diesem Artikel wird eine Architektur zum Ausführen von DB2 pureScale in Azure gezeigt.

## <a name="overview"></a>Übersicht

Unternehmen haben lange Zeit RDBMS-Plattformen (relationales Datenbank-Managementsystem) verwendet, um Anforderungen der Onlinetransaktionsverarbeitung (OLTP) zu erfüllen. Heutzutage führen viele eine Migration der Mainframe-basierten Datenbankumgebungen zu Azure durch, um so die Kapazität erweitern, Kosten senken und eine stabile Betriebskostenstruktur bewahren zu können.

Migration ist häufig der erste Schritt zur Modernisierung einer älteren Plattform. Beispielsweise hat ein Unternehmenskunde vor Kurzem seine IBM DB2-Umgebung unter z/OS auf einem neuen Host IBM DB2 pureScale in Azure zugewiesen. Zwar ist dies nicht mit der ursprünglichen Umgebung identisch, doch bietet IBM DB2 pureScale unter Linux ähnliche Hochverfügbarkeit und Skalierbarkeitsfeatures wie IBM DB2 für z/OS, das in einer Parallel Sysplex-Konfiguration auf dem Mainframe ausgeführt wird.

Dieser Artikel beschreibt die Architektur, die für diese Azure-Migration verwendet wurde. Der Kunde hat Red Hat Linux 7.4 zum Testen der Konfiguration verwendet. Diese Version ist im Azure Marketplace verfügbar. Prüfen Sie vor dem Auswählen einer Linux-Distribution die derzeit unterstützten Versionen. Ausführliche Informationen finden Sie in der Dokumentation zu [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) und [GlusterFS](https://docs.gluster.org/en/latest/).

Dieser Artikel dient als Ausgangspunkt für Ihren DB2-Implementierungsplan. Zwar sind Ihre Geschäftsanforderungen unterschiedlich, doch gilt das gleiche grundlegende Muster. Dieses Architekturmuster kann auch für Anwendungen zur analytischen Onlineverarbeitung (OLAP) in Azure verwendet werden.

In diesem Artikel werden weder die Unterschiede noch mögliche Migrationsaufgaben zum Verschieben einer IBM DB2 für z/OS-Datenbank zu IBM DB2 pureScale unter Linux behandelt. Es werden auch keine Schätzungen für Größenordnungen oder Workloadanalysen für den Übergang von DB2 z/OS zu DB2 PureScale bereitgestellt. 

Damit Sie sich für die optimale DB2 pureScale-Architektur für Ihre Umgebung entscheiden können, wird empfohlen, eine umfassende Schätzung der Größenordnung auszuführen und eine Hypothese aufzustellen. Stellen Sie auf dem Quellsystem sicher, dass DB2 z/OS Parallel Sysplex mit Datenfreigabearchitektur, Coupling Facility-Konfiguration und Distributed Data Facility-Nutzungsstatistiken (DDF) beachtet werden.

> [!NOTE]
> In diesem Artikel wird eine Methode der DB2-Migration beschrieben, doch gibt es auch noch andere. Beispielsweise kann DB2 pureScale auch in virtualisierten lokalen Umgebungen ausgeführt werden. IBM unterstützt DB2 auf Microsoft Hyper-V in verschiedenen Konfigurationen. Weitere Informationen finden Sie unter [Virtualisierungsarchitektur von DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) im IBM Knowledge Center.

## <a name="architecture"></a>Architecture

Zur Unterstützung von Hochverfügbarkeit und Skalierbarkeit in Azure kann eine Architektur mit gemeinsam genutzten Daten und horizontaler Skalierung für DB2 pureScale verwendet werden. Für die Kundenmigration wurde die folgende Beispielarchitektur verwendet.

![DB2 pureScale auf virtuellen Azure-Computern – Speicher und Netzwerk](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale auf virtuellen Azure-Computern – Speicher und Netzwerk")


Das Diagramm zeigt die logischen Ebenen, die für einen DB2 pureScale-Cluster erforderlich sind. Dazu gehören virtuelle Computer für Client, Verwaltung, Caching, Datenbank-Engine und freigegebenen Speicher. 

Zusätzlich zu den Datenbank-Engine-Knoten enthält das Diagramm zwei Knoten, die für Cluster-Caching-Funktionen (CFs) verwendet werden. Mindestens zwei Knoten werden für die Datenbank-Engine selbst verwendet. Ein DB2-Server, der zu einem pureScale-Cluster gehört, wird als „Member“ bezeichnet. 

Der Cluster ist über iSCSI mit einem GlusterFS-Cluster mit freigegebenem Speicher und drei Knoten verbunden, um Speicher für horizontale Skalierung und Hochverfügbarkeit zu bieten. DB2 pureScale wird auf virtuellen Azure-Computern installiert, auf denen Linux ausgeführt wird.

Dieser Ansatz ist eine Vorlage, die Sie der Größe und Skalierung Ihrer Organisation entsprechend ändern können. Sie basiert auf folgenden Voraussetzungen:

-   Mindestens zwei Datenbankmember werden mit mindestens zwei CF-Knoten kombiniert. Die Knoten verwalten einen globalen Pufferpool (GBP) für freigegebenen Speicher und globale Sperren-Manager-Dienste (GLM), um den freigegeben Zugriff und Sperrkonflikte zwischen mehreren aktiven Membern zu steuern. Ein CF-Knoten fungiert als primärer Knoten und der andere als sekundärer oder CF-Failoverknoten. Zum Vermeiden eines Single Point of Failure in der Umgebung sind mindestens vier Knoten für einen DB2 pureScale-Cluster erforderlich.

-   Freigegebener Hochleistungsspeicher (im Diagramm in Größe P30 dargestellt). Alle Gluster FS-Knoten verwenden diesen Speicher.

-   Hochleistungsnetzwerk für die Datenmember und freigegebenen Speicher.

### <a name="compute-considerations"></a>Computeaspekte

Bei dieser Architektur werden die Anwendungs-, Speicher- und Datenebenen auf virtuellen Azure-Computern ausgeführt. Die [Setupskripts für die Bereitstellung](https://aka.ms/db2onazure) erstellen Folgendes:

-   Einen DB2 pureScale-Cluster. Der Typ der in Azure benötigten Computeressourcen hängt von der jeweiligen Einrichtung ab. Im Allgemeinen können zwei Ansätze verwendet werden:

    -   Verwendung eines Netzwerks in HPC-Ausführung (High-Performance Computing) mit mehreren Knoten, bei dem kleine bis mittlere Instanzen auf freigegebenen Speicher zugreifen. Für diesen HPC-Konfigurationstyp bieten arbeitsspeicheroptimierte [virtuelle Computer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) der E-Serie oder L-Serie in Azure die erforderliche Computeleistung.

    -   Verwendung weniger großer VM-Instanzen für die Daten-Engines. Bei großen Instanzen sind die größten speicheroptimierten virtuellen Computer der [M-Serie](https://azure.microsoft.com/pricing/details/virtual-machines/series/) ideal für arbeitsspeicherintensive Workloads. Je nach Größe der logischen Partition (LPAR), die zum Ausführen von DB2 verwendet wird, kann eine dedizierte Instanz erforderlich sein.

-   Die DB2-CF-Instanz verwendet arbeitsspeicheroptimierte virtuelle Computer, z.B. der E-Serie oder L-Serie.

-   Der GlusterFS-Speicher verwendet virtuelle Computer des Typs Standard\_DS4\_v2, auf denen Linux ausgeführt wird.

-   Eine GlusterFS-Jumpbox ist ein virtueller Computer des Typs Standard\_DS2\_v2, auf dem Linux ausgeführt wird.

-   Der Client ist ein virtueller Computer des Typs Standard\_DS3\_v2, auf dem Windows ausgeführt wird (für Tests).

-   Ein Zeugenserver ist ein virtueller Computer des Typs Standard\_DS3\_v2, auf dem Linux ausgeführt wird (für DB2 pureScale).

> [!NOTE]
> In einem DB2 pureScale-Cluster sind mindestens zwei DB2-Instanzen erforderlich. Eine Cache-Instanz und eine Sperren-Manager-Instanz sind ebenfalls erforderlich.

### <a name="storage-considerations"></a>Speicheraspekt

Wie Oracle RAC ist auch DB2 pureScale eine Hochleistungsdatenbank mit Block-E/A und horizontaler Skalierung. Es wird empfohlen, die größte [Azure Premium SSD](disks-types.md)-Option zu verwenden, die Ihren Anforderungen entspricht. Kleinere Speicheroptionen eignen sich z.B. für Entwicklungs- und Testumgebungen, während Produktionsumgebungen häufig größere Speicherkapazität erfordern. Die Beispielarchitektur verwendet [P30](https://azure.microsoft.com/pricing/details/managed-disks/) aufgrund des Verhältnisses von IOPS zu Größe und Preis. Verwenden Sie unabhängig von der Größe Storage Premium für eine optimale Leistung.

DB2 pureScale verwendet eine Architektur mit umfassender Freigabe, bei der von allen Clusterknoten auf alle Daten zugegriffen werden kann. Premium-Speicher muss für instanzenübergreifend freigegeben werden – unabhängig davon, ob es sich um bedarfsgesteuerte oder dedizierte Instanzen handelt.

Ein großer DB2 pureScale-Cluster kann 200 Terabyte (TB) oder mehr freigegebenen Premium-Speicher mit einem IOPS-Wert von 100.000 erfordern. DB2 pureScale unterstützt eine iSCSI-Blockschnittstelle, die in Azure verwendet werden kann. Die iSCSI-Schnittstelle erfordert einen Cluster mit freigegebenem Speicher, der mit GlusterFS, S2D oder einem anderen Tool implementiert werden kann. Bei diesem Lösungstyp wird ein Gerät für das virtuelle Storage Area Network (vSAN) in Azure erstellt. DB2 pureScale verwendet das vSAN zum Installieren des gruppierten Dateisystems, mit dem Daten zwischen virtuellen Computern freigegeben werden.

In der Beispielarchitektur wurde GlusterFS verwendet, ein kostenloses, skalierbares, verteiltes Open-Source-Dateisystem, das für Cloudspeicher optimiert ist.

### <a name="networking-considerations"></a>Überlegungen zum Netzwerkbetrieb

IBM empfiehlt InfiniBand-Netzwerkfunktionen für alle Member in einem DB2 pureScale-Cluster. DB2 pureScale verwendet für die CFs außerdem Remotezugriff auf den direkten Speicher (Remote Direct Memory Access, RDMA), sofern dieser verfügbar ist.

Während des Setups erstellen Sie eine Azure-[Ressourcengruppe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), die dann alle virtuellen Computer enthält. Ressourcen werden allgemein auf der Grundlage ihrer Lebensdauer sowie auf der Grundlage der Benutzer gruppiert, die sie verwalten. Die virtuellen Computer in dieser Architektur erfordern [beschleunigten Netzwerkbetrieb](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Dies ist ein Azure-Feature, das konsistente, extrem geringe Netzwerklatenz über E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) für einen virtuellen Computer bietet.

Jeder virtuelle Azure-Computer wird in einem virtuellen Netzwerk mit mehreren Subnetzen bereitgestellt: Hauptsubnetz, GlusterFS-Front-End (gfsfe), GlusterFS-Back-End (bfsbe), DB2 pureScale (db2be) und DB2 pureScale-Front-End (db2fe). Das Installationsskript erstellt auch die primären [NICs](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) auf den virtuellen Computern im Hauptsubnetz.

Mithilfe von [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) werden der Netzwerkdatenverkehr innerhalb des virtuellen Netzwerks eingeschränkt und Subnetze isoliert.

In Azure muss DB2 pureScale TCP/IP als Netzwerkverbindung zum Speicher verwenden.

## <a name="next-steps"></a>Nächste Schritte

-   [Bereitstellen dieser Architektur in Azure](deploy-ibm-db2-purescale-azure.md)
