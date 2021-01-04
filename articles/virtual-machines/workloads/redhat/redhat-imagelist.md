---
title: Verfügbare RHEL-Images (Red Hat Enterprise Linux) in Azure
description: Erfahren Sie etwas über Red Hat Enterprise Linux-Images in Microsoft Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: ea0e8c148a847ecc23bab5e87a1dc60a626cf0c1
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95411613"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Verfügbare RHEL-Images (Red Hat Enterprise Linux) in Azure
Azure bietet eine Vielzahl von RHEL-Images für verschiedene Anwendungsfälle.

> [!NOTE]
> Alle RHEL-Images sind in öffentlichen Azure-Clouds und Azure Government-Clouds verfügbar. Sie sind nicht in Azure China-Clouds verfügbar.

## <a name="list-of-rhel-images"></a>Liste der RHEL-Images
Die folgende Liste enthält die in Azure verfügbaren RHEL-Images. Sofern nicht anders angegeben, sind alle Images mit LVM partitioniert und an reguläre RHEL-Repositorys angefügt (nicht EUS, nicht E4S). Die folgenden Images sind derzeit zur allgemeinen Verwendung verfügbar:

> [!NOTE]
> Unformatierte Images werden zugunsten von LVM-partitionierten Images nicht mehr erstellt. LVM bietet im Vergleich zum älteren (LVM-fremden) Partitionierungsschema mehrere Vorteile, einschließlich wesentlich flexiblerer Optionen für Änderungen der Partitionsgröße.

Angebot| SKU | Partitionierung | Bereitstellung | Notizen
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux-Agent | Die erweiterte Lebenszyklusunterstützung ist ab dem 1. Dezember verfügbar. [Ausführlichere Informationen finden Sie hier.](redhat-extended-lifecycle-support.md)
|             | 6,8      | RAW    | Linux-Agent | Die erweiterte Lebenszyklusunterstützung ist ab dem 1. Dezember verfügbar. [Ausführlichere Informationen finden Sie hier.](redhat-extended-lifecycle-support.md)
|             | 6.9      | RAW    | Linux-Agent | Die erweiterte Lebenszyklusunterstützung ist ab dem 1. Dezember verfügbar. [Ausführlichere Informationen finden Sie hier.](redhat-extended-lifecycle-support.md)
|             | 6.10     | RAW    | Linux-Agent | Die erweiterte Lebenszyklusunterstützung ist ab dem 1. Dezember verfügbar. [Ausführlichere Informationen finden Sie hier.](redhat-extended-lifecycle-support.md)
|             | 7-RAW    | RAW    | Linux-Agent | RHEL 7.x-Imagefamilie. <br> Standardmäßig mit regulären Repositorys verknüpft (nicht EUS).
|             | 7-LVM    | LVM    | Linux-Agent | RHEL 7.x-Imagefamilie. <br> Standardmäßig mit regulären Repositorys verknüpft (nicht EUS). Wenn Sie ein RHEL-Standardimage für die Bereitstellung suchen, verwenden Sie diese Gruppe von Images oder deren Entsprechung für die Generation 2.
|             | 7lvm-gen2| LVM    | Linux-Agent | Images der Generation 2, RHEL 7.x-Familie. <br> Standardmäßig mit regulären Repositorys verknüpft (nicht EUS). Wenn Sie ein RHEL-Standardimage für die Bereitstellung suchen, verwenden Sie diese Gruppe von Images oder deren Entsprechung für die Generation 1.
|             | 7-RAW-CI | RAW-CI | Cloud-Init  | RHEL 7.x-Imagefamilie. <br> Standardmäßig mit regulären Repositorys verknüpft (nicht EUS).
|             | 7.2      | RAW    | Linux-Agent |
|             | 7.3      | RAW    | Linux-Agent |
|             | 7.4      | RAW    | Linux-Agent | Ab April 2019 standardmäßig mit EUS-Repositorys verknüpft.
|             | 74-gen2  | RAW    | Linux-Agent | Standardmäßig mit EUS-Repositorys verknüpft.
|             | 7,5      | RAW    | Linux-Agent | Ab Juni 2019 standardmäßig mit EUS-Repositorys verknüpft.
|             | 75-gen2  | RAW    | Linux-Agent | Standardmäßig mit EUS-Repositorys verknüpft.
|             | 7.6      | RAW    | Linux-Agent | Ab Mai 2019 standardmäßig mit EUS-Repositorys verknüpft.
|             | 76-gen2  | RAW    | Linux-Agent | Standardmäßig mit EUS-Repositorys verknüpft.
|             | 7,7      | LVM    | Linux-Agent | Standardmäßig mit EUS-Repositorys verknüpft.
|             | 77-gen2  | LVM    | Linux-Agent | Standardmäßig mit EUS-Repositorys verknüpft.
|             | 7,8      | LVM    | Linux-Agent | An reguläre Repositorys angefügt (EUS nicht für RHEL 7.8 verfügbar)
|             | 78-gen2  | LVM    | Linux-Agent | An reguläre Repositorys angefügt (EUS nicht für RHEL 7.8 verfügbar)
|             | 7.9      | LVM    | Linux-Agent | An reguläre Repositorys angefügt (EUS nicht für RHEL 7.9 verfügbar)
|             | 79-gen2  | LVM    | Linux-Agent | An reguläre Repositorys angefügt (EUS nicht für RHEL 7.9 verfügbar)
|             | 8-LVM    | LVM    | Linux-Agent | RHEL 8.x-Imagefamilie. An reguläre Repositorys angefügt.
|             | 8-lvm-gen2| LVM    | Linux-Agent | Hyper-V Generation 2 – RHEL 8.x-Imagefamilie. An reguläre Repositorys angefügt.
|             | 8        | LVM    | Linux-Agent | RHEL 8.0-Images.
|             | 8-gen2   | LVM    | Linux-Agent | Hyper-V-Generation 2: RHEL 8.0-Images.
|             | 8.1      | LVM    | Linux-Agent | Standardmäßig mit EUS-Repositorys verknüpft.
|             | 81gen2   | LVM    | Linux-Agent | Hyper-V Generation 2: seit November 2020 an EUS-Repositorys angefügt.
|             | 8.1-ci   | LVM    | Linux-Agent | Seit November 2020 an EUS-Repositorys angefügt.
|             | 81-ci-gen2| LVM    | Linux-Agent | Hyper-V Generation 2: seit November 2020 an EUS-Repositorys angefügt.
|             | 8,2      | LVM    | Linux-Agent | Seit November 2020 an EUS-Repositorys angefügt.
|             | 82gen2   | LVM    | Linux-Agent | Hyper-V Generation 2: seit November 2020 an EUS-Repositorys angefügt.
|             | 8.3   | LVM    | Linux-Agent |  An reguläre Repositorys angefügt (EUS nicht für RHEL 8.3 verfügbar)
|             | 83-gen2   | LVM    | Linux-Agent |Hyper-V Generation 2: an reguläre Repositorys angefügt (EUS nicht für RHEL 8.3 verfügbar)
RHEL-SAP      | 7.4      | LVM    | Linux-Agent | RHEL 7.4 for SAP HANA und Geschäfts-Apps. An E4S-Repositorys angefügt, Premium-Berechnung für SAP und RHEL sowie Grundgebühr für Computekapazität.
|             | 74sap-gen2| LVM    | Linux-Agent | RHEL 7.4 for SAP HANA und Geschäfts-Apps. Image der Generation 2. An E4S-Repositorys angefügt, Premium-Berechnung für SAP und RHEL sowie Grundgebühr für Computekapazität.
|             | 7,5       | LVM    | Linux-Agent | RHEL 7.5 for SAP HANA und Geschäfts-Apps. An E4S-Repositorys angefügt, Premium-Berechnung für SAP und RHEL sowie Grundgebühr für Computekapazität.
|             | 75sap-gen2| LVM    | Linux-Agent | RHEL 7.5 for SAP HANA und Geschäfts-Apps. Image der Generation 2. An E4S-Repositorys angefügt, Premium-Berechnung für SAP und RHEL sowie Grundgebühr für Computekapazität.
|             | 7.6       | LVM    | Linux-Agent | RHEL 7.6 for SAP HANA und Geschäfts-Apps. An E4S-Repositorys angefügt, Premium-Berechnung für SAP und RHEL sowie Grundgebühr für Computekapazität.
|             | 76sap-gen2| LVM    | Linux-Agent | RHEL 7.6 for SAP HANA und Geschäfts-Apps. Image der Generation 2. An E4S-Repositorys angefügt, Premium-Berechnung für SAP und RHEL sowie Grundgebühr für Computekapazität.
|             | 7,7       | LVM    | Linux-Agent | RHEL 7.7 for SAP HANA und Geschäfts-Apps. An E4S-Repositorys angefügt, Premium-Berechnung für SAP und RHEL sowie Grundgebühr für Computekapazität.
RHEL-SAP-HANA (wird im November 2020 entfernt) | 6.7       | RAW    | Linux-Agent | RHEL 6.7 for SAP HANA. Veraltet zugunsten der RHEL-SAP-Images. Dieses Image wird im November 2020 entfernt. Weitere Informationen zu den SAP-Cloud-Angeboten von Red Hat finden Sie [hier](https://access.redhat.com/articles/3751271).
|             | 7.2       | LVM    | Linux-Agent | RHEL 7.2 for SAP HANA. Veraltet zugunsten der RHEL-SAP-Images. Dieses Image wird im November 2020 entfernt. Weitere Informationen zu den SAP-Cloud-Angeboten von Red Hat finden Sie [hier](https://access.redhat.com/articles/3751271).
|             | 7.3       | LVM    | Linux-Agent | RHEL 7.3 for SAP HANA. Veraltet zugunsten der RHEL-SAP-Images. Dieses Image wird im November 2020 entfernt. Weitere Informationen zu den SAP-Cloud-Angeboten von Red Hat finden Sie [hier](https://access.redhat.com/articles/3751271).
RHEL-SAP-APPS | 6,8       | RAW    | Linux-Agent | RHEL 6.8 for SAP Business Applications. Veraltet zugunsten der RHEL-SAP-Images.
|             | 7.3       | LVM    | Linux-Agent | RHEL 7.3 for SAP Business Applications. Veraltet zugunsten der RHEL-SAP-Images.
|             | 7.4       | LVM    | Linux-Agent | RHEL 7.4 for SAP Business Applications
|             | 7.6       | LVM    | Linux-Agent | RHEL 7.6 for SAP Business Applications
|             | 7,7       | LVM    | Linux-Agent | RHEL 7.7 for SAP Business Applications
|             | 77-gen2       | LVM    | Linux-Agent | RHEL 7.7 for SAP Business Applications Image der Generation 2
|             | 8.1       | LVM    | Linux-Agent | RHEL 8.1 für SAP Business Applications.
|             | 81-gen2      | LVM    | Linux-Agent | RHEL 8.1 für SAP Business Applications. Image der Generation 2.
|             | 8,2       | LVM    | Linux-Agent | RHEL 8.2 für SAP Business Applications.
|             | 82-gen2      | LVM    | Linux-Agent | RHEL 8.2 für SAP Business Applications. Image der Generation 2.
RHEL-Hochverfügbarkeit       | 7.4       | LVM    | Linux-Agent | RHEL 7.4 mit Hochverfügbarkeits-Add-On. Premium-Berechnung für HANA und RHEL zusätzlich zur Grundgebühr für Computekapazität. Veraltet zugunsten der RHEL-SAP-HA-Images
|             | 7,5       | LVM    | Linux-Agent | RHEL 7.5 mit Hochverfügbarkeits-Add-On. Premium-Berechnung für HANA und RHEL zusätzlich zur Grundgebühr für Computekapazität. Veraltet zugunsten der RHEL-SAP-HA-Images
|             | 7.6       | LVM    | Linux-Agent | RHEL 7.6 mit Hochverfügbarkeits-Add-On. Premium-Berechnung für HANA und RHEL zusätzlich zur Grundgebühr für Computekapazität. Veraltet zugunsten der RHEL-SAP-HA-Images
RHEL-SAP-HA   | 7.4          | LVM    | Linux-Agent | RHEL 7.4 für SAP mit Hochverfügbarkeit und Update Services. An E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 74sapha-gen2 | LVM    | Linux-Agent | RHEL 7.4 für SAP mit Hochverfügbarkeit und Update Services. Image der Generation 2. An E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 7,5          | LVM    | Linux-Agent | RHEL 7.5 für SAP mit Hochverfügbarkeit und Update Services. An E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 7.6          | LVM    | Linux-Agent | RHEL 7.6 für SAP mit Hochverfügbarkeit und Update Services. An E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 76sapha-gen2 | LVM    | Linux-Agent | RHEL 7.6 für SAP mit Hochverfügbarkeit und Update Services. Image der Generation 2. An E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 7,7          | LVM    | Linux-Agent | RHEL 7.7 für SAP mit Hochverfügbarkeit und Update Services. An E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 77sapha-gen2 | LVM    | Linux-Agent | RHEL 7.7 für SAP mit Hochverfügbarkeit und Update Services. Image der Generation 2. An E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 8.1          | LVM    | Linux-Agent | RHEL 8.1 für SAP mit Hochverfügbarkeit und Update Services. An E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 81sapha-gen2          | LVM    | Linux-Agent | RHEL 8.1 für SAP mit Hochverfügbarkeit und Update Services. Images der Generation 2: an E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 8,2          | LVM    | Linux-Agent | RHEL 8.2 für SAP mit Hochverfügbarkeit und Update Services. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
|             | 82sapha-gen2          | LVM    | Linux-Agent | RHEL 8.2 für SAP mit Hochverfügbarkeit und Update Services. Images der Generation 2: an E4S-Repositorys angefügt. Premium-Berechnung für SAP- und Hochverfügbarkeitsrepositorys sowie RHEL zusätzlich zur Grundgebühr für Computekapazität.
rhel-byos     |rhel-lvm74| LVM    | Linux-Agent | Bei RHEL 7.4 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm75| LVM    | Linux-Agent | Bei RHEL 7.5 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm76| LVM    | Linux-Agent | Bei RHEL 7.6 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm76-gen2| LVM    | Linux-Agent | Bei RHEL 7.6 Generation 2 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm77| LVM    | Linux-Agent | Bei RHEL 7.7 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm77-gen2| LVM    | Linux-Agent | Bei RHEL 7.7 Generation 2 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm78| LVM    | Linux-Agent | Bei RHEL 7.8 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm78-gen2| LVM    | Linux-Agent | Bei RHEL 7.8 Generation 2 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm8 | LVM    | Linux-Agent | Bei RHEL 8.0 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm8-gen2 | LVM    | Linux-Agent | Bei RHEL 8.0 Generation 2 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm81 | LVM    | Linux-Agent | Bei RHEL 8.1 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm81-gen2 | LVM    | Linux-Agent | Bei RHEL 8.1 Generation 2 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm82 | LVM    | Linux-Agent | Bei RHEL 8.2 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.
|             |rhel-lvm82-gen2 | LVM    | Linux-Agent | Bei RHEL 8.2 Generation 2 BYOS-Images, die nicht an eine Quelle für Updates angefügt sind, erfolgt keine Premium-Berechnung für RHEL.

> [!NOTE]
> Das RHEL-SAP-HANA-Produktangebot wird von Red Hat als am Ende des Lebenszyklus angesehen. Vorhandene Bereitstellungen funktionieren weiterhin normal, aber Red Hat empfiehlt Kunden, von den RHEL-SAP-HANA-Images zu den RHEL-SAP-HA-Images zu migrieren, die die SAP HANA-Repositorys und das Hochverfügbarkeits-Add-In beinhalten. Weitere Informationen zu den SAP-Cloud-Angeboten von Red Hat finden Sie [hier](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über die [Red Hat-Images in Azure](./redhat-images.md).
* Erfahren Sie mehr über die [Red Hat-Updateinfrastruktur](./redhat-rhui.md).
* Erfahren Sie mehr über das [RHEL-BYOS-Angebot](./byos.md).
* Informationen zu Red Hat-Supportrichtlinien für alle RHEL-Versionen finden Sie auf der Seite [Red Hat Enterprise Linux Life Cycle (Red Hat Enterprise Linux-Lebenszyklus)](https://access.redhat.com/support/policy/updates/errata).
