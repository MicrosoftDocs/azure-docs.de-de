---
title: Verwenden des Azure Cloud Shell-Editors | Microsoft-Dokumentation
description: Übersicht über die Verwendung des Azure Cloud Shell-Editors.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199209"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Verwenden des Azure Cloud Shell-Editors

Azure Cloud Shell enthält einen integrierten Dateieditor, der aus dem Open Source-[Monaco-Editor](https://github.com/Microsoft/monaco-editor) erstellt wurde. Der Cloud Shell-Editor unterstützt Funktionen wie z.B. Sprachhervorhebung, Befehlspalette und einen Datei-Explorer.

![Cloud Shell-Editor](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Öffnen des Editors

Starten Sie den Editor zum einfachen Erstellen und Bearbeiten von Dateien durch Ausführung von `code .` im Cloud Shell-Terminal. Diese Aktion öffnet den Editor in Ihrem aktiven Arbeitsverzeichnis im Terminal.

Um eine Datei direkt für die schnelle Bearbeitung zu öffnen, führen Sie `code <filename>` aus, um den Editor ohne Datei-Explorer zu öffnen.

Um den Editor über die Schaltfläche in der Benutzeroberfläche zu öffnen, klicken Sie auf der Symbolleiste auf das Editor-Symbol `{}`. Damit wird der Editor geöffnet und standardmäßig im Dateiexplorer das `/home/<user>`-Verzeichnis aktiviert.

## <a name="closing-the-editor"></a>Schließen des Editors

Um den Editor zu schließen, öffnen Sie den `...`-Aktionsbereich oben rechts im Editor und wählen `Close editor` aus.

![Editor schließen](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Befehlspalette

Um die Befehlspalette zu starten, verwenden Sie die `F1`-Taste, wenn der Editor im Fokus steht. Die Befehlspalette kann auch über den Aktionsbereich geöffnet werden.

![Befehlspalette](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Mitwirkung am Monaco-Editor

Die Sprachhervorhebung im Cloud Shell-Editor wird über die Upstreamfunktionalität in der Nutzung der Monarch-Syntaxdefinitionen durch den [Monaco-Editor](https://github.com/Microsoft/monaco-editor) unterstützt. Wie Sie Ihren Beitrag leisten können, erfahren Sie im [Leitfaden für die Mitwirkung am Monaco-Editor](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Nächste Schritte
[Versuchen Sie den Schnellstart für Bash in Cloud Shell](quickstart.md)
[Zeigen Sie die vollständige Liste der integrierten Tools von Cloud Shell an](features.md)