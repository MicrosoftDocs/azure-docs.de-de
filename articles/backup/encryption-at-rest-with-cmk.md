---
title: Verschlüsselung von Sicherungsdaten mit von Kunden verwalteten Schlüsseln
description: Hier erfahren Sie, wie Sie mit Azure Backup Sicherungsdaten mithilfe von kundenseitig verwalteten Schlüsseln (Customer-Managed Keys, CMK) verschlüsseln können.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 6e3eea4b5f44203b68c1263c0fb3ae843cabbe72
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895986"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Verschlüsselung von Sicherungsdaten mit von Kunden verwalteten Schlüsseln

Azure Backup ermöglicht es Ihnen, Ihre Sicherungsdaten mit von Kunden verwalteten Schlüsseln zu verschlüsseln, anstatt die standardmäßig aktivierten von der Plattform verwalteten Schlüssel zu verwenden. Die Schlüssel, die zum Verschlüsseln der Sicherungsdaten verwendet werden, müssen in [Azure Key Vault](../key-vault/index.yml) gespeichert werden.

Der Verschlüsselungsschlüssel, der zum Verschlüsseln von Sicherungen verwendet wird, kann sich von dem für die Quelle verwendeten Verschlüsselungsschlüssel unterscheiden. Die Daten werden mithilfe eines AES-256 basierten Datenverschlüsselungsschlüssels (DEK) geschützt, der wiederum mit Ihren Schlüsseln (KEK) geschützt wird. Dadurch erhalten Sie vollständige Kontrolle über die Daten und Schlüssel. Um die Verschlüsselung zu ermöglichen, muss dem Recovery Services-Tresor der Zugriff auf den Verschlüsselungsschlüssel in Azure Key Vault gewährt werden. Sie können den Schlüssel bei Bedarf ändern.

In diesem Artikel werden die folgenden Themen behandelt:

- Erstellen eines Recovery Services-Tresors
- Konfigurieren Ihres Recovery Services-Tresors zum Verschlüsseln von Sicherungsdaten mithilfe von kundenseitig verwalteten Schlüsseln
- Ausführen einer Sicherung in Tresore, die mit kundenseitig verwalteten Schlüsseln verschlüsselt wurden
- Wiederherstellen von Daten aus Sicherungen

## <a name="before-you-start"></a>Vorbereitung

- Diese Funktion ermöglicht Ihnen eine Verschlüsselung **nur bei neuen Recovery Services-Tresoren** . Eine Registrierung und der Versuch einer Registrierung von Tresoren, die vorhandene Elemente enthalten, werden nicht unterstützt.

- Nach der Aktivierung für einen Recovery Services-Tresor kann die Verschlüsselung mit kundenseitig verwalteten Schlüsseln nicht wieder auf die Verwendung plattformseitig verwalteter Schlüssel (Standardeinstellung) zurückgesetzt werden. Sie können die Verschlüsselungsschlüssel gemäß Ihren Anforderungen ändern.

- Diese Funktion **unterstützt derzeit keine Sicherung mit dem MARS-Agent** , und mit dem CMK verschlüsselte Tresore können dafür möglicherweise nicht verwendet werden. Der MARS-Agent verwendet eine Verschlüsselung, die auf einer Passphrase des Benutzers basiert. Diese Funktion unterstützt außerdem keine Sicherung von klassischen VMs.

- Dieses Feature hängt nicht mit [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) zusammen, das die gastbasierte Verschlüsselung der Datenträger eines virtuellen Computers mithilfe von BitLocker (für Windows) und DM-Crypt (für Linux) verwendet.

- Der Recovery Services-Tresor kann nur mit Schlüsseln verschlüsselt werden, die in einem Azure Key Vault gespeichert sind, der sich in der **gleichen Region** befindet. Außerdem dürfen nur **RSA 2048-Schlüssel** im Zustand **Aktiviert** als Schlüssel verwendet werden.

- Das Verschieben eines mit CMK verschlüsselten Recovery Services-Tresors in andere Ressourcengruppen und Abonnements wird derzeit nicht unterstützt.

- Diese Funktion kann derzeit nur über das Azure-Portal konfiguriert werden.

Wenn Sie Ihren Recovery Services-Tresor noch nicht erstellt und konfiguriert haben, erfahren Sie [hier](backup-create-rs-vault.md), wie Sie vorgehen können.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurieren eines Tresors mithilfe von kundenseitig verwalteten Schlüsseln

Dieser Vorgang umfasst die folgenden Schritte:

1. Aktivieren der verwalteten Identität für den Recovery Services-Tresor

1. Zuweisen von Berechtigungen zum Tresor für den Zugriff auf den Verschlüsselungsschlüssel im Azure Key Vault

1. Aktivieren des vorläufigen Löschens und des Löschschutzes im Azure Key Vault

1. Zuweisen des Verschlüsselungsschlüssels zum Recovery Services-Tresor

Alle diese Schritte müssen in der oben genannten Reihenfolge ausgeführt werden, um das gewünschte Ergebnis zu erzielen. Die einzelnen Schritte werden im Folgenden näher erläutert.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Aktivieren der verwalteten Identität für den Recovery Services-Tresor

Azure Backup verwendet die vom System zugewiesene verwaltete Identität, um den Recovery Services-Tresor für den Zugriff auf die Verschlüsselungsschlüssel zu authentifizieren, die im Azure Key Vault gespeichert sind. Führen Sie die unten aufgeführten Schritte aus, um die verwaltete Identität für den Recovery Services-Tresor zu aktivieren.

>[!NOTE]
>Nach der Aktivierung darf die verwaltete Identität **nicht** deaktiviert werden (auch nicht vorübergehend). Die Deaktivierung der verwalteten Identität kann zu inkonsistentem Verhalten führen.

1. Navigieren Sie zu Ihrem Recovery Services-Tresor und dann zu **Identität** .

    ![Identitätseinstellungen](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. Ändern Sie den **Status** in **Ein** , und klicken Sie auf **Speichern** .

1. Es wird eine Objekt-ID generiert, welche die vom System zugewiesene verwaltete Identität des Tresors darstellt.

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Zuweisen von Berechtigungen zum Recovery Services-Tresor für den Zugriff auf den Verschlüsselungsschlüssel im Azure Key Vault

Sie müssen jetzt dem Recovery Services-Tresor gestatten, auf den Azure Key Vault zuzugreifen, der den Verschlüsselungsschlüssel enthält. Dazu erlauben Sie der verwalteten Identität des Recovery Services-Tresors, auf den Key Vault zuzugreifen.

1. Navigieren Sie zu Ihrem Azure Key Vault und dann zu **Zugriffsrichtlinien** . Klicken Sie anschließend auf **+ Zugriffsrichtlinie hinzufügen** .

    ![Zugriffsrichtlinie hinzufügen](./media/encryption-at-rest-with-cmk/access-policies.png)

1. Wählen Sie unter **Schlüsselberechtigungen** die Vorgänge **Abrufen** , **Auflisten** , **Schlüssel entpacken** und **Schlüssel packen** aus. Hiermit werden die zulässigen Aktionen für den Schlüssel angegeben.

    ![Schlüsselberechtigungen zuweisen](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Wechseln Sie zu **Prinzipal auswählen** , und suchen Sie über das Suchfeld nach dem Namen oder der verwalteten Identität für den Tresor. Sobald der Tresor angezeigt wird, wählen Sie ihn und dann unten im Bereich **Auswählen** aus.

    ![Prinzipal auswählen](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Wählen Sie anschließend **Hinzufügen** aus, um die neue Zugriffsrichtlinie hinzuzufügen.

1. Wählen Sie **Speichern** aus, um die Änderungen an der Zugriffsrichtlinie von Azure Key Vault zu speichern.

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Aktivieren des vorläufigen Löschens und des Löschschutzes im Azure Key Vault

Sie müssen für den Azure Key Vault, in dem Ihr Verschlüsselungsschlüssel gespeichert ist, **vorläufiges Löschen und Löschschutz aktivieren** . Dies können Sie über die Azure Key Vault-Benutzeroberfläche ausführen, wie unten gezeigt. (Alternativ können diese Eigenschaften beim Erstellen des Key Vault festgelegt werden.) Weitere Informationen zu diesen Key Vault-Eigenschaften finden Sie [hier](../key-vault/general/soft-delete-overview.md).

![Vorläufiges Löschen und Löschschutz aktivieren](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Sie können vorläufiges Löschen und Löschschutz auch über PowerShell aktivieren, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich bei Ihrem Azure-Konto an.

    ```azurepowershell
    Login-AzAccount
    ```

1. Wählen Sie das Abonnement mit Ihrem Tresor aus.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Aktivieren von „Vorläufiges Löschen“

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Aktivieren des Löschschutzes

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>Zuweisen eines Verschlüsselungsschlüssels zum RS-Tresor

>[!NOTE]
> Überprüfen Sie die folgenden Punkte, bevor Sie fortfahren:
>
> - Alle oben genannten Schritte wurden erfolgreich ausgeführt:
>   - Die verwaltete Identität des Recovery Services-Tresors wurde aktiviert, und dieser wurden die erforderlichen Berechtigungen zugewiesen.
>   - Vorläufiges Löschen und Löschschutz sind für den Azure Key Vault aktiviert.
> - Der Recovery Services-Tresor, für den Sie die CMK-Verschlüsselung aktivieren möchten, weist **keine** geschützten oder registrierten Elemente auf.

Sobald Sie sich versichert haben, dass die genannten Bedingungen erfüllt sind, können Sie mit der Auswahl des Verschlüsselungsschlüssels für Ihren Tresor fortfahren.

So weisen Sie den Schlüssel zu

1. Navigieren Sie zu Ihrem Recovery Services-Tresor und dann zu **Eigenschaften** .

    ![Verschlüsselungseinstellungen](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Wählen Sie unter **Verschlüsselungseinstellungen** die Option **Aktualisieren** aus.

1. Wählen Sie im Bereich „Verschlüsselungseinstellungen“ **Eigenen Schlüssel verwenden** aus, und geben Sie den Schlüssel mithilfe einer der folgenden Methoden an: **Stellen Sie sicher, dass es sich bei dem Schlüssel, den Sie verwenden möchten, um einen RSA 2048-Schlüssel handelt, der den Zustand „Aktiviert“ aufweist.**

    1. Geben Sie den **Schlüssel-URI** für den Schlüssel ein, mit dem Sie die Daten in diesem Recovery Services-Tresor verschlüsseln möchten. Außerdem müssen Sie das Abonnement angeben, in dem der Azure Key Vault (der diesen Schlüssel enthält) vorhanden ist. Dieser Schlüssel-URI kann aus dem entsprechenden Schlüssel in Ihrem Azure Key Vault abgerufen werden. Vergewissern Sie sich, dass Sie den Schlüssel-URI korrekt kopiert haben. Es wird empfohlen, dass Sie die Schaltfläche **in Zwischenablage kopieren** neben dem Schlüsselbezeichner verwenden.

        ![Schlüssel-URI eingeben](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Suchen Sie über den Schlüsselauswahlbereich nach dem Schlüssel aus dem Key Vault, und wählen Sie ihn aus.

        ![Auswählen des Schlüssels aus dem Schlüsseltresor](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Wählen Sie **Speichern** aus.

1. **Verfolgen des Aktualisierungsstatus für den Verschlüsselungsschlüssel:** Sie können den Status der Schlüsselzuweisung mithilfe des **Aktivitätsprotokolls** im Recovery Services-Tresor verfolgen. Der Status sollte nach kurzer Zeit in **Erfolgreich** geändert werden. Ihr Tresor verschlüsselt nun alle Daten mit dem angegebenen Schlüssel als KEK.

    ![Verfolgen des Status mit dem Aktivitätsprotokoll](./media/encryption-at-rest-with-cmk/activity-log.png)

    ![Status „Erfolgreich“](./media/encryption-at-rest-with-cmk/status-succeeded.png)

>[!NOTE]
> Den gleichen Prozess führen Sie aus, wenn Sie den Verschlüsselungsschlüssel aktualisieren oder ändern möchten. Wenn Sie den Schlüssel aktualisieren und einen Schlüssel aus einer anderen Key Vault-Instanz verwenden möchten (der sich von dem derzeit verwendeten unterscheidet), stellen Sie Folgendes sicher:
>
> - Der Schlüsseltresor muss sich in derselben Region wie der Recovery Services-Tresor befinden.
>
> - Vorläufiges Löschen und Löschschutz sind für den Key Vault aktiviert.
>
> - Der Recovery Services-Tresor verfügt über die für den Zugriff auf den Key Vault erforderlichen Berechtigungen.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Sichern in einem Tresor, der mit kundenseitig verwalteten Schlüsseln verschlüsselt ist

Bevor Sie mit der Konfiguration des Schutzes fortfahren, sollten Sie unbedingt die folgende Checkliste durchgehen und sicherstellen, dass alle Anforderungen erfüllt sind. Dies ist aus folgendem Grund wichtig: Sobald für ein Element eine Konfiguration vorgenommen oder versucht wurde, die eine Sicherung in einem nicht mit CMK verschlüsselten Tresor vorsieht, kann die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für dieses Element nicht aktiviert werden, und es werden weiterhin plattformseitig verwaltete Schlüssel verwendet.

>[!IMPORTANT]
> Bevor Sie mit dem Konfigurieren des Schutzes fortfahren, müssen Sie die folgenden Schritte **erfolgreich** ausgeführt haben:
>
>1. Ihr Sicherungstresor wurde erstellt.
>1. Die vom System zugewiesene verwaltete Identität des Sicherungstresors wurde aktiviert.
>1. Dem Sicherungstresor wurden Berechtigungen für den Zugriff auf Verschlüsselungsschlüssel in Ihrem Key Vault zugewiesen.
>1. Vorläufiges Löschen und Löschschutz wurden für Ihren Key Vault aktiviert.
>1. Dem Sicherungstresor wurde ein gültiger Verschlüsselungsschlüssel zugewiesen.
>
>Wenn alle oben genannten Schritte bestätigt wurden, fahren Sie mit dem Konfigurieren der Sicherung fort.

Der Prozess zum Konfigurieren und Ausführen von Sicherungen für einen Recovery Services-Tresor, der mit kundenseitig verwalteten Schlüsseln verschlüsselt ist, ist identisch mit dem Prozess für einen Tresor, der plattformseitig verwaltete Schlüssel verwendet, und **die Funktionalität für den Benutzer ist die gleiche** . Dies gilt auch für die [Sicherung von virtuellen Azure-Computern](./quick-backup-vm-portal.md) sowie für die Sicherung von Workloads, die auf einem virtuellen Computer ausgeführt werden (z. B. [SAP HANA](./tutorial-backup-sap-hana-db.md)- oder [SQL Server](./tutorial-sql-backup.md)-Datenbanken).

## <a name="restoring-data-from-backup"></a>Wiederherstellen von Daten aus einer Sicherung

### <a name="vm-backup"></a>VM-Sicherung

Die im Recovery Services-Tresor gespeicherten Daten können gemäß den [hier](./backup-azure-arm-restore-vms.md) beschriebenen Schritten wiederhergestellt werden. Bei der Wiederherstellung aus einem Recovery Services-Tresor, der mit kundenseitig verwalteten Schlüsseln verschlüsselt wurde, können Sie die wiederhergestellten Daten mit einem Datenträgerverschlüsselungssatz (Disk Encryption Set, DES) verschlüsseln.

#### <a name="restoring-vm--disk"></a>Wiederherstellung von VMs/Datenträgern

1. Wenn Sie einen Datenträger oder eine VM aus einem Momentaufnahmen-Wiederherstellungspunkt wiederherstellen, werden die wiederhergestellten Daten mit dem DES verschlüsselt, der zum Verschlüsseln der Datenträger der virtuellen VM verwendet wurde.

1. Beim Wiederherstellen von Datenträgern/VMs von einem Wiederherstellungspunkt mit dem Wiederherstellungstyp „Tresor“ können Sie auswählen, ob die wiederhergestellten Daten mit dem zum Zeitpunkt der Wiederherstellung angegebenen DES verschlüsselt werden sollen. Alternativ können Sie die Wiederherstellung der Daten ohne Angabe eines DES fortsetzen. In diesem Fall werden sie mit von Microsoft verwalteten Schlüsseln verschlüsselt.

Sie können den wiederhergestellten Datenträger/virtuellen Computer nach Abschluss der Wiederherstellung verschlüsseln, unabhängig davon, welche Option Sie beim Starten der Wiederherstellung ausgewählt haben.

![Wiederherstellungspunkte](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Auswählen eines Datenträgerverschlüsselungssatzes beim Wiederherstellen vom Tresor-Wiederherstellungspunkt

Der Datenträgerverschlüsselungssatz wird im Bereich „Wiederherstellung“ unter „Verschlüsselungseinstellungen“ angegeben, wie unten dargestellt:

1. Wählen Sie für die **Verschlüsselung der Datenträger mit Ihrem Schlüssel** die Option **Ja** aus.

1. Wählen Sie in der Dropdownliste den DES aus, den Sie für die wiederhergestellten Datenträger verwenden möchten. **Stellen Sie sicher, dass Sie auf den DES zugreifen können.**

>[!NOTE]
>Wenn Sie eine VM wiederherstellen, die Azure Disk Encryption verwendet, können Sie bei der Wiederherstellung keinen DES auswählen.

![Verschlüsseln des Datenträgers mit Ihrem Schlüssel](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

#### <a name="restoring-files"></a>Wiederherstellen von Dateien

Beim Ausführen einer Dateiwiederherstellung werden die wiederhergestellten Daten mit dem Schlüssel verschlüsselt, der zum Verschlüsseln des Zielspeicherorts verwendet wurde.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Wiederherstellen von SAP HANA-/SQL-Datenbanken auf Azure-VMs

Beim Wiederherstellen einer gesicherten SAP HANA-/SQL-Datenbank, die auf einer Azure-VM ausgeführt wird, werden die wiederhergestellten Daten mit dem Verschlüsselungsschlüssel verschlüsselt, der am Zielspeicherort verwendet wird. Dabei kann es sich um einen kundenseitig verwalteten Schlüssel oder einen plattformseitig verwalteten Schlüssel handeln, der zum Verschlüsseln der Datenträger der VM verwendet wurde.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Kann ich einen vorhandenen Sicherungstresor mit kundenseitig verwalteten Schlüsseln verschlüsseln?

Nein, die CMK-Verschlüsselung kann nur für neue Tresore aktiviert werden. Daher dürfen in dem Tresor noch nie Elemente geschützt worden sein. Es darf noch nicht einmal versucht worden sein, Elemente im Tresor zu schützen, bevor die Verschlüsselung mit kundenseitig verwalteten Schlüsseln aktiviert wird.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Ich habe versucht, ein Element in meinem Tresor zu schützen, dabei ist jedoch ein Fehler aufgetreten, und der Tresor enthält immer noch keine geschützten Elemente. Kann ich die CMK-Verschlüsselung für diesen Tresor aktivieren?

Nein, es darf in der Vergangenheit nicht versucht worden sein, Elemente im Tresor zu schützen.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Ich verfüge über einen Tresor, der die CMK-Verschlüsselung verwendet. Kann ich später die Verschlüsselung mit plattformseitig verwalteten Schlüsseln wiederherstellen, selbst wenn ich Sicherungselemente im Tresor geschützt habe?

Nein, wenn Sie die CMK-Verschlüsselung aktiviert haben, ist ein Wechsel zurück zu plattformseitig verwalteten Schlüsseln nicht möglich. Sie können die verwendeten Schlüssel gemäß Ihren Anforderungen ändern.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Gilt die CMK-Verschlüsselung für Azure Backup auch für Azure Site Recovery?

Nein, in diesem Artikel wird nur die Verschlüsselung von Sicherungsdaten behandelt. Für Azure Site Recovery müssen Sie die in diesem Dienst verfügbare Eigenschaft separat festlegen.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Ich habe einen der Schritte in diesem Artikel versäumt und das Schützen meiner Datenquelle fortgesetzt. Kann ich die CMK-Verschlüsselung trotzdem verwenden?

Wenn Sie die Schritte im Artikel nicht befolgen und weiterhin Elemente schützen, kann dies dazu führen, dass die Verschlüsselung mit kundenseitig verwalteten Schlüsseln bei diesem Tresor nicht möglich ist. Es wird daher empfohlen, sich auf [diese Checkliste](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) zu beziehen, bevor Sie mit dem Schützen von Elementen fortfahren.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>Erhöhen sich bei Verwendung der CMK-Verschlüsselung die Kosten für meine Sicherungen?

Wenn Sie die CMK-Verschlüsselung für die Sicherung verwenden, fallen keine zusätzlichen Kosten an. Es können jedoch weiterhin Kosten für die Verwendung des Azure Key Vault anfallen, in dem Ihr Schlüssel gespeichert ist.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Sicherheitsfeatures in Azure Backup](security-overview.md)
