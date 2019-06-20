---
title: Onboardinganforderungen für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Onboardinganforderungen für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 252c84bce2b70f6931593fe9410abe6cc146b5bb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60679050"
---
# <a name="onboarding-requirements"></a>Onboardinganforderungen

Diese Liste führt die Anforderungen zum Ausführen von SAP HANA in Azure (große Instanzen) auf.

**Microsoft Azure**

- Ein Azure-Abonnement, das mit SAP HANA in Azure (große Instanzen) verknüpft werden kann.
- Microsoft Premier Support-Vertrag. Spezifische Informationen im Zusammenhang mit der Ausführung von SAP in Azure finden Sie im [SAP-Supporthinweis 2015553 – SAP in Microsoft Azure: Supportvoraussetzungen](https://launchpad.support.sap.com/#/notes/2015553). Wenn Sie Einheiten von HANA (große Instanz) mit 384 und mehr CPUs verwenden, muss der Premier Support-Vertrag Azure Rapid Response beinhalten.
- Kenntnis der SKUs von HANA (große Instanz), die Sie nach dem Ausführen einer Größenanpassung mit SAP benötigen.

**Netzwerkkonnektivität**

- ExpressRoute zwischen lokalen Systemen und Azure: Um Ihr lokales Rechenzentrum mit Azure zu verbinden, benötigen Sie eine Verbindung mit mindestens 1 GBit/s von Ihrem Internetdienstanbieter (ISP). Für die Verbindungen zwischen HANA-Einheiten (große Instanzen) und Azure wird ebenfalls ExpressRoute-Technologie verwendet. Diese ExpressRoute-Verbindung zwischen den HANA-Einheiten (große Instanzen) und Azure ist im Preis für die HANA-Einheiten (große Instanzen) enthalten, einschließlich aller Gebühren für eingehende und ausgehende Daten für diese spezifische ExpressRoute-Verbindung. Aus diesem Grund entstehen für Sie als Kunde über Ihre ExpressRoute-Verbindung zwischen lokalen Systemen und Azure hinaus keine zusätzlichen Kosten.

**Betriebssystem**

- Lizenzen für SUSE Linux Enterprise Server 12 für SAP-Anwendungen.

   > [!NOTE] 
   > Das von Microsoft bereitgestellte Betriebssystem ist nicht bei SUSE registriert. Es ist nicht mit einer Subscription Management Tool-Instanz verbunden.

- Bereitstellung von SUSE Linux Subscription Management Tool in Azure auf einer VM. Dieses Tool bietet die Möglichkeit, SAP HANA in Azure (große Instanzen) zu registrieren und entsprechend von SUSE aktualisieren zu lassen. (Im Rechenzentrum für HANA (große Instanz) besteht kein Internetzugriff.) 
- Lizenzen für Red Hat Enterprise Linux 6.7 oder 7.x für SAP HANA.

   > [!NOTE]
   > Das von Microsoft bereitgestellte Betriebssystem ist nicht bei Red Hat registriert. Es ist nicht mit einer Red Hat Subscription Manager-Instanz verbunden.

- Bereitstellung von Red Hat Subscription Manager in Azure auf einer VM. Der Red Hat Subscription Manager bietet die Möglichkeit, SAP HANA in Azure (große Instanzen) zu registrieren und entsprechend von Red Hat aktualisieren zu lassen. (In dem Mandanten, der im Azure-Umfeld der großen Instanz bereitgestellt wird, besteht kein direkter Internetzugriff.)
- SAP setzt voraus, dass Sie auch einen Support-Vertrag mit Ihrem Linux-Anbieter haben. Diese Anforderung gilt auch bei der Verwendung von HANA (große Instanz) und trotz der Tatsache, dass Sie Linux in Azure ausführen. Anders als bei einigen der Images im Linux Azure-Katalog ist die Servicegebühr *nicht* im Lösungsangebot von HANA (große Instanz) enthalten. Es liegt in Ihrer Verantwortung, die Anforderungen von SAP bezüglich der Supportverträge mit dem Linux-Vertreiber zu erfüllen. 
   - Für SUSE Linux finden Sie die Anforderungen für Supportverträge in [SAP-Hinweis 1984787 – SUSE Linux Enterprise Server 12: Installationshinweise](https://launchpad.support.sap.com/#/notes/1984787) und [SAP-Hinweis 1056161 – SUSE-Prioritätssupport für SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/1056161).
   - Für Red Hat Linux benötigen Sie die richtigen Abonnementebenen, die Support und Dienstupdates für die Betriebssysteme von HANA (große Instanz) umfassen. Red Hat empfiehlt das Red Hat Enterprise Linux-Abonnement für SAP-Lösungen. Lesen Sie https://access.redhat.com/solutions/3082481. 

Die Supportmatrix der anderen SAP HANA-Versionen mit den verschiedenen Linux-Versionen finden Sie im [SAP-Hinweis 2235581](https://launchpad.support.sap.com/#/notes/2235581).

Die Kompatibilitätsmatrix des Betriebssystems und der HLI-Firmware-/Treiberversionen finden Sie unter [Operating System Upgrade](os-upgrade-hana-large-instance.md) (Betriebssystemupgrade).


> [!IMPORTANT] 
> Derzeit wird für Einheiten vom Typ II nur die Betriebssystemversion SLES 12 SP2 unterstützt. 


**Datenbank**

- Lizenzen und Softwareinstallationskomponenten für SAP HANA (Plattform oder Enterprise Edition).

**Anwendungen**

- Lizenzen und Softwareinstallationskomponenten für alle SAP-Anwendungen, die eine Verbindung mit SAP HANA herstellen, sowie zugehörige SAP-Supportverträge.
- Lizenzen und Softwareinstallationskomponenten für alle Nicht-SAP-Anwendungen, die mit Umgebungen für SAP HANA in Azure (große Instanzen) verwendet werden, sowie zugehörige Supportverträge.

**Fähigkeiten**

- Erfahrung und Kenntnisse in Bezug auf IaaS und die zugehörigen Komponenten
- Erfahrung und Kenntnisse in Bezug auf die Bereitstellung von SAP-Workloads in Azure
- Für die SAP HANA-Installation zertifizierte Mitarbeiter.
- Kenntnisse der SAP-Architektur zum Entwerfen der Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA

**SAP**

- Es wird vorausgesetzt, das Sie ein SAP-Kunde sind und über einen Supportvertrag mit SAP verfügen.
- Insbesondere für Implementierungen der Typ-II-Klasse-SKUs von HANA (große Instanz) sollten Sie SAP bezüglich der SAP HANA-Versionen und möglichen Konfigurationen auf großer, zentral hochskalierter Hardware zurate ziehen.

**Nächste Schritte**
- Lesen Sie [Architektur von SAP HANA in Azure (große Instanzen)](hana-architecture.md).