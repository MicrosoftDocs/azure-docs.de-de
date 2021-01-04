---
title: Verwenden des JSON-Inhaltstyps für Schlüsselwerte
titleSuffix: Azure App Configuration
description: Erfahren Sie, wie Sie den JSON-Inhaltstyp für Schlüssel-Wert-Paare verwenden
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: ee262c0eb2431085e71d8ee0035bcdab9833d1cf
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565771"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>Verwenden des Inhaltstyps zum Speichern von JSON-Schlüssel-Wert-Paaren in App Configuration

Daten werden in App Configuration als Schlüssel-Wert-Paare gespeichert. Dabei werden Werte standardmäßig als Zeichenfolgentyp behandelt. Über die Inhaltstypeigenschaft, die jedem Schlüssel-Wert-Paar zugeordnet ist, können Sie jedoch einen benutzerdefinierten Typ angeben. Dadurch können Sie den ursprünglichen Typ Ihrer Daten beibehalten oder das Verhalten Ihrer Anwendung basierend auf dem Inhaltstyp ändern.


## <a name="overview"></a>Übersicht

In App Configuration kann der JSON-Medientyp als Inhaltstyp Ihrer Schlüssel-Wert-Paare verwendet werden, um u. a. von folgenden Vorteilen zu profitieren:
- **Vereinfachte Datenverwaltung**: Das Verwalten von Schlüssel-Wert-Paaren (z. B. Arrays) wird im Azure-Portal deutlich vereinfacht.
- **Erweiterter Datenexport**: Primitive Typen, Arrays und JSON-Objekte werden während des Datenexports beibehalten.
- **Native Unterstützung beim App Configuration-Anbieter**: Schlüssel-Wert-Paare mit JSON-Inhaltstyp können problemlos von App Configuration-Anbieterbibliotheken in Ihren Anwendungen genutzt werden.

#### <a name="valid-json-content-type"></a>Gültiger JSON-Inhaltstyp

Medientypen, wie die [hier](https://www.iana.org/assignments/media-types/media-types.xhtml) definierten, können dem Inhaltstyp zugewiesen werden, der den einzelnen Schlüssel-Wert-Paaren zugeordnet ist.
Ein Medientyp umfasst einen Typ und einen Untertyp. Wenn der Typ `application` ist und der Untertyp (oder das Suffix) `json`, wird der Medientyp als gültiger JSON-Inhaltstyp betrachtet.
Nachfolgend sind Beispiele für gültige JSON-Inhaltstypen aufgeführt:

- Anwendung/json
- application/activity+json
- application/vnd.foobar+json;charset=utf-8

#### <a name="valid-json-values"></a>Gültige JSON-Werte

Wenn ein Schlüssel-Wert-Paar den JSON-Inhaltstyp aufweist, muss der Wert im gültigen JSON-Format vorliegen, damit Clients ihn ordnungsgemäß verarbeiten können. Anderenfalls tritt bei der Verarbeitung durch Clients möglicherweise ein Fehler auf, oder der Wert wird als Zeichenfolge behandelt.
Nachfolgend sind Beispiele für gültige JSON-Werte aufgeführt:

- "John Doe"
- 723
- False
- NULL
- "2020-01-01T12:34:56.789Z"
- [1, 2, 3, 4]
- {"ObjectSetting":{"Targeting":{"Default":true,"Level":"Information"}}}

> [!NOTE]
> In den nachfolgenden Abschnitten dieses Artikels werden Schlüssel-Wert-Paare in App Configuration, die einen gültigen JSON-Inhaltstyp und einen gültigen JSON-Wert aufweisen, als **JSON-Schlüssel-Wert-Paar** bezeichnet. 

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen von JSON-Schlüssel-Wert-Paaren in App Configuration.
> * Importieren von JSON-Schlüssel-Wert-Paaren aus einer JSON-Datei.
> * Exportieren von JSON-Schlüssel-Wert-Paaren in eine JSON-Datei.
> * Verwenden von JSON-Schlüssel-Wert-Paaren in Ihren Anwendungen.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.10.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-an-app-configuration-store"></a>Erstellen eines App Configuration-Speichers

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>Erstellen von JSON-Schlüssel-Wert-Paaren in App Configuration

JSON-Schlüssel-Wert-Paare können im Azure-Portal, über die Azure CLI oder durch den Import aus einer JSON-Datei erstellt werden. In diesem Abschnitt finden Sie eine Anleitung zum Erstellen eines JSON-Schlüssel-Wert-Paars mithilfe jeder dieser drei Methoden.

### <a name="create-json-key-values-using-azure-portal"></a>Erstellen von JSON-Schlüssel-Wert-Paaren über das Azure-Portal

Wechseln Sie zu Ihrem App Configuration-Store, und wählen Sie **Konfigurations-Explorer** > **Erstellen** > **Schlüssel-Wert** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:

| Schlüssel | Wert | Inhaltstyp |
|---|---|---|
| Settings:BackgroundColor | "Green" | Anwendung/json |
| Settings:FontSize | 24 | Anwendung/json |
| Settings:UseDefaultRouting | false | Anwendung/json |
| Settings:BlockedUsers | NULL | Anwendung/json |
| Settings:ReleaseDate | "2020-08-04T12:34:56.789Z" | Anwendung/json |
| Settings:RolloutPercentage | [25,50,75,100] | Anwendung/json |
| Settings:Logging | {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}} | Anwendung/json |

Lassen Sie das Feld **Bezeichnung** leer, und wählen Sie **Anwenden** aus.

### <a name="create-json-key-values-using-azure-cli"></a>Erstellen von JSON-Schlüssel-Wert-Paaren über die Azure CLI

Mit den folgenden Befehlen werden JSON-Schlüssel-Wert-Paare in Ihrem App Configuration-Store erstellt. Ersetzen Sie `<appconfig_name>` durch den Namen Ihres App Configuration-Stores.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Wenn Sie die Azure CLI oder Azure Cloud Shell zum Erstellen von JSON-Schlüssel-Wert-Paaren verwenden, muss der angegebene Wert eine JSON-Zeichenfolge mit Escapezeichen sein.

### <a name="import-json-key-values-from-a-file"></a>Importieren von JSON-Schlüssel-Wert-Paaren aus einer Datei

Erstellen Sie eine JSON-Datei `Import.json` mit folgendem Inhalt, und importieren Sie sie als Schlüssel-Wert-Paare in App Configuration:

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> Das Argument `--depth` wird zum Vereinfachen hierarchischer Daten aus einer Datei in Schlüssel-Wert-Paare verwendet. In diesem Tutorial wird „depth“ angegeben, um zu veranschaulichen, dass sich JSON-Objekte auch als Werte in App Configuration speichern lassen. Wenn „depth“ nicht angegeben wird, werden JSON-Objekte standardmäßig auf die niedrigste Ebene vereinfacht.

Die JSON-Schlüssel-Wert-Paare, die Sie erstellt haben, sollten in App Configuration wie folgt dargestellt werden:

![Store-Konfiguration mit JSON-Schlüssel-Wert-Paaren](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>Exportieren von JSON-Schlüssel-Wert-Paaren in eine JSON-Datei

Einer der größten Vorteile bei der Verwendung von JSON-Schlüssel-Wert-Paaren ist die Möglichkeit, den ursprünglichen Datentyp Ihrer Werte beim Export beizubehalten. Wenn ein Schlüssel-Wert-Paar in App Configuration nicht den JSON-Inhaltstyp aufweist, wird der Wert als Zeichenfolge behandelt. 

Nachfolgend sind Schlüssel-Wert-Paare ohne JSON-Inhaltstyp aufgeführt:

| Schlüssel | Wert | Inhaltstyp |
|---|---|---|
| Settings:FontSize | 24 | |
| Settings:UseDefaultRouting | false | |

Beim Export dieser Schlüssel-Wert-Paare in eine JSON-Datei werden die Werte als Zeichenfolge exportiert:
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

Wenn Sie JSON-Schlüssel-Wert-Paare jedoch in eine Datei exportieren, wird für alle Werte der ursprüngliche Datentyp beibehalten. Um dies zu überprüfen, exportieren Sie Schlüssel-Wert-Paare aus App Configuration in eine JSON-Datei. Sie werden feststellen, dass die exportierte Datei dieselben Inhalte aufweist wie die `Import.json`-Datei, die Sie zuvor importiert haben.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> Wenn Ihr App Configuration-Store Schlüssel-Wert-Paare ohne JSON-Inhaltstyp aufweist, werden diese ebenfalls im Zeichenfolgenformat in dieselbe Datei exportiert. Wenn lediglich die JSON-Schlüssel-Wert-Paare exportiert werden sollen, weisen Sie Ihren JSON-Schlüssel-Wert-Paaren eine eindeutige Bezeichnung oder ein Präfix zu, und filtern Sie die Inhalte beim Export nach dieser Bezeichnung oder diesem Präfix.


## <a name="consuming-json-key-values-in-applications"></a>Verwenden von JSON-Schlüssel-Wert-Paaren in Anwendungen

Die einfachste Möglichkeit zum Verwenden von JSON-Schlüssel-Wert-Paaren in Ihrer Anwendung ist der Einsatz von App Configuration-Anbieterbibliotheken. Bei Verwendung von Anbieterbibliotheken muss keine spezielle Behandlung von JSON-Schlüssel-Wert-Paaren in Ihrer Anwendung implementiert werden. Es erfolgt immer eine Deserialisierung für Ihre Anwendung (wie bei anderen JSON-Konfigurationsanbieterbibliotheken). 

> [!Important]
> Native Unterstützung für JSON-Schlüssel-Wert-Paare ist in der .NET-Konfigurationsanbieterversion 4.0.0 oder höher verfügbar. Weitere Informationen finden Sie im Abschnitt [*Nächste Schritte*](#next-steps).

Wenn Sie das SDK oder die REST-API zum Lesen von Schlüssel-Wert-Paaren aus App Configuration verwenden, muss Ihre Anwendung (basierend auf dem Inhaltstyp) die Deserialisierung des Werts eines JSON-Schlüssel-Wert-Paars mithilfe eines standardmäßigen JSON-Deserialisierungsprogramms durchführen.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie mit JSON-Schlüssel-Wert-Paaren in Ihrem App Configuration-Store arbeiten, erstellen Sie nun eine Anwendung zum Verwenden dieser Schlüssel-Wert-Paare:

* [Schnellstartanleitung zu ASP.NET Core](./quickstart-aspnet-core-app.md)
    * Voraussetzung: [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore)-Paket v4.0.0 oder höher.

* [Schnellstartanleitung zu .NET Core](./quickstart-dotnet-core-app.md)
    * Voraussetzung: [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration)-Paket v4.0.0 oder höher.
