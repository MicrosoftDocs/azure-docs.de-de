---
title: Microsoft Azure-Zertifizierungen für SAP | Microsoft-Dokumentation
description: Aktualisierte Liste der aktuellen Konfigurationen und Zertifizierungen von SAP auf der Azure-Plattform.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: a38d5b44aaca0aa28e7ecd32543d2b0563964b3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65797447"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-Zertifizierungen und -Konfigurationen in Microsoft Azure

SAP und Microsoft können auf eine lange Geschichte enger Zusammenarbeit in einer starken Partnerschaft zurückblicken, die den Kunden beider Unternehmen Vorteile bringt. Microsoft führt kontinuierlich Aktualisierungen seiner Plattform durch und übermittelt stets neue Zertifizierungsinformationen an SAP, um sicherzustellen, dass Microsoft Azure die optimale Plattform zum Ausführen von SAP-Workloads ist. In den folgenden Tabellen werden die in Azure unterstützten Konfigurationen und die anwachsende Liste von SAP-Zertifizierungen beschrieben. 

## <a name="sap-hana-certifications"></a>SAP HANA-Zertifizierungen
Referenzen:

- [SAP HANA-zertifizierte IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) zur Unterstützung für SAP HANA für native Azure-VMs und HANA (große Instanzen)

| SAP-Produkt | Unterstütztes Betriebssystem | Azure-Angebote |
| --- | --- | --- |
| SAP HANA Developer Edition (einschließlich HANA-Clientsoftware, bestehend aus SQLODBC, ODBO [nur Windows], ODBC, JDBC-Treibern, HANA Studio und HANA Database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | VMs der D-Serie |
| Business One unter HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Kontrollierte Verfügbarkeit für GS5. Vollständige Unterstützung für M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA in Azure (große Instanzen) [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, SAP HANA in Azure (große Instanzen) <br /> [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise für BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA in Azure (große Instanzen) [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA in Azure (große Instanzen) <br /> [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Beachten Sie, dass SAP den Begriff „Clustering“ bei [zertifizierten SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) als Synonym für „Horizontale Skalierung“ und NICHT für Hochverfügbarkeits- „Clustering“ verwendet.

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-Zertifizierungen
Microsoft Azure ist für die folgenden SAP-Produkte zertifiziert. Microsoft und SAP bieten vollständigen Support für die Plattform.
Referenzen:

- [1928533 – SAP-Anwendungen in Azure: Unterstützte Produkte und Azure-VM-Typen](https://launchpad.support.sap.com/#/notes/1928533) für alle SAP NetWeaver-basierten Anwendungen, einschließlich SAP TREX, SAP LiveCache und SAP Content Server. sowie alle Datenbanken mit Ausnahme von SAP HANA


| SAP-Produkt | Gastbetriebssystem | RDBMS | VM-Typ (virtueller Computer) |
| --- | --- | --- | --- |
| SAP Business Suite Software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (nur Windows und Oracle Linux), DB2, SAP ASE |A5 bis A11, D11 bis D14, DS11 bis DS14, DS11_v2 bis DS15_v2, GS1 bis GS5, D2s_v3 bis D64s_v3, E2s_v3 bis E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (nur Windows und Oracle Linux), DB2, SAP ASE |A5 bis A11, D11 bis D14, DS11 bis DS14, DS11_v2 bis DS15_v2, GS1 bis GS5, D2s_v3 bis D64s_v3, E2s_v3 bis E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP BusinessObjects BI | Windows |– |A5 bis A11, D11 bis D14, DS11 bis DS14, DS11_v2 bis DS15_v2, GS1 bis GS5, D2s_v3 bis D64s_v3, E2s_v3 bis E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (nur Windows und Oracle Linux), DB2, SAP ASE |A5 bis A11, D11 bis D14, DS11 bis DS14, DS11_v2 bis DS15_v2, GS1 bis GS5, D2s_v3 bis D64s_v3, E2s_v3 bis E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Andere in Azure unterstützte SAP-Workloads

| SAP-Produkt | Gastbetriebssystem | RDBMS | VM-Typ (virtueller Computer) |
| --- | --- | --- | --- |
| SAP Business One unter SQL Server | Windows  | SQL Server | Alle NetWeaver-zertifizierten VM-Typen<br /> [SAP-Hinweis 928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows und Linux | | Alle NetWeaver-zertifizierten VM-Typen<br /> SAP-Hinweis 2451795 |
| SAP Business Objects BI-Plattform | Windows und Linux | | SAP-Hinweis 2145537 |
| SAP Data Services 4.2 | | | SAP-Hinweis 2288344 |
| SAP Hybris Commerce-Plattform 5.x und 6.x | Windows | SQL Server, Oracle | Alle NetWeaver-zertifizierten VM-Typen<br /> [Hybris-Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
