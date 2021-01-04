---
title: Integration von Azure Key Vault in eine Azure Stack Edge-Ressource und Geräteaktivierung
description: Hier wird beschrieben, wie Azure Key Vault während der Aktivierung eines Azure Stack Edge Pro-Geräts mit der Verwaltung von Geheimnissen in Verbindung steht.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: alkohli
ms.openlocfilehash: ec7a32739940d53d976e73a7e170df96a0acc245
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449525"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Integration von Azure Key Vault in Azure Stack Edge 

Azure Key Vault wird zur Verwaltung von Geheimnissen in eine Azure Stack Edge-Ressource integriert. Dieser Artikel enthält ausführliche Informationen zum Erstellen einer Azure Key Vault-Instanz für eine Azure Stack Edge-Ressource während der Geräteaktivierung und der anschließenden Nutzung für die Verwaltung von Geheimnissen. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Informationen zu Azure Key Vault und Azure Stack Edge

Mit dem Azure Key Vault-Clouddienst werden Token, Kennwörter, Zertifikate, API-Schlüssel und andere Geheimnisse sicher gespeichert und der Zugriff darauf gesteuert. Key Vault vereinfacht auch das Erstellen und Verwalten der zur Verschlüsselung Ihrer Daten verwendeten Verschlüsselungsschlüssel. Weitere Informationen zu zulässigen Transaktionen und den entsprechenden Kosten finden Sie unter [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/).

Eines der für den Azure Stack Edge-Dienst verwendeten Geheimnisse ist der Channel Integrity Key (CIK). Mit diesem Schlüssel können Sie Ihre Geheimnisse verschlüsseln. Durch die Integration von Key Vault wird der CIK sicher in der Key Vault-Instanz gespeichert. Weitere Informationen finden Sie unter [Sicheres Speichern von Geheimnissen und Schlüsseln](../key-vault/general/overview.md#securely-store-secrets-and-keys).


## <a name="key-vault-creation"></a>Erstellen einer Key Vault-Instanz

Bei der Generierung von Aktivierungsschlüsseln wird eine Key Vault-Instanz für eine Azure Stack Edge-Ressource erstellt. 

- Wenn Sie eine Azure Stack Edge-Ressource erstellen, müssen Sie den *Microsoft.KeyVault*-Ressourcenanbieter registrieren. Der Ressourcenanbieter wird automatisch registriert, wenn Sie als Besitzer oder Mitwirkender Zugriff auf das Abonnement haben. Die Key Vault-Instanz wird im gleichen Abonnement und der gleichen Ressourcengruppe erstellt wie die Azure Stack Edge-Ressource. 

- Beim Erstellen einer Azure Stack Edge-Ressource wird auch eine verwaltete Dienstidentität (Managed Service Identity, MSI) erstellt, die für die Lebensdauer der Ressource beibehalten wird und mit dem Ressourcenanbieter in der Cloud kommuniziert. 

    Wenn die MSI aktiviert ist, erstellt Azure eine vertrauenswürdige Identität für die Azure Stack Edge-Ressource.

- Nachdem Sie die Azure Stack Edge-Ressource erstellt und einen Aktivierungsschlüssel über das Azure-Portal generiert haben, wird eine Key Vault-Instanz erstellt. Diese Key Vault-Instanz wird für die Verwaltung von Geheimnissen verwendet und bleibt so lange bestehen, wie die Azure Stack Edge-Ressource vorhanden ist. 

    ![Während der Generierung von Aktivierungsschlüsseln erstellte Key Vault-Instanz](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- Sie können den Standardnamen übernehmen oder einen benutzerdefinierten Namen für die Key Vault-Instanz angeben. Der Name der Key Vault-Instanz muss zwischen 3 und 24 Zeichen lang sein. Sie können keine Key Vault-Instanz verwenden, die bereits verwendet wird. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![Während der Erstellung der Azure Stack Edge-Ressource erstellte MSI](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Um den Azure-Schlüsseltresor anzuzeigen, navigieren Sie in Ihrer Azure Stack Edge-Ressource zu **Eigenschaften**, und wählen Sie den Namen des gewünschten Schlüsseltresors aus. 

- Um ein versehentliches Löschen zu verhindern, wird eine Ressourcensperre für die Key Vault-Instanz aktiviert. Außerdem wird ein vorläufiges Löschen für die Key Vault-Instanz aktiviert, wodurch sie im Fall eines versehentlichen Löschens innerhalb von 90 Tagen wiederhergestellt werden kann. Weitere Informationen finden Sie unter [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](../key-vault/general/soft-delete-overview.md).

    Wenn die Key Vault-Instanz versehentlich gelöscht wird und die Löschschutzphase von 90 Tagen nicht verstrichen ist, führen Sie die Schritte zum [Wiederherstellen der Key Vault-Instanz](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates) aus. 

- Wenn Sie bereits vor der Integration der Azure Key Vault-Instanz in die Azure Stack Edge-Ressource über eine Azure Stack Edge-Ressource verfügten, sind Sie nicht betroffen. Sie können weiterhin die vorhandene Azure Stack Edge-Ressource verwenden. 

- Wenn die Azure Stack Edge-Ressource gelöscht wird, wird die Azure Key Vault-Instanz ebenfalls mit der Ressource gelöscht. Sie werden aufgefordert, diesen Schritt zu bestätigen. Wenn Sie diese Key Vault-Instanz nicht löschen möchten, können Sie auch die Einwilligung ablehnen. Es wird dann nur die Azure Stack Edge-Ressource gelöscht, und die Key Vault-Instanz bleibt erhalten. 

- Wenn diese Key Vault-Instanz zum Speichern anderer Schlüssel verwendet wurde, können Sie sie innerhalb von 90 Tagen nach dem Löschen wiederherstellen. Während dieser Löschschutzphase kann der Name der Key Vault-Instanz nicht zum Erstellen einer neuen Key Vault-Instanz verwendet werden.

Wenn Probleme im Zusammenhang mit der Key Vault-Instanz und der Geräteaktivierung auftreten, finden Sie weitere Informationen unter [Beheben von Problemen mit der Geräteaktivierung](azure-stack-edge-gpu-troubleshoot-activation.md).

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Generieren eines Aktivierungsschlüssels](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).