---
title: Migrieren von Azure-Sicherheitsressourcen – Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Sicherheitsressourcen von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 10d52899195f6dfc1598bc95a07c22a68ef8c278
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96006534"
---
# <a name="migrate-security-resources-to-global-azure"></a>Migrieren von Sicherheitsressourcen zu Azure weltweit

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Sicherheitsressourcen von Azure Deutschland zu Azure weltweit migrieren können.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-active-directory"></a>Azure Active Directory

Weitere Informationen zum Migrieren von Azure Active Directory finden Sie unter [Migration von Identity-Ressourcen von Azure Deutschland zu Azure weltweit](./germany-migration-identity.md#azure-active-directory).

## <a name="key-vault"></a>Key Vault

Einige Features von Azure Key Vault können nicht von Azure Deutschland zu Azure weltweit migriert werden.

### <a name="encryption-keys"></a>Verschlüsselungsschlüssel

Verschlüsselungsschlüssel können nicht migriert werden. Erstellen Sie neue Schlüssel in der Zielregion zu, und verwenden Sie dann diese Schlüssel, um die Zielressource (z. B. Azure Storage oder Azure SQL-Datenbank) zu schützen. Migrieren Sie die Daten sicher aus der alten Region in die neue Region.

### <a name="application-secrets"></a>Anwendungsgeheimnisse

Anwendungsgeheimnisse sind Zertifikate, Speicherkontoschlüssel und andere anwendungsbezogene Geheimnisse. Während einer Migration erstellen Sie zunächst einen neuen Schlüsseltresor in Azure weltweit. Danach führen Sie eine der folgenden Aktionen aus:

- Erstellen Sie neue Anwendungsgeheimnisse.
- Lesen Sie die aktuellen Geheimnisse in Azure Deutschland, und geben Sie den Wert dann in den neuen Tresor ein.

```powershell
Get-AzKeyVaultSecret -vaultname mysecrets -name Deploydefaultpw
```

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Key Vault](../key-vault/index.yml) durcharbeiten.
- Lesen Sie die [Übersicht über Key Vault](../key-vault/general/overview.md).
- Lesen Sie die Referenz zu den [PowerShell-Cmdlets für Key Vault](/powershell/module/az.keyvault/).

## <a name="vpn-gateway"></a>VPN Gateway

Das Migrieren einer Azure VPN Gateway-Instanz von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, in Azure weltweit eine neue VPN Gateway-Instanz zu erstellen und zu konfigurieren.

Informationen zu Ihrer aktuellen VPN Gateway-Konfiguration können Sie über das Portal oder mit PowerShell abrufen. Verwenden Sie in PowerShell einen Satz von Cmdlets, die mit `Get-AzVirtualNetworkGateway*` beginnen.

Stellen Sie sicher, dass Sie Ihre lokale Konfiguration aktualisieren. Löschen Sie auch alle vorhandenen Regeln für die alten IP-Adressbereiche, nachdem Sie Ihre Azure-Netzwerkumgebung aktualisiert haben.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu VPN Gateway](../vpn-gateway/index.yml) durcharbeiten.
- Im Artikel [Herstellen einer Site-to-Site-Verbindung](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) finden Sie weitere Informationen zu diesem Thema.
- Lesen Sie auch den Artikel über die PowerShell-Cmdlets [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway).
- Lesen Sie den Blogbeitrag [Herstellen einer Site-to-Site-Verbindung](/archive/blogs/ralfwi/connecting-clouds).
  
## <a name="application-gateway"></a>Application Gateway

Das Migrieren einer Azure Application Gateway-Instanz von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, in Azure weltweit ein neues Gateway zu erstellen und zu konfigurieren.

Informationen zu Ihrer aktuellen Gatewaykonfiguration können Sie über das Portal oder mit PowerShell abrufen. Verwenden Sie in PowerShell einen Satz von Cmdlets, die mit `Get-AzApplicationGateway*` beginnen.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Application Gateway](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md) durcharbeiten.
- Im Artikel [Erstellen eines Application Gateways](../application-gateway/quick-create-portal.md) finden Sie weitere Informationen zu diesem Thema.
- Lesen Sie auch den Artikel über die PowerShell-Cmdlets [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)