---
title: Kundenseitig verwaltete Schlüssel für die Kontoverschlüsselung
titleSuffix: Azure Storage
description: Sie können Ihren eigenen Verschlüsselungsschlüssel verwenden, um die Daten Ihres Speicherkontos zu schützen. Wenn Sie einen vom Kunden verwalteten Schlüssel angeben, wird dieser zum Schützen und Steuern des Zugriffs auf den Schlüssel verwendet, mit dem Ihre Daten verschlüsselt werden. Vom Kunden verwaltete Schlüssel ermöglichen eine höhere Flexibilität bei der Verwaltung von Zugriffssteuerungen.
services: storage
author: tamram
ms.service: storage
ms.date: 09/15/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 8eaadc031039b22e209db1023c65da39e1e096b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483282"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung

Sie können Ihren eigenen Verschlüsselungsschlüssel verwenden, um die Daten Ihres Speicherkontos zu schützen. Wenn Sie einen vom Kunden verwalteten Schlüssel angeben, wird dieser zum Schützen und Steuern des Zugriffs auf den Schlüssel verwendet, mit dem Ihre Daten verschlüsselt werden. Vom Kunden verwaltete Schlüssel ermöglichen eine höhere Flexibilität bei der Verwaltung von Zugriffssteuerungen.

Sie müssen Azure Key Vault oder Azure Key Vault Managed HSM (Hardware Security Module, Vorschau) verwenden, um Ihre kundenseitig verwalteten Schlüssel zu speichern. Sie können entweder Ihre eigenen Schlüssel erstellen und im Schlüsseltresor oder im verwalteten HSM speichern, oder Sie können mit den Azure Key Vault-APIs Schlüssel generieren. Das Speicherkonto und der Schlüsseltresor oder das verwaltete HSM müssen sich in derselben Region und im selben Azure Active Directory-Mandanten (Azure AD) befinden, aber sie können sich in verschiedenen Abonnements befinden.

Weitere Informationen zum Azure Key Vault finden Sie unter [What is Azure Key Vault? (Was ist der Azure Key Vault?)](../../key-vault/general/overview.md).

> [!NOTE]
> Azure Key Vault und Azure Key Vault Managed HSM unterstützen dieselben APIs und Verwaltungsschnittstellen für die Konfiguration.

## <a name="about-customer-managed-keys"></a>Informationen zu kundenseitig verwalteten Schlüsseln

Im folgenden Diagramm ist dargestellt, wie Azure Active Directory und ein Schlüsseltresor oder ein verwaltetes HSM von Azure Storage verwendet werden, um Anforderungen mit dem kundenseitig verwalteten Schlüssel zu senden:

![Das Diagramm zeigt, wie vom Kunden verwaltete Schlüssel in Azure Storage funktionieren](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

In der folgenden Liste werden die nummerierten Schritte im Diagramm erläutert:

1. Ein Azure Key Vault-Administrator erteilt Berechtigungen für Verschlüsselungsschlüssel an die dem Speicherkonto zugeordnete verwaltete Identität.
2. Ein Azure Storage-Administrator konfiguriert die Verschlüsselung mit einem vom Kunden verwalteten Schlüssel für das Speicherkonto.
3. Azure Storage verwendet die dem Speicherkonto zugeordnete verwaltete Identität, um den Zugriff auf Azure Key Vault über Azure Active Directory zu authentifizieren.
4. Azure Storage umschließt den Kontoverschlüsselungsschlüssel mit dem Kundenschlüssel in Azure Key Vault.
5. Bei Lese-/Schreibvorgängen sendet Azure Storage Anforderungen an Azure Key Vault, um die Umschließung für den Kontoverschlüsselungsschlüssel aufzuheben und so Verschlüsselungs- und Entschlüsselungsvorgänge durchzuführen.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Kundenseitig verwaltete Schlüssel für Warteschlangen und Tabellen

In Queue Storage und Table Storage gespeicherte Daten sind nicht automatisch durch einen kundenseitig verwalteten Schlüssel geschützt, wenn Schlüssel dieser Art für das Speicherkonto aktiviert sind. Sie können diese Dienste optional beim Erstellen des Speicherkontos konfigurieren, um sie in diese Schutzmaßnahme einzubeziehen.

Weitere Informationen zur Erstellung eines Speicherkontos, für das vom Kunden verwaltete Schlüssel für Warteschlangen und Tabellen unterstützt werden, finden Sie unter [Erstellen eines Kontos, das kundenseitig verwaltete Schlüssel für Tabellen und Warteschlangen unterstützt](account-encryption-key-create.md).

Daten in Blob Storage und Azure Files sind immer durch kundenseitig verwaltete Schlüssel geschützt, wenn diese für das Speicherkonto konfiguriert werden.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Aktivieren von kundenseitig verwalteten Schlüsseln für ein Speicherkonto

Wenn Sie einen kundenseitig verwalteten Schlüssel konfigurieren, umschließt Azure Storage den Stammdaten-Verschlüsselungsschlüssel für das Konto im zugeordneten Schlüsseltresor oder im verwalteten HSM mit dem kundenseitig verwalteten Schlüssel. Die Aktivierung der vom Kunden verwalteten Schlüssel hat keine Auswirkung auf die Leistung und ist sofort wirksam.

Wenn Sie vom Kunden verwaltete Schlüssel aktivieren oder deaktivieren oder den Schlüssel bzw. die Schlüsselversion ändern, ändert sich der Schutz des Stammverschlüsselungsschlüssels. Die Daten in Ihrem Azure Storage-Konto müssen aber nicht neu verschlüsselt werden.

Vom Kunden verwaltete Schlüssel können nur für vorhandene Speicherkonten aktiviert werden. Der Schlüsseltresor bzw. das verwaltete HSM muss konfiguriert werden, um die Berechtigungen für die verwaltete Identität zu erteilen, die dem Speicherkonto zugeordnet ist. Die verwaltete Identität ist erst verfügbar, nachdem das Speicherkonto erstellt wurde.

Sie können jederzeit zwischen kundenseitig verwalteten Schlüsseln und von Microsoft verwalteten Schlüsseln wechseln. Weitere Informationen zu von Microsoft verwalteten Schlüsseln finden Sie unter [Informationen zur Verwaltung von Verschlüsselungsschlüsseln](storage-service-encryption.md#about-encryption-key-management).

Informationen zum Konfigurieren der Azure Storage-Verschlüsselung mit kundenseitig verwalteten Schlüsseln in einem Schlüsseltresor finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](customer-managed-keys-configure-key-vault.md). Informationen zum Konfigurieren von kundenseitig verwalteten Schlüsseln in einem verwalteten HSM finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind](customer-managed-keys-configure-key-vault-hsm.md).

> [!IMPORTANT]
> Kundenseitig verwaltete Schlüssel basieren auf verwalteten Identitäten für Azure-Ressourcen, einem Feature von Azure AD. Verwaltete Identitäten unterstützen derzeit keine verzeichnisübergreifenden Szenarien. Wenn Sie vom Kunden verwaltete Schlüssel im Azure-Portal konfigurieren, wird Ihrem Speicherkonto eine verwaltete Identität im Hintergrund automatisch zugewiesen. Wenn Sie anschließend das Abonnement, die Ressourcengruppe oder das Speicherkonto von einem Azure AD-Verzeichnis in ein anderes Verzeichnis verschieben, wird die dem Speicherkonto zugeordnete verwaltete Identität nicht an den neuen Mandanten übertragen, sodass vom Kunden verwaltete Schlüssel möglicherweise nicht mehr funktionieren. Weitere Informationen finden Sie in [Häufig gestellte Fragen und bekannte Probleme mit verwalteten Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories) unter **Übertragen eines Abonnements zwischen Azure AD-Verzeichnissen**.  

Die Azure Storage-Verschlüsselung unterstützt RSA- und RSA-HSM-Schlüssel mit einer Größe von 2.048, 3.072 und 4.096 Bit. Weitere Informationen über Schlüssel finden Sie unter [Informationen zu Schlüsseln](../../key-vault/keys/about-keys.md).

Die Verwendung eines Schlüsseltresors oder eines verwalteten HSM verursacht Kosten. Weitere Informationen finden Sie auf der Seite [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="update-the-key-version"></a>Aktualisieren der Schlüsselversion

Wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln konfigurieren, haben Sie zwei Möglichkeiten, die Schlüsselversion zu aktualisieren:

- **Automatisches Update der Schlüsselversion:** Um einen kundenseitig verwalteten Schlüssel bei Verfügbarkeit einer neuen Version automatisch zu aktualisieren, lassen Sie die Schlüsselversion aus, wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für das Speicherkonto konfigurieren. Wenn die Schlüsselversion weggelassen wird, überprüft Azure Storage den Schlüsseltresor oder das verwaltete HSM täglich auf eine neue Version eines kundenseitig verwalteten Schlüssels. Azure Storage nutzt automatisch die neueste Version des Schlüssels.
- **Manuelles Update der Schlüsselversion:** Zur Verwendung einer bestimmten Version eines Schlüssels für die Azure Storage-Verschlüsselung geben Sie diese Schlüsselversion beim Aktivieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln für das Speicherkonto an. Wenn Sie die Schlüsselversion angeben, verwendet Azure Storage diese Version für die Verschlüsselung, bis Sie die Schlüsselversion manuell aktualisieren.

    Wenn die Schlüsselversion direkt angegeben wird, müssen Sie das Speicherkonto manuell für die Verwendung des neuen Schlüsselversions-URI aktualisieren, nachdem eine neue Version erstellt wurde. Informationen dazu, wie Sie das Speicherkonto für die Verwendung einer neuen Version des Schlüssels aktualisieren, finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](customer-managed-keys-configure-key-vault.md) und [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind](customer-managed-keys-configure-key-vault-hsm.md).

Durch das Aktualisieren der Schlüsselversion eines kundenseitig verwalteten Schlüssels werden die Daten im Speicherkonto nicht neu verschlüsselt. Es ist keine weitere Aktion durch den Benutzer erforderlich.

> [!NOTE]
> Um einen Schlüssel zu rotieren, erstellen Sie eine neue Version des Schlüssels im Schlüsseltresor oder im verwalteten HSM entsprechend Ihren Compliancerichtlinien. Sie können den Schlüssel manuell rotieren oder eine Funktion erstellen, um ihn nach einem Zeitplan zu rotieren.

## <a name="revoke-access-to-customer-managed-keys"></a>Widerrufen des Zugriffs auf von Kunden verwaltete Schlüssel

Sie können den Zugriff des Speicherkontos auf den vom Kunden verwalteten Schlüssel jederzeit widerrufen. Nachdem der Zugriff auf die vom Kunden verwalteten Schlüssel widerrufen wurde oder der Schlüssel deaktiviert oder gelöscht wurde, können Clients keine Vorgänge mehr aufrufen, bei denen für ein Blob oder die zugehörigen Metadaten Lese- oder Schreibvorgänge durchgeführt werden. Wenn versucht wird, die folgenden Vorgänge aufzurufen, tritt für alle Benutzer ein Fehler mit dem Code „403 (Verboten)“ auf:

- [List Blobs](/rest/api/storageservices/list-blobs), wenn der Aufruf mit dem Parameter `include=metadata` für den Anforderungs-URI erfolgt
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob), wenn der Aufruf mit dem Anforderungsheader `x-ms-meta-name` erfolgt
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Put Block](/rest/api/storageservices/put-block)
- [Put Block From URL](/rest/api/storageservices/put-block-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Append Block From URL](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Page](/rest/api/storageservices/put-page)
- [Put Page From URL](/rest/api/storageservices/put-page-from-url)
- [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)

Stellen Sie den Zugriff auf den vom Kunden verwalteten Schlüssel wieder her, um diese Vorgänge erneut aufzurufen.

Alle Datenvorgänge, die in diesem Abschnitt nicht aufgeführt sind, können durchgeführt werden, nachdem vom Kunden verwaltete Schlüssel widerrufen wurden oder ein Schlüssel deaktiviert oder gelöscht wurde.

Zum Widerrufen des Zugriffs auf von Kunden verwaltete Schlüssel können Sie [PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) oder die [Azure-Befehlszeilenschnittstelle](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) verwenden.

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Von Kunden verwaltete Schlüssel für verwaltete Azure-Datenträger

Mit von Kunden verwalteten Schlüsseln kann auch die Verschlüsselung von verwalteten Azure-Datenträgern verwaltet werden. Das Verhalten der von Kunden verwalteten Schlüssel unterscheidet sich für verwaltete Datenträger von dem für Azure Storage-Ressourcen. Weitere Informationen finden Sie unter [Serverseitige Verschlüsselung von Azure Managed Disks (Windows)](../../virtual-machines/disk-encryption.md) bzw. [Serverseitige Verschlüsselung von Azure Managed Disks (Linux)](../../virtual-machines/disk-encryption.md).

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](storage-service-encryption.md)
- [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](customer-managed-keys-configure-key-vault.md)
- [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind](customer-managed-keys-configure-key-vault-hsm.md)