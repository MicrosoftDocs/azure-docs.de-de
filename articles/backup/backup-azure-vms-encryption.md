---
title: Sichern und Wiederherstellen von verschlüsselten virtuellen Azure-Computern mit Azure Backup
description: Beschreibt, wie verschlüsselte virtuelle Azure-Computer (VMs) mit dem Azure Backup-Dienst gesichert und wiederhergestellt werden.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 893a22fb9f325625707869c8f6571d572b8f6b33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61216980"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Sichern und Wiederherstellen eines verschlüsselten virtuellen Azure-Computers

In diesem Artikel wird beschrieben, wie Sie virtuelle Azure-Computer (VMs) unter Windows oder Linux mit verschlüsselten Datenträgern mithilfe des Diensts [Azure Backup](backup-overview.md) sichern und wiederherstellen.

Wenn Sie zunächst mehr dazu erfahren möchten, wie Azure Backup mit Azure VMs interagiert, lesen Sie diese Ressourcen:

- [Überprüfen Sie](backup-architecture.md#architecture-direct-backup-of-azure-vms) die Architektur für die Azure-VM-Sicherung.
- [Erfahren Sie mehr](backup-azure-vms-introduction.md) über die Azure-VM-Sicherung und die Azure Backup-Erweiterung.

## <a name="encryption-support"></a>Verschlüsselungsunterstützung

Azure Backup unterstützt die Sicherung von Azure-VMs, deren Betriebssystem/Datenträger mit Azure Disk Encryption (ADE) verschlüsselt wurde(n). ADE verwendet BitLocker für die Verschlüsselung von Windows-VMs und die Funktion „dm-crypt“ für Linux-VMs. ADE ist in Azure Key Vault integriert, um die Verwaltung von Datenträger-Verschlüsselungsschlüsseln und Geheimnissen zu erleichtern. Key Vault-Schlüssel für die Schlüsselverschlüsselung (Key Encryption Keys, KEKs) können verwendet werden, um eine zusätzliche Sicherungsebene hinzuzufügen. Sie dient zum Verschlüsseln von Verschlüsselungsgeheimnissen, bevor sie in Key Vault geschrieben werden.

Wie in der nachstehenden Tabelle zusammengefasst, kann Azure Backup Azure-VMs mithilfe von ADE mit und ohne die Azure AD-App sichern und wiederherstellen.

**VM-Datenträgertyp** | **ADE (BEK/dm-crypt)** | **ADE und KEK**
--- | --- | ---
**Nicht verwaltet** | Ja | Ja
**Verwaltet**  | Ja | Ja

- Weitere Informationen zu [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md) und [KEKs](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Lesen Sie [Häufig gestellte Fragen](../security/azure-security-disk-encryption-faq.md) zur Datenträgerverschlüsselung für virtuelle Azure-Computer.



### <a name="limitations"></a>Einschränkungen

- Sie können verschlüsselte VMs innerhalb desselben Abonnements und derselben Region sichern und wiederherstellen.
- Azure Backup unterstützt mit eigenständigen Schlüsseln verschlüsselte VMs. Ein Schlüssel, der Teil eines Zertifikats ist, das zum Verschlüsseln eines virtuellen Computers verwendet wurde, wird derzeit nicht unterstützt.
- Sie können verschlüsselte VMs innerhalb desselben Abonnements und derselben Region als Recovery Services-Sicherungstresor sichern und wiederherstellen.
- Verschlüsselte virtuelle Computer können nicht auf Datei- oder Ordnerebene wiederhergestellt werden. Sie müssen den gesamten virtuellen Computer wiederherstellen, damit Dateien und Ordner wiederhergestellt werden.
- Beim Wiederherstellen eines virtuellen Computers können Sie die Option [Vorhandenen virtuellen Computer ersetzen](backup-azure-arm-restore-vms.md#restore-options) für verschlüsselte virtuelle Computer verwenden. Diese Option wird nur bei unverschlüsselten verwalteten Datenträgern unterstützt.




## <a name="before-you-start"></a>Vorbereitung

Führen Sie zunächst folgende Schritte aus:

1. Stellen Sie sicher, dass Sie über einen oder mehrere virtuelle [Windows](../security/azure-security-disk-encryption-windows.md)- oder [Linux](../security/azure-security-disk-encryption-linux.md)-Computer mit aktiviertem ADE verfügen.
2. Sehen Sie sich die [Unterstützungsmatrix](backup-support-matrix-iaas.md) für die Sicherung virtueller Azure-Computer an.
3. [Erstellen Sie](backup-azure-arm-vms-prepare.md#create-a-vault) einen Recovery Services-Sicherungstresor, wenn Sie noch keinen haben.
4. Wenn Sie die Verschlüsselung für VMs aktivieren, die bereits für Sicherung aktiviert wurden, müssen Sie Backup einfach Berechtigungen für den Zugriff auf den Key Vault gewähren, damit Sicherungen ohne Unterbrechung fortgesetzt werden können. [Erfahren Sie mehr](#provide-permissions) zum Zuweisen dieser Berechtigungen.

Darüber hinaus gibt es ein paar Schritte, die Sie in einigen Fällen möglicherweise ausführen müssen:

- **Installieren des VM-Agents auf dem virtuellen Computer:** Azure Backup sichert Azure-VMs durch die Installation einer Erweiterung für den Azure-VM-Agent auf dem Computer. Wenn Ihre VM aus einem Azure Marktplatz-Image erstellt wurde, ist der Agent installiert und aktiv. Wenn Sie eine benutzerdefinierte VM erstellen oder einen lokalen Computer migrieren, müssen Sie möglicherweise [den Agent manuell installieren](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Explizites Zulassen von ausgehendem Zugriff:** Im Allgemeinen müssen Sie den ausgehenden Netzwerkzugriff für eine Azure-VM nicht explizit zulassen, damit sie mit Azure Backup kommunizieren kann. Bei einigen VMs können jedoch Verbindungsprobleme auftreten. Dann wird bei einem Verbindungsversuch der Fehler **ExtensionSnapshotFailedNoNetwork** angezeigt. In diesem Fall sollten Sie [ausgehenden Zugriff explizit zulassen](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), damit die Azure Backup-Erweiterung mit öffentlichen IP-Adressen von Azure für den Sicherungsdatenverkehr kommunizieren kann.



## <a name="configure-a-backup-policy"></a>Konfigurieren einer Sicherungsrichtlinie

1. Wenn Sie noch keinen Recovery Services-Sicherungstresor erstellt haben, folgen Sie [diesen Anweisungen](backup-azure-arm-vms-prepare.md#create-a-vault).
2. Öffnen Sie den Tresor im Portal, und wählen Sie im Abschnitt **Erste Schritte** die Option **Sicherung** aus.

    ![Blatt „Sicherung“](./media/backup-azure-vms-encryption/select-backup.png)

3. Wählen Sie unter **Sicherungsziel** > **Wo wird Ihre Workload ausgeführt?** den Eintrag **Azure** aus.
4. Wählen Sie für **Was möchten Sie sichern?** die Option **Virtueller Computer** > **OK** aus.

      ![Blatt „Szenario“](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Wählen Sie unter **Sicherungsrichtlinie** > **Sicherungsrichtlinie auswählen** die Richtlinie aus, die Sie dem Tresor zuordnen möchten. Klicken Sie dann auf **OK**.
    - Eine Sicherungsrichtlinie gibt an, wann Sicherungen erstellt und wie lange sie gespeichert werden.
    - Die Details zur Standardrichtlinie werden unter dem Dropdownmenü aufgeführt.

    ![Blatt „Szenario“ öffnen](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Wenn Sie nicht die Standardrichtlinie verwenden möchten, wählen Sie **Neu erstellen** und [Benutzerdefinierte Richtlinie erstellen](backup-azure-arm-vms-prepare.md#create-a-custom-policy) aus.


7. Wählen Sie die verschlüsselten VMs, die Sie mit der ausgewählten Richtlinie sichern möchten, und dann **OK** aus.

      ![Auswählen von verschlüsselten VMs](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Wenn Sie Azure Key Vault verwenden, werden Sie auf der Tresorseite in einer Meldung informiert, dass Azure Backup schreibgeschützten Zugriff auf die Schlüssel und Geheimnisse im Key Vault benötigt.

    - Wenn diese Meldung angezeigt wird, ist keine Aktion erforderlich.

        ![Zugriff OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Wenn diese Meldung angezeigt wird, müssen Sie Berechtigungen entsprechend der Beschreibung im [Verfahren unten](#provide-permissions) festlegen.

        ![Zugriff Warnung](./media/backup-azure-vms-encryption/access-warning.png)

9. Klicken Sie auf **Sicherung aktivieren**, um die Sicherungsrichtlinie im Tresor bereitzustellen, und aktivieren Sie die Sicherung für die ausgewählten VMs.


## <a name="trigger-a-backup-job"></a>Auslösen eines Sicherungsauftrags

Die erste Sicherung wird entsprechend dem festgelegten Zeitplan ausgeführt; Sie können sie aber auch mit den folgenden Schritten sofort ausführen:

1. Klicken Sie im Tresormenü auf **Sicherungselemente**.
2. Klicken Sie unter **Sicherungselemente** auf **Virtueller Azure-Computer**.
3. Klicken Sie in der Liste **Sicherungselemente** auf das Auslassungszeichen „(...)“.
4. Klicken Sie auf **Jetzt sichern**.
5. Verwenden Sie unter **Jetzt sichern** den Kalender, um den letzten Tag zur Beibehaltung des Wiederherstellungspunkts auszuwählen. Klicken Sie dann auf **OK**.
6. Überwachen Sie die Portalbenachrichtigungen. Sie können den Auftragsstatus im Dashboard des Tresors unter **Sicherungsaufträge** > **In Bearbeitung** überwachen. Je nach Größe Ihrer VM kann das Erstellen der ersten Sicherung einige Zeit dauern.


## <a name="provide-permissions"></a>Gewähren von Berechtigungen

Azure VM benötigt schreibgeschützten Zugriff, um die Schlüssel und Geheimnisse zusammen mit den zugeordneten VMs zu sichern.

- Ihr Key Vault ist dem Azure AD-Mandanten des Azure-Abonnements zugeordnet. Wenn Sie ein **Mitgliedsbenutzer** sind, erhält Azure Backup ohne weitere Aktion Zugriff auf den Key Vault.
- Wenn Sie ein **Gastbenutzer** sind, müssen Sie Berechtigungen gewähren, damit Azure Backup auf den Schlüsseltresor zugreifen kann.

So legen Sie Berechtigungen fest:

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie nach **Schlüsseltresore**.
2. Wählen Sie den Schlüsseltresor aus, der dem verschlüsselten virtuellen Computer zugeordnet ist, den Sie sichern möchten.
3. Wählen Sie **Zugriffsrichtlinien** > **Neue hinzufügen** aus.
4. Wählen Sie **Prinzipal auswählen** aus, und geben Sie **Sicherungsverwaltung** ein.
5. Wählen Sie **Sicherungsverwaltungsdienst** > **Auswählen** aus.

    ![Auswählen des Sicherungsdiensts](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Wählen Sie unter **Zugriffsrichtlinie hinzufügen** > **Anhand einer Vorlage konfigurieren (optional)** die Option **Azure Backup** aus.
    - Unter **Schlüsselberechtigungen** und **Berechtigungen für Geheimnis** sind bereits die erforderlichen Berechtigungen angegeben.
    - Wenn Ihr virtueller Computer mithilfe von **Nur BEK** verschlüsselt ist, entfernen Sie die Auswahl für **Schlüsselberechtigungen**, da Sie Berechtigungen nur für Geheimnisse benötigen.

    ![Auswählen von Azure Backup](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Klicken Sie auf **OK**. **Sicherungsverwaltungsdienst** wird zu **Zugriffsrichtlinien** hinzugefügt.

    ![Zugriffsrichtlinien](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Klicken Sie auf **Speichern**, um Azure Backup die Berechtigungen bereitzustellen.

## <a name="restore-an-encrypted-vm"></a>Wiederherstellen eines verschlüsselten virtuellen Computers

Verschlüsselte virtuelle Computer stellen Sie wie folgt wieder her:

1. [Stellen Sie den VM-Datenträger wieder her](backup-azure-arm-restore-vms.md#restore-disks).
2. Führen Sie dann einen der folgenden Schritte aus:
    - Verwenden Sie die Vorlage, die während des Wiederherstellungsvorgangs generiert wurde, um VM-Einstellungen anzupassen und die Bereitstellung der VM auszulösen. [Weitere Informationen](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)
    - Erstellen Sie eine neue VM aus den wiederhergestellten Datenträgern mithilfe von PowerShell. [Weitere Informationen](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

## <a name="next-steps"></a>Nächste Schritte

Wenn Probleme auftreten, lesen Sie

- [Häufige Fehler](backup-azure-vms-troubleshoot.md) beim Sichern und Wiederherstellen von verschlüsselten virtuellen Azure-Computern.
- Probleme im Zusammenhang mit [Azure VM-Agent/Backup-Erweiterung](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
