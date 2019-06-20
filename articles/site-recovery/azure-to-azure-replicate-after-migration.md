---
title: Einrichten der Notfallwiederherstellung für Azure-VMs nach Migration in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Vorbereiten von VMs für die Einrichtung der Notfallwiederherstellung zwischen Azure-Regionen nach der Migration in Azure mithilfe von Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4b764c8e7c3d97ff521add05033265f705c4136f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399523"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Einrichten der Notfallwiederherstellung für Azure-VMs nach der Migration in Azure 


Lesen Sie diesen Artikel, wenn Sie mithilfe des [Site Recovery](site-recovery-overview.md)-Diensts [lokale Computer zu virtuellen Azure-Computern migriert](tutorial-migrate-on-premises-to-azure.md) haben und jetzt die virtuellen Computer für die Notfallwiederherstellung in einer sekundären Azure-Region einrichten möchten. In diesem Artikel erfahren Sie, wie Sie sicherstellen, dass der Azure-VM-Agent auf den migrierten virtuellen Computern installiert ist, und wie Sie den Site Recovery Mobility-Dienst entfernen, der nach der Migration nicht mehr erforderlich ist.



## <a name="verify-migration"></a>Überprüfen der Migration

Bevor Sie die Notfallwiederherstellung einrichten, stellen Sie sicher, dass die Migration wie erwartet abgeschlossen wurde. Um eine Migration erfolgreich abzuschließen, sollten Sie nach dem Failover die Option **Komplette Migration** für jeden Computer auswählen, den Sie migrieren möchten. 

## <a name="verify-the-azure-vm-agent"></a>Überprüfen des Azure-VM-Agents

Auf jedem virtuellen Azure-Computer muss der [Azure-VM-Agent](../virtual-machines/extensions/agent-windows.md) installiert sein. Zum Replizieren von virtuellen Azure-Computern installiert Azure Site Recovery eine Erweiterung auf dem Agent.

- Wird auf dem Computer Version 9.7.0.0 oder höher des Site Recovery Mobility-Diensts ausgeführt, wird der Azure-VM-Agent vom Mobility-Dienst automatisch auf den virtuellen Windows-Computern installiert. Bei früheren Versionen des Mobility-Diensts müssen Sie den Agent manuell installieren.
- Bei virtuellen Linux-Computern müssen Sie den Azure-VM-Agent manuell installieren. Dies ist jedoch nur dann erforderlich, wenn der auf dem migrierten Computer installierte Mobility-Dienst die Version 9.6 oder niedriger hat.


### <a name="install-the-agent-on-windows-vms"></a>Installieren des Agents auf virtuellen Windows-Computern

Wenn Sie eine Version des Site Recovery Mobility-Diensts vor 9.7.0.0 ausführen oder Sie aus anderen Gründen den Agent manuell installieren müssen, führen Sie die folgenden Schritte aus:  

1. Stellen Sie sicher, dass Sie auf dem virtuellen Computer über Administratorberechtigungen verfügen.
2. Laden Sie das [Installationsprogramm für den Azure-VM-Agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter.
3. Führen Sie die Datei mit dem Installationsprogramm aus.

#### <a name="validate-the-installation"></a>Überprüfen der Installation
So überprüfen Sie, ob der Agent installiert ist

1. In der Azure-VM sollte im Ordner „C:\WindowsAzure\Packages“ die Datei „WaAppAgent.exe“ vorhanden sein.
2. Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie in **Eigenschaften** die Registerkarte **Details** aus.
3. Im Feld **Produktversion** sollte 2.6.1198.718 oder eine höhere Version angegeben sein.

[Erfahren Sie mehr](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) über die Agent-Installation unter Windows.

### <a name="install-the-agent-on-linux-vms"></a>Installieren des Agents auf virtuellen Linux-Computern

Installieren Sie den [Azure-Linux-VM](../virtual-machines/extensions/agent-linux.md)-Agent manuell wie folgt:

1. Stellen Sie sicher, dass Sie auf dem virtuellen Computer über Administratorberechtigungen verfügen.
2. Es wird dringend empfohlen, den Linux-VM-Agent mit einem RPM- oder DEB-Paket aus dem Paketrepository der Distribution zu installieren. Das Azure Linux-Agent-Paket wird von allen [unterstützten Distributionsanbietern](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) in ihre jeweiligen Images und Repositorys integriert.
    - Es wird dringend empfohlen, den Agent nur über ein Repository der Distribution zu aktualisieren.
    - Wir raten davon ab, den Linux-VM-Agent direkt von GitHub zu installieren und dann zu aktualisieren.
    -  Falls der aktuelle Agent für Ihre Distribution nicht verfügbar ist, können Sie sich an den zuständigen Support wenden, um Informationen zur Installation zu erhalten. 

#### <a name="validate-the-installation"></a>Überprüfen der Installation 

1. Führen Sie den Befehl **ps -e** aus, um sicherzustellen, dass der Azure-Agent auf dem virtuellen Linux-Computer ausgeführt wird.
2. Wenn der Prozess nicht ausgeführt wird, starten Sie ihn mit den folgenden Befehlen neu:
    - Für Ubuntu: **service walinuxagent start**
    - Für andere Distributionen: **service waagent start**


## <a name="uninstall-the-mobility-service"></a>Deinstallieren von Mobility Service

1. Deinstallieren Sie den Mobility-Dienst manuell auf dem virtuellen Azure-Computer mit einer der folgenden Methoden. 
    - Unter Windows deinstallieren Sie in „Systemsteuerung“ > **Software**  den Eintrag **Microsoft Azure Site Recovery Mobility Service/Masterzielserver**. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten Folgendes aus:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Melden Sie sich unter Linux als Root-Benutzer an. Wechseln Sie in einem Terminal zu **/user/local/ASR**, und führen Sie den folgenden Befehl aus:
        ```
        ./uninstall.sh -Y
        ```
2. Bevor Sie die Replikation konfigurieren, starten Sie die VM neu.

## <a name="next-steps"></a>Nächste Schritte

[Behandeln von Problemen](site-recovery-extension-troubleshoot.md) mit der Site Recovery-Erweiterung auf dem Azure-VM-Agent.
[Schnelles Replizieren](azure-to-azure-quickstart.md) einer Azure-VM in eine sekundäre Region
