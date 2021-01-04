---
title: Abfolge beim Betriebssystem- und Runtimepatching
description: Erfahren Sie, wie Azure App Service das Betriebssystem und Runtimes aktualisiert, welche Runtimes und Patchebenen Ihre App hat und wie Sie Updateankündigungen erhalten.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8b52223aea0f0bdfecf58906ac192e893da3b47d
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558486"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Patchen von Betriebssystem und Runtime in Azure App Service

Dieser Artikel veranschaulicht das Abrufen bestimmter Versionsinformationen zu Betriebssystem oder Software in [App Service](overview.md). 

App Service ist eine Platform as a Service, d.h., Betriebssystem und Anwendungsstapel werden für Sie von Azure verwaltet; Sie verwalten nur Ihre Anwendung und deren Daten. Mehr Kontrolle über Betriebssystem und Anwendungsstapel haben Sie in [Azure Virtual Machines](../virtual-machines/index.yml). Vor diesem Hintergrund ist es dennoch für Sie als App Service-Benutzer hilfreich, weitere Informationen zu kennen, z.B.:

-   Wie und wann werden Betriebssystemupdates angewendet?
-   Wie wird App Service für erhebliche Sicherheitsrisiken (z.B. Zero-Day-Bedrohungen) gepatcht?
-   Von welchen Betriebssystem- und Runtimeversionen werden Ihre Apps ausgeführt?

Aus Sicherheitsgründen werden bestimmte Einzelheiten von Sicherheitsinformationen nicht veröffentlicht. Allerdings zielt der Artikel darauf ab, Bedenken durch Maximierung der Transparenz des Prozesses abzuschwächen, und indem Sie erfahren, wie Sie hinsichtlich sicherheitsbezogener Ankündigungen oder Runtimeupdates auf dem neuesten Stand bleiben können.

## <a name="how-and-when-are-os-updates-applied"></a>Wie und wann werden Betriebssystemupdates angewendet?

Azure verwaltet Betriebssystempatches auf zwei Ebenen, den physischen Servern und den virtuellen Gastcomputern (VMs), die die App Service-Ressourcen ausführen. Beide werden monatlich aktualisiert, was dem monatlichen [Patch-Dienstag](/security-updates/)-Zeitplan entspricht. Diese Updates werden automatisch auf eine Weise angewendet, die die Hochverfügbarkeits-SLA der Azure-Dienste erfüllt. 

Ausführliche Informationen zur Anwendung von Updates finden Sie unter [Demystifying the magic behind App Service OS updates](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html) (Entmystifizierung des hinter App Service-Betriebssystemupdates steckenden Zaubers).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Wie geht Azure mit erheblichen Sicherheitsrisiken um?

Wenn schwerwiegende Sicherheitslücken sofortiges Patchen erfordern, z.B. [Zero-Day-Bedrohungen](https://wikipedia.org/wiki/Zero-day_(computing)), werden die Updates mit hoher Priorität von Fall zu Fall behandelt.

Der [Azure Security Blog](https://azure.microsoft.com/blog/topics/security/) hält Sie stets über kritische Sicherheitsankündigungen in Azure auf dem Laufenden. 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Wann werden unterstützte Language Runtimes aktualisiert, hinzugefügt, oder wann sind sie veraltet?

Neue stabile Versionen der unterstützten Language Runtimes (Hauptversion, Nebenversion oder Patch) werden in regelmäßigen Abständen den App Service-Instanzen hinzugefügt. Einige Updates überschreiben die vorhandene Installation, während andere parallel zu vorhandenen Versionen installiert werden. Eine überschreibende Installation bedeutet, dass Ihre App automatisch auf der aktualisierten Runtime ausgeführt wird. Eine parallele Installation bedeutet, dass Sie Ihre App manuell migrieren müssen, um eine neue Version der Runtime zu nutzen. Weitere Informationen finden Sie in einem der Unterabschnitte.

Runtimeupdates und Veraltungen werden hier angekündigt:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Hier bereitgestellte Informationen gelten für Language Runtimes, die in einer App Service-App integriert sind. Eine benutzerdefinierte Runtime, die Sie in App Service hochladen, bleibt z.B. unverändert, solange Sie sie nicht manuell aktualisieren.
>
>

### <a name="new-patch-updates"></a>Neue Patchupdates

Patchupdates für .NET-, PHP-, Java SDK- oder Tomcat-Version werden automatisch durch Überschreiben der vorhandenen Installation mit der aktuellen Version angewendet. Node.js-Patchupdates werden parallel zu den vorhandenen Versionen installiert (ähnlich wie Haupt- und Nebenversionen im nächsten Abschnitt). Neue Python-Patchversionen können manuell über [Websiteerweiterungen](https://azure.microsoft.com/blog/azure-web-sites-extensions/) parallel zu integrierten Python-Installationen installiert werden.

### <a name="new-major-and-minor-versions"></a>Neue Haupt- und Nebenversionen

Wenn eine neue Haupt- oder Nebenversion hinzugefügt wird, wird sie parallel zu vorhandenen Versionen installiert. Sie können Ihre App manuell auf die neue Version aktualisieren. Wenn Sie die Runtimeversion in einer Konfigurationsdatei (z.B. `web.config` und `package.json`) konfiguriert haben, müssen Sie sie mit der gleichen Methode aktualisieren. Bei Verwendung einer App Service-Einstellung zum Konfigurieren der Runtimeversion können Sie sie im [Azure-Portal](https://portal.azure.com) oder durch Ausführen eines [Azure CLI](/cli/azure/get-started-with-azure-cli)-Befehls in der [Cloud Shell](../cloud-shell/overview.md) ändern, wie in den folgenden Beispielen gezeigt:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Veraltete Versionen  

Wenn eine ältere Version als veraltet markiert ist, wird das Entfernungsdatum angekündigt, damit Sie das Upgrade der Runtimeversion entsprechend planen können. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Wie kann ich den Updatestatus von Betriebssystem und Runtime auf meinen Instanzen abfragen?  

Der Zugriff auf kritische Betriebssysteminformationen ist zwar gesperrt (siehe [Betriebssystemfunktionen für Azure App Service](operating-system-functionality.md)), aber die [Kudu-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console) ermöglicht Ihnen, Ihre App Service-Instanz bezüglich der Betriebssystem- und Runtimeversion abzufragen. 

Die folgende Tabelle zeigt, wie Sie die Versionen von Windows und der Language Runtime, die Ihre Apps ausführen, abfragen:

| Information | Ort | 
|-|-|
| Windows-Version | Siehe `https://<appname>.scm.azurewebsites.net/Env.cshtml` (unter Systeminfo) |
| .NET-Version | Geben Sie unter `https://<appname>.scm.azurewebsites.net/DebugConsole` im Eingabeaufforderungsfenster den folgenden Befehl ein: <br>`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full"` |
| .NET Core-Version | Geben Sie unter `https://<appname>.scm.azurewebsites.net/DebugConsole` im Eingabeaufforderungsfenster den folgenden Befehl ein: <br> `dotnet --version` |
| PHP-Version | Geben Sie unter `https://<appname>.scm.azurewebsites.net/DebugConsole` im Eingabeaufforderungsfenster den folgenden Befehl ein: <br> `php --version` |
| Standardmäßige Node.js-Version | Führen Sie in der [Cloud Shell](../cloud-shell/overview.md)-Instanz folgenden Befehl aus: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-Version | Geben Sie unter `https://<appname>.scm.azurewebsites.net/DebugConsole` im Eingabeaufforderungsfenster den folgenden Befehl ein: <br> `python --version` |  
| Java-Version | Geben Sie unter `https://<appname>.scm.azurewebsites.net/DebugConsole` im Eingabeaufforderungsfenster den folgenden Befehl ein: <br> `java -version` |  

> [!NOTE]  
> Der Zugriff auf den Registrierungsspeicherort `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, wo Informationen zu [„KB“-Patches](/security-updates/SecurityBulletins/securitybulletins) gespeichert sind, ist gesperrt.
>
>

## <a name="more-resources"></a>Weitere Ressourcen

[Trust Center: Sicherheit](https://www.microsoft.com/en-us/trustcenter/security)  
[64 bit ASP.NET Core on Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7) (64-Bit-ASP.NET Core in Azure App Service)
