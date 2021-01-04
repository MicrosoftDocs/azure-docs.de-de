---
title: Übersicht über das Oracle-Notfallwiederherstellungsszenario in der Azure-Umgebung | Microsoft-Dokumentation
description: Ein Notfallwiederherstellungsszenario für eine Oracle Database 12c-Datenbank in Ihrer Azure-Umgebung.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: b8da0b5c55b291af42d9a30db23d6f55f7c0bf2d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022784"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Notfallwiederherstellungsszenario für eine Oracle Database 12c-Datenbank in einer Azure-Umgebung

## <a name="assumptions"></a>Annahmen

- Sie besitzen Grundkenntnisse des Aufbaus von Oracle Data Guard und von Azure-Umgebungen.


## <a name="goals"></a>Ziele
- Entwerfen der Topologie und Konfiguration, die Ihre Anforderungen an die Notfallwiederherstellung (Disaster Recovery, DR) erfüllen.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Szenario 1: Primäre und DR-Standorte in Azure

Ein Kunde verfügt am primären Standort über eine Oracle-Datenbank. Ein DR-Standort befindet sich in einer anderen Region. Der Kunde verwendet Oracle Data Guard für die schnelle Wiederherstellung zwischen diesen Standorten. Der primäre Standort verfügt auch über eine sekundäre Datenbank für Berichte und andere Zwecke. 

### <a name="topology"></a>Topologie

Hier ein Überblick über den Aufbau von Azure:

- Zwei Standorte (ein primärer Standort und ein DR-Standort)
- Zwei virtuelle Netzwerke
- Zwei Oracle-Datenbanken mit Data Guard (primär und Standby)
- Zwei Oracle-Datenbanken mit Golden Gate oder Data Guard (nur am primären Standort)
- Zwei Anwendungsdienste, einer am primären und einer am DR-Standort
- Eine *Verfügbarkeitsgruppe*, die für Datenbank- und Anwendungsdienste am primären Standort verwendet wird
- Eine Jumpbox an jedem Standort, die den Zugriff auf das private Netzwerk einschränkt und nur Anmeldung durch einen Administrator zulässt
- Jumpbox, Anwendungsdienst, Datenbank und VPN-Gateway befinden sich in separaten Subnetzen
- Netzwerksicherheitsgruppe (Network Security Group, NSG) wird auf den Anwendungs- und Datenbanksubnetzen erzwungen

![Diagramm mit primären und DR-Standorten in Azure.](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Szenario 2: Lokaler primärer Standort und DR-Standort in Azure

Ein Kunde verfügt über eine lokale Oracle-Datenbank (primärer Standort). Ein DR-Standort befindet sich in Azure. Oracle Data Guard wird für die schnelle Wiederherstellung zwischen diesen Standorten verwendet. Der primäre Standort verfügt auch über eine sekundäre Datenbank für Berichte und andere Zwecke. 

Es gibt zwei Ansätze für diesen Aufbau.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Ansatz 1: Direkte Verbindungen zwischen dem lokalen Standort und Azure, für die die TCP-Ports in der Firewall geöffnet sein müssen 

Wir empfehlen keine direkten Verbindungen, da sie die TCP-Ports für die Außenwelt verfügbar machen.

#### <a name="topology"></a>Topologie

Hier ein Überblick über den Aufbau von Azure:

- Ein DR-Standort 
- Ein virtuelles Netzwerk
- Eine Oracle-Datenbank mit Data Guard (aktiv)
- Ein Anwendungsdienst am DR-Standort
- Eine Jumpbox, die den Zugriff auf das private Netzwerk einschränkt und nur Anmeldung durch einen Administrator zulässt
- Jumpbox, Anwendungsdienst, Datenbank und VPN-Gateway befinden sich in separaten Subnetzen
- Netzwerksicherheitsgruppe (Network Security Group, NSG) wird auf den Anwendungs- und Datenbanksubnetzen erzwungen
- Eine NSG-Richtlinie/Regel, um den eingehenden TCP-Port 1521 (oder einen benutzerdefinierten) zuzulassen
- Eine NSG-Richtlinie/Regel, damit nur die lokale/n IP-Adresse/n (Datenbank oder Anwendung) auf das virtuelle Netzwerk zugreifen kann/können

![Diagramm: Direkte Verbindungen zwischen dem lokalen Standort und Azure, für die TCP-Ports in der Firewall geöffnet sein müssen.](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Ansatz 2: Site-to-Site-VPN
Das Site-to-Site-VPN ist ein besserer Ansatz. Weitere Informationen zum Einrichten eines VPN finden Sie unter [Erstellen eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung per CLI](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md).

#### <a name="topology"></a>Topologie

Hier ein Überblick über den Aufbau von Azure:

- Ein DR-Standort 
- Ein virtuelles Netzwerk 
- Eine Oracle-Datenbank mit Data Guard (aktiv)
- Ein Anwendungsdienst am DR-Standort
- Eine Jumpbox, die den Zugriff auf das private Netzwerk einschränkt und nur Anmeldung durch einen Administrator zulässt
- Jumpbox, Anwendungsdienst, Datenbank und VPN-Gateway befinden sich in separaten Subnetzen
- Netzwerksicherheitsgruppe (Network Security Group, NSG) wird auf den Anwendungs- und Datenbanksubnetzen erzwungen
- Site-to-Site-VPN-Verbindung zwischen lokalem Standort und Azure

![Screenshot der DR-Topologieseite](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Zusätzliche Lektüre

- [Entwerfen und Implementieren einer Oracle-Datenbank in Azure](oracle-design.md)
- [Konfigurieren von Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurieren von Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Sichern und Wiederherstellen einer Oracle-Datenbank](./oracle-overview.md)


## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen eines hoch verfügbaren virtuellen Computers](../../linux/create-cli-complete.md)
- [Erkunden der Azure CLI-Beispiele für die Bereitstellung virtueller Computer](../../linux/cli-samples.md)
