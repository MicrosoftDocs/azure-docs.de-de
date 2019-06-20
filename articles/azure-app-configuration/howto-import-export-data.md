---
title: Importieren oder Exportieren von Daten mit Azure App Configuration | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Daten für Azure App Configuration importieren oder exportieren.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 377c5088d39821e87412c517540b3190b0a14a00
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393284"
---
# <a name="import-or-export-configuration-data"></a>Importieren oder Exportieren von Konfigurationsdaten

Azure App Configuration unterstützt Datenimport- und -exportvorgänge. Verwenden Sie diese Vorgänge, um mit großen Mengen von Konfigurationsdaten zu arbeiten und Daten zwischen Ihrem App-Konfigurationsspeicher und dem Codeprojekt auszutauschen. Beispielsweise können Sie einen App-Konfigurationsspeicher zum Testen und einen anderen für die Produktion einrichten. Sie können dann per Datei Anwendungseinstellungen zwischen diesen Speichern kopieren, damit Sie Daten nicht zweimal eingeben müssen.

Dieser Artikel enthält eine Anleitung zum Importieren und Exportieren von Daten per App Configuration.

## <a name="import-data"></a>Daten importieren

Beim Import werden Konfigurationsdaten aus einer vorhandenen Quelle in einen App Configuration-Speicher eingefügt, anstatt sie manuell einzugeben. Verwenden Sie die Importfunktion zum Migrieren von Daten in einen App Configuration-Speicher oder zum Aggregieren von Daten aus mehreren Quellen. App Configuration unterstützt das Importieren aus JSON-, YAML- oder Eigenschaftendateien.

Sie können Daten entweder mit dem [Azure-Portal](https://portal.azure.com) oder über die [Azure CLI](./scripts/cli-import.md) importieren. Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie zu Ihrem App Configuration-Speicher, und wählen Sie **Import/Export**.

2. Wählen Sie auf der Registerkarte **Import** die Option **Quelldienst** > **Konfigurationsdatei**.

3. Wählen Sie **Für Sprache** > **Dateityp**.

4. Wählen Sie das Symbol **Ordner** aus, und greifen Sie auf die zu importierende Datei zu.

    ![Importieren einer Datei](./media/import-file.png)

5. Wählen Sie ein **Trennzeichen** aus, und geben Sie ein **Präfix** ein (optional), das für die Namen importierter Schlüssel verwendet werden soll.

6. Wählen Sie optional eine **Bezeichnung** aus.

7. Wählen Sie **Übernehmen**, um den Importvorgang abzuschließen.

    ![Dateiimport abgeschlossen](./media/import-file-complete.png)

## <a name="export-data"></a>Exportieren von Daten

Beim Export werden in App Configuration gespeicherte Daten an ein anderes Ziel geschrieben. Verwenden Sie die Exportfunktion beispielsweise zum Speichern von Daten eines App Configuration-Speichers in einer Datei, die während der Bereitstellung in Ihren Anwendungscode eingebettet wird.

Sie können Daten entweder mit dem [Azure-Portal](https://portal.azure.com) oder über die [Azure CLI](./scripts/cli-export.md) exportieren. Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie zu Ihrem App Configuration-Speicher, und wählen Sie **Import/Export**.

2. Wählen Sie auf der Registerkarte **Export** die Option **Zieldienst** > **Konfigurationsdatei**.

3. Geben Sie optional ein **Präfix** ein, und wählen Sie eine **Bezeichnung** und einen Zeitpunkt für den Export der Schlüssel aus.

4. Wählen Sie unter **Dateityp** > **Trennzeichen** ein Trennzeichen aus.

5. Wählen Sie **Übernehmen**, um den Exportvorgang abzuschließen.

    ![Dateiexport abgeschlossen](./media/export-file-complete.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer ASP.NET Core-Web-App](./quickstart-aspnet-core-app.md)  
