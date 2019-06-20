---
title: Einrichten der Notfallwiederherstellung für virtuelle Azure-Computer in einer sekundären Region mit Azure Site Recovery
description: Erfahren Sie, wie Sie die Notfallwiederherstellung für Azure-VMs in eine andere Region mit dem Dienst „Azure Site Recovery“ durchführen.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5602298ffe60ad15d3daf52587c50357c310200c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480109"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Einrichten der Notfallwiederherstellung für Azure-VMs

Der Dienst [Azure Site Recovery](site-recovery-overview.md) verwaltet und orchestriert die Replikation, das Failover und das Failback von lokalen Computern sowie Azure-VMs und unterstützt dadurch Ihre Strategie zur Notfallwiederherstellung.

In diesem Tutorial wird erläutert, wie Sie die Notfallwiederherstellung für Azure-VMs einrichten, indem Sie eine Replikation von einer Azure-Region in einer anderen ausführen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Recovery Services-Tresors
> * Überprüfen der Zielressourceneinstellungen
> * Einrichten der ausgehenden Netzwerkkonnektivität für VMs
> * Aktivieren der Replikation für eine VM

> [!NOTE]
> Dieser Artikel enthält Anweisungen für die Bereitstellung der Notfallwiederherstellung mit den einfachsten Einstellungen. Wenn Sie sich über benutzerdefinierte Einstellungen informieren möchten, lesen Sie die Artikel im Abschnitt mit den [Anleitungen](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Überprüfen Sie [Architektur und Komponenten des Szenarios](concepts-azure-to-azure-architecture.md).
- Sehen Sie sich die [Supportanforderungen](site-recovery-support-matrix-azure-to-azure.md) an, bevor Sie beginnen.

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Erstellen Sie den Tresor in einer beliebigen Region außer der Quellregion.

1. Melden Sie sich unter [Azure-Portal](https://portal.azure.com) > **Recovery Services** an.
2. Klicken Sie auf **Ressource erstellen** > **Verwaltungstools** > **Backup & Site Recovery**.
3. Geben Sie unter **Name**einen Anzeigenamen an, mit dem der Tresor identifiziert wird. Wenn Sie mehrere Abonnements haben, wählen Sie das gewünschte aus.
4. Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine vorhandene aus. Geben Sie eine Azure-Region an. Eine Liste mit den unterstützten Regionen finden Sie in den [Preisdetails zu Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)unter „Geografische Verfügbarkeit“.
5. Um schnell über das Dashboard auf den Tresor zuzugreifen, klicken Sie auf **An Dashboard anheften** und anschließend auf **Erstellen**.

   ![Neuer Tresor](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Der neue Tresor wird dem **Dashboard** unter **Alle Ressourcen** und der Hauptseite **Recovery Services-Tresore** hinzugefügt.

## <a name="verify-target-resource-settings"></a>Überprüfen der Zielressourceneinstellungen

1. Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen von VMs in der Zielregion zulässt. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.
2. Stellen Sie sicher, dass Ihr Abonnement über ausreichend Ressourcen verfügt, um die entsprechenden VM-Größen für Ihre Quell-VMs zu unterstützen. Site Recovery wählt dieselbe oder eine möglichst ähnliche Größe für die Ziel-VM aus.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Einrichten der ausgehenden Netzwerkkonnektivität für VMs

Damit Site Recovery erwartungsgemäß funktioniert, müssen Sie die ausgehende Netzwerkkonnektivität der VMs ändern, die Sie replizieren möchten.

> [!NOTE]
> Site Recovery unterstützt die Verwendung eines Authentifizierungsproxys zur Steuerung der Netzwerkkonnektivität nicht.


### <a name="outbound-connectivity-for-urls"></a>Ausgehende Konnektivität für URLs

Lassen Sie den Zugriff auf die folgenden URLs zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden.

| **URL** | **Details** |
| ------- | ----------- |
| *.blob.core.windows.net | Ermöglicht das Schreiben von Daten aus der VM in das Cachespeicherkonto in der Quellregion |
| login.microsoftonline.com | Stellt die Autorisierung und Authentifizierung für Site Recovery-Dienst-URLs bereit. |
| *.hypervrecoverymanager.windowsazure.com | Ermöglicht die Kommunikation der VM mit Site Recovery |
| *.servicebus.windows.net | Ermöglicht es der VM, die Site Recovery-Überwachung und -Diagnosedaten zu schreiben |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Ausgehende Konnektivität für IP-Adressbereiche

Wenn Sie die ausgehende Konnektivität nicht mit URLs, sondern mithilfe von IP-Adressen steuern möchten, lassen Sie diese Adressen für IP-basierte Firewalls, Proxys oder NSG-Regeln zu.

  - [IP-Bereiche für Microsoft Azure-Rechenzentren](https://www.microsoft.com/download/details.aspx?id=41653)
  - [IP-Bereiche für Azure-Rechenzentren in Deutschland](https://www.microsoft.com/download/details.aspx?id=54770)
  - [IP-Bereiche für Azure-Rechenzentren in China](https://www.microsoft.com/download/details.aspx?id=42064)
  - [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [IP-Adressen des Site Recovery-Dienstendpunkts](https://aka.ms/site-recovery-public-ips)

Bei Verwendung einer NSG können Sie Speicherdiensttag basierend auf NSG-Regeln für die Quellregion erstellen. [Weitere Informationen](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)

## <a name="verify-azure-vm-certificates"></a>Überprüfen der Azure-VM-Zertifikate

Überprüfen Sie, ob die VMs, die Sie replizieren möchten, über die aktuellen Stammzertifikate verfügen. Andernfalls kann die VM aufgrund von Sicherheitseinschränkungen nicht bei Site Recovery registriert werden.

- Installieren Sie alle aktuellen Windows-Updates auf Windows-VMs, damit alle vertrauenswürdigen Stammzertifikate auf den Computern vorhanden sind. Führen Sie in einer nicht verbundenen Umgebung den Standardprozess für Windows Update und Zertifikatupdates in Ihrer Organisation durch.
- Befolgen Sie bei Linux-VMs die Anleitung Ihres Linux-Händlers, um die aktuellen vertrauenswürdigen Stammzertifikate und die Zertifikatssperrliste auf der VM abzurufen.

## <a name="set-permissions-on-the-account"></a>Festlegen von Berechtigungen für das Konto

Azure Site Recovery bietet drei integrierte Rollen zum Steuern von Site Recovery-Verwaltungsvorgängen.

- **Site-Recovery Contributor**: Diese Rolle verfügt über alle erforderlichen Berechtigungen zum Verwalten von Azure Site Recovery-Vorgängen in einem Tresor von Recovery Services. Ein Benutzer mit dieser Rolle kann jedoch keinen Tresor von Recovery Services erstellen oder löschen oder anderen Benutzern Zugriffsrechte zuweisen. Diese Rolle eignet sich optimal für Administratoren der Notfallwiederherstellung, die die Notfallwiederherstellung für Anwendungen oder gesamte Unternehmen aktivieren und verwalten können.

- **Site Recovery-Operator**: Diese Rolle verfügt über Berechtigungen zum Ausführen und Verwalten von Failover- und Failbackvorgängen. Ein Benutzer, der über diese Rolle verfügt, kann die Replikation nicht aktivieren oder deaktivieren, keine Tresore erstellen oder löschen, keine neue Infrastruktur registrieren oder anderen Benutzern keine Zugriffsrechte zuweisen. Diese Rolle eignet sich optimal für einen Notfallwiederherstellungsexperten, der ein Failover für einen virtuellen Computer oder Anwendungen durchführen kann, wenn er dazu vom Besitzer der Anwendung oder IT-Administratoren angewiesen wird. Nachdem der Notfall behandelt wurde, kann der DR-Experte den virtuellen Computer erneut schützen und ein Failback durchführen.

- **Site Recovery-Leser**: Diese Rolle verfügt über Berechtigungen zum Anzeigen aller Site Recovery-Verwaltungsvorgänge. Diese Rolle eignet sich optimal für eine IT-Überwachungsführungskraft, die den aktuellen Schutzstatus überwachen und Supporttickets ausstellen kann.

Erfahren Sie mehr über [integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Aktivieren der Replikation für eine VM

### <a name="select-the-source"></a>Auswählen der Quelle

1. Klicken Sie unter „Recovery Services-Tresore“ auf den Tresornamen > **+Replizieren**.
2. Wählen Sie unter **Quelle** die Option **Azure**.
3. Wählen Sie unter **Quellstandort** die Azure-Quellregion aus, in der Ihre VMs derzeit ausgeführt werden.
4. Wählen Sie das **Quellabonnement** aus, in dem die virtuellen Computer ausgeführt werden. Dies kann ein beliebiges Abonnement im gleichen Azure Active Directory-Mandanten sein, in dem sich auch Ihr Recovery Services-Tresor befindet.
5. Wählen Sie die **Quellressourcengruppe** aus, und klicken Sie auf **OK**, um die Einstellungen zu speichern.

    ![Quelle einrichten](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Auswählen der virtuellen Computer

Site Recovery ruft eine Liste der virtuellen Computer ab, die dem Abonnement und der Ressourcengruppe bzw. dem Clouddienst zugeordnet sind.

1. Wählen Sie unter **Virtuelle Computer** die VMs aus, die Sie replizieren möchten.
2. Klicken Sie auf **OK**.

### <a name="configure-replication-settings"></a>Konfigurieren der Replikationseinstellungen

Site Recovery erstellt Standardeinstellungen und Replikationsrichtlinien für die Zielregion. Sie können die Einstellungen wie gewünscht ändern.

1. Klicken Sie auf **Einstellungen**, um die Ziel- und Replikationseinstellungen anzuzeigen.
2. Um die Standardzieleinstellungen zu überschreiben, klicken Sie neben **Ressourcengruppe, Netzwerk, Speicher und Verfügbarkeitsgruppen** auf **Anpassen**.

   ![Konfigurieren von Einstellungen](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Ändern Sie die Zieleinstellungen wie in der Tabelle zusammengefasst.

    **Einstellung** | **Details**
    --- | ---
    **Zielabonnement** | Zielabonnement und Quellabonnement sind standardmäßig identisch. Klicken Sie auf „Anpassen“, um ein anderes Zielabonnement im gleichen Azure Active Directory-Mandanten auszuwählen.
    **Zielstandort** | Die Zielregion, die zur Notfallwiederherstellung verwendet wird.<br/><br/> Der Zielspeicherort sollte mit dem Speicherort des Site Recovery-Tresors übereinstimmen.
    **Zielressourcengruppe** | Die Ressourcengruppe in der Zielregion, zu der Azure-VMs nach einem Failover gehören.<br/><br/> Site Recovery erstellt standardmäßig in der Zielregion eine neue Ressourcengruppe mit dem Suffix „asr“. Der Speicherort der Zielressourcengruppe kann eine beliebige Region sein, mit Ausnahme der Region, in der Ihre virtuellen Quellcomputer gehostet werden.
    **Virtuelles Zielnetzwerk** | Das Netzwerk in der Zielregion, in dem sich Azure-VMs nach einem Failover befinden.<br/><br/> Site Recovery erstellt standardmäßig in der Zielregion ein neues virtuelles Netzwerk (und Subnetze) mit dem Suffix „asr“.
    **Cachespeicherkonten** | Site Recovery verwendet ein Speicherkonto in der Quellregion. Änderungen an Quell-VMs werden vor der Replikation am Zielspeicherort an dieses Konto gesendet.<br/><br/> Wenn Sie ein Cachespeicherkonto mit aktivierter Firewall verwenden, müssen Sie **vertrauenswürdige Microsoft-Dienste zulassen**, indem Sie die entsprechende Option auswählen. [Weitere Informationen.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Zielspeicherkonten (wenn die Quell-VM keine verwalteten Datenträger verwendet)** | Standardmäßig erstellt Site Recovery ein neues Speicherkonto in der Zielregion, um das Quell-VM-Speicherkonto zu spiegeln.<br/><br/> Aktivieren Sie die Option **Vertrauenswürdige Microsoft-Dienste zulassen**, wenn Sie ein Cachespeicherkonto mit aktivierter Firewall verwenden.
    **Verwaltete Replikatdatenträger (wenn die Quell-VM verwaltete Datenträger verwendet)** | Site Recovery erstellt standardmäßig verwaltete Replikatdatenträger in der Zielregion, um die verwalteten Datenträger der Quell-VM zu spiegeln. Dabei wird der gleiche Speichertyp (Standard oder Premium) wie für die verwalteten Datenträger der Quell-VM verwendet. Sie können nur den Datenträgertyp anpassen 
    **Zielverfügbarkeitsgruppen** | Standardmäßig erstellt Azure Site Recovery in der Zielregion eine neue Verfügbarkeitsgruppe und verwendet das Namenssuffix „asr“ für die virtuellen Computer, die zu einer Verfügbarkeitsgruppe in der Quellregion gehören. Falls bereits eine von Azure Site Recovery erstellte Verfügbarkeitsgruppe vorhanden ist, wird diese wiederverwendet.
    **Zielverfügbarkeitszonen** | Site Recovery weist in der Zielregion standardmäßig die gleiche Anzahl von Zonen zu wie in der Quellregion, sofern die Zielregion Verfügbarkeitszonen unterstützt.<br/><br/> Wenn die Zielregion keine Verfügbarkeitszonen unterstützt, werden die virtuellen Zielcomputer standardmäßig als einzelne Instanzen konfiguriert.<br/><br/> Klicken Sie auf **Anpassen**, um virtuelle Computer als Teil einer Verfügbarkeitsgruppe in der Zielregion zu konfigurieren.<br/><br/> Nach der Aktivierung der Replikation können Sie den Verfügbarkeitstyp (einzelne Instanz, Verfügbarkeitsgruppe oder Verfügbarkeitszone) nicht mehr ändern. Wenn Sie den Verfügbarkeitstyp ändern möchten, müssen Sie die Replikation deaktivieren und wieder aktivieren.

4. Um die Einstellungen für die Replikationsrichtlinie anzupassen, klicken Sie neben **Replikationsrichtlinie** auf **Anpassen**, und ändern Sie die Einstellungen wie erforderlich.

    **Einstellung** | **Details**
    --- | ---
    **Name der Replikationsrichtlinie** | Der Richtlinienname.
    **Aufbewahrungszeitraum des Wiederherstellungspunkts** | Standardmäßig behält Site Recovery Wiederherstellungspunkte 24 Stunden lang bei. Sie können einen Wert zwischen 1 und 72 Stunden konfigurieren.
    **App-konsistente Momentaufnahmenhäufigkeit** | Standardmäßig erstellt Site Recovery alle 4 Stunden eine App-konsistente Momentaufnahme. Sie können einen Wert zwischen 1 und 12 Stunden konfigurieren.<br/><br/> Eine App-konsistente Momentaufnahme ist eine Zeitpunkt-Momentaufnahme der Anwendungsdaten innerhalb des virtuellen Computers. VSS (Volume Shadow Copy Service, Volumeschattenkopie-Dienst) stellt sicher, dass Apps zum Zeitpunkt der Momentaufnahme konsistent sind.
    **Replikationsgruppe** | Wenn für Ihre Anwendung VM-übergreifende Konsistenz mehrerer virtueller Computer erforderlich ist, können Sie eine Replikationsgruppe für diese VMs erstellen. Standardmäßig sind die ausgewählten VMs nicht Teil einer Replikationsgruppe.

5. Wenn Sie VMs einer neuen oder vorhandenen Replikationsgruppe hinzufügen möchten, wählen Sie unter **Anpassen** die Option **Ja** für Multi-VM-Konsistenz aus, Klicken Sie dann auf **OK**. 

    >[!NOTE]
    >- Alle Computer in einer Replikationsgruppe verfügen beim Failover über absturz- und anwendungskonsistente Wiederherstellungspunkte.
    >- Das Aktivieren von Multi-VM-Konsistenz kann die Workloadleistung beeinträchtigen (sie ist CPU-intensiv). Sie sollte nur verwendet werden, wenn Computer dieselbe Workload ausführen und Konsistenz für mehrere Computer erforderlich ist.
    >- Eine Replikationsgruppe kann maximal 16 virtuelle Computer enthalten.
    >- Wenn Sie die Multi-VM-Konsistenz aktivieren, kommunizieren Computer in der Replikationsgruppe über den Port 20004 miteinander. Stellen Sie sicher, dass die interne Kommunikation zwischen den VMs über diesen Port nicht durch eine Firewall blockiert wird.
    >- Stellen Sie bei Linux-VMs in einer Replikationsgruppe sicher, dass der ausgehende Datenverkehr auf Port 20004 gemäß den Anweisungen für die jeweilige Linux-Version manuell geöffnet wird.



### <a name="configure-encryption-settings"></a>Konfigurieren der Verschlüsselungseinstellungen

Wenn Azure Disk Encryption (ADE) für die Quell-VM aktiviert ist, überprüfen Sie die Einstellungen.

1. Überprüfen Sie die Einstellungen:
    - **Schlüsseltresore für Datenträgerverschlüsselung**: Standardmäßig erstellt Site Recovery einen neuen Schlüsseltresor für die Datenträger-Verschlüsselungsschlüssel der Quell-VM mit dem Suffix „asr“. Wenn der Schlüsseltresor bereits vorhanden ist, wird er wiederverwendet.
    - **Schlüsseltresore für Schlüsselverschlüsselung**: Site Recovery erstellt standardmäßig einen neuen Schlüsseltresor in der Zielregion. Der Name verfügt über das Suffix „asr“ und basiert auf den Schlüsselverschlüsselungsschlüsseln der Quell-VM. Falls bereits ein von Site Recovery erstellter Schlüsseltresor vorhanden ist, wird er wiederverwendet.

2. Klicken Sie auf **Anpassen**, um benutzerdefinierte Schlüsseltresore auszuwählen.

>[!NOTE]
>Azure Site Recovery unterstützt derzeit nur Azure-VMs, auf denen Windows-Betriebssysteme ausgeführt werden und die [für die Verschlüsselung mit der Azure AD-App aktiviert sind](https://aka.ms/ade-aad-app).
>

### <a name="track-replication-status"></a>Nachverfolgen des Replikationsstatus

1. Klicken Sie unter **Einstellungen**  auf **Aktualisieren**, um den aktuellen Status abzurufen.
2. Verfolgen Sie den Fortschritt und Status wie folgt nach:
    - Verfolgen Sie den Fortschritt des Auftrags **Schutz aktivieren** unter **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge**.
    - Unter **Einstellungen** > **Replizierte Elemente** können Sie den Status der VMs und den der ersten Replikation einsehen. Klicken Sie auf die VM, um ein Drilldown auf die zugehörigen Einstellungen auszuführen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Notfallwiederherstellung für eine Azure-VM konfiguriert. Jetzt können Sie ein Notfallwiederherstellungsverfahren initiieren, um zu überprüfen, ob das Failover wie erwartet funktioniert.

> [!div class="nextstepaction"]
> [Durchführen eines Notfallwiederherstellungsverfahrens](azure-to-azure-tutorial-dr-drill.md)
