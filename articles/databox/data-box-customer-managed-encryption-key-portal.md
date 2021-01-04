---
title: Verwenden des Azure-Portals zum Verwalten von kundenseitig verwalteten Schlüsseln für Azure Data Box
description: Erfahren Sie, wie Sie über das Azure-Portal kundenseitig verwaltete Schlüssel mit Azure Key Vault für eine Azure Data Box-Instanz erstellen und verwalten. Mit kundenseitig verwalteten Schlüsseln können Sie Zugriffssteuerungen erstellen, rotieren, deaktivieren und widerrufen.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: f75907dc1fa079cebb3b80874090c658fd7b8174
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302835"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Verwenden kundenseitig verwalteter Schlüssel in Azure Key Vault für Azure Data Box

Azure Data Box schützt den Schlüssel für die Geräteentsperrung (auch als Gerätekennwort bezeichnet), der zum Sperren des Geräts über einen Verschlüsselungsschlüssel verwendet wird. Standardmäßig ist dieser Verschlüsselungsschlüssel ein von Microsoft verwalteter Schlüssel. Zur weiteren Steuerung können Sie einen kundenseitig verwalteten Schlüssel verwenden.

Die Verwendung eines kundenseitig verwalteten Schlüssels wirkt sich nicht darauf aus, wie Daten auf dem Gerät verschlüsselt werden. Sie wirkt sich nur darauf aus, wie der Schlüssel zum Entsperren des Geräts verschlüsselt ist.

Um dieses Maß an Kontrolle im gesamten Bestellvorgang beizubehalten, verwenden Sie einen kundenseitig verwalteten Schlüssel, wenn Sie Ihre Bestellung erstellen. Weitere Informationen finden Sie im [Tutorial: Bestellen von Azure Data Box](data-box-deploy-ordered.md) ausgeführt.

In diesem Artikel wird gezeigt, wie Sie einen kundenseitig verwalteten Schlüssel im [Azure-Portal](https://portal.azure.com/) für Ihre vorhandene Data Box-Bestellung aktivieren. Sie erfahren, wie Sie Schlüsseltresor, Schlüssel, Version oder Identität für Ihren aktuellen kundenseitig verwalteten Schlüssel ändern oder zur Verwendung eines von Microsoft verwalteten Schlüssels zurückkehren.

Dieser Artikel gilt sowohl für Azure Data Box- als auch Azure Data Box Heavy-Geräte.

## <a name="requirements"></a>Requirements (Anforderungen)

Der kundenseitig verwaltete Schlüssel für eine Data Box-Bestellung muss die folgenden Anforderungen erfüllen:

- Der Schlüssel muss in einer Azure Key Vault-Instanz erstellt und gespeichert werden, in der **Vorläufiges Löschen** und **Nicht endgültig löschen** aktiviert sind. Weitere Informationen finden Sie unter [Was ist der Azure-Schlüsseltresor?](../key-vault/general/overview.md). Sie können einen Schlüsseltresor und Schlüssel erstellen, während Sie die Bestellung erstellen oder aktualisieren.

- Der Schlüssel muss ein RSA-Schlüssel mit einer Größe von mindestens 2.048 sein.

## <a name="enable-key"></a>Aktivieren des Schlüssels

Um einen kundenseitig verwalteten Schlüssel für Ihre vorhandene Data Box-Bestellung zu aktivieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie zum Bildschirm **Übersicht** für Ihre Data Box-Bestellung.

    ![Übersichtsbildschirm einer Data Box-Bestellung – 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Wechseln Sie zu **Einstellungen > Verschlüsselung**, und wählen Sie **Kundenseitig verwalteter Schlüssel** aus. Wählen Sie anschließend **Schlüssel und Schlüsseltresor auswählen** aus.

    ![Option zum Auswählen des kundenseitig verwalteten Schlüssels zur Verschlüsselung](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   Auf dem Bildschirm **Schlüssel aus Azure Key Vault auswählen** wird Ihr Abonnement automatisch aufgefüllt.

 3. Für **Schlüsseltresor** können Sie einen vorhandenen Schlüsseltresor aus der Dropdownliste auswählen oder **Neu erstellen** auswählen und einen neuen Schlüsseltresor erstellen.

     ![Schlüsseltresoroptionen bei der Auswahl eines kundenseitig verwalteten Schlüssels](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Um einen neuen Schlüsseltresor zu erstellen, geben Sie das Abonnement, die Ressourcengruppe, den Schlüsseltresornamen und andere Informationen im Bildschirm **Neuen Schlüsseltresor erstellen** ein. Stellen Sie sicher, dass unter **Wiederherstellungsoptionen** die Optionen **Vorläufiges Löschen** und **Löschschutz** aktiviert sind. Wählen Sie dann **Überprüfen + erstellen** aus.

      ![Überprüfen und Erstellen einer Azure Key Vault-Instanz](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Überprüfen Sie die Informationen für Ihren Schlüsseltresor, und wählen Sie **Erstellen** aus. Warten Sie einige Minuten, bis die Erstellung des Schlüsseltresors abgeschlossen ist.

       ![Erstellen einer Azure Key Vault-Instanz mit Ihren Einstellungen](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. Auf dem Bildschirm **Schlüssel aus Azure Key Vault auswählen** können Sie einen Schlüssel auswählen, der im Schlüsseltresor vorhanden ist, oder einen neuen erstellen.

    ![Auswählen des Schlüssels aus Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Wählen Sie die Option **Neu erstellen** aus, wenn Sie einen neuen Schlüssel erstellen möchten. Sie müssen einen RSA-Schlüssel verwenden. Der Wert für die Größe kann „2048“ oder höher lauten.

    ![Erstellen eines neuen Schlüssels in Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Geben Sie einen Namen für Ihren neuen Schlüssel ein, behalten Sie ansonsten die Standardeinstellungen bei, und wählen Sie **Erstellen** aus. Sie werden benachrichtigt, dass ein Schlüssel in Ihrem Schlüsseltresor erstellt wurde.

    ![Benennen eines neuen Schlüssels](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. Für **Version** können Sie eine vorhandene Schlüsselversion aus der Dropdownliste auswählen.

    ![Auswählen der Version für einen neuen Schlüssel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Wählen Sie die Option **Neu erstellen** aus, wenn Sie eine neue Schlüsselversion generieren möchten.

    ![Öffnen eines Dialogfelds zum Erstellen einer neuen Schlüsselversion](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Wählen Sie Einstellungen für die neue Schlüsselversion und dann die Option **Erstellen** aus.

    ![Erstellen einer neuen Schlüsselversion](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. Wenn Sie einen Schlüsseltresor, Schlüssel und eine Schlüsselversion ausgewählt haben, wählen Sie **Auswählen** aus.

    ![Ein Schlüssel in einer Azure Key Vault-Instanz](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    Die **Verschlüsselungstyp**-Einstellungen zeigen Schlüsseltresor und Schlüssel an, die Sie ausgewählt haben.

    ![Schlüssel und Schlüsseltresor für einen kundenseitig verwalteten Schlüssel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Wählen Sie den Identitätstyp aus, der zum Verwalten des kundenseitig verwalteten Schlüssels für diese Ressource verwendet werden soll. Sie können die **systemseitig zugewiesene** Identität verwenden, die während des Erstellens der Bestellung generiert wurde, oder eine benutzerseitig zugewiesene Identität auswählen.

    Eine benutzerseitig zugewiesene Identität ist eine unabhängige Ressource, die Sie verwenden können, um den Zugriff auf Ressourcen zu verwalten. Weitere Informationen finden Sie unter [Arten von verwalteten Identitäten](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Auswählen des Identitätstyps](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Wählen Sie **Benutzerseitig zugewiesen** aus, um eine Benutzeridentität zuzuweisen. Wählen Sie dann **Auswählen einer Benutzeridentität** aus, und wählen Sie die verwaltete Identität aus, die Sie verwenden möchten.

    ![Auswählen einer zu verwendenden Identität](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    Sie können hier keine neue Benutzeridentität erstellen. Wie Sie eine erstellen können, erfahren Sie unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer vom Benutzer zugewiesenen verwalteten Identität über das Azure-Portal](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).

    Die ausgewählte Benutzeridentität wird in den Einstellungen unter **Verschlüsselungstyp** angezeigt.

    ![Ausgewählte Benutzeridentität in den Einstellungen unter „Verschlüsselungstyp“](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. Wählen Sie **Speichern** aus, um die aktualisierten **Verschlüsselungstyp**-Einstellungen zu speichern.

     ![Speichern Ihres kundenseitig verwalteten Schlüssels](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    Die Schlüssel-URL wird unter **Verschlüsselungstyp** angezeigt.

    ![URL des Kundenseitig verwalteten Schlüssels](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Ändern des Schlüssels

Führen Sie die folgenden Schritte aus, um Schlüsseltresor, Schlüssel und/oder Schlüsselversion für den kundenseitig verwalteten Schlüssel zu ändern, den Sie zurzeit verwenden:

1. Wechseln Sie auf dem Bildschirm **Übersicht** für Ihre Data Box-Bestellung zu **Einstellungen** > **Verschlüsselung**, und klicken Sie auf **Schlüssel** ändern.

    ![Übersichtsbildschirm einer Data Box-Bestellung mit kundenseitig verwaltetem Schlüssel – 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. Wählen Sie **Anderen Schlüsseltresor und Schlüssel auswählen** aus.

    ![Übersichtsbildschirm einer Data Box-Bestellung, Option „Anderen Schlüsseltresor und Schlüssel auswählen“](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. Im Bildschirm **Schlüssel aus Schlüsseltresor auswählen** wird das Abonnement, jedoch nicht Schlüsseltresor, Schlüssel oder Schlüsselversion angezeigt. Sie können eine der folgenden Änderungen vornehmen:

   - Wählen Sie einen anderen Schlüssel aus demselben Schlüsseltresor aus. Sie müssen den Schlüsseltresor auswählen, bevor Sie Schlüssel und Version auswählen.

   - Wählen Sie einen anderen Schlüsseltresor aus, und weisen Sie einen neuen Schlüssel zu.

   - Ändern Sie die Version für den aktuellen Schlüssel.
   
    Wenn Sie die Änderungen abgeschlossen haben, wählen Sie **Auswählen** aus.

    ![Auswählen der Verschlüsselungsoption – 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. Wählen Sie **Speichern** aus.

    ![Speichern aktualisierter Verschlüsselungseinstellungen – 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Ändern der Identität

Führen Sie die folgenden Schritte aus, um die Identität zu ändern, die zum Verwalten des Zugriffs auf den kundenseitig verwalteten Schlüssel für diese Bestellung verwendet wird:

1. Wechseln Sie auf dem Bildschirm **Übersicht** für Ihre abgeschlossene Data Box-Bestellung zu **Einstellungen** > **Verschlüsselung**.

2. Nehmen Sie eine der folgenden Änderungen vor:

     - Um zu einer anderen Benutzeridentität zu wechseln, klicken Sie auf **Andere Benutzeridentität auswählen**. Wählen Sie dann im Bereich auf der rechten Seite des Bildschirms eine andere Identität und dann **Auswählen** aus.

       ![Option zum Ändern der benutzerseitig zugewiesenen Identität für einen kundenseitig verwalteten Schlüssel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Um zur systemseitig zugewiesenen Identität zu wechseln, die während des Erstellens der Bestellung generiert wurde, wählen Sie **Systemseitig zugewiesen** bei **Identitätstyp auswählen** aus.

     ![Option zum Ändern in eine systemseitig zugewiesene Identität für einen kundenseitig verwalteten Schlüssel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. Wählen Sie **Speichern** aus.

    ![Speichern aktualisierter Verschlüsselungseinstellungen – 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Verwenden des von Microsoft verwalteten Schlüssels

Gehen Sie folgendermaßen vor, um für Ihre Bestellung von der Verwendung eines kundenseitig verwalteten Schlüssels zum von Microsoft verwalteten Schlüssel zu wechseln:

1. Wechseln Sie auf dem Bildschirm **Übersicht** für Ihre abgeschlossene Data Box-Bestellung zu **Einstellungen** > **Verschlüsselung**.

2. Wählen Sie bei **Typ auswählen** die Option **Von Microsoft verwalteter Schlüssel** aus.

    ![Übersichtsbildschirm einer Data Box-Bestellung – 5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. Wählen Sie **Speichern** aus.

    ![Speichern der aktualisierten Verschlüsselungseinstellungen für einen von Microsoft verwalteten Schlüssel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Beheben von Fehlern

Wenn Ihnen Fehler im Zusammenhang mit dem kundenseitig verwalteten Schlüssel angezeigt werden, verwenden Sie zur Problembehandlung die folgende Tabelle.

| Fehlercode| Fehlerdetails| Wiederherstellbar?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Der Hauptschlüssel konnte nicht abgerufen werden, weil der kundenseitig verwaltete Schlüssel deaktiviert ist.| Ja, durch Aktivieren der Schlüsselversion.|
| SsemUserErrorEncryptionKeyExpired| Der Hauptschlüssel konnte nicht abgerufen werden, weil der kundenseitig verwaltete Schlüssel abgelaufen ist.| Ja, durch Aktivieren der Schlüsselversion.|
| SsemUserErrorKeyDetailsNotFound| Der Hauptschlüssel konnte nicht abgerufen werden, weil der kundenseitig verwaltete Schlüssel nicht gefunden wurde.| Wenn Sie den Schlüsseltresor gelöscht haben, können Sie den kundenseitig verwalteten Schlüssel nicht wiederherstellen.  Wenn Sie den Schlüsseltresor zu einem anderen Mandanten migriert haben, finden Sie weitere Informationen unter [Ändern der Mandanten-ID des Schlüsseltresors nach einer Abonnementverschiebung](../key-vault/general/move-subscription.md). Wenn Sie den Schlüsseltresor gelöscht haben:<ol><li>Ja, wenn Sie sich noch innerhalb des Zeitraums für den Schutz vor dem endgültigen Löschen befinden, können Sie die Schritte unter [Wiederherstellen eines Schlüsseltresors](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault) ausführen.</li><li>Nein, wenn der Zeitraum für den Schutz vor dem endgültigen Löschen abgelaufen ist.</li></ol><br>Andernfalls ja: Wenn der Schlüsseltresor eine Mandantenmigration durchlaufen hat, kann er mit einem der folgenden Schritte wiederhergestellt werden: <ol><li>Stellen Sie den Schlüsseltresor im alten Mandanten wieder her.</li><li>Legen Sie `Identity = None` fest, und setzen Sie dann den Wert auf `Identity = SystemAssigned` zurück. Dadurch wird die Identität gelöscht und neu erstellt, sobald die neue Identität erstellt wurde. Gewähren Sie der neuen Identität in der Zugriffsrichtlinie des Schlüsseltresors die Berechtigungen `Get`, `Wrap` und `Unwrap`.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | Ein vom Kunden verwalteter Schlüssel wurde angewendet, aber der Schlüsselzugriff wurde nicht erteilt oder wurde widerrufen, oder der Zugriff auf den Schlüsseltresor ist aufgrund einer aktivierten Firewall nicht möglich. | Fügen Sie Ihrem Schlüsseltresor die ausgewählte Identität hinzu, um den Zugriff auf den kundenseitig verwalteten Schlüssel zu aktivieren. Wenn für den Schlüsseltresor eine Firewall aktiviert ist, wechseln Sie zu einer systemseitig zugewiesenen Identität, und fügen Sie dann einen vom Kunden verwalteten Schlüssel hinzu. Weitere Informationen finden Sie unter [Aktivieren des Schlüssels](#enable-key). |
| SsemUserErrorKeyVaultDetailsNotFound| Der Hauptschlüssel konnte nicht abgerufen werden, weil der dem kundenseitig verwalteten Schlüssel zugeordnete Schlüsseltresor nicht gefunden wurde. | Wenn Sie den Schlüsseltresor gelöscht haben, können Sie den kundenseitig verwalteten Schlüssel nicht wiederherstellen.  Wenn Sie den Schlüsseltresor zu einem anderen Mandanten migriert haben, finden Sie weitere Informationen unter [Ändern der Mandanten-ID des Schlüsseltresors nach einer Abonnementverschiebung](../key-vault/general/move-subscription.md). Wenn Sie den Schlüsseltresor gelöscht haben:<ol><li>Ja, wenn Sie sich noch innerhalb des Zeitraums für den Schutz vor dem endgültigen Löschen befinden, können Sie die Schritte unter [Wiederherstellen eines Schlüsseltresors](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault) ausführen.</li><li>Nein, wenn der Zeitraum für den Schutz vor dem endgültigen Löschen abgelaufen ist.</li></ol><br>Andernfalls ja: Wenn der Schlüsseltresor eine Mandantenmigration durchlaufen hat, kann er mit einem der folgenden Schritte wiederhergestellt werden: <ol><li>Stellen Sie den Schlüsseltresor im alten Mandanten wieder her.</li><li>Legen Sie `Identity = None` fest, und setzen Sie dann den Wert auf `Identity = SystemAssigned` zurück. Dadurch wird die Identität gelöscht und neu erstellt, sobald die neue Identität erstellt wurde. Gewähren Sie der neuen Identität in der Zugriffsrichtlinie des Schlüsseltresors die Berechtigungen `Get`, `Wrap` und `Unwrap`.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Der Hauptschlüssel konnte nicht abgerufen werden, weil der kundenseitig verwaltete Schlüssel nicht gefunden wurde.| Ja. Überprüfen Sie Folgendes: <ol><li>Der Schlüsseltresor verfügt weiterhin über die MSI in der Zugriffsrichtlinie.</li><li>Die Identität ist vom Typ „Vom System zugewiesen“.</li><li>Gewähren Sie der neuen Identität in der Zugriffsrichtlinie des Schlüsseltresors die Berechtigungen „Get“, „Wrap“ und „Unwrap“.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | Beim Hinzufügen einer „Benutzerseitig zugewiesenen Identität“ ist ein Fehler aufgetreten, weil die maximale Anzahl benutzerseitig zugewiesener Identitäten, die hinzugefügt werden können, erreicht wurde. | Wiederholen Sie den Vorgang mit weniger Benutzeridentitäten, oder entfernen Sie einige benutzerseitig zugewiesene Identitäten aus der Ressource, bevor Sie den Vorgang wiederholen. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Fehler beim Zugriff auf eine verwaltete Identität. <br> Hinweis: Dies gilt für das Szenario, in dem das Abonnement in einen anderen Mandanten verschoben wird. Der Kunde muss die Identität manuell in den neuen Mandanten verschieben. Ausführlichere Informationen finden Sie in der PFA-Mail. | Verschieben Sie die ausgewählte Identität in den neuen Mandanten, unter dem das Abonnement vorhanden ist. Weitere Informationen finden Sie unter [Aktivieren des Schlüssels](#enable-key). |
| SsemUserErrorKekUserIdentityNotFound | Es wurde ein kundenseitig verwalteter Schlüssel angewendet, aber die benutzerseitig zugewiesene Identität mit Zugriff auf den Schlüssel wurde nicht in Active Directory gefunden. <br> Hinweis: Dies gilt für den Fall, dass die Benutzeridentität aus Azure gelöscht wird.| Versuchen Sie, dem Schlüsseltresor eine andere ausgewählte benutzerseitig zugewiesene Identität hinzuzufügen, um den Zugriff auf den kundenseitig verwalteten Schlüssel zu ermöglichen. Weitere Informationen finden Sie unter [Aktivieren des Schlüssels](#enable-key). |
| SsemUserErrorUserAssignedIdentityAbsent | Der Hauptschlüssel konnte nicht abgerufen werden, weil der kundenseitig verwaltete Schlüssel nicht gefunden wurde. | Auf den kundenseitig verwalteten Schlüssel konnte nicht zugegriffen werden. Entweder wird die dem Schlüssel zugeordnete benutzerseitig zugewiesene Identität (User Assigned Identity, UAI) gelöscht, oder der UAI-Typ wurde geändert. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Fehler beim Zugriff auf eine verwaltete Identität. <br> Hinweis: Dies gilt für das Szenario, in dem das Abonnement in einen anderen Mandanten verschoben wird. Der Kunde muss die Identität manuell in den neuen Mandanten verschieben. Ausführlichere Informationen finden Sie in der PFA-Mail. | Versuchen Sie, dem Schlüsseltresor eine andere ausgewählte benutzerseitig zugewiesene Identität hinzuzufügen, um den Zugriff auf den kundenseitig verwalteten Schlüssel zu ermöglichen. Weitere Informationen finden Sie unter [Aktivieren des Schlüssels](#enable-key).|
| SsemUserErrorKeyVaultBadRequestException | Ein vom Kunden verwalteter Schlüssel wurde angewendet, aber der Schlüsselzugriff wurde nicht erteilt oder wurde widerrufen, oder der Zugriff auf den Schlüsseltresor ist aufgrund einer aktivierten Firewall nicht möglich. | Fügen Sie Ihrem Schlüsseltresor die ausgewählte Identität hinzu, um den Zugriff auf den kundenseitig verwalteten Schlüssel zu aktivieren. Wenn für den Schlüsseltresor eine Firewall aktiviert ist, wechseln Sie zu einer systemseitig zugewiesenen Identität, und fügen Sie dann einen vom Kunden verwalteten Schlüssel hinzu. Weitere Informationen finden Sie unter [Aktivieren des Schlüssels](#enable-key). |
| Allgemeiner Fehler  | Der Hauptschlüssel konnte nicht abgerufen werden.| Hierbei handelt es sich um einen generischen Fehler. Wenden Sie sich an den Microsoft-Support, um den Fehler zu beheben, und ermitteln Sie die nächsten Schritte.|

## <a name="next-steps"></a>Nächste Schritte

- [Was ist der Azure-Schlüsseltresor?](../key-vault/general/overview.md)
- [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe des Azure-Portals](../key-vault/secrets/quick-create-portal.md)
