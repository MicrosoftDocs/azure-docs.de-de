---
title: Ausführen Ihrer App aus einem ZIP-Paket
description: Hier erfahren Sie, wie Sie das ZIP-Paket Ihrer App mit Unteilbarkeit bereitstellen. Verbessern Sie die Vorhersagbarkeit und Zuverlässigkeit des Verhaltens Ihrer App während der ZIP-Bereitstellung.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 3440653455626af4e3705d89349a66d6bf2fbfc0
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008128"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Direktes Ausführen Ihrer App in Azure App Service aus einem ZIP-Paket

In [Azure App Service](overview.md) können Sie Ihre Apps direkt aus einer ZIP-Bereitstellungspaketdatei ausführen. In diesem Artikel erfahren Sie, wie Sie diese Funktion in Ihrer App aktivieren.

Alle anderen Bereitstellungsmethoden in App Service haben eines gemeinsam: Ihre Dateien werden unter *D:\home\site\wwwroot* in Ihrer App bereitgestellt (bei Linux-Apps unter */home/site/wwwroot*). Da dieses Verzeichnis zur Laufzeit von Ihrer App verwendet wird, kann es vorkommen, dass die Bereitstellung aufgrund von Dateisperrkonflikten nicht erfolgreich ist und dass sich die App unvorhersehbar verhält, da einige der Dateien noch nicht aktualisiert wurden.

Wenn Sie die App dagegen direkt aus einem Paket ausführen, werden die Dateien im Paket nicht in das Verzeichnis *wwwroot* kopiert. Stattdessen wird das eigentliche ZIP-Paket direkt als schreibgeschütztes Verzeichnis *wwwroot* eingebunden. Die direkte Ausführung aus einem Paket hat mehrere Vorteile:

- Keine Dateisperrkonflikte zwischen Bereitstellung und Laufzeit.
- Gewährleistet, dass immer nur vollständig bereitgestellte Apps ausgeführt werden.
- Kann in einer Produktions-App (mit Neustart) bereitgestellt werden.
- Verbessert die Leistung von Azure Resource Manager-Bereitstellungen.
- Kann Kaltstartzeiten verringern, insbesondere für JavaScript-Funktionen mit großen npm-Paketstrukturen.

> [!NOTE]
> Derzeit werden nur ZIP-Paketdateien unterstützt.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Ermöglichen der Ausführung aus einem Paket

Die Ausführung aus einem Paket wird durch die App-Einstellung `WEBSITE_RUN_FROM_PACKAGE` ermöglicht. Führen Sie zum Festlegen dieser Einstellung den folgenden Befehl über die Azure-Befehlszeilenschnittstelle aus:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

Mit `WEBSITE_RUN_FROM_PACKAGE="1"` können Sie Ihre App aus einem für Ihre App lokalen Paket ausführen. Es besteht aber auch die Möglichkeit zum [Ausführen aus einem Remotepaket](#run-from-external-url-instead).

## <a name="run-the-package"></a>Ausführen des Pakets

Die einfachste Möglichkeit zum Ausführen eines Pakets in App Service stellt der Azure CLI-Befehl [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) dar. Beispiel:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Da die App-Einstellung `WEBSITE_RUN_FROM_PACKAGE` festgelegt ist, wird der Paketinhalt durch diesen Befehl nicht in das Verzeichnis *D:\home\site\wwwroot* Ihrer App extrahiert. Stattdessen wird die ZIP-Datei unverändert in *D:\home\data\SitePackages* hochgeladen, und es wird eine Datei namens *packagename.txt* im gleichen Verzeichnis erstellt, die den Namen des ZIP-Pakets enthält, das zur Laufzeit geladen werden soll. Wenn Sie Ihr ZIP-Paket auf andere Weise hochladen (beispielsweise per [FTP](deploy-ftp.md)), müssen Sie das Verzeichnis *D:\home\data\SitePackages* und die Datei *packagename.txt* manuell erstellen.

Durch den Befehl wird die App außerdem neu gestartet. Da `WEBSITE_RUN_FROM_PACKAGE` festgelegt ist, bindet App Service das hochgeladene Paket als schreibgeschütztes Verzeichnis *wwwroot* ein und führt die App direkt aus diesem eingebundenen Verzeichnis aus.

## <a name="run-from-external-url-instead"></a>Ausführen von einer externen URL aus

Ein Paket kann auch von einer externen URL aus (beispielsweise Azure Blob Storage) ausgeführt werden. Sie können den [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) zum Hochladen von Dateien in Ihr Blob Storage-Konto verwenden. Es empfiehlt sich, einen privaten Speichercontainer mit einer [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) zu verwenden, damit die App Service-Laufzeit sicher auf das Paket zugreifen kann. 

Wenn Sie Ihre Datei in Blob Storage hochgeladen haben und über eine SAS-URL für die Datei verfügen, können Sie die App-Einstellung `WEBSITE_RUN_FROM_PACKAGE` auf die URL festlegen. Im folgenden Beispiel wird dazu die Azure-Befehlszeilenschnittstelle verwendet:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Wenn Sie ein aktualisiertes Paket mit dem gleichen Namen in Blob Storage veröffentlichen, müssen Sie Ihre App neu starten, damit das aktualisierte Paket in App Service geladen wird.

## <a name="troubleshooting"></a>Problembehandlung

- Beim direkten Ausführen aus einem Paket wird `wwwroot` schreibgeschützt. Wenn Ihre App versucht, Dateien in dieses Verzeichnis zu schreiben, tritt ein Fehler auf.
- Das TAR- und das GZIP-Format werden nicht unterstützt.
- Dieses Feature ist nicht mit [lokalem Cache](overview-local-cache.md) kompatibel.
- Um die Kaltstartleistung zu verbessern, verwenden Sie die lokale ZIP-Option (`WEBSITE_RUN_FROM_PACKAGE`=1).

## <a name="more-resources"></a>Weitere Ressourcen

- [Continuous Deployment in Azure App Service](deploy-continuous-deployment.md)
- [Bereitstellen der App in Azure App Service mit einer ZIP- oder WAR-Datei](deploy-zip.md)
