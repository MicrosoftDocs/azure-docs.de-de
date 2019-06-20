---
title: Azure Site Recovery-Problembehandlung für Azure-zu-Azure-Replikationsprobleme und -fehler| Microsoft-Dokumentation
description: Problembehandlungsfehler und -probleme beim Replizieren von virtuellen Azure-Computern für die Notfallwiederherstellung
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sujayt
ms.openlocfilehash: 3c87e159022b6dcf13daf2a2659c88c0529a8f48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65796422"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Problembehandlung für Azure-zu-Azure-VM-Replikationsprobleme

In diesem Artikel werden die Probleme beschrieben, die in Azure Site Recovery beim Replizieren und Wiederherstellen von virtuellen Azure-Computern aus einer Region in einer anderen Region häufiger auftreten können, und es wird erklärt, wie die Problembehandlung durchgeführt werden kann. Weitere Informationen zu unterstützten Konfigurationen finden Sie unter [Azure Site Recovery support matrix for replicating from Azure to Azure](site-recovery-support-matrix-azure-to-azure.md) (Azure Site Recovery-Supportmatrix zum Replizieren aus Azure in Azure).

## <a name="list-of-errors"></a>Liste der Fehler
- **[Probleme mit dem Azure-Ressourcenkontingent (Fehlercode 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Vertrauenswürdige Stammzertifikate (Fehlercode: 151066)](#trusted-root-certificates-error-code-151066)**
- **[Ausgehende Konnektivität für Site Recovery (Fehlercode 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Probleme mit dem Azure-Ressourcenkontingent (Fehlercode 150097)
Für Ihr Abonnement sollte es möglich sein, Azure-VMs in der Zielregion zu erstellen, die Sie als Region für die Notfallwiederherstellung nutzen möchten. Außerdem sollte für Ihr Abonnement ein ausreichendes Kontingent für die Erstellung von VMs einer bestimmten Größe vorhanden sein. Site Recovery wählt für die Ziel-VM und die Quell-VM standardmäßig die gleiche Größe. Falls keine übereinstimmende Größe verfügbar ist, wird automatisch die nächstgelegene Größe gewählt. Wenn keine übereinstimmende Größe vorhanden ist, für die die Konfiguration der Quell-VM unterstützt wird, wird diese Fehlermeldung angezeigt:

**Fehlercode** | **Mögliche Ursachen** | **Empfehlung**
--- | --- | ---
150097<br></br>**Meldung**: Die Replikation konnte für den virtuellen Computer VmName nicht aktiviert werden. | - Für Ihre Abonnement-ID ist die Erstellung von VMs am Standort der Zielregion ggf. nicht aktiviert.</br></br>- Für Ihre Abonnement-ID ist die Erstellung von bestimmten VM-Größen am Standort der Zielregion ggf. nicht aktiviert, oder es ist kein ausreichendes Kontingent vorhanden.</br></br>- Für die Abonnement-ID ist am Standort der Zielregion keine geeignete Größe der Ziel-VM vorhanden, die mit der NIC-Anzahl (2) der Quell-VM übereinstimmt.| Wenden Sie sich unter [Anforderungen zur Erhöhung von Resource Manager-Kernkontingenten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) an den Azure-Abrechnungssupport, um die VM-Erstellung für die erforderlichen VM-Größen am Zielstandort für Ihr Abonnement aktivieren zu lassen. Wiederholen Sie den Vorgang, nachdem die Aktivierung durchgeführt wurde.

### <a name="fix-the-problem"></a>Beheben des Problems
Sie können sich unter [Anforderungen zur Erhöhung von Resource Manager-Kernkontingenten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) an den Azure-Abrechnungssupport wenden, um Ihr Abonnement für die Erstellung von VMs der erforderlichen Größen am Zielstandort aktivieren zu lassen.

Falls für den Zielstandort eine Kapazitätsbeschränkung besteht, sollten Sie die Replikation deaktivieren und für einen anderen Standort aktivieren, an dem für Ihr Abonnement ein ausreichendes Kontingent zum Erstellen von VMs der erforderlichen Größen zur Verfügung steht.

## <a name="trusted-root-certificates-error-code-151066"></a>Vertrauenswürdige Stammzertifikate (Fehlercode: 151066)

Wenn auf der VM nicht alle aktuellen vertrauenswürdigen Stammzertifikate vorhanden sind, ist der Auftrag „Replikation aktivieren“ ggf. nicht erfolgreich. Ohne die Zertifikate tritt für die Authentifizierung und Autorisierung von Site Recovery-Dienstaufrufen über die VM ein Fehler auf. Die Fehlermeldung für den fehlgeschlagenen Site Recovery-Auftrag „Replikation aktivieren“ wird angezeigt:

**Fehlercode** | **Mögliche Ursache** | **Empfehlungen**
--- | --- | ---
151066<br></br>**Meldung**: Fehler bei der Site Recovery-Konfiguration. | Die erforderlichen vertrauenswürdigen Stammzertifikate, die für die Autorisierung und Authentifizierung verwendet werden, sind auf dem Computer nicht vorhanden. | - Für eine VM, auf der das Windows-Betriebssystem ausgeführt wird, sollten Sie sicherstellen, dass die vertrauenswürdigen Stammzertifikate auf dem Computer vorhanden sind. Informationen finden Sie unter [Konfigurieren vertrauenswürdiger Stämme und unzulässiger Zertifikate](https://technet.microsoft.com/library/dn265983.aspx).<br></br>- Befolgen Sie für eine VM, auf der das Linux-Betriebssystem ausgeführt wird, die Anleitung für vertrauenswürdige Stammzertifikate. Diese Anleitung wird vom Distributor der Linux-Betriebssystemversion veröffentlicht.

### <a name="fix-the-problem"></a>Beheben des Problems
**Windows**

Installieren Sie alle aktuellen Windows-Updates auf der VM, damit alle vertrauenswürdigen Stammzertifikate auf dem Computer vorhanden sind. Wenn Sie sich in einer nicht verbundenen Umgebung befinden, können Sie den Standardprozess für Windows-Updates in Ihrer Organisation durchführen, um die Zertifikate abzurufen. Falls die erforderlichen Zertifikate auf der VM nicht vorhanden sind, schlagen die Aufrufe des Site Recovery-Diensts aus Sicherheitsgründen fehl.

Führen Sie den üblichen Prozess Ihrer Organisation für die Verwaltung von Windows-Updates oder Zertifikaten durch, um alle aktuellen Stammzertifikate und die aktualisierte Zertifikatssperrliste auf den VMs abzurufen.

Navigieren Sie mit einem Browser auf Ihrer VM zu „login.microsoftonline.com“, um zu überprüfen, ob das Problem behoben wurde.

**Linux**

Befolgen Sie die Anleitung Ihres Linux-Distributors, um die aktuellen vertrauenswürdigen Stammzertifikate und die aktuelle Zertifikatssperrliste auf der VM abzurufen.

Führen Sie diese Schritte aus, da SuSE Linux zum Verwalten einer Zertifikatliste symlinks verwendet:

1.  Melden Sie sich als Stammbenutzer an.

2.  Führen Sie den folgenden Befehl aus, um das Verzeichnis zu ändern:

      ``# cd /etc/ssl/certs``

1. Überprüfen Sie, ob das Symantec-Zertifikat der Stammzertifizierungsstelle vorhanden ist:

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Wurde das Symantec-Zertifikat der Stammzertifizierungsstelle nicht gefunden, führen Sie den folgenden Befehl aus, um die Datei herunterzuladen. Überprüfen Sie, ob Fehler aufgetreten sind, und führen Sie die empfohlenen Maßnahmen für Netzwerkfehler aus.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Überprüfen Sie, ob das Baltimore-Zertifikat der Stammzertifizierungsstelle vorhanden ist:

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Wurde das Baltimore-Zertifikat der Stammzertifizierungsstelle nicht gefunden, laden Sie das Zertifikat herunter.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Überprüfen Sie, ob das Zertifikat „DigiCert_Global_Root_CA“ vorhanden ist:

    ``# ls DigiCert_Global_Root_CA.pem``

6. Wurde „DigiCert_Global_Root_CA“ nicht gefunden, führen Sie die folgenden Befehle aus, um das Zertifikat herunterzuladen:

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Führen Sie das Skript für das erneute Ausführen von Hashvorgängen aus, um die Zertifikatantragstellerhashes für die neu heruntergeladenen Zertifikate zu aktualisieren.

    ``# c_rehash``

8.  Überprüfen Sie, ob die Antragstellerhashes als Symlinks für die Zertifikate erstellt werden.

    - Get-Help

      ``# ls -l | grep Baltimore``

    - Output

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Get-Help

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Output

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Get-Help

      ``# ls -l | grep DigiCert_Global_Root``

    - Output

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Erstellen Sie eine Kopie der Datei „VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem“ mit dem Dateinamen „b204d74a.0“.

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Erstellen Sie eine Kopie der Datei „Baltimore_CyberTrust_Root.pem“ mit dem Dateinamen „653b494a.0“.

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Erstellen Sie eine Kopie der Datei „DigiCert_Global_Root_CA.pem“ mit dem Dateinamen „3513523f.0“.

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Überprüfen Sie, ob die Dateien vorhanden sind.  

    - Get-Help

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Output

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Ausgehende Konnektivität für Site Recovery-URLs oder IP-Bereiche (Fehlercode 151037 oder 151072)

Damit die Site Recovery-Replikation funktioniert, ist für die VM die ausgehende Konnektivität zu bestimmten URLs oder IP-Bereichen erforderlich. Wenn sich Ihr virtueller Computer hinter einer Firewall befindet oder Netzwerksicherheitsgruppen-Regeln (NSG-Regeln) zum Steuern der ausgehenden Konnektivität verwendet werden, wird ggf. eine dieser Fehlermeldungen angezeigt.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: Fehler beim Registrieren des virtuellen Azure-Computers bei Site Recovery (151195) </br>
- **Mögliche Ursache** </br>
  - Die Verbindung mit Site Recovery-Endpunkten kann aufgrund eines Fehlers bei der DNS-Auflösung nicht hergestellt werden.
  - Dies ist häufiger der Fall beim erneuten Schutz, wenn Sie für den virtuellen Computer ein Failover ausgeführt haben, der DNS-Server in der Notfallwiederherstellungsregion jedoch nicht erreichbar ist.

- **Lösung**
   - Wenn Sie benutzerdefiniertes DNS verwenden, stellen Sie sicher, dass der DNS-Server in der Notfallwiederherstellungsregion erreichbar ist. Um zu überprüfen, ob Sie benutzerdefiniertes DNS verwenden, navigieren Sie zum virtuellen Computer > Notfallwiederherstellungsnetzwerk > DNS-Server. Versuchen Sie, über den virtuellen Computer auf den DNS-Server zuzugreifen. Wenn dies nicht möglich ist, führen Sie ein Failover für den DNS-Server aus, oder stellen Sie eine Verbindung zwischen dem Notfallwiederherstellungsnetzwerk und DNS her.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Fehler bei der Site Recovery-Konfiguration (151196)
- **Mögliche Ursache** </br>
  - Die Verbindung mit IP4-Endpunkten für die Authentifizierung und Identität von Office 365 kann nicht hergestellt werden.

- **Lösung**
  - Azure Site Recovery benötigt zur Authentifizierung Zugriff auf Office 365-IP-Adressbereiche.
    Wenn Sie zum Steuern der ausgehenden Netzwerkkonnektivität auf dem virtuellen Computer Azure-Netzwerksicherheitsgruppen-Regeln (NSG-Regeln) oder einen Firewallproxy verwenden, müssen Sie die Kommunikation mit Office 365-IP-Adressbereichen zulassen. Erstellen Sie basierend auf der NSG-Regel ein [Azure Active Directory-Diensttag (AAD)](../virtual-network/security-overview.md#service-tags) für den Zugriff auf alle IP-Adressen für AAD.
      - Wenn in Azure Active Directory (AAD) später neue Adressen hinzugefügt werden, müssen Sie neue NSG-Regeln erstellen.

> [!NOTE]
> Wenn die VMs sich hinter dem internen **Standard**-Lastenausgleich befinden würden, hätte er keinen Zugriff auf Office 365-IP-Adressen, d.h. standardmäßig „login.micorsoftonline.com“. Ändern Sie ihn entweder in einen internen Lastenausgleich des Typs **Basic**, oder erstellen Sie ausgehenden Zugriff wie in diesem [Artikel](https://aka.ms/lboutboundrulescli) erwähnt.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Fehler bei der Site Recovery-Konfiguration (151197)
- **Mögliche Ursache** </br>
  - Die Verbindung mit Azure Site Recovery-Dienstendpunkten kann nicht hergestellt werden.

- **Lösung**
  - Azure Site Recovery benötigt abhängig von der Region Zugriff auf [Site Recovery-IP-Adressbereiche](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). Stellen Sie sicher, dass über den virtuellen Computer auf die erforderlichen IP-Bereiche zugegriffen werden kann.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: Fehler bei der A2A-Replikation, wenn der Netzwerkdatenverkehr lokale Proxyserver durchläuft (151072)
- **Mögliche Ursache** </br>
  - Die benutzerdefinierten Proxyeinstellungen sind ungültig, und der ASR Mobility Service-Agent hat die Proxyeinstellungen von Internet Explorer nicht automatisch erkannt.


- **Lösung**
  1. Der Mobilitätsdienst-Agent erkennt die Proxyeinstellungen von Internet Explorer unter Windows und /etc/environment unter Linux.
  2. Wenn nur für ASR Mobility Service ein Proxy festgelegt werden soll, können Sie die Proxydetails in ProxyInfo.conf angeben, die sich hier befinden:</br>
     - ``/usr/local/InMage/config/`` unter ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` unter ***Windows***
  3. ProxyInfo.conf sollte die Proxyeinstellungen im folgenden INI-Format aufweisen.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Der ASR Mobility Service-Agent unterstützt nur ***nicht authentifizierte Proxys***.


### <a name="fix-the-problem"></a>Beheben des Problems
Führen Sie die Schritte im [Dokument mit der Netzwerkanleitung](site-recovery-azure-to-azure-networking-guidance.md) aus, um die [erforderlichen URLs](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) bzw. die [erforderlichen IP-Bereiche](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) auf eine Positivliste zu setzen.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Datenträger wurde auf dem Computer nicht gefunden (Fehlercode 150039)

Es muss ein neuer Datenträger initialisiert werden, der an die VM angefügt ist.

**Fehlercode** | **Mögliche Ursachen** | **Empfehlungen**
--- | --- | ---
150039<br></br>**Meldung**: Der Azure-Datenträger (DiskName) (DiskURI) mit der logischen Gerätenummer (LUN) (LUNValue) wurde keinem entsprechenden Datenträger zugeordnet, der vom virtuellen Computer gemeldet wird und denselben LUN-Wert aufweist. | - Es wurde ein neuer Datenträger an den virtuellen Computer angefügt, aber der Datenträger wurde nicht initialisiert.</br></br>- Der Datenträger innerhalb des virtuellen Computers meldet nicht den richtigen LUN-Wert, mit dem der Datenträger an den virtuellen Computer angefügt wurde.| Stellen Sie sicher, dass die Datenträger initialisiert wurden, und wiederholen Sie den Vorgang.</br></br>Windows: [Anfügen und Initialisieren eines neuen Datenträgers](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Linux: [Initialisieren eines neues Datenträgers unter Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Beheben des Problems
Stellen Sie sicher, dass die Datenträger initialisiert wurden, und wiederholen Sie den Vorgang:

- Windows: [Anfügen und Initialisieren eines neuen Datenträgers](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Für Linux: [Hinzufügen eines neuen Datenträgers in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)

Wenden Sie sich an den Support, wenn das Problem weiterhin besteht.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>Mindestens ein Datenträger ist für Schutz verfügbar (Fehlercode: 153039)
- **Mögliche Ursache** </br>
  - Mindestens ein Datenträger wurde dem virtuellen Computer vor kurzem nach dem Schutz hinzugefügt. 
  - Mindestens ein Datenträger wurde später nach dem Schutz des virtuellen Computers initialisiert.

### <a name="fix-the-problem"></a>Beheben des Problems
Sie können wahlweise entweder die Datenträger schützen oder die Warnung ignorieren, um als Replikationsstatus für den virtuellen Computer wieder „Fehlerfrei“ zu erhalten.</br>
1. Sie möchten die Datenträger schützen. Navigieren Sie zu „Replizierte Elemente“ > „VM“ > „Datenträger“, klicken Sie auf einen nicht geschützten Datenträger, und klicken Sie auf „Replikation aktivieren“.
 ![Datenträger hinzufügen](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. Sie möchten die Warnung ignorieren. Navigieren Sie zu „Replizierte Elemente“ > „VM“, klicken Sie im Übersichtsabschnitt auf „Warnung schließen“.
![Warnung schließen](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)
## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Azure-VM oder Ressourcengruppe wird nicht in „Replikation aktivieren“ angezeigt

 **Ursache 1:  Ressourcengruppe und der virtuelle Quellcomputer befinden sich jeweils an einem anderen Speicherort.** <br>
Azure Site Recovery bestimmt derzeit, dass sich die Quellressourcengruppe für die Region und der virtuelle Computer im selben Speicherort befinden müssen. Wenn das nicht der Fall ist, können Sie den virtuellen Computer während des Schutzzeitraums nicht finden. Als Problemumgehung können Sie die Replikation über den virtuellen Computer statt aus dem Recovery Services-Tresor aktivieren. Navigieren Sie zur Quell-VM > „Eigenschaften“ > „Notfallwiederherstellung“, und aktivieren Sie die Replikation.

**Ursache 2: Diese Ressourcengruppe ist nicht Teil des ausgewählten Abonnements.** <br>
Möglicherweise können Sie die Ressourcengruppe während des Schutzzeitraums nicht finden, wenn sie nicht Teil des angegebenen Abonnements ist. Stellen Sie sicher, dass die Ressourcengruppe zum verwendeten Abonnement gehört.

 **Ursache 3: Veraltete Konfiguration.** <br>
Wird der virtuelle Computer, für den Sie die Replikation aktivieren möchten, nicht angezeigt, ist die Ursache unter Umständen eine veraltete Site Recovery-Konfiguration auf dem virtuellen Azure-Computer. Die veraltete Konfiguration kann in den folgenden Fällen auf einer Azure-VM verbleiben:

- Sie haben die Replikation für die Azure-VM mithilfe von Site Recovery aktiviert und anschließend den Site Recovery-Tresor gelöscht, ohne die Replikation auf der VM explizit zu deaktivieren.
- Sie haben die Replikation für die Azure-VM mithilfe von Site Recovery aktiviert und anschließend die Ressourcengruppe mit dem Site Recovery-Tresor gelöscht, ohne die Replikation auf der VM explizit zu deaktivieren.

### <a name="fix-the-problem"></a>Beheben des Problems

>[!NOTE]
>
>Aktualisieren Sie das Modul „AzureRM.Resources“ unbedingt, bevor Sie das nachstehende Skript verwenden.

Sie können das [Skript zum Entfernen veralteter ASR-Konfigurationen](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1) verwenden und die veraltete Site Recovery-Konfiguration auf der Azure-VM entfernen. Nach dem Entfernen der veralteten Konfiguration sollte der virtuelle Computer angezeigt werden.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Auswählen des virtuellen Computers zum Schutz nicht möglich
 **Ursache 1:  Auf dem virtuellen Computer ist eine fehlerhafte oder nicht reagierende Erweiterung installiert.** <br>
 Wechseln Sie zum virtuellen Computer > Einstellung > Erweiterungen und überprüfen Sie, ob es fehlerhafte Erweiterungen gibt. Deinstallieren Sie die fehlerhafte Erweiterung, und wiederholen Sie den Schutzvorgang für den virtuellen Computer.<br>
 **Ursache 2:  [Der Bereitstellungsstatus des virtuellen Computers ist ungültig.](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Der Bereitstellungsstatus des virtuellen Computers ist ungültig (Fehlercode 150019)

Um die Replikation auf dem virtuellen Computer zu aktivieren, muss der Bereitstellungsstatus **Erfolgreich** lauten. Sie können den Status des virtuellen Computers überprüfen, indem Sie die nachstehenden Schritte ausführen.

1.  Wählen Sie unter **Alle Dienste** im Azure-Portal **Ressourcen-Explorer** aus.
2.  Erweitern Sie die Liste **Abonnements**, und wählen Sie Ihr Abonnement aus.
3.  Erweitern Sie die Liste **Ressourcengruppen**, und wählen Sie die Ressourcengruppe des virtuellen Computers aus.
4.  Erweitern Sie die Liste **Ressourcen**, und wählen Sie den virtuellen Computer aus.
5.  Überprüfen Sie das Feld **provisioningState** in der Instanzansicht rechts.

### <a name="fix-the-problem"></a>Beheben des Problems

- Wenn **provisioningState** den Status **Fehler** aufweist, wenden Sie sich mit den Informationen zur Problembehandlung an den Support.
- Wenn **provisioningState** den Status **Aktualisieren** aufweist, wird möglicherweise gerade eine andere Erweiterung bereitgestellt. Überprüfen Sie, ob gerade Vorgänge mit dem virtuellen Computer ausgeführt werden, warten Sie, bis diese abgeschlossen sind, und wiederholen Sie dann den Auftrag **Replikation aktivieren** für die Sitewiederherstellung.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Das virtuelle Zielnetzwerk kann nicht ausgewählt werden, die Registerkarte „Netzwerkauswahl“ ist ausgegraut.

**Ursache 1: Ihr virtueller Computer ist mit einem Netzwerk verbunden, das bereits einem Zielnetzwerk zugeordnet ist.**
- Wenn der virtuelle Quellcomputer Teil eines virtuellen Netzwerks ist, und ein anderer virtueller Computer des gleichen virtuellen Netzwerks bereits einem Netzwerk in der Zielressourcengruppe zugeordnet ist, wird das Dropdownfeld „Netzwerkauswahl“ standardmäßig deaktiviert.

![Netzwerk_Auswahl_ausgegraut](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Ursache 2: Sie haben den virtuellen Computer zuvor mithilfe von Azure Site Recovery geschützt und die Replikation deaktiviert.**
 - Durch das Deaktivieren der Replikation eines virtuellen Computers wird die Netzwerkzuordnung nicht gelöscht. Diese muss in dem Recovery Services-Tresor gelöscht werden, in dem der virtuelle Computer geschützt wurde. </br>
 Navigieren Sie zu Recovery Services-Tresor > Site Recovery-Infrastruktur > Netzwerkzuordnung. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Das während der Notfallwiederherstellungseinrichtung konfigurierte Zielnetzwerk kann nach dem erstmaligen Einrichten und nachdem der virtuelle Computer geschützt ist, geändert werden. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Beachten Sie, dass sich die Änderung der Netzwerkzuordnung auf alle geschützten virtuellen Computer auswirken, die diese bestimmte Netzwerkzuordnung verwenden.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM+/Volumeschattenkopie: Dienstfehler (Fehlercode 151025)

**Fehlercode** | **Mögliche Ursachen** | **Empfehlungen**
--- | --- | ---
151025<br></br>**Meldung**: Fehler beim Installieren der Site Recovery-Erweiterung | - Der Dienst „COM+-Systemanwendung“ ist deaktiviert.</br></br>- Der Dienst „Volumeschattenkopie“ ist deaktiviert.| Legen Sie für „COM+-Systemanwendung“ und „Volumeschattenkopie“ den automatischen oder manuellen Startmodus fest.

### <a name="fix-the-problem"></a>Beheben des Problems

Sie können die Konsole „Dienste“ öffnen und sicherstellen, dass für „COM+-Systemanwendung“ und „Volumeschattenkopie“ die Option „Starttyp“ nicht auf „Deaktiviert“ festgelegt ist.
  ![com-error](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nicht unterstützte Größe verwalteter Datenträger (Fehlercode: 150172)


**Fehlercode** | **Mögliche Ursachen** | **Empfehlungen**
--- | --- | ---
150172<br></br>**Meldung**: Schutz konnte für den virtuellen Computer nicht aktiviert werden, da dieser einen (DiskName) mit einer Größe von (DiskSize) besitzt. Das ist kleiner als das unterstützte Minimum von 1024 MB. | Der Datenträger ist kleiner als die unterstützte Größe von 1024 MB.| Stellen Sie sicher, dass die Datenträgergrößen innerhalb des unterstützten Größenbereichs liegen, und wiederholen Sie den Vorgang.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Fehler bei Schutz aktiviert, da in der GRUB-Konfiguration erwähnter Gerätename statt UUID vorliegt (Fehlercode 151126)

**Mögliche Ursache**: </br>
Die GRUB-Konfigurationsdateien („/boot/grub/menu.lst“, „/boot/grub/grub.cfg“, „/boot/grub2/grub.cfg“ oder „/etc/default/grub“) könnten den Wert für die Parameter **root** und **resume** als tatsächliche Gerätenamen statt UUID enthalten. Site Recovery bevorzugt den UUID-Ansatz, da Gerätenamen sich beim Neustart des virtuellen Computers ändern können, da die VM möglicherweise bei dem Failover nicht den Namen beibehält, was zu Problemen führen kann. Beispiel: </br>


- Die folgende Zeile stammt aus der GRUB-Datei **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- Die folgende Zeile stammt aus der GRUB-Datei **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Beachten Sie die fett formatierte Zeichenfolge oben: GRUB benutzt die tatsächlichen Gerätenamen für die Parameter „root“ und „resume“ statt UUID.

**Wie behebe ich das Problem?**<br>
Die Gerätenamen sollten mit dem entsprechenden UUID ersetzt werden.<br>


1. Suchen Sie die UUID des Geräts durch Ausführen des Befehls „blkid \<device name>“. Beispiel:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=\<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Restart the protection again

## Enable protection failed as device mentioned in the GRUB configuration doesn't exist(error code 151124)
**Possible Cause:** </br>
The GRUB configuration files ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") may contain the parameters "rd.lvm.lv" or "rd_LVM_LV" to indicate the LVM device that should be discovered at the time of booting. If these LVM devices doesn't exist, then the protected system itself will not boot and stuck in the boot process. Even the same will be observed with the failover VM. Below are few examples:

Few examples: </br>

1. The following line is from the GRUB file **"/boot/grub2/grub.cfg"** on RHEL7. </br>
   *linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/etc/default/grub"** on RHEL7 </br>
   *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/boot/grub/menu.lst"** on RHEL6 </br>
   *kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".<br>

**How to Fix:**<br>

If the LVM device doesn't exist, fix either by creating it or remove the parameter for the same from the GRUB configuration files and then retry the enable protection. </br>

## Site recovery mobility service update completed with warnings ( error code 151083)
Site Recovery mobility service has many components, one of which is called filter driver. Filter driver gets loaded into system memory only at a time of system reboot. Whenever there are  site recovery mobility service updates that has filter driver changes, we update the machine but still gives you warning that some fixes require a reboot. It means that the filter driver fixes can only be realized when a new filter driver is loaded which can happen only at the time of system reboot.<br>
**Please note** that this is just a warning and existing replication keeps on working even after the new agent update. You can choose to reboot anytime you want to get the benefits of new filter driver but if you don't reboot than also old filter driver keeps on working. Apart from filter driver, **benefits of  any other enhancements and fixes in mobility service get realized without any reboot when the agent gets updated.**  


## Protection couldn't be enabled as replica managed disk 'diskname-replica' already exists without expected tags in the target resource group( error code 150161

**Cause**: It can occur if the  virtual machine was protected earlier in the past and during disabling the replication, replica disk was not cleaned due to some reason.</br>
**How to fix:**
Delete the mentioned replica disk in the error message and restart the failed protection job again.

## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
