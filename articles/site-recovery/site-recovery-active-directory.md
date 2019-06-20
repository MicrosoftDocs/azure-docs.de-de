---
title: Einrichten der Notfallwiederherstellung für Active Directory und DNS mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Implementieren einer Notfallwiederherstellungslösung für Active Directory und DNS mit Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 58e360bb355c7faf9608b00dd65b14f27aca4367
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038716"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Einrichten der Notfallwiederherstellung für Active Directory und DNS

Für Unternehmensanwendungen, z.B. SharePoint, Dynamics AX und SAP, ist eine Active Directory- und DNS-Infrastruktur erforderlich, damit sie richtig funktionieren. Wenn Sie Notfallwiederherstellung für Anwendungen einrichten, müssen Sie häufig Active Directory und DNS vorrangig vor den anderen Anwendungskomponenten wiederherstellen, um eine ordnungsgemäße Anwendungsfunktionalität sicherzustellen.

Sie können [Site Recovery](site-recovery-overview.md) verwenden, um einen Notfallwiederherstellungsplan für Active Directory zu erstellen. Wenn eine Unterbrechung auftritt, können Sie ein Failover einleiten. Sie können Active Directory in wenigen Minuten ausführen. Wenn Sie Active Directory für mehrere Anwendungen am primären Standort bereitgestellt haben, z.B. für SharePoint und SAP, sollten Sie ein Failover des gesamten Standorts durchführen. Sie können zuerst ein Failover von Active Directory mithilfe von Site Recovery ausführen. Führen Sie dann ein Failover der anderen Anwendungen mit anwendungsspezifischen Wiederherstellungsplänen aus.

In diesem Artikel wird erläutert, wie eine Notfallwiederherstellungslösung für Active Directory erstellt wird. Die Voraussetzungen werden beschrieben, und es werden Failoveranweisungen bereitgestellt. Sie sollten mit Active Directory und Site Recovery vertraut sein, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Bei einer Replikation nach Azure: [Bereiten Sie die Azure-Ressourcen vor](tutorial-prepare-azure.md), einschließlich eines Azure-Abonnements, eines virtuellen Azure-Netzwerks, eines Speicherkontos und eines Recovery Services-Tresors.
* Überprüfen Sie die [Supportanforderungen](site-recovery-support-matrix-to-azure.md) für alle Komponenten.

## <a name="replicate-the-domain-controller"></a>Replizieren des Domänencontrollers

- Sie müssen die Site Recovery-Replikation auf mindestens einem virtuellen Computer einrichten, der einen Domänencontroller oder DNS hostet.
- Wenn Ihre Umgebung mehrere Domänencontroller enthält, müssen Sie auch einen zusätzlichen Domänencontroller am Zielstandort einrichten. Der zusätzliche Domänencontroller kann in Azure oder einem sekundären lokalen Datencenter vorhanden sein.
- Wenn Sie nur wenige Anwendungen und einen Domänencontroller haben, sollten Sie ein Failover des gesamten Standorts ausführen. In diesem Fall sollten Sie mithilfe von Site Recovery den Domänencontroller im Zielstandort replizieren (entweder in Azure oder in einem sekundären lokalen Rechenzentrum). Sie können denselben replizierten Domänencontroller bzw. virtuellen DNS-Computer auch für ein [Testfailover](#test-failover-considerations) verwenden.
- - Bei vielen Anwendungen und mehreren Domänencontrollern in der Umgebung, oder wenn Sie das gleichzeitige Failover verschiedener Anwendungen planen, empfehlen wir Ihnen, zusätzlich zum Replizieren des virtuellen Computers, auf dem sich der Domänencontroller befindet, mit Site Recovery auch die Einrichtung eines zusätzlichen Domänencontrollers am Zielstandort (entweder in Azure oder einem sekundären lokalen Rechenzentrum). Für ein [Testfailover](#test-failover-considerations) können Sie den von Site Recovery replizierten Domänencontroller verwenden. Für das Failover können Sie den zusätzlichen Domänencontroller am Zielstandort verwenden.

## <a name="enable-protection-with-site-recovery"></a>Aktivieren des Schutzes mit Site Recovery

Mit Site Recovery können Sie den virtuellen Computer schützen, der den Domänencontroller oder DNS hostet.

### <a name="protect-the-vm"></a>Schützen des virtuellen Computers
Der mithilfe von Site Recovery replizierte Domänencontroller wird für ein [Testfailover](#test-failover-considerations) verwendet. Stellen Sie sicher, dass folgende Anforderungen erfüllt sind:

1. Der Domänencontroller ist ein globaler Katalogserver.
2. Der Domänencontroller sollte der FSMO-Rollenbesitzer für Rollen sein, die während eines Testfailovers erforderlich sind. Andernfalls müssen diese Rollen nach dem Failover [übertragen](https://aka.ms/ad_seize_fsmo) werden.

### <a name="configure-vm-network-settings"></a>Konfigurieren von VM-Netzwerkeinstellungen
Konfigurieren Sie für die VM, die den Domänencontroller bzw. DNS hostet, die Netzwerkeinstellungen in Site Recovery unter den Einstellungen für **Compute und Netzwerk** des replizierten virtuellen Computers. So wird sichergestellt, dass der virtuelle Computer nach einem Failover mit dem richtigen Netzwerk verbunden wird.

## <a name="protect-active-directory"></a>Schützen von Active Directory

### <a name="site-to-site-protection"></a>Standort-zu-Standort-Schutz
Erstellen Sie einen Domänencontroller am sekundären Standort. Geben Sie beim Heraufstufen des Servers auf eine Domänencontrollerrolle den Namen der gleichen Domäne an, die am primären Standort verwendet wird. Sie können das Snap-In **Active Directory-Standorte und Dienste** zum Konfigurieren von Einstellungen für das Standortverknüpfungsobjekt nutzen, dem die Standorte hinzugefügt werden. Durch Konfigurieren von Einstellungen für eine Standortverknüpfung können Sie steuern, wann und wie oft eine Replikation zwischen zwei oder mehr Standorten erfolgt. Weitere Informationen finden Sie unter [Planen der Replikation zwischen Standorten](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Standort-zu-Azure-Schutz
Erstellen Sie zuerst einen Domänencontroller in einem virtuellen Azure-Netzwerk. Geben Sie beim Heraufstufen des Servers auf eine Domänencontrollerrolle den gleichen Domänennamen an, der am primären Standort verwendet wird.

Konfigurieren Sie dann den DNS-Server für das virtuelle Netzwerk neu, um den DNS-Server in Azure zu verwenden.

![Azure-Netzwerk](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure-zu-Azure-Schutz
Erstellen Sie zuerst einen Domänencontroller in einem virtuellen Azure-Netzwerk. Geben Sie beim Heraufstufen des Servers auf eine Domänencontrollerrolle den gleichen Domänennamen an, der am primären Standort verwendet wird.

Konfigurieren Sie dann den DNS-Server für das virtuelle Netzwerk neu, um den DNS-Server in Azure zu verwenden.

## <a name="test-failover-considerations"></a>Überlegungen zum Test-Failover
Um Auswirkungen auf Produktionsworkloads zu vermeiden, erfolgt das Testfailover in einem Netzwerk, das vom Produktionsnetzwerk isoliert ist.

Bei den meisten Anwendungen muss ein Domänencontroller oder ein DNS-Server vorhanden sein. Bevor ein Failover für die Anwendung erfolgt, müssen Sie darum einen Domänencontroller im isolierten Netzwerk erstellen, der für das Testfailover verwendet werden soll. Die einfachste Möglichkeit hierzu besteht darin, mit Site Recovery eine VM zu replizieren, die einen Domänencontroller bzw. ein DNS hostet. Führen Sie dann ein Testfailover des virtuellen Domänencontrollercomputers aus, bevor Sie ein Testfailover des Wiederherstellungsplans für die Anwendung ausführen. Gehen Sie hierzu wie folgt vor:

1. [Replizieren](vmware-azure-tutorial.md) Sie mit Site Recovery den virtuellen Computer, der den Domänencontroller oder das DNS hostet.
2. Erstellen Sie ein isoliertes Netzwerk. Jedes in Azure erstellte virtuelle Netzwerk ist standardmäßig von anderen Netzwerken isoliert. Sie sollten denselben IP-Adressbereich für dieses Netzwerk verwenden, den Sie in Ihrem Produktionsnetzwerk verwenden. Aktivieren Sie nicht die Standort-zu-Standort-Konnektivität in diesem Netzwerk.
3. Geben Sie eine DNS-IP-Adresse im isolierten Netzwerk an. Verwenden Sie die IP-Adresse, die der virtuelle DNS-Computer abrufen soll. Wenn Sie in Azure replizieren, geben Sie die IP-Adresse für den virtuellen Computer an, der bei einem Failover verwendet wird. Um die IP-Adresse einzugeben, wählen Sie im replizierten virtuellen Computer in den Einstellungen für **Compute und Netzwerk** die **Ziel-IP-** Einstellungen.

    ![Azure-Testnetzwerk](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery versucht, virtuelle Testcomputer in einem Subnetz mit demselben Namen und derselben IP-Adresse zu erstellen, die in den Einstellungen des virtuellen Computers unter **Compute und Netzwerk** angegeben sind. Wenn ein Subnetz mit demselben Namen nicht im virtuellen Azure-Netzwerk für das Testfailover verfügbar ist, wird ein virtueller Testcomputer im (in alphabetischer Reihenfolge) ersten Subnetz erstellt.
    >
    > Wenn die Ziel-IP-Adresse Teil des ausgewählten Subnetzes ist, versucht Site Recovery, die Testfailover-VM mit der Ziel-IP-Adresse zu erstellen. Wenn die Ziel-IP-Adresse nicht Teil des ausgewählten Subnetzes ist, wird die Testfailover-VM mit der nächsten verfügbaren IP-Adresse im ausgewählten Subnetz erstellt.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Testfailover an einen sekundären Standort

1. Wenn Sie zu einem anderen lokalen Standort replizieren und DHCP verwenden, [richten Sie DNS und DHCP für das Testfailover ein](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Führen Sie ein Testfailover des virtuellen Computers mit dem Domänencontroller im isolierten Netzwerk aus. Verwenden Sie den neuesten verfügbaren *anwendungskonsistenten* Wiederherstellungspunkt des virtuellen Computers mit dem Domänencontroller, um das Testfailover durchzuführen.
3. Führen Sie ein Testfailover für den Wiederherstellungsplan aus, der virtuelle Computer enthält, auf denen die Anwendung ausgeführt wird.
4. Nach dem Abschluss der Tests *bereinigen Sie das Testfailover* auf dem virtuellen Domänencontrollercomputer. Mit diesem Schritt wird der für das Testfailover erstellte Domänencontroller gelöscht.


### <a name="remove-references-to-other-domain-controllers"></a>Entfernen von Verweisen auf andere Domänencontroller
Wenn Sie ein Testfailover initiieren, beziehen Sie nicht alle Domänencontroller im Testnetzwerk ein. Um Verweise auf andere in der Produktionsumgebung vorhandene Domänencontroller zu entfernen, müssen Sie für fehlende Domänencontroller ggf. [FSMO-Funktionen von Active Directory-Rollen übernehmen](https://aka.ms/ad_seize_fsmo) und [Metadaten bereinigen](https://technet.microsoft.com/library/cc816907.aspx).


### <a name="issues-caused-by-virtualization-safeguards"></a>Probleme aufgrund von Virtualisierungssicherheitsmechanismen

> [!IMPORTANT]
> Einige der in diesem Abschnitt beschriebenen Konfigurationen sind nicht die Standardkonfigurationen für Domänencontroller. Wenn Sie diese Änderungen an einem Domänencontroller der Produktionsumgebung nicht vornehmen möchten, können Sie einen dedizierten Domänencontroller für das Site Recovery-Testfailover erstellen. Nehmen Sie diese Änderungen nur an diesem Domänencontroller vor.  
>
>

Ab Windows Server 2012 [sind zusätzliche Sicherheitsmechanismen in Active Directory Domain Services (AD DS) integriert](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Mit diesen Sicherheitsmechanismen sind virtualisierte Domänencontroller besser vor USN-Rollbacks geschützt, solange die zugrunde liegende Hypervisorplattform **VM-GenerationID** unterstützt. Azure unterstützt **VM-GenerationID**. Deswegen verfügen Domänencontroller, die Windows Server 2012 oder höher auf virtuellen Azure-Computern ausführen, über diese zusätzlichen Sicherheitsmechanismen.


Wenn **VM-GenerationID** zurückgesetzt wird, wird der **InvocationID**-Wert der AD DS-Datenbank auch zurückgesetzt. Darüber hinaus wird der RID-Pool verworfen und der Ordner „sysvol“ als nicht autorisierend gekennzeichnet. Weitere Informationen finden Sie unter [Einführung in die Virtualisierung der Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) und [Sichere Virtualisierung von DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Ein Failover in Azure könnte **VM-GenerationID** zurücksetzen. Das Zurücksetzen von **VM-GenerationID** löst beim Starten der virtuellen Domänencontrollercomputer in Azure zusätzliche Sicherheitsmechanismen aus. Dies kann zu einer *erheblichen Verzögerung* bei der Anmeldung beim virtuellen Domänencontrollercomputer führen.

Da dieser Domänencontroller nur in einem Testfailover verwendet wird, sind keine Virtualisierungsschutzmechanismen erforderlich. Um sicherzustellen, dass der Wert **VM-GenerationID** für den virtuellen Domänencontrollercomputer nicht geändert wird, können Sie auf dem lokalen Domänencontroller den folgenden DWORD-Wert in **4** ändern:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Symptome der Virtualisierungssicherheitsmechanismen

Wenn Virtualisierungssicherheitsmechanismen nach einem Testfailover ausgelöst wurden, können folgende Symptome auftreten:  

* Der **GenerationID**-Wert ändert sich.

    ![Änderung der Generierungs-ID](./media/site-recovery-active-directory/Event2170.png)

* Der **InvocationID**-Wert ändert sich.

    ![Änderung der Aufrufkennung](./media/site-recovery-active-directory/Event1109.png)

* Sysvol-Ordner und NETLOGON-Freigaben sind nicht verfügbar.

    ![Sysvol-Ordnerfreigabe](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs sysvol-Ordner](./media/site-recovery-active-directory/Event13565.png)

* DFSR-Datenbanken werden gelöscht.

    ![DFSR-Datenbanken werden gelöscht.](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Behandlung von Domänencontrollerproblemen während des Testfailovers

> [!IMPORTANT]
> Einige der in diesem Abschnitt beschriebenen Konfigurationen sind nicht die Standardkonfigurationen für Domänencontroller. Wenn Sie diese Änderungen an einem Domänencontroller der Produktionsumgebung nicht vornehmen möchten, können Sie einen dedizierten Domänencontroller für das Site Recovery-Testfailover erstellen. Nehmen Sie diese Änderungen nur an diesem dedizierten Domänencontroller vor.  
>
>

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um zu überprüfen, ob die Ordner „sysvol“ und „NETLOGON“ freigegeben sind:

    `NET SHARE`

2. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um sicherzustellen, dass der Domänencontroller ordnungsgemäß funktioniert:

    `dcdiag /v > dcdiag.txt`

3. Suchen Sie im Ausgabeprotokoll den folgenden Text. Der Text bestätigt, dass der Domänencontroller richtig funktioniert.

    * „passed test Connectivity“
    * „passed test Advertising“
    * „passed test MachineAccount“

Wenn die oben genannten Bedingungen erfüllt sind, ist es wahrscheinlich, dass der Domänencontroller funktioniert. Falls nicht, führen Sie die folgenden Schritte aus:

1. Führen Sie eine autoritative Wiederherstellung des Domänencontrollers durch. Beachten Sie Folgendes:
    * Wir empfehlen zwar nicht die [FRS-Replikation](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), doch führen Sie bei Verwendung der FRS-Replikation die Schritte für eine autorisierende Wiederherstellung aus. Der Prozess wird in [Verwenden des BurFlags-Registrierungsschlüssels zur erneuten Initialisierung des Dateireplikationsdiensts](https://support.microsoft.com/kb/290762) beschrieben.

        Weitere Informationen zu BurFlags finden Sie im Blogbeitrag [D2 and D4: What is it for? (D2 und D4: Wozu dient es?)](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Wenn Sie die DFSR-Replikation verwenden, führen Sie die Schritte für eine autorisierende Wiederherstellung aus. Der Prozess wird in [Eine autorisierende und nicht autorisierende Synchronisierung für den DFSR-replizierten Ordner „sysvol“ erzwingen (wie „D4/D2“ für FRS)](https://support.microsoft.com/kb/2218556) beschrieben.

        Sie können auch die PowerShell-Funktionen verwenden. Weitere Informationen finden Sie unter [DFSR-SYSVOL authoritative/non-authoritative restore PowerShell functions (DFSR-SYSVOL: PowerShell-Funktionen für autorisierende/nicht autorisierende Wiederherstellung)](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Umgehen Sie die angeforderte Erstsynchronisierung, indem Sie den folgenden Registrierungsschlüssel auf dem lokalen Domänencontroller auf **0** festlegen. Wenn dieses DWORD-Element nicht vorhanden ist, können Sie es unter dem Knoten **Parameter** erstellen.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Weitere Informationen finden Sie unter [Problembehandlung bei DNS-Ereignis-ID 4013: Der DNS-Server konnte die in AD integrierten DNS-Zonen nicht laden](https://support.microsoft.com/kb/2001093).

3. Deaktivieren Sie die Anforderung, dass ein globaler Katalogserver zur Überprüfung der Benutzeranmeldung verfügbar sein muss. Legen Sie hierzu auf dem lokalen Domänencontroller für den folgenden Registrierungsschlüssel **1** fest. Wenn dieses DWORD-Element nicht vorhanden ist, können Sie es unter dem Knoten **Lsa** erstellen.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Weitere Informationen finden Sie unter [Deaktivieren der Anforderung, dass ein globaler Katalogserver zur Überprüfung der Benutzeranmeldung verfügbar sein muss](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS und Domänencontroller auf unterschiedlichen Computern

Wenn Sie den Domänencontroller und das DNS auf der gleichen VM ausführen, können Sie dieses Verfahren überspringen.


Wenn sich das DNS nicht auf der gleichen VM wie der Domänencontroller befindet, müssen Sie eine DNS-VM für das Testfailover erstellen. Sie können einen neuen DNS-Server verwenden und alle erforderlichen Zonen erstellen. Wenn Ihre Active Directory-Domäne beispielsweise „contoso.com“ lautet, können Sie eine DNS-Zone mit dem Namen „contoso.com“ erstellen. Die zu Active Directory gehörenden Einträge müssen wie folgt in DNS aktualisiert werden:

1. Stellen Sie sicher, dass diese Einstellungen vorhanden sind, bevor ein anderer virtueller Computer im Wiederherstellungsplan startet:
   * Die Zone muss nach dem Stammnamen der Gesamtstruktur benannt werden.
   * Der Zone muss eine Datei zugrunde liegen.
   * Die Zone muss für sichere und nicht sichere Updates aktiviert sein.
   * Die Auflösung des virtuellen Computers, der den Domänencontroller hostet, muss auf die IP-Adresse des virtuellen DNS-Computers zeigen.

2. Führen Sie auf dem virtuellen Computer, der den Domänencontroller hostet, folgenden Befehl aus:

    `nltest /dsregdns`

3. Führen Sie die folgenden Befehle aus, um dem DNS-Server eine Zone hinzufügen, nicht sichere Updates zuzulassen und einen Eintrag für die Zone im DNS hinzuzufügen:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Schutz von Unternehmensworkloads mit Azure Site Recovery](site-recovery-workload.md).
