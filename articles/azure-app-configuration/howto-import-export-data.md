---
title: Importieren oder Exportieren von Daten mit Azure App Configuration
description: Hier erfahren Sie, wie Sie Konfigurationsdaten in Azure App Configuration importieren oder daraus exportieren. Tauschen Sie Daten zwischen dem App Configuration-Speicher und dem Codeprojekt.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: alkemper
ms.openlocfilehash: 5e4eeb37bb5efa11a656600072e4aa364fc71500
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931791"
---
# <a name="import-or-export-configuration-data"></a>Importieren oder Exportieren von Konfigurationsdaten

Azure App Configuration unterstützt Datenimport- und -exportvorgänge. Verwenden Sie diese Vorgänge, um mit großen Mengen von Konfigurationsdaten zu arbeiten und Daten zwischen Ihrem App-Konfigurationsspeicher und dem Codeprojekt auszutauschen. Beispielsweise können Sie einen App-Konfigurationsspeicher zum Testen und einen anderen für die Produktion einrichten. Sie können Anwendungseinstellungen zwischen diesen Speichern kopieren, damit Sie Daten nicht zweimal eingeben müssen.

Dieser Artikel enthält eine Anleitung zum Importieren und Exportieren von Daten per App Configuration. Wenn Sie eine fortlaufende Synchronisierung mit Ihrem GitHub-Repository einrichten möchten, sehen Sie sich unsere [GitHub-Aktion](./concept-github-action.md) an.

## <a name="import-data"></a>Daten importieren

Beim Import werden Konfigurationsdaten aus einer vorhandenen Quelle in einen App Configuration-Speicher eingefügt. Verwenden Sie die Importfunktion zum Migrieren von Daten in einen App Configuration-Speicher oder zum Aggregieren von Daten aus mehreren Quellen. App Configuration unterstützt das Importieren aus JSON-, YAML- oder Eigenschaftendateien.

Sie können Daten entweder mit dem [Azure-Portal](https://portal.azure.com) oder über die [Azure CLI](./scripts/cli-import.md) importieren. Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie zu Ihrem App Configuration-Speicher, und wählen Sie **Importieren/Exportieren** im Menü **Vorgänge** aus.

1. Wählen Sie auf der Registerkarte **Import** die Option **Quelldienst** > **Konfigurationsdatei**.

1. Wählen Sie **Für Sprache** aus, und wählen Sie Ihren gewünschten Eingabetyp aus.

1. Wählen Sie das Symbol **Ordner** aus, und greifen Sie auf die zu importierende Datei zu.

    ![Importieren einer Datei](./media/import-file.png)

1. Wählen Sie ein **Trennzeichen** aus, und geben Sie ein **Präfix** ein (optional), das für die Namen importierter Schlüssel verwendet werden soll.

1. Wählen Sie optional eine **Bezeichnung** aus.

1. Wählen Sie **Übernehmen**, um den Importvorgang abzuschließen.

    ![Dateiimport abgeschlossen](./media/import-file-complete.png)

## <a name="export-data"></a>Daten exportieren

Beim Export werden in App Configuration gespeicherte Daten an ein anderes Ziel geschrieben. Verwenden Sie die Exportfunktion beispielsweise zum Speichern von Daten eines App Configuration-Speichers in einer Datei, die während der Bereitstellung in Ihren Anwendungscode eingebettet wird.

Sie können Daten entweder mit dem [Azure-Portal](https://portal.azure.com) oder über die [Azure CLI](./scripts/cli-export.md) exportieren. Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie zu Ihrem App Configuration-Speicher, und wählen Sie **Import/Export**.

1. Wählen Sie auf der Registerkarte **Export** die Option **Zieldienst** > **Konfigurationsdatei**.

1. Geben Sie optional ein **Präfix** ein, und wählen Sie eine **Bezeichnung** und einen Zeitpunkt für den Export der Schlüssel aus.

1. Wählen Sie unter **Dateityp** > **Trennzeichen** ein Trennzeichen aus.

1. Wählen Sie **Übernehmen**, um den Exportvorgang abzuschließen.

    ![Dateiexport abgeschlossen](./media/export-file-complete.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer ASP.NET Core-Web-App](./quickstart-aspnet-core-app.md)