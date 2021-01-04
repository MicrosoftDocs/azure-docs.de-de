---
title: Tutorial`:` Verwenden einer verwalteten Identität für den Zugriff auf Azure Key Vault – Linux – Azure AD
description: In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers verwenden, um auf Azure Resource Manager zuzugreifen.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cfcaec38a939291090da7d2229c4a95f984bf28
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360439"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie ein virtueller Linux-Computer mithilfe einer systemseitig zugewiesenen verwalteten Identität auf [Azure Key Vault](../../key-vault/general/overview.md) zugreifen kann. Key Vault dient als Bootstrap und ermöglicht es Ihrer Clientanwendung, mithilfe des Geheimnisses auf Ressourcen zuzugreifen, die nicht von Azure Active Directory (AD) geschützt sind. Verwaltete Dienstidentitäten werden von Azure automatisch verwaltet und ermöglichen Ihnen die Authentifizierung für Dienste, die die Azure AD-Authentifizierung unterstützen, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen.

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Gewähren des Zugriffs auf ein in einer Key Vault gespeicherten Geheimnisses für den virtuellen Computer 
> * Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Abrufen des Geheimnisses aus dem Schlüsseltresor 
 
## <a name="prerequisites"></a>Voraussetzungen

- Kenntnisse im Bereich verwaltete Identitäten. Wenn Sie mit der Funktion für verwaltete Identitäten für Azure-Ressourcen nicht vertraut sind, finden Sie hier eine [Übersicht](overview.md). 
- Ein Azure-Konto. [Registrieren Sie sich für ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/)
- Berechtigungen vom Typ „Besitzer“ für den entsprechenden Bereich (Ihr Abonnement oder die Ressourcengruppe), um die erforderlichen Schritte zur Ressourcenerstellung und Rollenverwaltung durchführen zu können. Wenn Sie Unterstützung bei der Rollenzuweisung benötigen, finden Sie weitere Informationen unter [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](../../role-based-access-control/role-assignments-portal.md).
- Außerdem benötigen Sie einen virtuellen Linux-Computer, auf dem systemseitig zugewiesene verwaltete Identitäten aktiviert sind.
  - Wenn Sie einen virtuellen Computer für dieses Tutorial erstellen müssen, können Sie den Artikel [Schnellstart: Erstellen eines virtuellen Linux-Computers im Azure-Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine) durcharbeiten.


## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors  

In diesem Abschnitt wird gezeigt, wie Sie einem virtuellen Computer den Zugriff auf ein in einer Key Vault-Instanz gespeichertes Geheimnis gewähren. Mithilfe von verwalteten Identitäten für Azure-Ressourcen kann der Code Zugriffstoken zur Authentifizierung von Ressourcen abrufen, die die Azure AD-Authentifizierung unterstützen.Allerdings unterstützen nicht alle Azure-Dienste die Azure AD-Authentifizierung. Um verwaltete Identitäten für Azure-Ressourcen mit diesen Diensten zu verwenden, speichern Sie die Dienstanmeldeinformationen in Azure Key Vault, und greifen Sie mit der verwalteten Identität des virtuellen Computers auf Key Vault zu, um die Anmeldeinformationen abzurufen.

Zunächst müssen Sie eine Key Vault-Instanz erstellen und der systemseitig zugewiesenen verwalteten Identität des virtuellen Computers den Zugriff darauf erteilen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).
1. Wählen Sie oben auf der linken Navigationsleiste **Ressource erstellen** aus.  
1. Geben Sie im Feld **Marketplace durchsuchen** den Suchbegriff **Key Vault** ein, und drücken Sie die **EINGABETASTE**.  
1. Wählen Sie in den Ergebnissen **Key Vault** aus.
1. Klicken Sie auf **Erstellen**
1. Geben Sie unter **Name** einen Namen für die neue Key Vault an.

    ![Bildschirm zum Erstellen eines Schlüsseltresors](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. Geben Sie alle erforderlichen Informationen ein. Wählen Sie dabei unbedingt das Abonnement und die Ressourcengruppe aus, in denen Sie den für dieses Tutorial verwendeten virtuellen Computer erstellt haben.
1. Wählen Sie **Überprüfen + erstellen** aus.
1. Klicken Sie auf **Erstellen**

## <a name="grant-access"></a>Gewähren von Zugriff

Der vom virtuellen Computer verwalteten Identität muss Zugriff gewährt werden, damit sie das in Key Vault gespeicherte Geheimnis lesen kann.

1. Navigieren Sie zur neu erstellten Key Vault-Instanz.
1. Wählen Sie im Menü auf der linken Seite die Option **Zugriffsrichtlinie** aus.
1. Wählen Sie **Zugriffsrichtlinie hinzufügen** aus.

   ![Key Vault-Bildschirm zum Erstellen einer Zugriffsrichtlinie](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. Wählen Sie im Abschnitt **Zugriffsrichtlinie hinzufügen** unter **Anhand einer Vorlage konfigurieren (optional)** im Pulldownmenü die Option **Verwaltung von Geheimnissen** aus.
1. Wählen Sie **Prinzipal auswählen**, und geben Sie im Suchfeld den Namen des zuvor erstellten virtuellen Computers ein.  Wählen Sie in der Ergebnisliste den virtuellen Computer und dann **Auswählen** aus.
1. Wählen Sie **Hinzufügen** aus.
1. Wählen Sie **Speichern** aus.

## <a name="create-a-secret"></a>Erstellen eines Geheimnisses

Fügen Sie als Nächstes der Key Vault-Instanz ein Geheimnis hinzu, das Sie später mithilfe von Code abrufen können, der auf dem virtuellen Computer ausgeführt wird. Im Rahmen dieses Tutorials wird PowerShell verwendet. Die gleichen Konzepte gelten jedoch für jeden Code, der auf diesem virtuellen Computer ausgeführt wird.

1. Navigieren Sie zur neu erstellten Key Vault-Instanz.
1. Wählen Sie **Geheimnisse** aus, und klicken Sie auf **Hinzufügen**.
1. Wählen Sie die Option **Generieren/importieren** aus.
1. Lassen Sie auf dem Bildschirm **Geheimnis erstellen** unter **Uploadoptionen** die Option **Manuell** ausgewählt.
1. Geben Sie einen Namen und einen Wert für das Geheimnis ein.  Dabei kann es sich um einen beliebigen Wert handeln. 
1. Lassen Sie das Aktivierungs- und das Ablaufdatum leer, und übernehmen Sie für **Aktiviert** die ausgewählte Option **Ja**. 
1. Klicken Sie auf **Erstellen**, um das Geheimnis zu erstellen.

   ![Erstellen eines Geheimnisses](./media/tutorial-linux-vm-access-nonaad/create-secret.png)
 
## <a name="access-data"></a>Zugreifen auf Daten

Zum Ausführen dieser Schritte benötigen Sie einen SSH-Client.  Wenn Sie Windows verwenden, können Sie den SSH-Client im [Windows-Subsystem für Linux](/windows/wsl/about) verwenden. Wenn Sie Hilfe beim Konfigurieren der SSH-Clientschlüssel benötigen, lesen Sie die Informationen unter [Vorgehensweise: Verwenden von SSH-Schlüsseln mit Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) oder [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
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
    
    Sie können dieses Zugriffstoken zur Authentifizierung bei Azure Key Vault verwenden.  Die nächste CURL-Anforderung zeigt, wie Sie mithilfe von CURL und der Key Vault-REST-API ein Geheimnis aus der Key Vault lesen.  Sie benötigen die URL Ihrer Key Vaults. Diese befindet sich im Abschnitt **Zusammenfassung** der Seite **Übersicht** der Key Vault.  Darüber hinaus benötigen Sie auch das Zugriffstoken, das Sie im vorhergehenden Aufruf abgerufen haben. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Die Antwort sieht so aus: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Nachdem Sie das Geheimnis aus der Key Vault abgerufen haben, können Sie es für die Authentifizierung bei einem Dienst verwenden, für den ein Name und ein Kennwort angegeben werden müssen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Gehen Sie wie folgt vor, wenn Sie die Ressourcen bereinigen möchten: Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressourcengruppen** und dann die Ressourcengruppe aus, die in diesem Tutorial erstellt wurde (z. B. `mi-test`), und verwenden Sie anschließend den Befehl **Ressourcengruppe löschen**.

Diesen Schritt können Sie alternativ über [PowerShell oder die CLI](../../azure-resource-manager/management/delete-resource-group.md) ausführen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers verwenden, um auf Azure Key Vault zuzugreifen.  Weitere Informationen zu Azure Key Vault finden Sie in folgendem Artikel:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)