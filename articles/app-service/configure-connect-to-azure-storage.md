---
title: Hinzufügen von Azure Storage (Container)
description: Informieren Sie sich, wie Sie eine benutzerdefinierte Netzwerkfreigabe in einer containerisierten App in Azure App Service anfügen. Nutzen Sie Dateien in verschiedenen Apps, verwalten Sie statische Inhalte remote, greifen Sie lokal darauf zu u. v. m.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: e75eef86a4a0c679a44a61267f94d337538daaa8
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007618"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Zugreifen auf Azure Storage (Vorschau) als Netzwerkfreigabe von einem Container in App Service

::: zone pivot="container-windows"

In diesem Leitfaden erfahren Sie, wie Sie Azure Storage-Dateien als Netzwerkfreigabe an einen Windows-Container in App Service anfügen. Nur [Azure Files-Dateifreigaben](../storage/files/storage-how-to-use-files-cli.md) und [Premium-Dateifreigaben](../storage/files/storage-how-to-create-premium-fileshare.md) werden unterstützt. Zu den Vorteilen gehören sicherer Inhalt, die Portabilität des Inhalts, Zugriff auf mehrere Apps und mehrere Übertragungsmethoden.

> [!NOTE]
>Azure Storage in App Service befindet sich **in der Vorschau** und wird für **Produktionsszenarien** noch **nicht unterstützt**.

::: zone-end

::: zone pivot="container-linux"

Der vorliegende Leitfaden zeigt, wie Sie Azure Storage an einen Linux-Container in App Service anfügen. Zu den Vorteilen gehören sicherer Inhalt, die Portabilität des Inhalts, persistente Speicherung, Zugriff auf mehrere Apps und mehrere Übertragungsmethoden.

> [!NOTE]
>Azure Storage in App Service befindet sich für App Service unter Linux und Web-App für Container **in der Vorschau**. Diese Funktion wird für **Produktionsszenarien** **nicht unterstützt**.

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

::: zone pivot="container-windows"

- [Eine vorhandene Windows-Container-App in Azure App Service](quickstart-custom-container.md)
- [Erstellen einer Azure Files-Dateifreigabe](../storage/files/storage-how-to-use-files-cli.md)
- [Hochladen von Dateien in eine Azure Files-Dateifreigabe](../storage/files/storage-files-deployment-guide.md)

::: zone-end

::: zone pivot="container-linux"

- Eine vorhandene [App Service für Linux-App](index.yml).
- Ein [Azure Storage-Konto](../storage/common/storage-account-create.md?tabs=azure-cli).
- [Azure-Dateifreigabe und -Verzeichnis](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Azure Files ist ein nicht standardmäßiger Speicher, wird separat berechnet und ist nicht in der Web-App enthalten. Aufgrund von Infrastrukturbeschränkungen wird die Verwendung der Firewallkonfiguration nicht unterstützt.
>

## <a name="limitations"></a>Einschränkungen

::: zone pivot="container-windows"

- Azure Storage in App Service wird zurzeit für Szenarien mit Verwendung von eigenem Code (nicht containerisierte Windows-Apps) **nicht unterstützt**.
- Aufgrund von Infrastruktureinschränkungen bietet Azure Storage in App Service **keine Unterstützung** für die Verwendung der **Storage-Firewall**-Konfiguration.
- Mit Azure Storage mit App Service können Sie **bis zu fünf** Bereitstellungspunkte pro App angeben.
- Auf Azure-Speicher, der für eine App bereitgestellt wurde, kann nicht über App Service-FTP/FTPs-Endpunkte zugegriffen werden. Verwenden Sie den [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Azure Storage in App Service unterstützt das Bereitstellen von **Azure Files-Containern** (Lese-/Schreibzugriff) und **Azure-Blobcontainern** (schreibgeschützt).
- Mit Azure Storage in App Service können Sie **bis zu fünf** Bereitstellungspunkte pro App angeben.
- Auf Azure-Speicher, der für eine App bereitgestellt wurde, kann nicht über App Service-FTP/FTPs-Endpunkte zugegriffen werden. Verwenden Sie den [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Verknüpfen des Speichers mit Ihrer App

::: zone pivot="container-windows"

Nachdem Sie Ihr [Azure Storage-Konto sowie die zugehörige Dateifreigabe und das Verzeichnis](#prerequisites) erstellt haben, können Sie Ihre App jetzt mit Azure Storage konfigurieren.

Um eine Azure Files-Dateifreigabe in einem Verzeichnis in Ihrer App Service-App bereitzustellen, verwenden Sie den Befehl [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add). Der Speichertyp muss „AzureFiles“ sein.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Gehen Sie ebenso für alle anderen Verzeichnisse vor, die mit einer Azure Files-Dateifreigabe verknüpft werden sollen.

::: zone-end

::: zone pivot="container-linux"

Nachdem Sie Ihr [Azure Storage-Konto sowie die zugehörige Dateifreigabe und das Verzeichnis](#prerequisites) erstellt haben, können Sie Ihre App jetzt mit Azure Storage konfigurieren.

Um ein Speicherkonto in einem Verzeichnis in Ihre App Service-App bereitzustellen, verwenden Sie den Befehl [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add). Als Speichertyp kann „AzureBlob“ oder „AzureFiles“ verwendet werden. In diesem Beispiel wird AzureFiles verwendet. Die Einstellung für den Einbindungspfad entspricht dem Ordner innerhalb des Containers, den Sie in Azure Storage einbinden möchten. Wenn Sie diese Einstellung auf „/“ festlegen, wird der gesamte Container in Azure Storage eingebunden.


> [!CAUTION]
> Das als Bereitstellungspfad angegebene Verzeichnis in Ihrer Web-App sollte leer sein. Jegliche Inhalte in diesem Verzeichnis werden gelöscht, wenn eine externe Bereitstellung hinzugefügt wird. Wenn Sie Dateien für eine bestehende Anwendung migrieren, erstellen Sie vor Beginn eine Sicherung Ihrer Anwendung und ihres Inhalts.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Gehen Sie genauso für alle anderen Verzeichnisse vor, die mit einem Speicherkonto verknüpft werden sollen.

::: zone-end

## <a name="verify-linked-storage"></a>Überprüfen des verknüpften Speichers

Sobald die Dateifreigabe mit der App verknüpft ist, können Sie die Verknüpfung mithilfe des folgenden Befehls überprüfen:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Nächste Schritte

::: zone pivot="container-windows"

- [Migrieren benutzerdefinierter Software zu Azure App Service mithilfe eines benutzerdefinierten Containers](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [Konfigurieren eines benutzerdefinierten Containers](configure-custom-container.md?pivots=platform-linux)

::: zone-end
