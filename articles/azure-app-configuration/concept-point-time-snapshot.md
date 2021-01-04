---
title: Abrufen von Schlüssel-Wert-Paaren für einen bestimmten Zeitpunkt
titleSuffix: Azure App Configuration
description: Rufen Sie alte Schlüssel-Wert-Paare mithilfe von Point-in-Time-Momentaufnahmen in Azure App Configuration ab, wo ein Datensatz von Änderungen an Schlüsselwerten verwaltet wird.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929896"
---
# <a name="point-in-time-snapshot"></a>Point-in-Time-Momentaufnahme

In Azure App Configuration wird ein Datensatz von Änderungen an Schlüssel-Werten verwaltet. Dieser Datensatz stellt eine Zeitachse der Schlüssel-Wert-Änderungen bereit. Sie können den Verlauf eines beliebigen Schlüssel-Werts wiederherstellen und einen vergangenen Wert jederzeit innerhalb des Schlüsselverlaufzeitraums angeben. Dieser beträgt sieben Tage für Speicher der kostenlosen Dienstebene oder 30 Tage für Speicher der Standard-Dienstebene. Mithilfe dieses Features können Sie ein altes Schlüssel-Wert-Paar aus der Vergangenheit abrufen. Beispielsweise können Sie Konfigurationseinstellungen wiederherstellen, die vor der neuesten Bereitstellung verwendet wurden, um ein Rollback der Anwendung zu einer vorherigen Konfiguration durchzuführen.

## <a name="key-value-retrieval"></a>Abrufen von Schlüssel-Wert-Paaren

Sie können mithilfe des Azure-Portals oder der CLI frühere Schlüsselwerte abrufen. Verwenden Sie in der Azure CLI `az appconfig revision list`, und fügen Sie die entsprechenden Parameter hinzu, um die erforderlichen Werte abzurufen.  Geben Sie die Azure App Configuration-Instanz über den Speichernamen (`--name <app-config-store-name>`) oder eine Verbindungszeichenfolge (`--connection-string <your-connection-string>`) an. Schränken Sie die Ausgabe ein, indem Sie einen bestimmten Zeitpunkt (`--datetime`) und die maximale Anzahl zurückzugebender Elemente angeben (`--top`).

Wenn Sie die Azure CLI nicht lokal installiert haben, können Sie optional [Azure Cloud Shell](../cloud-shell/overview.md) verwenden.

Rufen Sie alle aufgezeichneten Änderungen an Ihren Schlüssel-Wert-Paaren ab.

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

Rufen Sie alle aufgezeichneten Änderungen für den Schlüssel `environment` und die Bezeichnungen `test` und `prod` ab.

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Rufen Sie alle aufgezeichneten Änderungen im hierarchischen Schlüsselraum `environment:prod` ab.

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Rufen Sie alle aufgezeichneten Änderungen für den Schlüssel `color` zu einem bestimmten Zeitpunkt ab.

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Rufen Sie die letzten 10 aufgezeichneten Änderungen an Ihren Schlüsselwerten ab, und geben Sie nur die Werte für `key`, `label` und den Zeitstempel `last_modified` zurück.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer ASP.NET Core-Web-App](./quickstart-aspnet-core-app.md)