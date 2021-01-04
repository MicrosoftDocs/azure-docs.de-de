---
title: Herstellen einer Verbindung mit Azure Deutschland über Visual Studio | Microsoft-Dokumentation
description: Informationen zum Verwalten Ihres Abonnements in Azure Deutschland über Visual Studio
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: f5749ed9d2080a92a33993d8131595359110b75e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018603"
---
# <a name="connect-to-azure-germany-by-using-visual-studio"></a>Herstellen einer Verbindung mit Azure Deutschland über Visual Studio

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Entwickler verwenden Visual Studio beim Erstellen von Lösungen zur einfachen Verwaltung ihrer Azure-Abonnements. Auf der Visual Studio-Benutzeroberfläche kann derzeit keine Verbindung mit Azure Deutschland konfiguriert werden.

## <a name="visual-studio-2017-and-visual-studio-2019"></a>Visual Studio 2017 und Visual Studio 2019

Visual Studio benötigt zum Herstellen einer Verbindung mit Azure Deutschland eine Konfigurationsdatei. Wenn diese Datei vorhanden ist, stellt Visual Studio eine Verbindung mit Azure Deutschland statt mit der globalen Azure-Umgebung her.

### <a name="create-a-configuration-file-for-azure-germany"></a>Erstellen einer Konfigurationsdatei für Azure Deutschland

Erstellen Sie eine Datei namens *AadProvider.Configuration.json* mit folgendem Inhalt:

```json
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
```

### <a name="update-visual-studio-for-azure-germany"></a>Aktualisieren von Visual Studio für Azure Deutschland

1. Schließen Sie Visual Studio.
1. Legen Sie die Datei *AadProvider.Configuration.json* in *%localappdata%\\.IdentityService\AadConfigurations* ab. Erstellen Sie diesen Ordner, wenn er nicht vorhanden ist.
1. Starten Sie Visual Studio, und beginnen Sie mit der Verwendung Ihres Azure Deutschland-Kontos.

> [!NOTE]
> Mit der Konfigurationsdatei ist nur der Zugriff auf Azure Deutschland-Abonnements möglich. Zuvor konfigurierte Abonnements werden zwar noch angezeigt, funktionieren jedoch nicht, da Visual Studio jetzt mit Azure Deutschland statt mit der globalen Azure-Umgebung verbunden ist. Um eine Verbindung mit der globalen Azure-Umgebung herzustellen, entfernen Sie die Datei.
>

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>Zurücksetzen einer Visual Studio-Verbindung auf Azure Deutschland

Damit Visual Studio eine Verbindung mit der globalen Azure-Umgebung herstellen kann, müssen Sie die Konfigurationsdatei entfernen, die eine Verbindung mit Azure Deutschland ermöglicht.

1. Schließen Sie Visual Studio.
1. Löschen Sie den Ordner *%localappdata%\.IdentityService\AadConfigurations*, oder benennen Sie ihn um.
1. Starten Sie Visual Studio neu, und beginnen Sie mit der Verwendung Ihres Kontos für die globale Azure-Umgebung.

> [!NOTE]
> Nach dem Zurücksetzen dieser Konfiguration können Sie nicht mehr auf Ihre Azure Deutschland-Abonnements zugreifen.
>

## <a name="visual-studio-2015"></a>Visual Studio 2015

Bei Visual Studio 2015 ist eine Registrierungsänderung erforderlich, um eine Verbindung mit Azure Deutschland herstellen zu können. Nachdem Sie diesen Registrierungsschlüssel festgelegt haben, stellt Visual Studio eine Verbindung mit Azure Deutschland statt mit der globalen Azure-Umgebung her.

### <a name="update-visual-studio-2015-for-azure-germany"></a>Aktualisieren von Visual Studio 2015 für Azure Deutschland

Sie müssen die Registrierung aktualisieren, damit Visual Studio eine Verbindung mit Azure Deutschland herstellen kann.

1. Schließen Sie Visual Studio.
1. Erstellen Sie eine Textdatei mit dem Namen *VisualStudioForAzureGermany.reg*.
1. Kopieren Sie den folgenden Text, und fügen Sie ihn in *VisualStudioForAzureGermany.reg* ein:

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"="https://login.microsoftonline.de/"
"adaluri"="https://management.microsoftazure.de"
"AzureRMEndpoint"="https://management.microsoftazure.de"
"AzureRMAudienceEndpoint"="https://management.core.cloudapi.de"
"EnableAzureRMIdentity"="true"
"GraphUrl"="graph.cloudapi.de"
"AadApplicationTenant"="f577cd82-810c-43f9-a1f6-0cc532871050"
```

1. Speichern Sie die Datei, und doppelklicken Sie anschließend darauf, um sie auszuführen. Sie werden aufgefordert, die Datei mit Ihrer Registrierung zusammenzuführen.
1. Starten Sie Visual Studio. Nun können Sie den [Cloud-Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer) mit Ihrem Azure Deutschland-Konto verwenden.

> [!NOTE]
> Nach dem Festlegen dieses Registrierungsschlüssels können Sie nur auf Azure Deutschland-Abonnements zugreifen. Zuvor konfigurierte Abonnements werden zwar noch angezeigt, funktionieren jedoch nicht, da Visual Studio jetzt mit Azure Deutschland statt mit der globalen Azure-Umgebung verbunden ist. Um eine Verbindung mit der globalen Azure-Umgebung herzustellen, machen Sie die Änderungen rückgängig.
>

### <a name="revert-a-visual-studio-2015-connection-to-azure-germany"></a>Zurücksetzen einer Visual Studio 2015-Verbindung mit Azure Deutschland

Damit Visual Studio eine Verbindung mit der globalen Azure-Umgebung herstellen kann, müssen Sie die Registrierungseinträge entfernen, die eine Verbindung mit Azure Deutschland ermöglichen.

1. Schließen Sie Visual Studio.
1. Erstellen Sie eine Textdatei mit dem Namen *VisualStudioForAzureGermany_Remove.reg*.
1. Kopieren Sie den folgenden Text, und fügen Sie ihn in *VisualStudioForAzureGermany_Remove.reg* ein:

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"=-
"adaluri"=-
"AzureRMEndpoint"=-
"AzureRMAudienceEndpoint"=-
"EnableAzureRMIdentity"=-
"GraphUrl"=-
```

1. Speichern Sie die Datei, und doppelklicken Sie anschließend darauf, um sie auszuführen. Sie werden aufgefordert, die Datei mit Ihrer Registrierung zusammenzuführen.
1. Starten Sie Visual Studio.

> [!NOTE]
> Nach dem Zurücksetzen dieses Registrierungsschlüssels werden Ihre Azure Deutschland-Abonnements zwar angezeigt, es kann jedoch nicht auf sie zugegriffen werden. Sie können diese sicher entfernen.
>

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Herstellen der Verbindung mit Azure Deutschland finden Sie in den folgenden Ressourcen:

* [Herstellen einer Verbindung mit Azure Deutschland über PowerShell](./germany-get-started-connect-with-ps.md)
* [Herstellen einer Verbindung mit Azure Deutschland über die Azure CLI](./germany-get-started-connect-with-cli.md)
* [Herstellen einer Verbindung mit Azure Deutschland über das Azure-Portal](./germany-get-started-connect-with-portal.md)