---
title: Problembehandlung beim Failback auf lokale Umgebungen während der Notfallwiederherstellung von VMware-VMs in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel beschreibt Möglichkeiten zum Beheben von Fehlern beim Failback und erneuten Schützen während der Notfallwiederherstellung von VMware-VMs in Azure mit Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 20cb7a446befb1d31f0e069d91d0230fc4a2a901
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565598"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Beheben von Fehlern beim Failback aus Azure auf lokale Umgebungen

Dieser Artikel beschreibt, wie Sie Probleme behandeln, die auftreten können, wenn Sie für virtuelle Azure-Computer ein Failback auf Ihre lokale VMware-Infrastruktur nach einem Failover in Azure mit [Azure Site Recovery](site-recovery-overview.md) durchgeführt haben.

Ein Failback umfasst im Wesentlichen zwei Schritte. Als ersten Schritt nach dem Failover müssen Sie virtuelle Azure-Computer lokal erneut schützen, damit sie mit der Replikation beginnen. Der zweite Schritt besteht darin, ein Failover aus Azure durchzuführen, um ein Failback auf Ihren lokalen Standort zu erreichen.

## <a name="troubleshoot-reprotection-errors"></a>Problembehandlung von Fehlern beim erneuten Schützen

In diesem Abschnitt werden häufig auftretende Fehler beim erneuten Schützen und deren Behandlung beschrieben.

### <a name="error-code-95226"></a>Fehlercode 95226

**Fehler beim erneuten Anwenden des Schutzes, weil der virtuelle Azure-Computer den lokalen Konfigurationsserver nicht erreichen konnte.**

Dieser Fehler tritt unter den folgenden Umständen auf:

* Der virtuelle Azure-Computer kann den lokalen Konfigurationsserver nicht erreichen. Der virtuelle Computer kann nicht ermittelt und beim Konfigurationsserver registriert werden.
* Der InMage Scout-Anwendungsdienst wird nach dem Failover nicht auf dem virtuellen Azure-Computer ausgeführt. Der Dienst ist für die Kommunikation mit dem lokalen Konfigurationsserver erforderlich.

So lösen Sie dieses Problem:

* Überprüfen Sie, ob das Azure-VM-Netzwerk es dem virtuellen Azure-Computer ermöglicht, mit dem lokalen Konfigurationsserver zu kommunizieren. Sie können ein Site-to-Site-VPN mit Ihrem lokalen Datencenter einrichten oder eine Azure ExpressRoute-Verbindung mit privatem Peering für das virtuelle Netzwerk des virtuellen Azure-Computers konfigurieren.
* Wenn die VM mit dem lokalen Konfigurationsserver kommunizieren kann, können Sie sich an der VM anmelden. Überprüfen Sie anschließend den InMage Scout-Anwendungsdienst. Starten Sie den Dienst manuell, wenn Sie sehen, dass er nicht ausgeführt wird. Stellen Sie sicher, dass der Starttyp für den Dienst auf **Automatisch** festgelegt ist.

### <a name="error-code-78052"></a>Fehlercode 78052

**Der Schutz konnte für den virtuellen Computer nicht abgeschlossen werden.**

Dieses Problem kann auftreten, wenn sich bereits ein virtueller Computer mit dem gleichen Namen auf dem Masterzielserver befindet, auf den das Failback stattfindet.

So lösen Sie dieses Problem:

* Wählen Sie einen anderen Masterzielserver auf einem anderen Host aus, sodass der Computer beim erneuten Schützen auf einem anderen Host erstellt und der Namenskonflikt somit vermieden wird.
* Sie können auch vMotion verwenden, um das Masterziel auf einen anderen Host zu verschieben, für den es nicht zu einem Namenskonflikt kommt. Wenn es sich bei dem vorhandenen virtuellen Computer um einen vereinzelten Computer handelt, sollten Sie ihn umbenennen, damit der neue virtuelle Computer auf demselben ESXi-Host erstellt werden kann.


### <a name="error-code-78093"></a>Fehlercode 78093

**Die VM wird nicht ausgeführt, reagiert nicht oder ist nicht erreichbar.**

So lösen Sie dieses Problem:

Zum erneuten Schützen einer VM nach einem Failover muss die Azure-VM ausgeführt werden, damit der Mobilitätsdienst beim Konfigurationsserver lokal registriert wird und mit der Replikation beginnen kann, indem er mit dem Prozessserver kommuniziert. Wenn sich der Computer in einem falschen Netzwerk befindet oder nicht ausgeführt wird (also nicht reagiert oder heruntergefahren ist), kann der Konfigurationsserver Mobility Service auf dem virtuellen Computer nicht erreichen und den Schutz nicht erneut aktivieren.

* Starten Sie den virtuellen Computer neu, damit er wieder mit der lokalen Umgebung kommunizieren kann.
* Starten Sie den Auftrag für erneutes Schützen nach dem Neustart des virtuellen Azure-Computers neu.

### <a name="error-code-8061"></a>Fehlercode 8061

**Der Datenspeicher ist über den ESXi-Host nicht zugänglich.**

Informationen zum Failback finden Sie unter [Reprotect machines from Azure to an on-premises site](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) (Erneutes Schützen von Computern aus Azure an einem lokalen Standort).


## <a name="troubleshoot-failback-errors"></a>Problembehandlung von Failbackfehlern

In diesem Abschnitt werden häufige Fehler beschrieben, die beim Failback auftreten können.

### <a name="error-code-8038"></a>Fehlercode 8038

**Fehler beim Aktivieren des lokalen virtuellen Computers aufgrund des Fehlers.**

Dieses Problem tritt auf, wenn der lokale virtuelle Computer auf einem Host aktiviert wird, auf dem nicht genügend Arbeitsspeicher verfügbar ist. 

So lösen Sie dieses Problem:

* Stellen Sie mehr Speicher auf dem ESXi-Host bereit.
* Außerdem können Sie den virtuellen Computer mithilfe von vMotion auf einen anderen ESXi-Host verschieben, auf dem ausreichend Speicher zum Starten des virtuellen Computers zur Verfügung steht.
