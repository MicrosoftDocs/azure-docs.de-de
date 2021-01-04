---
title: Datenverschlüsselung – Azure-Portal – Azure Database for MySQL
description: Erfahren Sie, wie Sie über das Azure-Portal die Datenverschlüsselung für Azure Database for MySQL einrichten und verwalten.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00670746c1686bca354adc989ddce6c9dd336491
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519058"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Datenverschlüsselung für Azure Database for MySQL über das Azure-Portal

Erfahren Sie, wie Sie über das Azure-Portal die Datenverschlüsselung für Azure Database for MySQL einrichten und verwalten.

## <a name="prerequisites-for-azure-cli"></a>Voraussetzungen für die Azure-Befehlszeilenschnittstelle

* Sie müssen über ein Azure-Abonnement verfügen und ein Administrator für dieses Abonnement sein.
* Erstellen Sie in Azure Key Vault einen Schlüsseltresor und einen Schlüssel, der als vom Kunden verwalteter Schlüssel verwendet werden soll.
* Der Schlüsseltresor muss die folgenden Eigenschaften aufweisen, damit er für vom Kunden verwaltete Schlüssel verwendet werden kann:
  * [Vorläufiges Löschen](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Löschschutz](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * Auf 90 Tage festgelegte Datenaufbewahrung
  
    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* Der Schlüssel muss die folgenden Attribute aufweisen, damit er als vom Kunden verwalteter Schlüssel verwendet werden kann:
  * Kein Ablaufdatum
  * Nicht deaktiviert
  * Ausführen der Vorgänge **get**, **wrap** und **unwrap**
  * Das recoverylevel-Attribut ist auf **Recoverable** festgelegt. (Dafür muss vorläufiges Löschen mit einer Beibehaltungsdauer von 90 Tagen aktiviert sein.)
  * Bereinigungsschutz aktiviert

Sie können die oben genannten Attribute des Schlüssels mit dem folgenden Befehl überprüfen:

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>Festlegen der richtigen Berechtigungen für Schlüsselvorgänge

1. Wählen Sie in Key Vault **Zugriffsrichtlinien** > **Zugriffsrichtlinie hinzufügen** aus.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Screenshot von Key Vault mit hervorgehobenen Optionen „Zugriffsrichtlinien“ und „Zugriffsrichtlinie hinzufügen“":::

2. Wählen Sie **Schlüsselberechtigungen** und anschließend **Abrufen**, **Packen** und **Entpacken** aus. Wählen Sie dann den **Prinzipal** aus (der Name des MySQL-Servers). Wenn Ihr Serverprinzipal nicht in der Liste der vorhandenen Prinzipale enthalten ist, müssen Sie ihn registrieren. Wenn Sie erstmalig versuchen, die Datenverschlüsselung einzurichten, und der Vorgang mit einem Fehler abgebrochen wird, werden Sie aufgefordert, Ihren Serverprinzipal zu registrieren.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Zugriffsrichtlinienübersicht":::

3. Wählen Sie **Speichern** aus.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Festlegen der Datenverschlüsselung für Azure Database for MySQL

1. Wählen Sie in Azure Database for MySQL die Option **Datenverschlüsselung** aus, um den vom Kunden verwalteten Schlüssel einzurichten.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Screenshot von Azure Database for MySQL mit hervorgehobener Option „Datenverschlüsselung“":::

2. Sie können entweder einen Schlüsseltresor und ein Schlüsselpaar auswählen oder einen Schlüsselbezeichner eingeben.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Screenshot von Azure Database for MySQL mit hervorgehobenen Optionen zur Datenverschlüsselung":::

3. Wählen Sie **Speichern** aus.

4. Starten Sie den Server neu, um sicherzustellen, dass alle Dateien (einschließlich temporäre Dateien) vollständig verschlüsselt werden.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Verwenden von Datenverschlüsselung für Wiederherstellungs- oder Replikatserver

Nachdem Azure Database for MySQL mit einem vom Kunden verwalteten Schlüssel verschlüsselt wurde, der in Key Vault gespeichert ist, wird jede neu erstellte Kopie des Servers ebenfalls verschlüsselt. Sie können diese neue Kopie entweder durch einen lokalen Wiederherstellungsvorgang, einen Geowiederherstellungsvorgang oder durch einen Replikationsvorgang (lokal/regionsübergreifend) erstellen. Sie können für einen verschlüsselten MySQL-Server also die folgenden Schritte ausführen, um einen verschlüsselten wiederhergestellten Server zu erstellen.

1. Wählen Sie auf Ihrem Server **Übersicht** > **Wiederherstellung** aus.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Screenshot von Azure Database for MySQL mit hervorgehobenen Optionen „Übersicht“ und „Wiederherstellung“":::

   Wählen Sie alternativ für einen replikationsfähigen Server unter **Einstellungen** die Option **Replikation** aus.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="Screenshot von Azure Database for MySQL mit hervorgehobener Option „Replikation“":::

2. Nachdem der Wiederherstellungsvorgang abgeschlossen wurde, ist der neu erstellte Server mit dem Schlüssel des primären Servers verschlüsselt. Allerdings sind die Features und Optionen auf dem Server deaktiviert, und auf den Server kann nicht zugegriffen werden. Da der Identität des neuen Servers noch keine Berechtigung für den Zugriff auf den Schlüsseltresor erteilt wurde, wird auf diese Weise jegliche Datenbearbeitung verhindert.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Screenshot von Azure Database for MySQL mit hervorgehobenem Status „Kein Zugriff“":::

3. Um den Server zugänglich zu machen, überprüfen Sie den Schlüssel auf dem wiederhergestellten Server erneut. Wählen Sie dazu **Datenverschlüsselung** > **Schlüssel erneut überprüfen** aus.

   > [!NOTE]
   > Beim ersten Versuch, den Schlüssel erneut zu überprüfen, tritt ein Fehler auf, da dem Dienstprinzipal des neuen Servers Zugriff auf den Schlüsseltresor gewährt werden muss. Um den Dienstprinzipal zu generieren, wählen Sie **Schlüssel erneut überprüfen** aus. Dies führt zwar zu einem Fehler, doch der Dienstprinzipal wird generiert. Führen Sie anschließend [diese Schritte](#set-the-right-permissions-for-key-operations) weiter oben in diesem Artikel aus.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Screenshot von Azure Database for MySQL mit hervorgehobenem Schritt zur erneuten Überprüfung des Schlüssels":::

   Sie müssen dem Schlüsseltresor Zugriff auf den neuen Server gewähren.

4. Nachdem Sie den Dienstprinzipal registriert haben, müssen Sie den Schlüssel erneut überprüfen, damit der Server wieder normal funktioniert.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Screenshot von Azure Database for MySQL mit wiederhergestellter Funktionalität":::

## <a name="next-steps"></a>Nächste Schritte

 Weitere Informationen zur Datenverschlüsselung finden Sie unter [Azure Database for MySQL-Datenverschlüsselung mit einem vom Kunden verwalteten Schlüssel](concepts-data-encryption-mysql.md).
