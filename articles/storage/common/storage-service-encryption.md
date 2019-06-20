---
title: Azure Storage-Verschlüsselung für ruhende Daten | Microsoft-Dokumentation
description: Azure Storage schützt Ihre Daten, indem der Dienst diese automatisch verschlüsselt, bevor diese in der Cloud gespeichert werden. Alle Daten in Azure Storage werden auf transparente Weise mit AES-256-Verschlüsselung ver- und entschlüsselt und sind mit dem FIPS 140-2-Standard konform.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1e95adbd1a564fb34d3f0506ac1cc25bc5a63c62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65790054"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-Verschlüsselung für ruhende Daten

Azure Storage verschlüsselt Ihre Daten beim Speichern in der Cloud automatisch. Die Verschlüsselung schützt Ihre Daten und unterstützt Sie beim Einhalten der Sicherheits- und Complianceanforderungen Ihrer Organisation. Daten in Azure Storage werden auf transparente Weise mit der [AES-256-Verschlüsselung](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) ver- und entschlüsselt, einer der stärksten verfügbaren Blockchiffren, und sind mit dem FIPS 140-2-Standard konform. Die Azure Storage-Verschlüsselung ähnelt der BitLocker-Verschlüsselung unter Windows.

Die Azure Storage-Verschlüsselung wird für alle neuen und vorhandenen Speicherkonten aktiviert und kann nicht deaktiviert werden. Da Ihre Daten standardmäßig geschützt werden, müssen Sie weder Code noch Anwendungen ändern, um die Azure Storage-Verschlüsselung nutzen zu können. 

Speicherkonten werden unabhängig von ihrer Leistungsstufe (Standard oder Premium) und ihrem Bereitstellungsmodell (Azure Resource Manager oder klassisch) verschlüsselt. Alle Redundanzoptionen in Azure Storage unterstützten die Verschlüsselung, und alle Kopien eines Speicherkontos werden verschlüsselt. Alle Azure Storage-Ressourcen werden verschlüsselt, z. B. Blobs, Datenträger, Dateien, Warteschlangen und Tabellen. Objektmetadaten werden ebenfalls verschlüsselt.

Die Verschlüsselung hat keine Auswirkungen auf die Leistung von Azure Storage. Es fallen keine zusätzlichen Kosten für die Azure Storage-Verschlüsselung an.

Weitere Informationen zu den kryptografischen Modulen, die der Azure Storage-Verschlüsselung zugrunde liegen, finden Sie unter [Kryptografie-API: Die nächste Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Schlüsselverwaltung

Sie können von Microsoft verwaltete Schlüssel für die Verschlüsselung Ihres Speicherkontos nutzen, oder Sie können die Verschlüsselung mit Ihren eigenen Schlüsseln mithilfe vom Azure Key Vault durchführen.

### <a name="microsoft-managed-keys"></a>Von Microsoft verwaltete Schlüssel

Standardmäßig verwendet Ihr Speicherkonto von Microsoft verwaltete Verschlüsselungsschlüssel. Im Bereich **Verschlüsselung** im [Azure-Portal](https://portal.azure.com) können Sie sich wie auf dem folgenden Screenshot gezeigt die Verschlüsselungseinstellungen für Ihr Speicherkonto ansehen:

![Konto, das mit von Microsoft verwalteten Schlüsseln verschlüsselt wird](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Vom Kunden verwaltete Schlüssel

Sie können die Azure Storage-Verschlüsselung auch mit von Kunden verwalteten Schlüsseln verwalten. Mit von Kunden verwalteten Schlüsseln können Sie Zugriffssteuerungen flexibler erstellen, rotieren, deaktivieren und widerrufen. Außerdem können Sie die zum Schutz Ihrer Daten verwendeten Verschlüsselungsschlüssel überwachen. 

Verwenden Sie den Azure Key Vault, um Ihre Schlüssel zu verwalten und die Schlüsselnutzung zu überwachen. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren. Das Speicherkonto und der Schlüsseltresor müssen sich in derselben Region befinden, dürfen aber zu verschiedenen Abonnements gehören. Weitere Informationen zum Azure Key Vault finden Sie unter [What is Azure Key Vault? (Was ist der Azure Key Vault?)](../../key-vault/key-vault-overview.md).

Informationen zum Widerrufen von Kunden verwalteter Schlüsseln finden Sie in den Artikeln zu [Azure Key Vault-PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) und zur [Azure Key Vault-CLI](https://docs.microsoft.com/cli/azure/keyvault). Durch das Widerrufen des Zugriffs wird der Zugriff auf alle Daten im Speicherkonto blockiert, da Azure Storage keinen Zugriff mehr auf den Verschlüsselungsschlüssel hat.

In den folgenden Artikeln erhalten Sie Informationen zum Verwenden von Kunden verwalteter Schlüsseln mit Azure Storage:

- [Configure customer-managed keys for Azure Storage encryption from the Azure portal (Konfigurieren von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung mithilfe des Azure-Portals)](storage-encryption-keys-portal.md)
- [Configure customer-managed keys for Azure Storage encryption from PowerShell (Konfigurieren von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung mithilfe von Azure PowerShell)](storage-encryption-keys-powershell.md)
- [Use customer-managed keys with Azure Storage encryption from Azure CLI (Verwenden von Kunden verwalteter Schlüssel mit der Azure Storage-Verschlüsselung mithilfe der Azure CLI)](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Von Kunden verwaltete Schlüssel basieren auf verwalteten Identitäten für Azure-Ressourcen, einem Feature von Azure Active Directory (Azure AD). Wenn Sie ein Abonnement aus einem Azure AD-Verzeichnis in ein anderes übertragen, werden verwaltete Identitäten nicht aktualisiert, und von Kunden verwaltete Schlüssel funktionieren möglicherweise nicht mehr. Weitere Informationen finden Sie in [Häufig gestellte Fragen und bekannte Probleme mit verwalteten Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories) unter **Übertragen eines Abonnements zwischen Azure AD-Verzeichnissen**.  

> [!NOTE]  
> Von Kunden verwaltete Schlüssel werden nicht für [verwaltete Azure-Datenträger](../../virtual-machines/windows/managed-disks-overview.md) unterstützt.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Vergleich: Azure Storage-Verschlüsselung und Datenträgerverschlüsselung

Mit der Azure Storage-Verschlüsselung werden alle Azure-Speicherkonten und darin enthaltene Ressourcen verschlüsselt, z. B. Seitenblobs, auf denen Datenträger auf Azure-VMs basieren. Darüber hinaus können Datenträger von Azure-VMs mit [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md) verschlüsselt werden. Für Azure Disk Encryption wird die branchenübliche [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)-Verschlüsselung unter Windows und [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) unter Linux verwendet, um betriebssystembasierte Verschlüsselungslösungen bereitzustellen, die mit dem Azure Key Vault integriert werden können.

## <a name="next-steps"></a>Nächste Schritte

- [Was ist der Azure-Schlüsseltresor?](../../key-vault/key-vault-overview.md)
