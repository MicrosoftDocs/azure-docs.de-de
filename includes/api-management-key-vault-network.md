---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491009"
---
#### <a name="requirements-for-key-vault-firewall"></a>Anforderungen an Key Vault-Firewall

Wenn die [Key Vault-Firewall](../articles/key-vault/general/network-security.md) in Ihrem Schlüsseltresor aktiviert ist, gelten die folgenden zusätzlichen Anforderungen:

* Sie müssen die **systemseitig zugewiesene** verwaltete Identität der API Management-Instanz verwenden, um auf den Schlüsseltresor zuzugreifen.
* Aktivieren Sie in der Key Vault-Firewall die Option **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?** .

#### <a name="virtual-network-requirements"></a>Anforderungen für virtuelle Netzwerke

Wenn die API Management-Instanz in einem virtuellen Netzwerk bereitgestellt wird, müssen Sie darüber hinaus die folgenden Netzwerkeinstellungen konfigurieren:

* Aktivieren Sie im API Management-Subnetz einen [Dienstendpunkt](../articles/key-vault/general/overview-vnet-service-endpoints.md) für Azure Key Vault.
* Konfigurieren Sie eine Netzwerksicherheitsgruppen-Regel (NSG), um ausgehenden Datenverkehr an die [Diensttags](../articles/virtual-network/service-tags-overview.md) AzureKeyVault und AzureActiveDirectory zuzulassen. 

Weitere Informationen zur Netzwerkkonfiguration finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Netzwerk](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues).