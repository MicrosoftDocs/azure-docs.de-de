---
title: Verwenden des Azure Cloud Shell-Fensters | Microsoft-Dokumentation
description: Übersicht über die Verwendung des Azure Cloud Shell-Fensters.
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
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 2511f2c8fb706e232cde9ee4c02c7f8114bd3a2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60200611"
---
# <a name="using-the-azure-cloud-shell-window"></a>Verwenden des Azure Cloud Shell-Fensters

Dieses Dokument erläutert, wie das Cloud Shell-Fenster verwendet wird.

## <a name="swap-between-bash-and-powershell-environments"></a>Wechsel zwischen Bash- und PowerShell-Umgebungen

Verwenden Sie die Umgebungsauswahl auf der Cloud Shell-Symbolleiste, um zwischen Bash- und PowerShell-Umgebungen zu wechseln.  
![Auswählen der Umgebung](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Neustarten der Cloud Shell
Klicken Sie auf das Neustartsymbol auf der Cloud Shell-Symbolleiste, um den Computerzustand zurückzusetzen.  
![Neustarten der Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Durch Neustart der Cloud Shell wird der Computerstatus zurückgesetzt und alle Dateien, die nicht von Ihrer Azure-Dateifreigabe beibehalten werden, gehen verloren.

## <a name="change-the-text-size"></a>Ändern der Textgröße
Klicken Sie oben links im Fenster auf das Einstellungssymbol, und bewegen Sie dann den Mauszeiger über die Option "Textgröße", und wählen Sie die gewünschte Textgröße aus. Ihre Auswahl wird sitzungsübergreifend beibehalten.
![Textgröße](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Ändern der Schriftart
Klicken Sie links oben im Fenster auf das Einstellungssymbol, und bewegen Sie dann den Mauszeiger über die Option „Schriftart“, und wählen Sie die gewünschte Schriftart aus.  Ihre Auswahl wird sitzungsübergreifend beibehalten.
![Schriftart](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Hochladen und Herunterladen von Dateien
Klicken Sie links oben im Fenster auf das Symbol „Dateien hochladen/herunterladen“, und wählen Sie dann „Hochladen“ oder „Herunterladen“ aus.  
![Hoch-/Herunterladen von Dateien](media/using-the-shell-window/uploaddownload.png)
* Verwenden Sie zum Hochladen von Dateien das Popupelement, um zur Datei auf Ihrem lokalen Computer zu navigieren, wählen Sie die gewünschte Datei aus, und klicken Sie auf die Schaltfläche „Öffnen“.  Die Datei wird in das Verzeichnis `/home/user` hochgeladen.
* Geben Sie zum Herunterladen von Dateien den vollqualifizierten Dateipfad in das Popupfenster ein, und wählen Sie die Schaltfläche „Herunterladen“ aus.  
> [!NOTE] 
> Bei Dateien und Dateipfaden wird in der Cloud Shell die Groß-/Kleinschreibung berücksichtigt. Überprüfen Sie die Groß-/Kleinschreibung Ihres Dateipfads.

## <a name="open-another-cloud-shell-window"></a>Öffnen eines weiteren Cloud Shell-Fensters
Cloud Shell erlaubt mehrere gleichzeitige Sitzungen auf individuellen Browserregisterkarten, wobei jede Sitzung als separater Prozess vorhanden sein kann.
Achten Sie beim Beenden einer Sitzung darauf, jedes Sitzungsfenster zu schließen, da die Prozesse unabhängig voneinander ausgeführt werden, obwohl sie auf dem gleichen Computer ausgeführt werden.  
Klicken Sie links oben im Fenster auf das Symbol „Neue Sitzung öffnen“. Eine neue Registerkarte wird mit einer anderen Sitzung geöffnet, die mit dem vorhandenen Container verbunden ist.
![Neue Sitzung öffnen](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell-Editor
* Weitere Informationen finden Sie auf der Seite [Verwenden des Azure Cloud Shell-Editors](using-cloud-shell-editor.md).

## <a name="web-preview"></a>Webvorschau
Klicken Sie links oben im Fenster auf das Symbol „Webvorschau“, wählen Sie „Konfigurieren“ aus, und geben Sie den Port an, der geöffnet werden soll.  Wählen Sie „Port öffnen“ aus, um lediglich den Port zu öffnen, oder wählen Sie „Öffnen und durchsuchen“ aus, um den Port zu öffnen und auf einer neuen Registerkarte eine Vorschau des Ports anzuzeigen.  
![Webvorschau](media/using-the-shell-window/preview.png)  
<br>
![Konfigurieren des Ports](media/using-the-shell-window/preview-configure.png)  
Klicken Sie links oben im Fenster auf das Symbol „Webvorschau“, und wählen Sie „Vorschauport“ aus, um für einen geöffneten Port eine Vorschau auf einer neuen Registerkarte anzuzeigen. Klicken Sie links oben im Fenster auf das Symbol „Webvorschau“, und wählen Sie „Port schließen“ aus, um den geöffneten Port zu schließen.  
![Anzeigen einer Vorschau/Schließen eines Ports](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimieren und Maximieren des Cloud Shell-Fensters
Klicken Sie auf das Symbol „Minimieren“ oben rechts im Fenster, um es auszublenden. Klicken Sie erneut auf das Cloud Shell-Symbol, um es einzublenden.
Klicken Sie auf das Symbol „Maximieren“, um die maximale Höhe des Fensters festzulegen. Klicken Sie auf „Wiederherstellen“, um das Fenster in vorheriger Größe wiederherstellen.  
![Minimieren oder Maximieren des Fensters](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopieren und Einfügen
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Ändern der Größe des Cloud Shell-Fensters
Klicken Sie auf den oberen Rand der Symbolleiste, und ziehen Sie ihn nach oben oder unten, um die Größe des Cloud Shell-Fensters zu ändern.

## <a name="scrolling-text-display"></a>Scrollen der Textanzeige
Scrollen Sie mit Ihrer Maus oder Ihrem Touchpad, um Terminaltext zu verschieben.

## <a name="exit-command"></a>Befehl „Beenden“
Ausführen von `exit` beendet die aktive Sitzung. Dieses Verhalten tritt standardmäßig nach 20 Minuten ohne Interaktion auf.

## <a name="next-steps"></a>Nächste Schritte

[Bash in Cloud Shell – Schnellstart](quickstart.md) <br>
[Schnellstart für PowerShell in Cloud Shell](quickstart-powershell.md)