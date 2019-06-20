---
title: Herstellen einer Verbindung mit Azure Deutschland über Visual Studio | Microsoft-Dokumentation
description: Informationen zum Verwalten Ihres Abonnements in Azure Deutschland über Visual Studio
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 9d7131d7ce6a8fd86ba2f154b8f7ca9db8afc3ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61369955"
---
# <a name="connect-to-azure-germany-by-using-visual-studio"></a>Herstellen einer Verbindung mit Azure Deutschland über Visual Studio
Entwickler verwenden Visual Studio beim Erstellen von Lösungen zur einfachen Verwaltung ihrer Azure-Abonnements. Sie können derzeit in der Visual Studio-Benutzeroberfläche keine Verbindung mit Azure Deutschland konfigurieren.  

## <a name="visual-studio-2017"></a>Visual Studio 2017
Visual Studio 2017 benötigt zum Herstellen einer Verbindung mit Azure Deutschland eine Konfigurationsdatei für Visual Studio. Wenn diese Datei vorhanden ist, stellt Visual Studio eine Verbindung mit Azure Deutschland statt mit der globalen Azure-Umgebung her.

### <a name="create-a-configuration-file-for-azure-germany"></a>Erstellen einer Konfigurationsdatei für Azure Deutschland
Erstellen Sie eine Datei namens **AadProvider.Configuration.json** mit folgendem Inhalt:

        {
          "AuthenticationQueryParameters":null,
          "AsmEndPoint":"https://management.microsoftazure.de/",
          "Authority":"https://login.microsoftonline.de/",
          "AzureResourceManagementEndpoint":"https://management.microsoftazure.de/",
          "AzureResourceManagementAudienceEndpoints":["https://management.core.cloudapi.de/"],
          "ClientIdentifier":"872cd9fa-d31f-45e0-9eab-6e460a02d1f1",
          "EnvironmentName":"BlackForest",
          "GraphEndpoint":"https://graph.cloudapi.de",
          "MsaHomeTenantId":"f577cd82-810c-43f9-a1f6-0cc532871050",
          "NativeClientRedirect":"urn:ietf:wg:oauth:2.0:oob",
          "PortalEndpoint":"https://portal.core.cloudapi.de/",
          "ResourceEndpoint":"https://management.microsoftazure.de/",
          "ValidateAuthority":true,
          "VisualStudioOnlineEndpoint":"https://app.vssps.visualstudio.com/",
          "VisualStudioOnlineAudience":"499b84ac-1321-427f-aa17-267ca6975798"
        }

### <a name="update-visual-studio-for-azure-germany"></a>Aktualisieren von Visual Studio für Azure Deutschland

1.  Schließen Sie Visual Studio.
2.  Legen Sie die Datei **AadProvider.Configuration.json** in **%localappdata%\\.IdentityService\AadConfigurations** ab. Erstellen Sie diesen Ordner, wenn er nicht vorhanden ist.
3.  Starten Sie Visual Studio, und beginnen Sie mit der Verwendung Ihres Azure Deutschland-Kontos.

> [!NOTE]
> Mit der Konfigurationsdatei ist nur der Zugriff auf Azure Deutschland-Abonnements möglich. Zuvor konfigurierte Abonnements werden zwar noch angezeigt, funktionieren jedoch nicht, da Visual Studio jetzt mit Azure Deutschland statt mit der globalen Azure-Umgebung verbunden ist. Um eine Verbindung mit der globalen Azure-Umgebung herzustellen, entfernen Sie die Datei.
> 
> 

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>Zurücksetzen einer Visual Studio-Verbindung auf Azure Deutschland
Damit Visual Studio eine Verbindung mit der globalen Azure-Umgebung herstellen kann, müssen Sie die Konfigurationsdatei entfernen, die eine Verbindung mit Azure Deutschland ermöglicht.

1.  Schließen Sie Visual Studio.
2.  Löschen Sie den Ordner **%localappdata%\.IdentityService\AadConfigurations**, oder benennen Sie ihn um.
3.  Starten Sie Visual Studio neu, und beginnen Sie mit der Verwendung Ihres Kontos für die globale Azure-Umgebung.

> [!NOTE]
> Nachdem Sie diese Konfiguration zurückgesetzt haben, können Sie nicht mehr auf Ihre Azure Deutschland-Abonnements zugreifen.
> 
>

## <a name="visual-studio-2015"></a>Visual Studio 2015
Visual Studio 2015 benötigt zum Herstellen einer Verbindung mit Azure Deutschland eine Registrierungsänderung für Visual Studio. Sobald dieser Registrierungsschlüssel festgelegt ist, stellt Visual Studio eine Verbindung mit Azure Deutschland statt mit der globalen Azure-Umgebung her.

### <a name="update-visual-studio-for-azure-germany"></a>Aktualisieren von Visual Studio für Azure Deutschland
Sie müssen die Registrierung aktualisieren, damit Visual Studio eine Verbindung mit Azure Deutschland herstellen kann.

1. Schließen Sie Visual Studio.
2. Erstellen Sie eine Textdatei mit dem Namen **VisualStudioForAzureGermany.reg**.
3. Kopieren Sie den folgenden Text, und fügen Sie ihn in **VisualStudioForAzureGermany.reg** ein:
   
        Windows Registry Editor Version 5.00
 
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login.microsoftonline.de/"
        "adaluri"="https://management.microsoftazure.de"
        "AzureRMEndpoint"="https://management.microsoftazure.de"
        "AzureRMAudienceEndpoint"="https://management.core.cloudapi.de"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.cloudapi.de"
        "AadApplicationTenant"="f577cd82-810c-43f9-a1f6-0cc532871050"

4. Speichern Sie die Datei, und doppelklicken Sie anschließend darauf, um sie auszuführen. Sie werden aufgefordert, die Datei in der Registrierung zusammenzuführen.
5. Starten Sie Visual Studio. Nun können Sie den [Cloud-Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md) mit Ihrem Azure Deutschland-Konto verwenden.

> [!NOTE]
> Nach dem Festlegen dieses Registrierungsschlüssels können Sie nur auf Azure Deutschland-Abonnements zugreifen. Zuvor konfigurierte Abonnements werden zwar noch angezeigt, funktionieren jedoch nicht, da Visual Studio jetzt mit Azure Deutschland statt mit der globalen Azure-Umgebung verbunden ist. Um eine Verbindung mit der globalen Azure-Umgebung herzustellen, machen Sie die Änderungen rückgängig.
> 
> 

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>Zurücksetzen einer Visual Studio-Verbindung auf Azure Deutschland
Damit Visual Studio eine Verbindung mit der globalen Azure-Umgebung herstellen kann, müssen Sie die Registrierungseinträge entfernen, die eine Verbindung mit Azure Deutschland ermöglichen.

1. Schließen Sie Visual Studio.
2. Erstellen Sie eine Textdatei mit dem Namen **VisualStudioForAzureGermany_Remove.reg**.
3. Kopieren Sie den folgenden Text, und fügen Sie ihn in **VisualStudioForAzureGermany_Remove.reg** ein:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
        
4. Speichern Sie die Datei, und doppelklicken Sie anschließend darauf, um sie auszuführen. Sie werden aufgefordert, die Datei in der Registrierung zusammenzuführen.
5. Starten Sie Visual Studio.

> [!NOTE]
> Nach dem Zurücksetzen dieses Registrierungsschlüssels werden Ihre Azure Deutschland-Abonnements zwar angezeigt, der Zugriff darauf ist jedoch nicht möglich. Sie können diese sicher entfernen.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Herstellen der Verbindung mit Azure Deutschland finden Sie in den folgenden Ressourcen:

* [Herstellen einer Verbindung mit Azure Deutschland über PowerShell](./germany-get-started-connect-with-ps.md)
* [Herstellen einer Verbindung mit Azure Deutschland über die Azure CLI](./germany-get-started-connect-with-cli.md)
* [Herstellen einer Verbindung mit Azure Deutschland über das Azure-Portal](./germany-get-started-connect-with-portal.md)





