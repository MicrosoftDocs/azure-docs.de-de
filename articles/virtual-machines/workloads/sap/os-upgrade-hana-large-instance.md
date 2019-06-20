---
title: Durchführen eines Betriebssystemupgrades für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Durchführen von Betriebssystemupgrades für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7d451f3831309b4755170915b35a23da8910510
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60935635"
---
# <a name="operating-system-upgrade"></a>Betriebssystemupgrade
In diesem Dokument werden die Details zu Betriebssystemupgrades für SAP HANA (große Instanzen) beschrieben.

>[!NOTE]
>Das Betriebssystemupgrade ist Zuständigkeit des Kunden. Der Microsoft Operations-Support kann Sie dabei auf wichtige Punkte hinweisen, die beim Upgrade zu beachten sind. Bevor Sie Planungen für ein Upgrade treffen, sollten Sie auch den Hersteller Ihres Betriebssystems zurate ziehen.

Das Microsoft Operations-Team installiert das Betriebssystem bei der Bereitstellung der HLI-Einheiten. Im Lauf der Zeit müssen Sie Wartungsvorgänge für das Betriebssystem für die HLI-Einheit vornehmen (z.B. Patches erstellen, optimieren, Upgrades durchführen).

Bevor Sie bedeutende Änderungen am Betriebssystem vornehmen (z.B. ein Upgrade von SP1 auf SP2), müssen Sie sich zunächst an das Microsoft Operations-Team wenden, indem Sie ein Supportticket öffnen.

Geben Sie in Ihrem Ticket Folgendes an:

* Ihre HLI-Abonnement-ID
* Ihren Servernamen
* Die Patchebene, die Sie anwenden möchten
* Das Datum, an dem Sie diese Änderung planen 

Es empfiehlt sich, dieses Ticket mindestens eine Woche vor dem gewünschten Datum des Upgrades zu öffnen, damit das Operations-Team überprüfen kann, ob ein Firmwareupgrade auf Ihrer Serverblade erforderlich ist.


Die Supportmatrix der anderen SAP HANA-Versionen mit den verschiedenen Linux-Versionen finden Sie im [SAP-Hinweis 2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Bekannte Probleme

Im Folgenden werden einige bekannte Probleme bei Upgrades erläutert:
- Bei SKUs von SKU-Typ II wird Software Foundation Software (SFS) nach der Durchführung eines Betriebssystemupgrades entfernt. Nach dem Betriebssystemupgrade müssen Sie die kompatible SFS-Version neu installieren.
- Für Ethernet-Kartentreiber (ENIC und FNIC) wird ein Rollback auf die ältere Version ausgeführt. Nach dem Upgrade müssen Sie die kompatible Version der Treiber neu installieren.

## <a name="next-steps"></a>Nächste Schritte
- Informationen zur SKU von Typ I für Betriebssystemsicherungen finden Sie unter [Sicherung und Wiederherstellung](hana-overview-high-availability-disaster-recovery.md).
- Informationen zur SKU von Typ II finden Sie unter [Betriebssystemsicherung für SKUs vom Typ II](os-backup-type-ii-skus.md).
