---
title: 'Azure Key Vault: Verwenden des vorläufigen Löschens mit PowerShell'
description: Beispiele für Anwendungsfälle für vorläufiges Löschen mit PowerShell-Codeausschnitten
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: mbaldwin
ms.openlocfilehash: ecc87e03a80ce10bedbe26b3ebb452ec704eefcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461364"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Verwenden des vorläufigen Löschens in Key Vault mit PowerShell

Das Azure Key Vault-Feature für vorläufiges Löschen ermöglicht die Wiederherstellung gelöschter Tresore und Tresorobjekte. Insbesondere werden bei vorläufigem Löschen die folgenden Szenarien behandelt:

- Unterstützung für das wiederherstellbare Löschen von Schlüsseltresoren
- Unterstützung für das wiederherstellbare Löschen von Key Vault-Objekten, Schlüsseln, Geheimnissen und Zertifikaten

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 oder höher: Sie müssen Azure PowerShell installieren und Ihrem Azure-Abonnement zuweisen, sofern noch nicht geschehen. Informationen dazu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Eine veraltete Version unserer Key Vault PowerShell-Ausgabeformatierungsdatei wurde **möglicherweise** anstelle der richtigen Version in Ihre Umgebung geladen. Es ist eine aktualisierte Version von PowerShell mit den erforderlichen Korrekturen bei der Ausgabeformatierung vorgesehen. Dieses Thema wird dann entsprechend aktualisiert. Zur Umgehung dieses Formatierungsproblems sollten Sie zurzeit folgendermaßen vorgehen:
> - Wenn Sie feststellen, dass die zum vorläufigen Löschen fähige Eigenschaft, die in diesem Thema beschrieben wird, nicht angezeigt wird, verwenden Sie die folgende Abfrage: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Spezifische Key Vault-Referenzinformationen für PowerShell finden Sie in der [PowerShell-Referenz für Azure Key Vault](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Key Vault-Vorgänge werden wie folgt separat über RBAC-Berechtigungen (Role-Based Access Control, rollenbasierte Zugriffssteuerung) verwaltet:

| Vorgang | BESCHREIBUNG | Benutzerberechtigung |
|:--|:--|:--|
|Auflisten|Listet gelöschte Schlüsseltresore auf.|Microsoft.KeyVault/deletedVaults/read|
|Wiederherstellen|Stellt einen gelöschten Schlüsseltresor wieder her.|Microsoft.KeyVault/vaults/write|
|Bereinigen|Entfernt einen gelöschten Schlüsseltresor und seine Inhalte endgültig.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Weitere Informationen zu Berechtigungen und Zugriffssteuerung finden Sie unter [Schützen einer Key Vault-Instanz](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Aktivieren des vorläufigen Löschens

Sie können das vorläufige Löschen aktivieren, im die Wiederherstellung eines gelöschten Schlüsseltresors oder eines in einem Schlüsseltresor gespeicherten Objekts zu ermöglichen.

### <a name="existing-key-vault"></a>Vorhandener Schlüsseltresor

Für einen vorhandenen Schlüsseltresor mit dem Namen „ContosoVault“ aktivieren Sie vorläufiges Löschen wie folgt: 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Neuer Schlüsseltresor

Für einen neuen Schlüsseltresor wird vorläufiges Löschen bei der Erstellung aktiviert. Dazu wird das Flag für die Aktivierung des vorläufigen Löschens zum Erstellungsbefehl hinzugefügt.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Überprüfen der Aktivierung des vorläufigen Löschens

Um sicherzustellen, dass für einen Schlüsseltresor vorläufiges Löschen aktiviert ist, führen Sie den *show*-Befehl aus, und suchen Sie nach dem Attribut „Soft Delete Enabled?“ Attribut:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Löschen eines mit vorläufigem Löschen geschützten Schlüsseltresors

Der Befehl zum Löschen eines Schlüsseltresors ändert das Verhalten, je nachdem, ob das vorläufige Löschen aktiviert ist.

> [!IMPORTANT]
>Wenn Sie den folgenden Befehl für einen Schlüsseltresor ausführen, für den vorläufiges Löschen nicht aktiviert ist, werden dieser Schlüsseltresor und sein gesamter Inhalt ohne Optionen für die Wiederherstellung endgültig gelöscht.

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Schützen der Schlüsseltresore mit vorläufigem Löschen

Vorläufiges Löschen ist aktiviert:

- Ein gelöschter Schlüsseltresor wird aus der Ressourcengruppe entfernt und in einem reservierten Namespace abgelegt, der dem Speicherort seiner Erstellung zugeordnet ist. 
- Auf gelöschte Objekte, beispielsweise Schlüssel, Geheimnisse und Zertifikate, kann nicht zugegriffen werden. Das ändert sich nicht, solange sich ihr Schlüsseltresor im gelöschten Zustand befindet. 
- Der DNS-Name für einen gelöschten Schlüsseltresor ist reserviert, sodass kein neuer Schlüsseltresor mit gleichem Namen erstellt werden kann.  

Sie können Schlüsseltresore im gelöschten Zustand für Ihr Abonnement mit dem folgenden Befehl anzeigen:

```powershell
Get-AzKeyVault -InRemovedState 
```

- Anhand des Felds *ID* kann beim Wiederherstellen oder Bereinigen die Ressource ermittelt werden. 
- *Ressourcen-ID* ist die ursprüngliche Ressourcen-ID dieses Tresors. Da sich dieser Schlüsseltresor nun in einem gelöschten Zustand befindet, ist keine Ressource mit dieser Ressourcen-ID vorhanden. 
- Im Feld *Geplantes Datum für die Bereinigung* ist angegeben, wann der Tresor endgültig gelöscht wird, wenn keine Aktion ausgeführt wird. Die Standardaufbewahrungsdauer, die zum Berechnen von *Scheduled Purge Date* (Geplantes Datum für die Bereinigung) verwendet wird, ist 90 Tage.

## <a name="recovering-a-key-vault"></a>Wiederherstellen eines Schlüsseltresors

Um einen Schlüsseltresor wiederherzustellen, müssen Sie den Schlüsseltresornamen, die Ressourcengruppe und den Speicherort angeben. Notieren Sie sich den Speicherort und die Ressourcengruppe des gelöschten Schlüsseltresors, da Sie diese Angaben für die Wiederherstellung benötigen.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Wenn ein Schlüsseltresor wiederhergestellt wird, wird eine neue Ressource mit der ursprünglichen Ressourcen-ID des Schlüsseltresors erstellt. Wenn die ursprüngliche Ressourcengruppe entfernt wird, muss vor dem Versuch der Wiederherstellung eine mit dem gleichen Namen erstellt werden.

## <a name="deleting-and-purging-key-vault-objects"></a>Löschen und Bereinigen von Schlüsseltresorobjekten

Der folgende Befehl löscht den Schlüssel „ContosoFirstKey“ in einem Schlüsselspeicher namens „ContosoVault“, bei dem das vorläufige Löschen aktiviert ist:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Wenn für einen Schlüsseltresor vorläufiges Löschen aktiviert ist und ein Schlüssel gelöscht wird, sieht es weiterhin so aus, als ob der Schlüssel gelöscht wurde – außer beim expliziten Auflisten oder Abrufen gelöschter Schlüssel. Bei den meisten Vorgängen für Schlüssel im gelöschten Zustand tritt ein Fehler auf, außer beim Auflisten, Wiederherstellen oder Bereinigen eines gelöschten Schlüssels. 

Der folgende Befehl listet gelöschte Schlüssel im Schlüsseltresor „ContosoVault“ auf:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Übergangsstatus 

Wenn Sie einen Schlüssel in einem Schlüsseltresor löschen, für den vorläufiges Löschen aktiviert ist, dauert es unter Umständen einige Sekunden, bis der Übergang abgeschlossen ist. Während dieses Übergangs sieht es möglicherweise so aus, als ob sich der Schlüssel nicht im aktiven oder gelöschten Zustand befindet. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Verwenden des vorläufigen Löschens mit Schlüsseltresorobjekten

Wie bei Schlüsseltresoren verbleiben gelöschte Schlüssel, Geheimnisse oder Zertifikate 90 Tage lang im gelöschten Zustand, es sei denn, Sie stellen sie wieder her oder bereinigen sie. 

#### <a name="keys"></a>Schlüssel

So stellen Sie einen vorläufig gelöschten Schlüssel wieder her:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

So löschen Sie einen vorläufig gelöschten Schlüssel dauerhaft (auch als Bereinigung bezeichnet):

> [!IMPORTANT]
> Bei der Bereinigung eines Schlüssels wird er endgültig gelöscht. Das bedeutet, dass er nicht wiederhergestellt werden kann. 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

Den Aktionen zum **Wiederherstellen** und **Bereinigen** sind in einer Schlüsseltresor-Zugriffsrichtlinie eigene Berechtigungen zugewiesen. Damit ein Benutzer oder Dienstprinzipal eine Aktion zum **Wiederherstellen** oder **Bereinigen** ausführen kann, muss er über die entsprechende Berechtigung für diesen Schlüssel oder das Geheimnis verfügen. Die Berechtigung zum **Bereinigen** wird standardmäßig nicht zur Zugriffsrichtlinie eines Schlüsseltresors hinzugefügt, wenn mit der Verknüpfung „Alle“ alle Berechtigungen gewährt werden. Sie müssen die Berechtigung zum **Bereinigen** explizit gewähren. 

#### <a name="set-a-key-vault-access-policy"></a>Festlegen einer Schlüsseltresor-Zugriffsrichtlinie

Der folgende Befehl erteilt user@contoso.com die Berechtigung zum Ausführen verschiedener Vorgänge für Schlüssel in *ContosoVault*, einschließlich **Bereinigen**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Für einen vorhandenen Schlüsseltresor, für den gerade vorläufiges Löschen aktiviert wurde, besitzen Sie unter Umständen keine Berechtigungen zum **Wiederherstellen** und **Bereinigen**.

#### <a name="secrets"></a>Geheimnisse

Genauso wie Schlüssel werden auch geheime Schlüssel mit ihren eigenen Befehlen verwaltet:

- Löschen eines Geheimnisses mit dem Namen „SQLPassword“: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Auflisten aller gelöschten Geheimnisse in einem Schlüsseltresor: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Wiederherstellen eines Geheimnisses im gelöschten Zustand: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Bereinigen eines Geheimnisses im gelöschten Zustand: 

  > [!IMPORTANT]
  > Bei der Bereinigung eines Geheimnisses wird es endgültig gelöscht. Das bedeutet, dass es nicht wiederhergestellt werden kann.

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Bereinigen eines mit vorläufigem Löschen geschützten Schlüsseltresors

> [!IMPORTANT]
> Bei der Bereinigung eines Schlüsseltresors oder eines seiner enthaltenen Objekte wird das Element endgültig gelöscht. Das bedeutet, dass es nicht wiederhergestellt werden kann.

Die Bereinigungsfunktion wird verwendet, um ein Schlüsseltresorobjekt oder einen ganzen Schlüsseltresor, der zuvor vorläufig gelöscht war, dauerhaft zu löschen. Wie im vorherigen Abschnitt gezeigt, können Objekte, die in einem Schlüsseltresor mit aktiviertem vorläufigem Löschen gespeichert werden, mehrere Zustände durchlaufen:
- **Aktiv**: Vor dem Löschen
- **Vorläufig gelöscht**: Nach dem Löschen, das Objekt kann aufgelistet und wieder in den aktiven Zustand zurückversetzt werden.
- **Dauerhaft gelöscht**: Nach der Bereinigung, das Objekt kann nicht wiederhergestellt werden.


Das Gleiche gilt für Schlüsseltresore. Um vorläufig gelöschte Schlüsseltresore und deren Inhalt dauerhaft zu löschen, müssen Sie den Schlüsseltresor selbst bereinigen.

### <a name="purging-a-key-vault"></a>Bereinigen eines Schlüsseltresors

Wenn ein Schlüsseltresor bereinigt wird, wird sein gesamter Inhalt, d.h. Schlüssel, Geheimnisse und Zertifikate, endgültig gelöscht. Verwenden Sie zum Bereinigen eines vorläufig gelöschten Schlüsseltresors den Befehl `Remove-AzKeyVault` mit der Option `-InRemovedState`. Geben Sie dabei den Speicherort des gelöschten Schlüsseltresors mit dem Argument `-Location location` an. Den Speicherort eines gelöschten Tresor ermitteln Sie mit dem Befehl `Get-AzKeyVault -InRemovedState`.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Berechtigungen zum Bereinigen erforderlich
- Zum Bereinigen eines gelöschten Schlüsseltresors benötigt der Benutzer eine RBAC-Berechtigung für den Vorgang *Microsoft.KeyVault/locations/deletedVaults/purge/action*. 
- Zum Auflisten eines gelöschten Schlüsseltresors benötigt der Benutzer eine RBAC-Berechtigung für den Vorgang *Microsoft.KeyVault/deletedVaults/read*. 
- Standardmäßig besitzt nur ein Abonnementadministrator diese Berechtigungen. 

### <a name="scheduled-purge"></a>Geplante Bereinigung

Beim Auflisten Ihrer gelöschten Schlüsseltresorobjekte wird angezeigt, wann ihre Bereinigung durch Key Vault geplant ist. Im Feld *Geplantes Datum für die Bereinigung* ist angegeben, wann ein Schlüsseltresorobjekt endgültig gelöscht wird, wenn keine Aktion ausgeführt wird. Die Aufbewahrungsdauer für ein gelöschtes Schlüsseltresorobjekt ist standardmäßig 90 Tage.

>[!IMPORTANT]
>Ein Tresorobjekt, dessen Bereinigung auf der Grundlage des Felds *Scheduled Purge Date* (Geplantes Datum für Bereinigung) veranlasst wurde, wird endgültig gelöscht. Es kann nicht wiederhergestellt werden.

## <a name="enabling-purge-protection"></a>Aktivieren des Bereinigungsschutzes

Wenn der Bereinigungsschutz aktiviert ist, kann ein Tresor oder ein Objekt im gelöschten Zustand nicht endgültig gelöscht werden, bis die Aufbewahrungsdauer von 90 Tagen abgelaufen ist. Solch ein Tresor oder Objekt kann noch wiederhergestellt werden. Dieses Feature versichert zusätzlich, dass ein Tresor oder ein Objekt nie dauerhaft gelöscht werden kann, bevor die Aufbewahrungsdauer abgelaufen ist.

Sie können den Bereinigungsschutz nur aktivieren, wenn auch das vorläufige Löschen aktiviert ist. 

Verwenden Sie das Cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0), um beim Erstellen eines Tresors sowohl das vorläufige Löschen als auch den Bereinigungsschutz zu aktivieren:

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Verwenden Sie die Cmdlets [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0) und [Set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0), um einem vorhandenen Tresor einen Bereinigungsschutz hinzuzufügen (für den bereits das vorläufige Löschen aktiviert ist):

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Weitere Ressourcen

- Eine Übersicht über das Feature für vorläufiges Löschen finden Sie unter [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](key-vault-ovw-soft-delete.md).
- Eine allgemeine Übersicht über die Nutzung von Azure Key Vault finden Sie unter [Was ist Azure Key Vault?](key-vault-overview.md).