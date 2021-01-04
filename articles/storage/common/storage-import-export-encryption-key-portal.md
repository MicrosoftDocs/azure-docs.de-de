---
title: Verwenden des Azure-Portals zum Konfigurieren von kundenseitig verwalteten Schlüsseln für den Import/Export-Dienst
description: Erfahren Sie, wie Sie über das Azure-Portal kundenseitig verwaltete Schlüssel mit Azure Key Vault für den Azure Import/Export-Dienst konfigurieren. Mit von Kunden verwalteten Schlüsseln können Sie Zugriffssteuerungen erstellen, rotieren, deaktivieren und widerrufen.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: fb91a490083629101470565a630b659c090e071b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843365"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Verwenden kundenseitig verwalteter Schlüssel in Azure Key Vault für den Import/Export-Dienst

Azure Import/Export schützt die BitLocker-Schlüssel, die zum Sperren der Laufwerke verwendet werden, mit einem Verschlüsselungsschlüssel. Standardmäßig werden BitLocker-Schlüssel mit von Microsoft verwalteten Schlüsseln verschlüsselt. Um zusätzliche Kontrolle über die Verschlüsselungsschlüssel zu erhalten, können Sie auch kundenseitig verwaltete Schlüssel bereitstellen.

Kundenseitig verwaltete Schlüssel müssen in Azure Key Vault erstellt und gespeichert werden. Weitere Informationen zum Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](../../key-vault/general/overview.md)

In diesem Artikel wird gezeigt, wie Sie kundenseitig verwaltete Schlüssel mit dem Import/Export-Dienst im [Azure-Portal](https://portal.azure.com/) verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben einen Import- oder Exportauftrag gemäß den Anleitungen in folgenden Artikeln erstellt:

    - [Erstellen eines Importauftrags für Blobs](storage-import-export-data-to-blobs.md)
    - [Erstellen eines Importauftrags für Dateien](storage-import-export-data-to-files.md)
    - [Erstellen eines Exportauftrags für Blobs](storage-import-export-data-from-blobs.md)

2. Sie verfügen über eine vorhandene Azure Key Vault-Instanz mit einem Schlüssel, den Sie zum Schützen Ihres BitLocker-Schlüssels verwenden können. Informationen zum Erstellen eines Schlüsseltresors über das Azure-Portal finden Sie unter [Schnellstart: Erstellen Sie eine Azure Key Vault-Instanz mithilfe des Azure-Portals](../../key-vault/general/quick-create-portal.md).

    - Für Ihre vorhandene Key Vault-Instanz ist **Vorläufiges Löschen** und **Nicht bereinigen** festgelegt. Diese Eigenschaften sind standardmäßig nicht aktiviert. Informationen zum Aktivieren dieser Eigenschaften finden Sie in den Abschnitten **Aktivieren des vorläufigen Löschens** und **Aktivieren des Bereinigungsschutzes** in einem der folgenden Artikel:

        - [Verwenden des vorläufigen Löschens mit PowerShell](../../key-vault/general/key-vault-recovery.md)
        - [Verwenden des vorläufigen Löschens mit der CLI](../../key-vault/general/key-vault-recovery.md)
    - Der vorhandene Schlüsseltresor muss über einen RSA-Schlüssel mit einer Größe von mindestens 2048 verfügen. Weitere Informationen über Schlüssel finden Sie unter [Informationen zu Schlüsseln](../../key-vault/keys/about-keys.md).
    - Der Schlüsseltresor muss sich in derselben Region wie das Speicherkonto für Ihre Daten befinden.  
    - Wenn Sie noch nicht über eine Azure Key Vault-Instanz verfügen, können Sie diese auch inline erstellen, wie im folgenden Abschnitt beschrieben.

## <a name="enable-keys"></a>Aktivieren von Schlüsseln

Das Konfigurieren eines kundenseitig verwalteten Schlüssels für den Import/Export-Dienst ist optional. Standardmäßig verwendet der Import/Export-Dienst einen von Microsoft verwalteten Schlüssel zum Schützen des BitLocker-Schlüssels. Um vom Kunden verwaltete Schlüssel im Azure-Portal zu aktivieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie zum Blatt **Übersicht** für Ihren Importauftrag.
2. Wählen Sie im rechten Bereich die Option **Wählen Sie aus, wie Ihre BitLocker-Schlüssel verschlüsselt werden**.

    ![Auswählen der Verschlüsselungsoption](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. Auf dem Blatt **Verschlüsselung** können Sie den BitLocker-Schlüssel des Geräts anzeigen und kopieren. Unter **Verschlüsselungstyp** können Sie auswählen, wie Sie den BitLocker-Schlüssel schützen möchten. Standardmäßig wird ein von Microsoft verwalteter Schlüssel verwendet.

    ![Anzeigen des BitLocker-Schlüssels](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Sie haben die Möglichkeit, einen kundenseitig verwalteten Schlüssel anzugeben. Nachdem Sie den kundenseitig verwalteten Schlüssel ausgewählt haben, wählen Sie die Option **Schlüsseltresor und Schlüssel auswählen** aus.

    ![Auswählen eines kundenseitig verwalteten Schlüssels](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Auf dem Blatt **Schlüssel aus Azure Key Vault auswählen** wird das Abonnementfeld automatisch aufgefüllt. Für **Schlüsseltresor** können Sie einen vorhandenen Schlüsseltresor aus der Dropdownliste auswählen.

    ![Auswählen oder Erstellen einer Azure Key Vault-Instanz](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Sie können auch **Neu erstellen** auswählen, um einen neuen Schlüsseltresor zu erstellen. Geben Sie auf dem Blatt **Schlüsseltresor erstellen** die Ressourcengruppe und den Namen des Schlüsseltresors ein. Behalten Sie alle anderen Standardeinstellungen bei. Klicken Sie auf **Überprüfen + erstellen**.

    ![Erstellen einer neuen Azure Key Vault-Instanz](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Überprüfen Sie die Informationen im Zusammenhang mit Ihrem Schlüsseltresor, und wählen Sie **Erstellen** aus. Warten Sie einige Minuten, bis die Erstellung des Schlüsseltresors abgeschlossen ist.

    ![Azure Key Vault erstellen](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. Unter **Schlüssel aus Azure Key Vault auswählen** können Sie einen Schlüssel im vorhandenen Schlüsseltresor auswählen.

9. Wenn Sie einen neuen Schlüsseltresor erstellt haben, wählen Sie **Neu erstellen** aus, um einen Schlüssel zu erstellen. Der RSA-Schlüssel kann eine Größe von 2048 oder mehr haben.

    ![Erstellen eines neuen Schlüssels in Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Wenn das vorläufige Löschen und der Bereinigungsschutz beim Erstellen des Schlüsseltresors nicht aktiviert wurden, wird der Schlüsseltresor so aktualisiert, dass vorläufiges Löschen und Bereinigungsschutz aktiviert sind.

10. Geben Sie den Namen für Ihren Schlüssel an, behalten Sie ansonsten die Standardeinstellungen bei, und wählen Sie **Erstellen** aus.

    ![Erstellen eines neuen Schlüssels](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Wählen Sie die **Version** und dann **Auswählen** aus. Sie werden benachrichtigt, dass ein Schlüssel in Ihrem Schlüsseltresor erstellt wurde.

    ![Neu erstellter Schlüssel im Schlüsseltresor](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

Auf dem Blatt **Verschlüsselung** werden der Schlüsseltresor und der Schlüssel angezeigt, der als kundenseitig verwalteter Schlüssel ausgewählt wurde.

> [!IMPORTANT]
> Sie können in jeder Phase des Import-/Exportauftrags lediglich von Microsoft verwaltete Schlüssel deaktivieren und zu kundenseitig verwalteten Schlüsseln wechseln. Den kundenseitig verwalteten Schlüssel können Sie nach der Erstellung jedoch nicht deaktivieren.

## <a name="troubleshoot-customer-managed-key-errors"></a>Beheben von Fehlern beim kundenseitig verwalteten Schlüssel

Wenn Ihnen Fehler im Zusammenhang mit dem kundenseitig verwalteten Schlüssel angezeigt werden, verwenden Sie zur Problembehandlung die folgende Tabelle:

| Fehlercode     |Details     | Wiederherstellbar?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | Der Zugriff auf den kundenseitig verwalteten Schlüssel wird aufgehoben.                                                       | Ja. Überprüfen Sie Folgendes: <ol><li>Der Schlüsseltresor verfügt weiterhin über die MSI in der Zugriffsrichtlinie.</li><li>In der Zugriffsrichtlinie sind die Berechtigungen „Get“, „Wrap“ und „Unwrap“ aktiviert.</li><li>Wenn sich der Schlüsseltresor in einem VNET hinter der Firewall befindet, überprüfen Sie, ob **Vertrauenswürdige Microsoft-Dienste zulassen** aktiviert ist.</li><li>Überprüfen Sie, ob die MSI der Auftragsressource mithilfe von APIs auf `None` zurückgesetzt wurde.<br>Wenn dies der Fall ist, legen Sie den Wert erneut auf `Identity = SystemAssigned` fest. Dadurch wird die Identität für die Auftragsressource neu erstellt.<br>Nachdem die neue Identität erstellt wurde, gewähren Sie der neuen Identität in der Zugriffsrichtlinie des Schlüsseltresors die Berechtigungen `Get`, `Wrap` und `Unwrap`.</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | Der kundenseitig verwaltete Schlüssel ist deaktiviert.                                         | Ja, durch Aktivieren der Schlüsselversion     |
| CmkErrorKeyNotFound      | Der kundenseitig verwaltete Schlüssel wurde nicht gefunden. | Ja, wenn der Schlüssel noch innerhalb der Bereinigungsdauer gelöscht wurde, mithilfe von [Entfernen des Schlüssels des Schlüsseltresors rückgängig machen](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval).<br>Ansonsten: <ol><li>Ja, wenn der Kunde den Schlüssel gesichert hat und ihn wiederherstellt.</li><li>Andernfalls nein.</li></ol>
| CmkErrorVaultNotFound |Der Schlüsseltresor des kundenseitig verwalteten Schlüssels wurde nicht gefunden. |   Wenn der Schlüsseltresor gelöscht wurde:<ol><li>Ja, wenn Sie sich noch innerhalb des Zeitraums für den Schutz vor dem endgültigen Löschen befinden, können Sie die Schritte unter [Wiederherstellen eines Schlüsseltresors](../../key-vault/general/soft-delete-overview.md#key-vault-recovery) ausführen.</li><li>Nein, wenn der Zeitraum für den Schutz vor dem endgültigen Löschen abgelaufen ist.</li></ol><br>Andernfalls ja: Wenn der Schlüsseltresor zu einem anderen Mandanten migriert wurde, kann er mit einem der folgenden Schritte wiederhergestellt werden:<ol><li>Stellen Sie den Schlüsseltresor im alten Mandanten wieder her.</li><li>Legen Sie `Identity = None` fest, und setzen Sie dann den Wert auf `Identity = SystemAssigned` zurück. Dadurch wird die Identität gelöscht und neu erstellt, sobald die neue Identität erstellt wurde. Gewähren Sie der neuen Identität in der Zugriffsrichtlinie des Schlüsseltresors die Berechtigungen `Get`, `Wrap` und `Unwrap`.</li></ol>|

## <a name="next-steps"></a>Nächste Schritte

- [What is Azure Key Vault? (Was ist Azure Key Vault?)](../../key-vault/general/overview.md)