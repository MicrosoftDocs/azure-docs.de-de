---
title: Tutorial`:` Verwenden einer verwalteten Identität für den Zugriff auf Azure Key Vault – Windows – Azure AD
description: In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Windows-Computers verwenden, um auf Azure Key Vault zuzugreifen.
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
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 668d3cb044512220ff7afbc165c77da704a9a5d7
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107514"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: Verwenden der systemseitig zugewiesenen verwalteten Identität eines virtuellen Windows-Computers für den Zugriff auf Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie ein virtueller Windows-Computer mithilfe einer systemseitig zugewiesenen verwalteten Identität auf [Azure Key Vault](../../key-vault/general/overview.md) zugreifen kann. Key Vault dient als Bootstrap und ermöglicht es Ihrer Clientanwendung, mithilfe des Geheimnisses auf Ressourcen zuzugreifen, die nicht von Azure Active Directory (AD) geschützt sind. Verwaltete Dienstidentitäten werden von Azure automatisch verwaltet und ermöglichen Ihnen die Authentifizierung für Dienste, die die Azure AD-Authentifizierung unterstützen, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen.

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Gewähren des Zugriffs auf ein in einer Key Vault gespeicherten Geheimnisses für den virtuellen Computer
> * Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Abrufen des Geheimnisses aus der Key Vault 

## <a name="prerequisites"></a>Voraussetzungen

- Kenntnisse im Bereich verwaltete Identitäten. Wenn Sie mit der Funktion für verwaltete Identitäten für Azure-Ressourcen nicht vertraut sind, finden Sie hier eine [Übersicht](overview.md). 
- Ein Azure-Konto. [Registrieren Sie sich für ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/)
- Berechtigungen vom Typ „Besitzer“ für den entsprechenden Bereich (Ihr Abonnement oder die Ressourcengruppe), um die erforderlichen Schritte zur Ressourcenerstellung und Rollenverwaltung durchführen zu können. Wenn Sie Unterstützung bei der Rollenzuweisung benötigen, finden Sie weitere Informationen unter [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](../../role-based-access-control/role-assignments-portal.md).
- Außerdem benötigen Sie einen virtuellen Windows-Computer, auf dem systemseitig zugewiesene verwaltete Identitäten aktiviert sind.
  - Wenn Sie einen virtuellen Computer für dieses Tutorial erstellen müssen, können Sie den Artikel [Aktivieren einer vom System zugewiesenen verwalteten Identität beim Erstellen eines virtuellen Computers](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) durcharbeiten.

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors  

In diesem Abschnitt wird gezeigt, wie Sie einem virtuellen Computer den Zugriff auf ein in einer Key Vault-Instanz gespeichertes Geheimnis gewähren. Mithilfe von verwalteten Identitäten für Azure-Ressourcen kann der Code Zugriffstoken zur Authentifizierung von Ressourcen abrufen, die die Azure AD-Authentifizierung unterstützen.Allerdings unterstützen nicht alle Azure-Dienste die Azure AD-Authentifizierung. Um verwaltete Identitäten für Azure-Ressourcen mit diesen Diensten zu verwenden, speichern Sie die Dienstanmeldeinformationen in Azure Key Vault, und greifen Sie mit der verwalteten Identität des virtuellen Computers auf Key Vault zu, um die Anmeldeinformationen abzurufen.

Zunächst müssen Sie eine Key Vault-Instanz erstellen und der systemseitig zugewiesenen verwalteten Identität des virtuellen Computers den Zugriff darauf erteilen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).
1. Wählen Sie oben auf der linken Navigationsleiste **Ressource erstellen** aus.  
1. Geben Sie im Feld **Marketplace durchsuchen** den Suchbegriff **Key Vault** ein, und drücken Sie die **EINGABETASTE**.  
1. Wählen Sie in den Ergebnissen **Key Vault** aus.
1. Klicken Sie auf **Erstellen**
1. Geben Sie unter **Name** einen Namen für die neue Key Vault an.

    ![Bildschirm zum Erstellen eines Schlüsseltresors](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

1. Geben Sie alle erforderlichen Informationen ein. Wählen Sie dabei unbedingt das Abonnement und die Ressourcengruppe aus, in denen Sie den für dieses Tutorial verwendeten virtuellen Computer erstellt haben.
1. Wählen Sie **Überprüfen + erstellen** aus.
1. Klicken Sie auf **Erstellen**

### <a name="create-a-secret"></a>Erstellen eines Geheimnisses

Fügen Sie als Nächstes der Key Vault-Instanz ein Geheimnis hinzu, das Sie später mithilfe von Code abrufen können, der auf dem virtuellen Computer ausgeführt wird. Im Rahmen dieses Tutorials wird PowerShell verwendet. Die gleichen Konzepte gelten jedoch für jeden Code, der auf diesem virtuellen Computer ausgeführt wird.

1. Navigieren Sie zur neu erstellten Key Vault-Instanz.
1. Wählen Sie **Geheimnisse** aus, und klicken Sie auf **Hinzufügen**.
1. Wählen Sie die Option **Generieren/importieren** aus.
1. Lassen Sie auf dem Bildschirm **Geheimnis erstellen** unter **Uploadoptionen** die Option **Manuell** ausgewählt.
1. Geben Sie einen Namen und einen Wert für das Geheimnis ein.  Dabei kann es sich um einen beliebigen Wert handeln. 
1. Lassen Sie das Aktivierungs- und das Ablaufdatum leer, und übernehmen Sie für **Aktiviert** die ausgewählte Option **Ja**. 
1. Klicken Sie auf **Erstellen**, um das Geheimnis zu erstellen.

   ![Erstellen eines Geheimnisses](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Gewähren von Zugriff

Der vom virtuellen Computer verwalteten Identität muss Zugriff gewährt werden, damit sie das in Key Vault gespeicherte Geheimnis lesen kann.

1. Navigieren Sie zur neu erstellten Key Vault-Instanz.
1. Wählen Sie im Menü auf der linken Seite die Option **Zugriffsrichtlinie** aus.
1. Wählen Sie **Zugriffsrichtlinie hinzufügen** aus.

   ![Key Vault-Bildschirm zum Erstellen einer Zugriffsrichtlinie](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. Wählen Sie im Abschnitt **Zugriffsrichtlinie hinzufügen** unter **Anhand einer Vorlage konfigurieren (optional)** im Pulldownmenü die Option **Verwaltung von Geheimnissen** aus.
1. Wählen Sie **Prinzipal auswählen**, und geben Sie im Suchfeld den Namen des zuvor erstellten virtuellen Computers ein.  Wählen Sie in der Ergebnisliste den virtuellen Computer und dann **Auswählen** aus.
1. Wählen Sie **Hinzufügen** aus.
1. Wählen Sie **Speichern** aus.


## <a name="access-data"></a>Zugreifen auf Daten  

In diesem Abschnitt wird gezeigt, wie Sie mithilfe der VM-Identität ein Zugriffstoken abrufen und es zum Abrufen des Geheimnisses aus Key Vault verwenden. Wenn bei Ihnen nicht PowerShell 4.3.1 oder höher installiert ist, müssen Sie [die neueste Version herunterladen und installieren](/powershell/azure/).

Zuerst wird mit der systemseitig zugewiesenen verwalteten Identität des virtuellen Computers ein Zugriffstoken für die Authentifizierung bei Key Vault abgerufen:
 
1. Navigieren Sie im Portal zu **Virtuelle Computer**, wechseln Sie dann zu Ihrem virtuellen Windows-Computer, und klicken Sie in der **Übersicht** auf **Verbinden**.
2. Geben Sie Ihren **Benutzernamen** und Ihr **Kennwort** ein, das Sie beim Erstellen des **rs** hinzugefügt haben.  
3. Sie haben nun eine **Remotedesktopverbindung** mit dem virtuellen Computer erstellt. Öffnen Sie jetzt PowerShell in der Remotesitzung.  
4. Rufen Sie in PowerShell die Webanforderung für den Mandanten auf, um das Token für den lokalen Host am spezifischen Port des virtuellen Computers abzurufen.  

Die PowerShell-Anforderung:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

Die Antwort sieht in etwa wie folgt aus:

![Anforderung mit Tokenantwort](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

Extrahieren Sie dann das Zugriffstoken aus der Antwort.  

```powershell
   $KeyVaultToken = $Response.access_token
```

Verwenden Sie abschließend den PowerShell-Befehl „Invoke-WebRequest“, um das zuvor in der Key Vault erstellte Geheimnis abzurufen. Übergeben Sie dabei das Zugriffstoken im Autorisierungsheader.  Sie benötigen die URL Ihrer Key Vaults. Diese befindet sich im Abschnitt **Zusammenfassung** der Seite **Übersicht** der Key Vault.  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

Die Antwort sieht so aus: 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

Nachdem Sie das Geheimnis aus der Key Vault abgerufen haben, können Sie es für die Authentifizierung bei einem Dienst verwenden, für den ein Name und ein Kennwort angegeben werden müssen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Gehen Sie wie folgt vor, wenn Sie die Ressourcen bereinigen möchten: Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressourcengruppen** und dann die Ressourcengruppe aus, die in diesem Tutorial erstellt wurde (z. B. `mi-test`), und verwenden Sie anschließend den Befehl **Ressourcengruppe löschen**.

Diesen Schritt können Sie alternativ über [PowerShell oder die CLI](../../azure-resource-manager/management/delete-resource-group.md) ausführen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Windows-Computers verwenden, um auf Azure Key Vault zuzugreifen.  Weitere Informationen zu Azure Key Vault finden Sie in folgendem Artikel:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)