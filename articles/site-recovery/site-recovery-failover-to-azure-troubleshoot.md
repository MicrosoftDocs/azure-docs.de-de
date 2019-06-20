---
title: Beheben von Problemen bei einem Failover nach Azure | Microsoft-Dokumentation
description: Dieser Artikel beschreibt Möglichkeiten zum Beheben allgemeiner Probleme beim Failover nach Azure.
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/04/2019
ms.author: mayg
ms.openlocfilehash: 2156ee6cf27ecfa32b19ad5bbef7549e99c3f7ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61280625"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Beheben von Fehlern beim Ausführen eines Failovers einer VMware-VM oder eines physischen Computers nach Azure

Bei einem Failover eines virtuellen Computers nach Azure tritt eventuell einer der folgenden Fehler auf. Gehen Sie für die Problembehandlung gemäß den für den jeweiligen Fehlerzustand beschriebenen Schritten vor.

## <a name="failover-failed-with-error-id-28031"></a>Fehler beim Failover mit der Fehler-ID 28031

Site Recovery konnte einen virtuellen Computer, für den ein Failover ausgeführt wurde, nicht in Azure erstellen. Dies kann aus einem der folgenden Gründe auftreten:

* Das verfügbare Kontingent reicht nicht zum Erstellen des virtuellen Computers: Sie können das verfügbare Kontingent unter „Abonnement“ > „Nutzung + Kontingente“ prüfen. Sie können eine [neue Supportanfrage](https://aka.ms/getazuresupport) eröffnen, um das Kontingent zu erhöhen.

* Sie versuchen, ein Failover für virtuelle Computer unterschiedlicher Größensätze in derselben Verfügbarkeitsgruppe durchzuführen. Verwenden Sie unbedingt dieselben Größen Familien für alle virtuellen Computer in derselben Verfügbarkeitsgruppe. Sie können die Größe ändern, indem Sie zu den Einstellungen für Compute und Netzwerk des virtuellen Computers navigieren. Wiederholen Sie das Failover anschließend.

* Eine Richtlinie für das Abonnement verhindert die Erstellung eines virtuellen Computers. Ändern Sie die Richtlinie, um das Erstellen eines virtuellen Computers zuzulassen, und wiederholen dann das Failover.

## <a name="failover-failed-with-error-id-28092"></a>Fehler beim Failover mit der Fehler-ID 28092

Site Recovery konnte für den virtuellen Computer, für den ein Failover ausgeführt wurde, keine Netzwerkschnittstelle erstellen. Stellen Sie sicher, dass Sie in Ihrem Abonnement über ein ausreichendes Kontingent zum Erstellen von Netzwerkschnittstellen verfügen. Sie können das verfügbare Kontingent unter „Abonnement“ > „Nutzung + Kontingente“ prüfen. Sie können eine [neue Supportanfrage](https://aka.ms/getazuresupport) eröffnen, um das Kontingent zu erhöhen. Wenn Sie über ein ausreichendes Kontingent verfügen, ist dies möglicherweise nur ein vorübergehendes Problem. Wiederholen Sie den Vorgang. Wenn das Problem auch nach Wiederholungen weiter besteht, hinterlassen Sie am Ende dieses Dokuments einen Kommentar.  

## <a name="failover-failed-with-error-id-70038"></a>Fehler beim Failover mit der Fehler-ID 70038

Site Recovery konnte einen klassischen virtuellen Computer, für den ein Failover ausgeführt wurde, nicht in Azure erstellen. Dies kann mehrere Ursachen haben:

* Eine der Ressourcen – z.B. ein virtuelles Netzwerk, das für die Erstellung des virtuellen Computers erforderlich ist – ist nicht vorhanden. Erstellen Sie das virtuelle Netzwerk wie in den Einstellungen für Compute und Netzwerk des virtuellen Computers angegeben, oder ändern Sie die Einstellung in ein virtuelles Netzwerk, das bereits vorhanden ist. Wiederholen Sie dann das Failover.

## <a name="failover-failed-with-error-id-170010"></a>Fehler beim Failover mit der Fehler-ID 170010

Site Recovery konnte einen virtuellen Computer, für den ein Failover ausgeführt wurde, nicht in Azure erstellen. Dies kann passieren, wenn bei einer internen Hydration für den lokalen virtuellen Computer ein Fehler auftritt.

Um einen Computer in Azure aufzurufen, erfordert die Azure-Umgebung, dass sich einige der Treiber im Boot-Startzustand befinden und Dienste wie DHCP im Autostartzustand sind. Durch die Hydration zum Zeitpunkt des Failover wird der Startuptyp von **atapi-, intelide-, storflt-, vmbus- und storvsc-Treibern** in Bootstart umgewandelt. Außerdem wird der Starttyp einiger Dienste wie DHCP in Autostart konvertiert. Aufgrund umgebungsspezifischer Probleme können bei dieser Aktivität Fehler auftreten. 

Um den Starttyp der Treiber für das **Windows-Gastbetriebssystem** manuell zu ändern, führen Sie die folgenden Schritte aus:

1. Laden Sie das „no-hydration“- Skript [herunter](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1), und führen Sie es folgendermaßen aus: Dieses Skript überprüft, ob für die VM eine Hydration erforderlich ist.

    `.\Script-no-hydration.ps1`

    Es wird folgendes Ergebnis ausgegeben, wenn eine Hydration erforderlich ist:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    Wenn für die VM keine Hydration erforderlich ist, gibt das Skript folgendes Ergebnis aus: „Für dieses System ist keine Hydration erforderlich“. In diesem Fall befinden sich alle Treiber und Dienste im von Azure geforderten Zustand und eine Hydration auf der VM ist nicht erforderlich.

2. Führen Sie das „no-hydration-set“-Skript wie folgt aus, wenn die VM die Anforderung nicht erfüllt und eine Hydration erforderlich ist.

    `.\Script-no-hydration.ps1 -set`
    
    Damit wird der Startuptyp der Treiber und konvertiert und das folgende Ergebnis ausgegeben:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Fehler beim Verbindungsaufbau über RDP/SSH mit dem virtuellen Computer (VM), für den ein Failover ausgeführt wurde, aufgrund ausgegrauter Schaltfläche „Verbinden“ auf der VM

Wenn die Schaltfläche **Verbinden** auf dem fehlerhaften virtuellen Computer abgeblendet ist und Sie nicht über ExpressRoute oder Site-to-Site-VPN mit Azure verbunden sind, gehen Sie wie folgt vor:

1. Navigieren Sie zu **VM** > **Netzwerk**, und klicken Sie auf den Namen der jeweiligen Netzwerkschnittstelle.  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navigieren Sie zu **IP-Konfigurationen**, und klicken Sie dann auf das Namensfeld der gewünschten IP-Konfiguration. ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Um die öffentliche IP-Adresse zu aktivieren, klicken Sie auf **Aktivieren**. ![Aktivieren der IP-Adresse](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klicken Sie auf **Erforderliche Einstellungen konfigurieren** > **Neue erstellen**. ![Neue erstellen](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Geben Sie den Namen der öffentliche Adresse ein, wählen Sie die Standardoptionen für **SKU** und **Zuweisung**, und klicken Sie dann auf **OK**.
6. Um die Änderungen nun zu speichern, klicken Sie auf **Speichern**.
7. Schließen Sie die Bereiche, und navigieren Sie zum Abschnitt **Übersicht** des virtuellen Computers, mit dem per RDP eine Verbindung hergestellt werden soll.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Verbindung nicht möglich/RDP/SSH – Schaltfläche zum Verbinden des virtuellen Computers verfügbar

Wenn die Schaltfläche **Verbinden** auf dem fehlerhaften virtuellen Computer in Azure verfügbar ist (nicht abgeblendet), überprüfen Sie **Startdiagnose** auf Ihrem virtuellen Computer, und suchen Sie nach den in [diesem Artikel](../virtual-machines/windows/boot-diagnostics.md) aufgeführten Fehlern.

1. Falls der virtuelle Computer nicht gestartet wurde, führen Sie ein Failover auf einen früheren Wiederherstellungspunkt aus.
2. Wird die Anwendung auf dem virtuellen Computer nicht gestartet, führen Sie ein Failover auf einen anwendungskonsistenten Wiederherstellungspunkt aus.
3. Ist der virtuelle Computer Mitglied einer Domäne, stellen Sie sicher, dass der Domänencontroller ordnungsgemäß funktioniert. Dies kann durch Ausführen der unten angegebenen Schritte geschehen:

    a. Erstellen Sie einen neuen virtuellen Computer im selben Netzwerk.

    b.  Stellen Sie sicher, dass dieser derselben Domäne beitreten kann, in der der fehlerhafte virtuelle Computer erwartet wird.

    c. Funktioniert der Domänencontroller **nicht** ordnungsgemäß, melden Sie sich bei dem virtuellen Computer, für den ein Failover ausgeführt wurde, mit einem lokalen Administratorkonto an.
4. Vergewissern Sie sich bei Verwendung eines benutzerdefinierten DNS-Servers, dass dieser erreichbar ist. Dies kann durch Ausführen der unten angegebenen Schritte geschehen:

    a. Erstellen Sie einen neuen virtuellen Computer im selben Netzwerk.

    b. Überprüfen Sie, ob der virtuelle Computer Namen mit dem benutzerdefinierten DNS-Server auflösen kann.

>[!Note]
>Der Azure-VM-Agent muss vor dem Failover auf dem virtuellen Computer installiert werden, wenn Sie andere Einstellungen als die Startdiagnose aktivieren möchten.

## <a name="unexpected-shutdown-message-event-id-6008"></a>Nachricht über unerwartetes Herunterfahren (Ereignis-ID 6008)

Wenn beim Starten eines virtuellen Windows-Computers nach dem Failover auf dem wiederhergestellten virtuellen Computer eine Nachricht über ein unerwartetes Herunterfahren ausgegeben wird, bedeutet dies, dass ein Status des Herunterfahrens des virtuellen Computers nicht in dem für das Failover verwendeten Wiederherstellungspunkt erfasst wurde. Dies geschieht, wenn Sie eine Wiederherstellung zu einem Zeitpunkt durchführen, zu dem der virtuelle Computer nicht vollständig heruntergefahren war.

Normalerweise ist das kein Anlass zur Sorge und kann in der Regel für ungeplante Failover ignoriert werden. Stellen Sie im Fall eines geplanten Failovers sicher, dass der virtuelle Computer vor dem Failover ordnungsgemäß heruntergefahren wird, und lassen Sie genügend Zeit, damit ausstehende lokale Daten zur Replikation an Azure gesendet werden können. Verwenden Sie dann die Option **Neueste** auf dem Bildschirm [Failover](site-recovery-failover.md#run-a-failover), damit alle ausstehenden Daten in Azure in einem Wiederherstellungspunkt verarbeitet werden, der dann für VM-Failover verwendet wird.

## <a name="unable-to-select-the-datastore"></a>Datenspeicher kann nicht ausgewählt werden

Dieses Problem wird angezeigt, falls der Datenspeicher im Azure-Portal nicht angezeigt werden kann, wenn Sie versuchen, den virtuellen Computer, für den ein Failover ausgeführt wurde, erneut zu schützen. Der Grund: Das Masterziel wird unter vCenter-Instanzen, die Azure Site Recovery hinzugefügt wurden, nicht als virtueller Computer erkannt.

Weitere Informationen zum erneuten Schützen eines virtuellen Computers finden Sie unter [Erneutes Schützen und Ausführen eines Failbacks für Computer auf einen lokalen Standort nach einem Failover auf Azure](vmware-azure-reprotect.md).

So lösen Sie das Problem:

Erstellen Sie das Masterziel manuell in der vCenter-Instanz, die Ihren Quellcomputer verwaltet. Der Datenspeicher ist nach der nächsten vCenter-Ermittlung und Fabric-Aktualisierung verfügbar.

> [!Note]
> 
> Ermittlung und Fabric-Aktualisierung können bis zu 30 Minuten dauern. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>SSL-Fehler 35 bei der Linux-Masterzielregistrierung beim Konfigurationsserver 

Die Azure Site Recovery-Masterzielregistrierung beim Konfigurationsserver ist nicht erfolgreich, da auf dem Masterziel der authentifizierte Proxy aktiviert ist. 
 
Dieser Fehler wird durch folgende Zeichenfolgen im Installationsprotokoll angegeben: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

So lösen Sie das Problem:
 
1. Öffnen Sie auf dem virtuellen Konfigurationsservercomputer eine Eingabeaufforderung, und überprüfen Sie die Proxyeinstellungen mithilfe folgender Befehle:

    cat /etc/environment  echo $http_proxy  echo $https_proxy 

2. Sollte in der Ausgabe der vorherigen Befehle angegeben sein, dass die Einstellung „http_proxy“ oder „https_proxy“ definiert ist, verwenden Sie eine der folgenden Methoden, um die Blockierung der Masterzielkommunikation mit dem Konfigurationsserver zu beseitigen:
   
   - Laden Sie das [PsExec-Tool](https://aka.ms/PsExec) herunter.
   - Verwenden Sie das Tool, um auf den Systembenutzerkontext zuzugreifen und zu ermitteln, ob die Proxyadresse konfiguriert ist. 
   - Ist der Proxy konfiguriert, öffnen Sie Internet Explorer mithilfe des PsExec-Tools in einem Systembenutzerkontext.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - So stellen Sie sicher, dass der Masterzielserver mit dem Konfigurationsserver kommunizieren kann:
  
     - Ändern Sie die Proxyeinstellungen in Internet Explorer, um die IP-Adresse des Masterzielservers über den Proxy zu umgehen.   
     oder
     - Deaktivieren Sie den Proxy auf dem Masterzielserver. 


## <a name="next-steps"></a>Nächste Schritte
- Beheben von Fehlern bei der [RDP-Verbindung mit einem virtuellen Windows-Computer](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Beheben von Fehlern bei der [SSH-Verbindung mit einem virtuellen Linux-Computer](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Wenn Sie weitere Hilfe benötigen, veröffentlichen Sie Ihre Abfrage im [Site Recovery-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr), oder hinterlassen einen Kommentar am Ende dieses Dokuments. Wir verfügen über eine aktive Community, die Sie unterstützen kann.
