---
title: Verwenden eines Tools zum Erstellen und Überprüfen von DTDL-Modellen | Microsoft-Dokumentation
description: Installieren Sie den DTDL-Editor in Visual Studio Code oder Visual Studio 2019, und erstellen Sie IoT Plug & Play-Modelle damit.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 40d1ae4da07e159c24970c065d1c39e22b89a29a
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280193"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>Installieren und Verwenden der DTDL-Erstellungstools

Bei den in der [Definitionssprache für digitale Zwillinge (Digital Twins Definition Language, DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) beschriebenen Modellen handelt es sich um JSON-Dateien. Sie können diese Modelldateien mithilfe einer Erweiterung für Visual Studio Code oder Visual Studio 2019 erstellen und überprüfen.

## <a name="install-and-use-the-vs-code-extension"></a>Installieren und Verwenden der VS Code-Erweiterung

Die DTDL-Erweiterung für VS Code fügt die folgenden DTDL-Erstellungsfeatures hinzu:

- Syntaxüberprüfung von DTDL v2
- IntelliSense, einschließlich des Features „AutoVervollständigen“, als Unterstützung für die Sprachsyntax
- Fähigkeit zum Erstellen von Schnittstellen über die Befehlspalette

Zur Installation der DTDL-Erweiterung müssen Sie zum [DTDL-Editor für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) wechseln. Sie können auch in VS Code in der Ansicht „Erweiterungen“ nach **DTDL** suchen.

Wenn Sie die Erweiterung installiert haben, erstellen Sie DTDL-Modelldateien in Visual Studio Code damit:

- Die Erweiterung bietet Syntaxüberprüfung in DTDL-Modelldateien und hebt Fehler hervor, wie im folgenden Screenshot zu sehen ist:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Modellüberprüfung in VS Code":::

- Verwenden Sie IntelliSense und das Feature „AutoVervollständigen“ beim Bearbeiten von DTDL-Modellen:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="Verwenden von IntelliSense für DTDL-Modelle in VS Code":::

- Erstellen Sie eine neue DTDL-Schnittstelle. Der Befehl **DTDL: Schnittstelle erstellen** erstellt eine JSON-Datei mit einer neuen Schnittstelle. Die Schnittstelle enthält Beispiele für Telemetrie, Eigenschaften und Befehlsdefinitionen.

## <a name="install-and-use-the-visual-studio-extension"></a>Installieren der Visual Studio-Erweiterung

Die DTDL-Erweiterung für Visual Studio 2019 fügt die folgenden DTDL-Erstellungsfeatures hinzu:

- Syntaxüberprüfung von DTDL v2
- IntelliSense, einschließlich des Features „AutoVervollständigen“, als Unterstützung für die Sprachsyntax

Für die Installation der DTDL-Erweiterung müssen Sie zur [DTDL-Sprachunterstützung für Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport) wechseln. Sie können auch in Visual Studio unter **Erweiterungen verwalten** nach **DTDL** suchen.

Wenn Sie die Erweiterung installiert haben, erstellen Sie DTDL-Modelldateien in Visual Studio damit:

- Die Erweiterung bietet Syntaxüberprüfung in DTDL-Modelldateien und hebt Fehler hervor, wie im folgenden Screenshot zu sehen ist:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Modellüberprüfung in Visual Studio":::

- Verwenden Sie IntelliSense und das Feature „AutoVervollständigen“ beim Bearbeiten von DTDL-Modellen:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Verwenden von IntelliSense für DTDL-Modelle in Visual Studio":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mithilfe der DTDL-Erweiterungen für Visual Studio Code und Visual Studio 2019 DTDL-Modelldateien erstellen und überprüfen. Als Nächstes sollten Sie sich darüber informieren, wie der [Azure IoT-Explorer mit Ihren Modellen und Geräten](./howto-use-iot-explorer.md) verwendet wird.
