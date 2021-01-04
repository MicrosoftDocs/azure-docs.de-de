---
title: Übersicht über die Azure Key Vault-Wiederherstellung | Microsoft-Dokumentation
description: Die Wiederherstellungsfeatures von Key Vault sollen das versehentliche oder böswillige Löschen Ihres Schlüsseltresors und der Geheimnisse, Schlüssel und Zertifikate verhindern, die im Schlüsseltresor gespeichert sind.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: ShaneBala-keyvault
ms.author: sudbalas
manager: ravijan
ms.date: 09/30/2020
ms.openlocfilehash: 86190fa307133360c411aafc070412e7d527039e
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324957"
---
# <a name="how-to-enable-soft-delete-and-purge-protection"></a>Vorläufiges Löschen und Löschschutz aktivieren

In diesem Artikel werden die beiden Wiederherstellungsfeatures vorläufiges Löschen und Löschschutz von Azure Key Vault behandelt. Dieses Dokument enthält eine Übersicht über diese Features und zeigt, wie Sie sie über das Azure-Portal, die Azure-Befehlszeilenschnittstelle und Azure PowerShell verwalten.

## <a name="what-are-soft-delete-and-purge-protection"></a>Vorläufiges Löschen und Löschschutz

Vorläufiger Löschen und Löschschutz sind zwei verschiedene Key Vault-Wiederherstellungsfeatures.
> [!IMPORTANT]
> Der Schutz durch vorläufiges Löschen muss für alle Schlüsseltresore aktiviert werden. Die Möglichkeit zum Deaktivieren des vorläufigen Löschens wird im Dezember 2020 eingestellt. Ausführliche Informationen finden Sie [**hier**](soft-delete-change.md).

**Vorläufiges Löschen** soll verhindern, dass Ihr Schlüsseltresor oder die in ihm gespeicherten Schlüssel, Geheimnisse und Zertifikate versehentlich gelöscht werden. Stellen Sie sich das vorläufige Löschen wie einen Papierkorb vor. Wenn Sie einen Schlüsseltresor oder ein Schlüsseltresorobjekt löschen, kann dieser oder dieses für eine vom Benutzer konfigurierbare Dauer (Aufbewahrungszeitraum, standardmäßig 90 Tage) wiederhergestellt werden. Schlüsseltresore, die vorläufig gelöscht wurden, können auch **bereinigt** und dadurch dauerhaft gelöscht werden. Dies ermöglicht es Ihnen, Schlüsseltresore und Schlüsseltresorobjekte mit demselben Namen neu zu erstellen. Sowohl das Wiederherstellen als auch das Löschen von Schlüsseltresoren und -objekten erfordert erweiterte Zugriffsrichtlinienberechtigungen. **Wenn das vorläufige Löschen aktiviert wurde, kann es nicht mehr deaktiviert werden.**

Beachten Sie auch, dass **Schlüsseltresornamen global eindeutig** sind. Aus diesem Grund können Sie keinen Schlüsseltresor mit dem gleichen Namen wie ein vorläufig gelöschter Schlüsseltresor erstellen. Auch die Namen von Schlüsseln, Geheimnissen und Zertifikaten sind innerhalb eines Schlüsseltresors eindeutig. Sie können kein Geheimnis, keinen Schlüssel und kein Zertifikat mit demselben Namen wie ein anderes vorläufig gelöschtes Objekt erstellen.

Der **Löschschutz** soll das Löschen von Schlüsseltresoren, Schlüsseln, Geheimnissen und Zertifikaten durch böswillige interne Benutzer verhindern. Stellen Sie sich dieses Features als einen Papierkorb mit einem Zeitschloss vor. Sie können Elemente während des konfigurierbaren Aufbewahrungszeitraums jederzeit wiederherstellen. **Sie können einen Schlüsseltresor erst dann dauerhaft löschen oder bereinigen, wenn der Aufbewahrungszeitraum abgelaufen ist.** Nach Ablauf des Aufbewahrungszeitraums wird der Schlüsseltresor oder das Schlüsseltresorobjekt automatisch bereinigt.

> [!NOTE]
> Der Löschschutz ist so konzipiert, dass er nicht durch Administratorrollen oder -berechtigungen überschrieben, deaktiviert oder umgangen werden kann. **Nach dem Aktivieren kann der Löschschutz nicht mehr deaktiviert oder überschrieben werden, auch nicht von Microsoft.** Dies bedeutet, dass Sie einen gelöschten Schlüsseltresor entweder wiederherstellen oder warten müssen, bis der Aufbewahrungszeitraum abläuft, bevor Sie den Schlüsseltresornamen wiederverwenden können.

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Überprüfen, ob vorläufiges Löschen für einen Schlüsseltresor aktiviert ist, und Aktivieren des vorläufigen Löschens

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie Ihren Schlüsseltresor aus.
1. Klicken Sie auf das Blatt „Eigenschaften“.
1. Überprüfen Sie, ob das Optionsfeld neben „Vorläufiges Löschen“ auf „Wiederherstellung dieses Tresors und der zugehörigen Objekte ermöglichen (empfohlen)“ festgelegt ist.
1. Wenn für den Schlüsseltresor vorläufiges Löschen nicht aktiviert ist, klicken Sie auf das Optionsfeld, um das vorläufige Löschen zu aktivieren, und klicken Sie dann auf „Speichern“.

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="Für Eigenschaften ist das vorläufige Löschen hervorgehoben, so wie auch der Wert, um es zu aktivieren.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Gewähren des Zugriffs auf einen Dienstprinzipal zum Bereinigen und Wiederherstellen gelöschter Geheimnisse

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie Ihren Schlüsseltresor aus.
1. Klicken Sie auf das Blatt „Zugriffsrichtlinie“.
1. Suchen Sie in der Tabelle die Zeile des Sicherheitsprinzipals, dem Sie den Zugriff gewähren möchten (oder fügen Sie einen neuen Sicherheitsprinzipal hinzu).
1. Klicken Sie auf das Dropdownmenü für Schlüssel, Zertifikate und Geheimnisse.
1. Scrollen Sie im Dropdownmenü ganz nach unten, und klicken Sie auf „Wiederherstellen“ und „Bereinigen“.
1. Sicherheitsprinzipale benötigen für die meisten Vorgänge auch die Funktionen zum Abrufen und Auflisten.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="Im linken Navigationsbereich sind die Richtlinien für den Zugriff hervorgehoben. Für die Zugriffsrichtlinien wird die Dropdownliste für geheime Positionen angezeigt und es sind vier Einträge ausgewählt: Abrufen, Auflisten, Wiederherstellen und Bereinigen.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Auflisten, Wiederherstellen oder Bereinigen vorläufig gelöschter Schlüsseltresore

1. Melden Sie sich beim Azure-Portal an.
1. Klicken Sie oben auf der Seite auf die Suchleiste.
1. Klicken Sie unter „Zuletzt verwendete Dienste“ auf „Key Vault“. Klicken Sie nicht auf einen bestimmten Schlüsseltresor.
1. Klicken Sie oben auf dem Bildschirm auf die Option „Gelöschte Tresore verwalten“.
1. Auf der rechten Seite des Bildschirms wird ein Kontextbereich geöffnet.
1. Wählen Sie Ihr Abonnement aus.
1. Wenn Ihr Schlüsseltresor vorläufig gelöscht wurde, wird er im Kontextbereich auf der rechten Seite angezeigt.
1. Wenn zu viele Tresore angezeigt werden, klicken Sie unten im Kontextbereich auf „Weitere laden“, oder rufen Sie die Ergebnisse über die Befehlszeilenschnittstelle oder PowerShell ab.
1. Wenn Sie den Tresor gefunden haben, den Sie wiederherstellen oder löschen möchten, aktivieren Sie das Kontrollkästchen neben ihm.
1. Wählen Sie die Option „Wiederherstellen“ am unteren Rand des Kontextbereichs aus, wenn Sie den Schlüsseltresor wiederherstellen möchten.
1. Wählen Sie die Option „Bereinigen“ aus, wenn Sie den Schlüsseltresor dauerhaft löschen möchten.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="Für Schlüsseltresore ist die Option zum Verwalten gelöschter Tresore hervorgehoben.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="Für „Gelöschte Schlüsseltresore verwalten“ ist der einzige aufgeführte Schlüsseltresor hervorgehoben und ausgewählt. Zudem ist die Schaltfläche „Wiederherstellen“ hervorgehoben.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Auflisten, Wiederherstellen oder Bereinigen vorläufig gelöschter Geheimnisse, Schlüssel und Zertifikate

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie Ihren Schlüsseltresor aus.
1. Wählen Sie das Blatt des Geheimnistyps aus, den Sie verwalten möchten (Schlüssel, Geheimnisse oder Zertifikate).
1. Klicken Sie oben auf dem Bildschirm auf „Gelöschte Schlüssel (Geheimnisse, Zertifikate) verwalten“.
1. Auf der rechten Seite des Bildschirms wird ein Kontextbereich angezeigt.
1. Wenn das Geheimnis, der Schlüssel oder das Zertifikat in der Liste nicht angezeigt wird, wurde es bzw. er nicht vorläufig gelöscht.
1. Wählen Sie das Geheimnis, den Schlüssel oder das Zertifikat aus, den oder das Sie verwalten möchten.
1. Wählen Sie unten im Kontextbereich eine der Optionen „Wiederherstellen“ oder „Bereinigen“ aus.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="Für „Schlüssel“ ist die Option „Gelöschte Schlüssel verwalten“ hervorgehoben.":::

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (Befehlszeilenschnittstelle)

* Überprüfen, ob vorläufiges Löschen für einen Schlüsseltresor aktiviert ist

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Aktivieren des vorläufigen Löschens für den Schlüsseltresor

    Für alle neuen Schlüsseltresore ist vorläufiges Löschen standardmäßig aktiviert. Wenn Sie derzeit über einen Schlüsseltresor verfügen, für den vorläufiges Löschen nicht aktiviert ist, können Sie es mit dem folgenden Befehl aktivieren.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Löschen eines Schlüsseltresors (wiederherstellbar, wenn vorläufiges Löschen aktiviert ist)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Auflisten aller vorläufig gelöschten Schlüsseltresore

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Wiederherstellen eines vorläufig gelöschten Schlüsseltresors

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Bereinigen eines vorläufig gelöschten Schlüsseltresors **(WARNUNG! DURCH DIESEN VORGANG WIRD DER SCHLÜSSELTRESOR DAUERHAFT GELÖSCHT.)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Aktivieren des Löschschutzes für einen Schlüsseltresor

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Zertifikate (Befehlszeilenschnittstelle)

* Gewähren des Zugriffs zum Bereinigen und Wiederherstellen von Zertifikaten

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Löschen eines Zertifikats

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Auflisten gelöschter Zertifikate

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Wiederherstellen gelöschter Zertifikate

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Bereinigen eines vorläufig gelöschten Zertifikats **(WARNUNG! DURCH DIESEN VORGANG WIRD DAS ZERTIFIKAT DAUERHAFT GELÖSCHT.)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Schlüssel (Befehlszeilenschnittstelle)

* Gewähren des Zugriffs zum Bereinigen und Wiederherstellen von Schlüsseln

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* ENTF-TASTE

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Auflisten gelöschter Schlüssel

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Wiederherstellen gelöschter Schlüssel

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Bereinigen eines vorläufig gelöschten Schlüssels **(WARNUNG! DURCH DIESEN VORGANG WIRD DER SCHLÜSSEL DAUERHAFT GELÖSCHT.)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Geheimnisse (Befehlszeilenschnittstelle)

* Gewähren des Zugriffs zum Bereinigen und Wiederherstellen von Geheimnissen

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Löschen von Geheimnissen

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Auflisten gelöschter Geheimnisse

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Wiederherstellen gelöschter Geheimnisse

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Bereinigen eines vorläufig gelöschten Geheimnisses **(WARNUNG! DURCH DIESEN VORGANG WIRD DAS GEHEIMNIS DAUERHAFT GELÖSCHT.)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Überprüfen, ob vorläufiges Löschen für einen Schlüsseltresor aktiviert ist

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Löschen von Schlüsseltresoren

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Auflisten aller vorläufig gelöschten Schlüsseltresore

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Wiederherstellen eines vorläufig gelöschten Schlüsseltresors

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Bereinigen eines vorläufig gelöschten Schlüsseltresors **(WARNUNG! DURCH DIESEN VORGANG WIRD DER SCHLÜSSELTRESOR DAUERHAFT GELÖSCHT.)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Aktivieren des Löschschutzes für einen Schlüsseltresor

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Zertifikate (PowerShell)

* Erteilen von Berechtigungen zum Wiederherstellen und Bereinigen von Zertifikaten

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Löschen eines Zertifikats

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Auflisten aller gelöschten Zertifikate in einem Schlüsseltresor

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Wiederherstellen eines Zertifikats im gelöschten Zustand

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Bereinigen eines vorläufig gelöschten Zertifikats **(WARNUNG! DURCH DIESEN VORGANG WIRD DAS ZERTIFIKAT DAUERHAFT GELÖSCHT.)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Schlüssel (PowerShell)

* Erteilen von Berechtigungen zum Wiederherstellen und Bereinigen von Schlüsseln

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Löschen eines Schlüssels

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Auflisten aller gelöschten Zertifikate in einem Schlüsseltresor

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Wiederherstellen eines vorläufig gelöschten Schlüssels

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Bereinigen eines vorläufig gelöschten Schlüssels **(WARNUNG! DURCH DIESEN VORGANG WIRD DER SCHLÜSSEL DAUERHAFT GELÖSCHT.)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Geheimnisse (PowerShell)

* Erteilen von Berechtigungen zum Wiederherstellen und Bereinigen von Geheimnissen

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Löschen eines Geheimnisses mit dem Namen „SQLPassword“

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Auflisten aller gelöschten Geheimnisse in einem Schlüsseltresor

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Wiederherstellen eines Geheimnisses im gelöschten Zustand

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Bereinigen eines Geheimnisses im gelöschten Zustand **(WARNUNG! DURCH DIESEN VORGANG WIRD DAS GEHEIMNIS DAUERHAFT GELÖSCHT.)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
