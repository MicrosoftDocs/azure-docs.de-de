---
title: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Key Vault
description: In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers verwenden, um auf Azure Resource Manager zuzugreifen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a4ce0419e3a5615cc5a6d57fe2f1cfecad2f09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66228580"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für einen virtuellen Linux-Computer verwenden, um auf Azure Key Vault zuzugreifen. Key Vault dient als Bootstrap und ermöglicht es Ihrer Clientanwendung, mithilfe des Geheimnisses auf Ressourcen zuzugreifen, die nicht von Azure Active Directory (AD) geschützt sind. Verwaltete Identitäten für Azure-Ressourcen werden von Azure automatisch verwaltet und ermöglichen Ihnen die Authentifizierung für Dienste, die die Azure AD-Authentifizierung unterstützen, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen. 

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Gewähren des Zugriffs auf ein in einer Key Vault gespeicherten Geheimnisses für den virtuellen Computer 
> * Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Abrufen des Geheimnisses aus dem Schlüsseltresor 
 
## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Gewähren des Zugriffs auf ein in einer Key Vault gespeicherten Geheimnisses für den virtuellen Computer  

Mithilfe verwalteter Dienstidentitäten für Azure-Ressourcen kann der Code Zugriffstoken zur Authentifizierung von Ressourcen abrufen, die die Azure Active Directory-Authentifizierung unterstützen. Allerdings unterstützen nicht alle Azure-Dienste die Azure AD-Authentifizierung. Um verwaltete Identitäten für Azure-Ressourcen mit diesen Diensten zu verwenden, speichern Sie die Dienstanmeldeinformationen in Azure Key Vault, und greifen Sie mit verwalteten Identitäten für Azure-Ressourcen auf Key Vault zu, um die Anmeldeinformationen abzurufen. 

Zunächst müssen Sie eine Key Vault-Instanz erstellen und der systemseitig zugewiesenen verwalteten Identität des virtuellen Computers den Zugriff darauf erteilen.   

1. Wählen Sie oben in der linken Navigationsleiste **Ressource erstellen** > **Sicherheit + Identität** > **Key Vault** aus.  
2. Geben Sie unter **Name** einen Namen für die neue Key Vault an. 
3. Suchen Sie die Key Vault in dem Abonnement und in der Ressourcengruppe, in der sich auch der zuvor erstellte virtuelle Computer befindet. 
4. Wählen Sie **Zugriffsrichtlinien** aus, und klicken Sie auf **Neue hinzufügen**. 
5. Wählen Sie unter „Anhand einer Vorlage konfigurieren“ die Option **Verwaltung von Geheimnissen** aus. 
6. Wählen Sie **Prinzipal auswählen**, und geben Sie im Suchfeld den Namen des zuvor erstellten virtuellen Computers ein.  Wählen Sie in der Ergebnisliste den virtuellen Computer aus, und klicken Sie auf **Auswählen** . 
7. Klicken Sie auf **OK**, um die neue Zugriffsrichtlinie hinzuzufügen. Klicken Sie dann erneut auf **OK**, um die Auswahl der Zugriffsrichtlinie abzuschließen. 
8. Klicken Sie auf **Erstellen**, um die Key Vault zu erstellen. 

    ![Alternativer Bildtext](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Fügen Sie der Key Vault nun ein Geheimnis hinzu, das Sie später mithilfe von Code abrufen können, der auf dem virtuellen Computer ausgeführt wird: 

1. Wählen Sie **Alle Ressourcen** aus, suchen Sie den erstellten Schlüsseltresor, und wählen Sie diesen aus. 
2. Wählen Sie **Geheimnisse** aus, und klicken Sie auf **Hinzufügen**. 
3. Wählen Sie unter **Uploadoptionen** die Option **Manuell** aus. 
4. Geben Sie einen Namen und einen Wert für das Geheimnis ein.  Dabei kann es sich um einen beliebigen Wert handeln. 
5. Lassen Sie das Aktivierungs- und das Ablaufdatum leer, und übernehmen Sie für **Aktiviert** die ausgewählte Option **Ja**. 
6. Klicken Sie auf **Erstellen**, um das Geheimnis zu erstellen. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Abrufen des Geheimnisses aus dem Schlüsseltresor  

Zum Ausführen dieser Schritte benötigen Sie einen SSH-Client.  Wenn Sie Windows verwenden, können Sie den SSH-Client im [Windows-Subsystem für Linux](https://msdn.microsoft.com/commandline/wsl/about) verwenden. Wenn Sie Hilfe beim Konfigurieren der SSH-Clientschlüssel benötigen, lesen Sie die Informationen unter [Vorgehensweise: Verwenden von SSH-Schlüsseln mit Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) oder [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. Navigieren Sie im Portal zu Ihrem virtuellen Linux-Computer, und klicken Sie in der **Übersicht** auf **Verbinden**. 
2. **Verbinden** Sie den virtuellen Computer mit dem gewünschten SSH-Client. 
3. Übermitteln Sie im Terminalfenster mit cURL eine Anforderung an den lokalen Endpunkt der verwalteten Identitäten für Azure-Ressourcen, um ein Zugriffstoken für Azure Key Vault abzurufen.  
 
    Die CURL-Anforderung für das Zugriffstoken finden Sie weiter unten.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    Die Antwort enthält das Zugriffstoken, das Sie für den Zugriff auf Resource Manager benötigen. 
    
    Antwort:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Sie können dieses Zugriffstoken zur Authentifizierung bei Azure Key Vault verwenden.  Die nächste CURL-Anforderung zeigt, wie Sie mithilfe von CURL und der Key Vault-REST-API ein Geheimnis aus der Key Vault lesen.  Sie benötigen die URL Ihrer Key Vault. Diese befindet sich im Abschnitt **Zusammenfassung** der Seite **Übersicht** der Key Vault.  Darüber hinaus benötigen Sie auch das Zugriffstoken, das Sie im vorhergehenden Aufruf abgerufen haben. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Die Antwort sieht so aus: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Nachdem Sie das Geheimnis aus der Key Vault abgerufen haben, können Sie es für die Authentifizierung bei einem Dienst verwenden, für den ein Name und ein Kennwort angegeben werden müssen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers verwenden, um auf Azure Key Vault zuzugreifen.  Weitere Informationen zu Azure Key Vault finden Sie in folgendem Artikel:

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-whatis)




