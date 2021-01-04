---
title: Behandeln von Replikationsproblemen bei der Migration virtueller VMware-Computer ohne Agent
description: Hilfe zu Fehlern in Replikationszyklen
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: e19c5064dd69538dfc025b0d244baf4fa74706b2
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753534"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Behandeln von Replikationsproblemen bei der Migration virtueller VMware-Computer ohne Agent

Dieser Artikel beschreibt einige häufig auftretende Probleme und bestimmte Fehler bei der Replikation von lokalen virtuellen VMware-Computern mithilfe der Methode für die Servermigration ohne Agent von Azure Migrate.

Wenn Sie einen virtuellen VMware-Computer mithilfe der Replikationsmethode ohne Agent replizieren, werden Daten von den Datenträgern des virtuellen Computers (VMDKs) auf die verwalteten Datenträger des Replikats in Ihrem Azure-Abonnement repliziert. Wenn die Replikation für einen virtuellen Computer gestartet wird, werden in einem ersten Replikationszyklus vollständige Kopien der Datenträger repliziert. Nachdem die erste Replikation abgeschlossen ist, werden in regelmäßigen Abständen inkrementelle Replikationszyklen geplant, um Änderungen zu übertragen, die seit dem vorherigen Replikationszyklus aufgetreten sind.

Es kann vorkommen, dass Replikationszyklen für einen virtuellen Computer fehlschlagen. Diese Fehler können von Problemen bei der lokalen Netzwerkkonfiguration bis zu Problemen beim Clouddienst-Back-End von Azure Migrate reichen. In diesem Artikel wird erläutert, wie Sie

 - den Replikationsstatus überwachen und Fehler beheben können.
 - einige der häufigsten Replikationsfehler auflisten und zusätzliche Schritte ausführen, um diese zu beheben.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Überwachen des Replikationsstatus mithilfe des Azure-Portals

Führen Sie die folgenden Schritte aus, um den Replikationsstatus Ihrer virtuellen Computer zu überwachen:

  1. Wechseln Sie im Azure-Portal in Azure Migrate zur Seite „Server“.
  ![Abbildung 1](./media/troubleshoot-changed-block-tracking-replication/image0.png)
  1. Navigieren Sie zur Seite „Aktuell replizierte Computer“, indem Sie in der Kachel „Servermigration“ auf „Server werden repliziert“ klicken.
  ![Abbildung 2](./media/troubleshoot-changed-block-tracking-replication/image1.png)
  1. Eine Liste der replizierten Server wird zusammen mit zusätzlichen Informationen angezeigt, z. B. zum Status, zur Integrität, zur Zeit der letzten Synchronisierung usw. In der Spalte „Integrität“ ist die aktuelle Replikationsintegrität des virtuellen Computers angegeben. Die Werte „Kritisch“ oder „Warnung“ in der Spalte „Integrität“ geben in der Regel an, dass beim vorherigen Replikationszyklus für die VM ein Fehler aufgetreten ist. Klicken Sie mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie „Fehlerdetails“ aus, um weitere Informationen zu erhalten. Die Seite „Fehlerdetails“ enthält Informationen zum Fehler und weitere Einzelheiten zur Problembehandlung. Außerdem wird der Link „Aktuelle Ereignisse“ angezeigt, der verwendet werden kann, um zur Ereignisseite für den virtuellen Computer zu navigieren.
  ![Abbildung 3](./media/troubleshoot-changed-block-tracking-replication/image2.png)
  1. Klicken Sie auf „Aktuelle Ereignisse“, um die Fehler vorheriger Replikationszyklen für den virtuellen Computer anzuzeigen. Suchen Sie auf der Ereignisseite das letzte Ereignis vom Typ „Replication cycle failed“ (Fehler bei Replikationszyklus) oder „Replication cycle failed for disk“ (Fehler bei Replikationszyklus für Datenträger) für den virtuellen Computer.
  ![Abbildung 4](./media/troubleshoot-changed-block-tracking-replication/image3.png)
  1. Klicken Sie auf das Ereignis, um die möglichen Ursachen für den Fehler und die empfohlenen Schritte zu dessen Behebung zu ermitteln. Verwenden Sie die bereitgestellten Informationen, um das Problem bzw. den Fehler zu beheben.
 ![Abbildung 5](./media/troubleshoot-changed-block-tracking-replication/image4.png)

## <a name="common-replication-errors"></a>Häufige Replikationsfehler

In diesem Abschnitt werden einige häufige Fehler und Möglichkeiten beschrieben, diese zu beheben.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>Fehler beim Key Vault-Vorgang beim Versuch, VMs zu replizieren

**Fehler:** „Fehler beim Key Vault-Vorgang. Vorgang: Beim Konfigurieren des verwalteten Speicherkontos, Schlüsseltresor: Schlüsseltresorname, Speicherkonto: Speicherkontoname ist folgender Fehler aufgetreten:“

**Fehler:** „Fehler beim Key Vault-Vorgang. Vorgang: Beim Generieren einer Shared Access Signature-Definition, Schlüsseltresor: Schlüsseltresorname, Speicherkonto: Speicherkontoname ist folgender Fehler aufgetreten:“

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

Dieser Fehler tritt normalerweise auf, da die Benutzerzugriffsrichtlinie für den Schlüsseltresor dem derzeit angemeldeten Benutzer nicht die erforderlichen Berechtigungen zum Konfigurieren von Speicherkonten für die Verwaltung mit Key Vault gewährt. Wechseln Sie zum Überprüfen der Benutzerzugriffsrichtlinie im Schlüsseltresor zur Key Vault-Seite im Portal für den Schlüsseltresor, und wählen Sie „Zugriffsrichtlinien“ aus. 

Wenn das Portal den Schlüsseltresor erstellt, wird auch eine Benutzerzugriffsrichtlinie hinzugefügt, die dem derzeit angemeldeten Benutzer Berechtigungen zum Konfigurieren von Speicherkonten für die Verwaltung mit Key Vault erteilt. Dabei kann aus zwei Gründen ein Fehler auftreten.

- Der angemeldete Benutzer ist ein Remoteprinzipal im Azure-Mandanten des Kunden (CSP-Abonnement, und der angemeldete Benutzer ist der Partneradministrator). Die Problemumgehung besteht in diesem Fall darin, den Schlüsseltresor zu löschen, sich vom Portal abzumelden und dann mit einem Benutzerkonto aus dem Kundenmandanten (kein Remoteprinzipal) wieder anzumelden und den Vorgang zu wiederholen. Der CSP-Partner verfügt in der Regel über ein Benutzerkonto im Azure Active Directory-Mandanten des Kunden, das er verwenden kann. Andernfalls kann er ein neues Benutzerkonto für sich im Azure Active Directory-Mandanten des Kunden erstellen, sich beim Portal als der neue Benutzer anmelden und den Replikationsvorgang dann wiederholen. Das verwendete Konto muss über die Berechtigungen als Besitzer oder als Mitwirkender und Benutzerzugriffsadministrator für das Konto in der Ressourcengruppe (Migrationsprojekt-Ressourcengruppe) verfügen.

- Der andere Fall, in dem dies auftreten kann, ist wenn ein Benutzer (user1) anfänglich versucht hat, die Replikation einzurichten, und dabei ein Fehler aufgetreten ist, der Schlüsseltresor jedoch bereits erstellt wurde (und die Benutzerzugriffsrichtlinie diesem Benutzer ordnungsgemäß zugewiesen wurde). Zu einem späteren Zeitpunkt versucht ein anderer Benutzer (user2), die Replikation einzurichten, aber beim Vorgang zum Konfigurieren eines verwalteten Speicherkontos oder zum Generieren einer SAS-Definition tritt ein Fehler auf, da keine entsprechende Benutzerzugriffsrichtlinie für user2 im Schlüsseltresor vorhanden ist.

**Lösung:** Um dieses Problem zu umgehen, erstellen Sie im Schlüsseltresor eine Benutzerzugriffsrichtlinie für user2, die ihm die Berechtigungen zum Konfigurieren des verwalteten Speicherkontos und zum Generieren von SAS-Definitionen gewährt. Dies kann user2 über Azure PowerShell mithilfe der nachstehenden Cmdlets erreichen:

$userPrincipalId = $(Get-AzureRmADUser -UserPrincipalName "user2_E-Mail-Adresse").Id

Set-AzureRmKeyVaultAccessPolicy -VaultName "keyvaultname" -ObjectId $userPrincipalId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**Fehler-ID:** 181008

**Fehlermeldung:** VM: VMName. Error: Encountered timeout event 'DisposeArtefactsTimeout' in the state &'['Gateway.Service.StateMachine.SnapshotReplication.SnapshotReplicationEngine+WaitingForArtefactsDisposalPreCycle' ('WaitingForArtefactsDisposalPreCycle')]' (Fehler: Timeoutereignis 'DisposeArtefactsTimeout' im Zustand &'['Gateway.Service.StateMachine.SnapshotReplication.SnapshotReplicationEngine+WaitingForArtefactsDisposalPreCycle' ('WaitingForArtefactsDisposalPreCycle')]').

**Mögliche Ursachen:**

Die Komponente, die versucht, Daten in Azure zu replizieren, ist entweder ausgefallen oder reagiert nicht. Mögliche Ursachen:

- Der Gatewaydienst, der in der Azure Migrate-Appliance ausgeführt wird, ist ausgefallen.
- Der Gatewaydienst hat Probleme beim Verbinden mit dem Service Bus/Event Hub/Appliance-Speicherkonto.

**Identifizieren der exakten Ursache für DisposeArtefactsTimedOut und der entsprechenden Lösung:**

1. Stellen Sie sicher, dass die Azure Migrate-Appliance betriebsbereit ist und ausgeführt wird.
2. Überprüfen Sie, ob der Gatewaydienst für die Appliance ausgeführt wird:
   1.  Melden Sie sich mit Remotedesktop bei der Azure Migrate-Appliance an, und gehen Sie wie folgt vor.

   2.  Öffnen Sie das MMC-Snap-In für Microsoft-Dienste (führen Sie „services.msc“ aus), und überprüfen Sie, ob der Microsoft Azure-Gatewaydienst ausgeführt wird. Wenn der Dienst beendet wurde oder nicht ausgeführt wird, starten Sie ihn. Alternativ können Sie eine Eingabeaufforderung oder PowerShell öffnen und „Net Start asrgwy“ ausführen.

3. Überprüfen Sie, ob Verbindungsprobleme zwischen der Azure Migrate-Appliance und dem Appliancespeicherkonto vorliegen: 

    Führen Sie den folgenden Befehl aus, nachdem Sie „azcopy“ in die Azure Migrate-Appliance heruntergeladen haben:
    
    _azcopy bench https://[account].blob.core.windows.net/[container]?SAS_
    
    **Schritte zum Ausführen des Leistungsbenchmarktests:**
    
      1. [Laden Sie „azcopy“ herunter](../storage/common/storage-use-azcopy-v10.md).
        
      2. Suchen Sie das Speicherkonto der Appliance in der Ressourcengruppe. Das Speicherkonto weist einen Namen auf, der „migrategwsa\*\*\*\*\*\*\*\*\*\*“ ähnlich ist. Dies ist der Wert des Parameters [account] im obigen Befehl.
        
      3. Suchen Sie im Azure-Portal Ihr Speicherkonto. Stellen Sie sicher, dass das Abonnement, das Sie für die Suche verwenden, das gleiche Abonnement (Zielabonnement) ist, in dem das Speicherkonto erstellt wurde. Wechseln Sie im Abschnitt „Blob-Dienst“ zu „Container“. Klicken Sie auf „+Container“, und erstellen Sie einen Container. Verwenden Sie für „Öffentliche Zugriffsebene“ den ausgewählten Standardwert.
        
      4. Wechseln Sie unter „Einstellungen“ zu „Shared Access Signature“. Wählen Sie unter „Zulässiger Ressourcentyp“ die Option „Container“ aus. Klicken Sie auf „SAS und Verbindungszeichenfolge generieren“. Kopieren Sie den SAS-Wert.
        
      5. Führen Sie den obigen Befehl an der Eingabeaufforderung aus, indem Sie „account“, „container“ und „SAS“ durch die Werte aus den Schritten 2, 3 bzw. 4 ersetzen.
        
      Alternativ können Sie den Azure Storage-Explorer in die Appliance [herunterladen](https://go.microsoft.com/fwlink/?linkid=2138967) und versuchen, 10 Blobs von etwa 64 MB in die Speicherkonten hochzuladen. Wenn kein Problem auftritt, sollte der Upload erfolgreich sein.
        
    **Lösung:** Wenn der Test fehlschlägt, liegt ein Netzwerkproblem vor. Wenden Sie sich an Ihr lokales Netzwerkteam, um Verbindungsprobleme zu überprüfen. In der Regel können die Fehler durch verschiedene Firewalleinstellungen verursacht werden.
    
4.  Stellen Sie fest, ob Verbindungsprobleme zwischen der Azure Migrate-Appliance und dem Service Bus vorliegen:

    Mit diesem Test wird überprüft, ob die Azure Migrate-Appliance mit dem Clouddienst-Back-End von Azure Migrate kommunizieren kann. Die Appliance kommuniziert über den Service Bus und Event Hub-Nachrichtenwarteschlangen mit dem Dienst-Back-End. Um die Konnektivität zwischen der Appliance und dem Service Bus zu überprüfen, [laden](https://go.microsoft.com/fwlink/?linkid=2139104) Sie den Service Bus Explorer herunter, und versuchen, eine Verbindung mit dem Service Bus der Appliance herzustellen und eine Nachricht zu senden/zu empfangen. Wenn kein Problem auftritt, sollte dies erfolgreich verlaufen.

    **Schritte zum Ausführen des Tests:**

    1. Kopieren Sie die Verbindungszeichenfolge aus dem Service Bus, der im Migrate-Projekt erstellt wurde.
    2. Öffnen Sie den Service Bus Explorer.
    3. Wechseln Sie zu „Datei“ und dann zu „Verbinden“.
    4. Fügen Sie die Verbindungszeichenfolge ein, und klicken Sie auf „Verbinden“.
    5. Dadurch wird der Service Bus-Namespace geöffnet.
    6. Wählen Sie im Thema „Snapshot Manager“ aus. Klicken Sie mit der rechten Maustaste auf „Snapshot Manager“, wählen Sie „Nachrichten empfangen“ und dann „Einsehen“ aus, und klicken Sie auf „OK“.
    7. Wenn die Verbindung erfolgreich hergestellt wurde, wird in der Konsolenausgabe „[x] messages received“ ([x] Nachrichten empfangen) angezeigt. Wenn die Verbindung nicht erfolgreich ist, wird in einer Meldung darauf hingewiesen, dass keine Verbindung hergestellt werden konnte.
    
    **Lösung:** Wenn der Test fehlschlägt, liegt ein Netzwerkproblem vor. Wenden Sie sich an Ihr lokales Netzwerkteam, um Verbindungsprobleme zu überprüfen. In der Regel können die Fehler durch verschiedene Firewalleinstellungen verursacht werden.

5. Verbindungsprobleme zwischen der Azure Migrate-Appliance und Azure Key Vault:

    Mit diesem Test werden Verbindungsprobleme zwischen der Azure Migrate-Appliance und Azure Key Vault ermittelt. Key Vault wird verwendet, um den Zugriff auf das Speicherkonto zu verwalten, das für die Replikation verwendet wird.
    
    **Schritte zum Überprüfen der Konnektivität:**
    
    1. Rufen Sie den Key Vault-URI aus der Liste der Ressourcen in der Ressourcengruppe ab, die dem Azure Migrate-Projekt entspricht.
    
    1. Öffnen Sie PowerShell in der Azure Migrate-Appliance, und führen Sie den folgenden Befehl aus:
    
    _test-netconnection Key Vault URI -P 443_
    
    Mit diesem Befehl wird eine TCP-Verbindung hergestellt und eine Ausgabe zurückgegeben.
    
     - Überprüfen Sie in der Ausgabe das Feld „_TcpTestSucceeded_“. Wenn der Wert „_True_“ lautet, liegt kein Konnektivitätsproblem zwischen der Azure Migrate-Appliance und Azure Key Vault vor. Wenn der Wert „False“ lautet, besteht ein Konnektivitätsproblem.
    
    **Lösung:** Wenn der Test fehlschlägt, liegt ein Konnektivitätsproblem zwischen der Azure Migrate-Appliance und Azure Key Vault vor. Wenden Sie sich an Ihr lokales Netzwerkteam, um Verbindungsprobleme zu überprüfen. In der Regel können die Fehler durch verschiedene Firewalleinstellungen verursacht werden.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**Fehler-ID:** 1011

**Fehlermeldung:** The upload of data for disk DiskPath, DiskId of virtual machine VMName; VMId did not complete within the expected time (Der Upload von Daten für den Datenträger DiskPath [DiskId] des virtuellen Computers VMName [VMId] wurde nicht innerhalb der erwarteten Zeit abgeschlossen).

Dieser Fehler weist in der Regel darauf hin, dass die Azure Migrate-Appliance, die die Replikation ausführt, keine Verbindung mit Azure Cloud Services herstellen kann oder dass die Replikation langsam verläuft, wodurch der Replikationszyklus ein Timeout verursacht.

Mögliche Ursachen:

- Die Azure Migrate-Appliance ist ausgefallen.
- Der Replikations-Gatewaydienst der Appliance wird nicht ausgeführt.
- Beim Replikations-Gatewaydienst treten Probleme bei der Verbindung mit einer der folgenden Azure-Dienstkomponenten auf, die für die Replikation verwendet werden: Service Bus/Event Hub/Azure-Cachespeicherkonto/Azure Key Vault
- Der Gatewaydienst wird beim Versuch, vom Datenträger zu lesen, auf vCenter-Ebene gedrosselt.

**Ermitteln der Grundursache und Lösen des Problems:**

1. Stellen Sie sicher, dass die Azure Migrate-Appliance betriebsbereit ist und ausgeführt wird.
2. Überprüfen Sie, ob der Gatewaydienst für die Appliance ausgeführt wird:
   1.  Melden Sie sich mit Remotedesktop bei der Azure Migrate-Appliance an, und gehen Sie wie folgt vor.

   2.  Öffnen Sie das MMC-Snap-In für Microsoft-Dienste (führen Sie „services.msc“ aus), und überprüfen Sie, ob der Microsoft Azure-Gatewaydienst ausgeführt wird. Wenn der Dienst beendet wurde oder nicht ausgeführt wird, starten Sie ihn. Alternativ können Sie eine Eingabeaufforderung oder PowerShell öffnen und „Net Start asrgwy“ ausführen.


3. **Stellen Sie fest, ob Verbindungsprobleme zwischen der Azure Migrate-Appliance und dem Cachespeicherkonto vorliegen:** 

    Führen Sie den folgenden Befehl aus, nachdem Sie „azcopy“ in die Azure Migrate-Appliance heruntergeladen haben:
    
    _azcopy bench https://[account].blob.core.windows.net/[container]?SAS_
    
    **Schritte zum Ausführen des Leistungsbenchmarktests:**
    
      1. [Laden Sie „azcopy“ herunter](../storage/common/storage-use-azcopy-v10.md).
        
      2. Suchen Sie das Speicherkonto der Appliance in der Ressourcengruppe. Das Speicherkonto weist einen Namen ähnlich wie „migratelsa\*\*\*\*\*\*\*\*\*\*“ auf. Dies ist der Wert des Parameters [account] im obigen Befehl.
        
      3. Suchen Sie im Azure-Portal Ihr Speicherkonto. Stellen Sie sicher, dass das Abonnement, das Sie für die Suche verwenden, das gleiche Abonnement (Zielabonnement) ist, in dem das Speicherkonto erstellt wurde. Wechseln Sie im Abschnitt „Blob-Dienst“ zu „Container“. Klicken Sie auf „+Container“, und erstellen Sie einen Container. Verwenden Sie für „Öffentliche Zugriffsebene“ den ausgewählten Standardwert.
        
      4. Wechseln Sie unter „Einstellungen“ zu „Shared Access Signature“. Wählen Sie unter „Zulässiger Ressourcentyp“ die Option „Container“ aus. Klicken Sie auf „SAS und Verbindungszeichenfolge generieren“. Kopieren Sie den SAS-Wert.
        
      5. Führen Sie den obigen Befehl an der Eingabeaufforderung aus, indem Sie „account“, „container“ und „SAS“ durch die Werte aus den Schritten 2, 3 bzw. 4 ersetzen.
        
      Alternativ können Sie den Azure Storage-Explorer in die Appliance [herunterladen](https://go.microsoft.com/fwlink/?linkid=2138967) und versuchen, 10 Blobs von etwa 64 MB in die Speicherkonten hochzuladen. Wenn kein Problem auftritt, sollte der Upload erfolgreich sein.
        
    **Lösung:** Wenn der Test fehlschlägt, liegt ein Netzwerkproblem vor. Wenden Sie sich an Ihr lokales Netzwerkteam, um Verbindungsprobleme zu überprüfen. In der Regel können die Fehler durch verschiedene Firewalleinstellungen verursacht werden.
                
4.  **Verbindungsprobleme zwischen der Azure Migrate-Appliance und Azure Service Bus:**

    Mit diesem Test wird überprüft, ob die Azure Migrate-Appliance mit dem Clouddienst-Back-End von Azure Migrate kommunizieren kann. Die Appliance kommuniziert über den Service Bus und Event Hub-Nachrichtenwarteschlangen mit dem Dienst-Back-End. Um die Konnektivität zwischen der Appliance und dem Service Bus zu überprüfen, [laden](https://go.microsoft.com/fwlink/?linkid=2139104) Sie den Service Bus Explorer herunter, und versuchen, eine Verbindung mit dem Service Bus der Appliance herzustellen und eine Nachricht zu senden/zu empfangen. Wenn kein Problem auftritt, sollte dies erfolgreich verlaufen.

    **Schritte zum Ausführen des Tests:**
    
    1. Kopieren Sie die Verbindungszeichenfolge aus dem Service Bus, der in der Ressourcengruppe erstellt wurde, die dem Azure Migrate-Projekt entspricht.
    
    1. Öffnen Sie Service Bus-Explorer.
    
    1. Wechseln Sie zu „Datei“ > „Verbinden“.
    
    1. Fügen Sie die in Schritt 1 kopierte Verbindungszeichenfolge ein, und klicken Sie auf „Verbinden“.
    
    1. Dadurch wird der Service Bus-Namespace geöffnet.
    
    1. Wählen Sie im Thema im Namespace „Snapshot Manager“ aus. Klicken Sie mit der rechten Maustaste auf „Snapshot Manager“, wählen Sie „Nachrichten empfangen“ und dann „Einsehen“ aus, und klicken Sie auf „OK“.
    
    Wenn die Verbindung erfolgreich hergestellt wurde, wird in der Konsolenausgabe „[x] messages received“ ([x] Nachrichten empfangen) angezeigt. Wenn die Verbindung nicht erfolgreich ist, wird in einer Meldung darauf hingewiesen, dass keine Verbindung hergestellt werden konnte.
    
    **Lösung:** Wenn der Test fehlschlägt, liegt ein Konnektivitätsproblem zwischen der Azure Migrate-Appliance und Service Bus vor. Wenden Sie sich an Ihr lokales Netzwerkteam, um diese Verbindungsprobleme zu überprüfen. In der Regel können die Fehler durch verschiedene Firewalleinstellungen verursacht werden.
    
 5. **Verbindungsprobleme zwischen der Azure Migrate-Appliance und Azure Key Vault:**

    Mit diesem Test werden Verbindungsprobleme zwischen der Azure Migrate-Appliance und Azure Key Vault ermittelt. Key Vault wird verwendet, um den Zugriff auf das Speicherkonto zu verwalten, das für die Replikation verwendet wird.
    
    **Schritte zum Überprüfen der Konnektivität:**
    
    1. Rufen Sie den Key Vault-URI aus der Liste der Ressourcen in der Ressourcengruppe ab, die dem Azure Migrate-Projekt entspricht.
    
    1. Öffnen Sie PowerShell in der Azure Migrate-Appliance, und führen Sie den folgenden Befehl aus:
    
    _test-netconnection Key Vault URI -P 443_
    
    Mit diesem Befehl wird eine TCP-Verbindung hergestellt und eine Ausgabe zurückgegeben.
    
    1. Überprüfen Sie in der Ausgabe das Feld „_TcpTestSucceeded_“. Wenn der Wert „_True_“ lautet, liegt kein Konnektivitätsproblem zwischen der Azure Migrate-Appliance und Azure Key Vault vor. Wenn der Wert „False“ lautet, besteht ein Konnektivitätsproblem.
    
    **Lösung:** Wenn der Test fehlschlägt, liegt ein Konnektivitätsproblem zwischen der Azure Migrate-Appliance und Azure Key Vault vor. Wenden Sie sich an Ihr lokales Netzwerkteam, um Verbindungsprobleme zu überprüfen. In der Regel können die Fehler durch verschiedene Firewalleinstellungen verursacht werden.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Fehler beim Versuch, geänderte Blöcke abzurufen

Fehlermeldung: 'Encountered an error while trying to fetch change blocks' (Fehler beim Versuch, geänderte Blöcke abzurufen)

Die Replikationsmethode ohne Agent verwendet die Changed Block Tracking-Technologie (CBT) von VMware, um Daten in Azure zu replizieren. Mit CBT kann das Servermigrationstool nur die Blöcke nachverfolgen und replizieren, die seit dem letzten Replikationszyklus geändert wurden. Dieser Fehler tritt auf, wenn Changed Block Tracking für eine replizierenden virtuellen Computer zurückgesetzt wird, oder wenn die Changed Block Tracking-Datei beschädigt ist.

Dieser Fehler kann auf die folgenden zwei Arten behoben werden:

- Wenn Sie sich dafür entschieden haben, die Replikation automatisch reparieren zu lassen, indem Sie „Ja“ ausgewählt haben, als Sie die Replikation des virtuellen Computers initiiert haben, startet das Tool einen Reparaturversuch. Klicken Sie mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie „Replikation reparieren“ aus.
- Wenn Sie die Replikation nicht automatisch reparieren lassen wollten oder der oben angegebene Schritt nicht funktioniert hat, beenden Sie die Replikation für den virtuellen Computer, [setzen Changed Block Tracking für den virtuellen Computer zurück](https://go.microsoft.com/fwlink/?linkid=2139203) und konfigurieren die Replikation dann neu.

Ein bekanntes Problem dieser Art, das eine CBT-Zurücksetzung des virtuellen Computers unter VMware vSphere 5.5 verursachen kann, ist in [VMware KB 2048201: Changed Block Tracking](https://go.microsoft.com/fwlink/?linkid=2138888) is reset after a storage vMotion operation in vSphere 5.x (Changed Block Tracking wird nach einem Storage vMotion-Vorgang in vSphere 5.x zurückgesetzt) beschrieben. Wenn Sie VMware vSphere 5.5 verwenden, installieren Sie unbedingt die in dieser KB beschriebenen Updates.

Alternativ können Sie VMware Changed Block Tracking auf einem virtuellen Computer mithilfe von VMware PowerCLI zurücksetzen.

## <a name="an-internal-error-occurred"></a>Ein interner Fehler ist aufgetreten.

Manchmal können Fehler aufgrund von Problemen in der VMware-Umgebung/-API auftreten. Die folgenden Fehler beziehen sich auf die VMware-Umgebung. Die Fehler weisen ein festes Format auf.

_Fehlermeldung: Ein interner Fehler ist aufgetreten. [Fehlermeldung]_

Beispiel: Fehlermeldung: Ein interner Fehler ist aufgetreten. [An Invalid snapshot configuration was detected] (Es wurde eine ungültige Momentaufnahmekonfiguration erkannt).

Im folgenden Abschnitt werden einige häufige VMware-Fehler und Lösungsmöglichkeiten aufgelistet.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Fehlermeldung: Ein interner Fehler ist aufgetreten. [Server Refused Connection] (Der Server hat die Verbindung verweigert).

Dies ist ein bekannter VMware-Fehler, der in VDDK 6.7 auftritt. Sie müssen den Gatewaydienst beenden, der in der Azure Migrate-Appliance ausgeführt wird, [ein Update aus dem VMware-KB-Artikel herunterladen](https://go.microsoft.com/fwlink/?linkid=2138889) und den Gatewaydienst neu starten.

Schritte zum Beenden des Gatewaydiensts:

1. Drücken Sie die WINDOWS-TASTE+R, und öffnen Sie „services.msc“. Klicken Sie auf den Microsoft Azure-Gatewaydienst, und beenden Sie diesen.
2. Alternativ können Sie eine Eingabeaufforderung oder PowerShell öffnen und „Net Stop asrgwy“ ausführen. Warten Sie, bis Sie die Meldung erhalten, dass der Dienst nicht mehr ausgeführt wird.

Schritte zum Starten des Gatewaydiensts:

1. Drücken Sie die WINDOWS-TASTE+R, und öffnen Sie „services.msc“. Klicken Sie mit der rechten Maustaste auf den Microsoft Azure-Gatewaydienst, und starten Sie diesen.
2. Alternativ können Sie eine Eingabeaufforderung oder PowerShell öffnen und „Net Start asrgwy“ ausführen.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Fehlermeldung: Ein interner Fehler ist aufgetreten. [An Invalid snapshot configuration was detected] (Es wurde eine ungültige Momentaufnahmekonfiguration erkannt].

Wenn Sie über einen virtuellen Computer mit mehreren Datenträgern verfügen, kann dieser Fehler auftreten, wenn Sie einen Datenträger vom virtuellen Computer entfernen. Um dieses Problem zu beheben, führen Sie die Schritte in [diesem VMware-Artikel](https://go.microsoft.com/fwlink/?linkid=2138890) aus.

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Fehlermeldung: Ein interner Fehler ist aufgetreten. [Generate Snapshot Hung] (Der Task zum Generieren einer Momentaufnahme reagiert nicht mehr).

Dieses Problem tritt auf, wenn der Task zum Generieren einer Momentaufnahme nicht mehr reagiert. Wenn dieses Problem auftritt, werden Sie feststellen, dass der Task zum Erstellen einer Momentaufnahme bei 95 % oder 99 % abbricht. Sie finden die Lösung für dieses Problem in diesem [VMware-KB](https://go.microsoft.com/fwlink/?linkid=2138969)-Artikel.

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Fehlermeldung: Ein interner Fehler ist aufgetreten. [Failed to consolidate the disks on VM _[Reasons]_ ] (Fehler beim Konsolidieren der Datenträger des virtuellen Computers [Ursachen])

Wenn Datenträger am Ende des Replikationszyklus konsolidiert werden, tritt ein Fehler auf. Befolgen Sie die Anweisungen im [VMware-KB](https://go.microsoft.com/fwlink/?linkid=2138970)-Artikel, indem Sie die entsprechende _Ursache_ auswählen, um das Problem zu beheben.

Die folgenden Fehler treten auf, wenn Vorgänge im Zusammenhang mit VMware-Momentaufnahmen wie das Erstellen, Löschen oder Konsolidieren von Datenträgern fehlschlagen. Befolgen Sie die Anweisungen im nächsten Abschnitt, um die Fehler zu beheben:

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Fehlermeldung: Ein interner Fehler ist aufgetreten. [Another task is already in progress] (Ein anderer Task befindet sich bereits in Bearbeitung).

Dieses Problem tritt auf, wenn im Hintergrund in Konflikt stehende Tasks für virtuelle Computer ausgeführt werden oder wenn ein Task innerhalb von vCenter Server ein Timeout verursacht. Befolgen Sie die im folgenden [VMware-KB](https://go.microsoft.com/fwlink/?linkid=2138891)-Artikel angegebenen Lösungsschritte.

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Fehlermeldung: Ein interner Fehler ist aufgetreten. [Operation not allowed in current state] (Vorgang ist im aktuellen Zustand nicht zulässig).

Dieses Problem tritt auf, wenn vCenter Server-Verwaltungs-Agents nicht mehr funktionieren. Um dieses Problem zu beheben, befolgen Sie die Lösungsschritte im folgenden [VMware-KB](https://go.microsoft.com/fwlink/?linkid=2138971)-Artikel.

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Fehlermeldung: Ein interner Fehler ist aufgetreten. [Snapshot Disk size invalid] (Größe des Momentaufnahme-Datenträgers ist ungültig).

Dies ist ein bekanntes VMware-Problem, bei dem die für die Momentaufnahme angegebene Datenträgergröße 0 (null) wird. Befolgen Sie die im [VMware-KB](https://go.microsoft.com/fwlink/?linkid=2138972)-Artikel angegebenen Lösungsschritte.

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Fehlermeldung: Ein interner Fehler ist aufgetreten. [Memory allocation failed. Out of memory] (Fehler bei der Speicherbelegung. Nicht genügend Arbeitsspeicher).

Dies geschieht, wenn der NFC-Hostpuffer nicht über genügend Arbeitsspeicher verfügt. Um dieses Problem zu beheben, müssen Sie den virtuellen Computer (Compute vMotion) auf einen anderen Host mit freien Ressourcen verlagern.

## <a name="next-steps"></a>Nächste Schritte

Fortsetzen der VM-Replikation und Ausführen einer [Testmigration](./tutorial-migrate-vmware.md#run-a-test-migration)
