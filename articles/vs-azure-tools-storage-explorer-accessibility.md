---
title: Barrierefreiheit des Azure Storage-Explorers (Vorschau) | Microsoft-Dokumentation
description: Barrierefreiheit des Azure Storage-Explorers (Vorschau)
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: aa42d6b898a3cc474575f6c8dae65ff420fc8e30
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60457265"
---
# <a name="storage-explorer-accessibility"></a>Barrierefreiheit des Storage-Explorers

## <a name="screen-readers"></a>Bildschirmsprachausgaben

Storage-Explorer unterstützt die Verwendung einer Bildschirmsprachausgabe unter Windows und Mac. Die folgenden Bildschirmsprachausgaben werden für die jeweilige Plattform empfohlen:

Plattform | Sprachausgabe
---------|--------------
Windows  | NVDA
Mac      | VoiceOver
Linux    | (Sprachausgaben werden unter Linux nicht unterstützt)

Wenn bei der Verwendung des Storage-Explorers ein Problem hinsichtlich der Barrierefreiheit auftritt, [erstellen Sie ein Problem auf GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="zoom"></a>Zoom

Sie können den Text im Storage-Explorer durch Zoomen vergrößern. Klicken Sie dazu im Menü „Hilfe“ auf **Vergrößern**. Sie können das Menü „Hilfe“ auch verwenden, um den Text zu verkleinern und den Zoomfaktor auf die Standardeinstellung zurückzusetzen.

![Zoomoptionen im Menü „Hilfe“][0]

Durch die Zoomeinstellung werden die meisten Elemente der Benutzeroberfläche vergrößert. Es wird empfohlen, auch die Option für große Schrift und Zoomeinstellungen für Ihr Betriebssystem zu aktivieren, um sicherzustellen, dass alle Elemente der Benutzeroberfläche ordnungsgemäß skaliert werden.

## <a name="high-contrast-themes"></a>Designs mit hohem Kontrast

Storage-Explorer verfügt über zwei Designs mit hohem Kontrast: **Hell mit starkem Kontrast** und **Dunkel mit starkem Kontrast**. Sie können das Design im Menü „Hilfe“ > „Designs“ ändern.

![Untermenü „Designs“][1]

Die Designeinstellung ändert die Farbe der meisten Elemente der Benutzeroberfläche. Es wird empfohlen, auch das entsprechende Design mit hohem Kontrast Ihres Betriebssystems zu aktivieren, um sicherzustellen, dass alle Elemente der Benutzeroberfläche korrekt farblich dargestellt werden.

## <a name="shortcut-keys"></a>Tastenkombinationen

### <a name="window-commands"></a>Fensterbefehle

Get-Help       | Tastenkombinationen
--------------|--------------------
Neues Fenster    | **STRG+UMSCHALT+N**
Editor schließen  | **STRG+F4**
Beenden          | **STRG+UMSCHALT+W**

### <a name="navigation-commands"></a>Navigationsbefehle

Get-Help                | Tastenkombinationen
-----------------------|----------------------
Fokus in nächster Gruppe       | **F6**
Fokus in vorheriger Gruppe   | **UMSCHALT+F6**
Explorer               | **STRG+UMSCHALT+E**
Kontoverwaltung     | **STRG+UMSCHALT+A**
Randleiste umschalten        | **STRG+B**
Aktivitätsprotokoll           | **STRG+UMSCHALT+L**
Aktionen und Eigenschaften | **STRG+UMSCHALT+P**
Aktueller Editor         | **STRG+POS1**
Nächster Editor            | **STRG+BILD-AB**
Vorheriger Editor        | **STRG+BILD-AUF**

### <a name="zoom-commands"></a>Zoombefehle

Get-Help  | Tastenkombinationen
---------|------------------
Vergrößern  | **STRG+=**
Verkleinern | **STRG+-**

### <a name="blob-and-file-share-editor-commands"></a>Befehle für den Blob- und Dateifreigaben-Editor

Get-Help | Tastenkombinationen
--------|--------------------
Zurück    | **ALT+NACH-LINKS**
Weiter | **ALT+NACH-RECHTS**
Nach oben      | **ALT+NACH-OBEN**

### <a name="editor-commands"></a>Editor-Befehle

Get-Help | Tastenkombinationen
--------|------------------
Kopieren    | **STRG+C**
Ausschneiden     | **STRG+X**
Einfügen   | **STRG+V**
Aktualisieren  | **STRG+R**

### <a name="other-commands"></a>Andere Befehle

Get-Help                | Tastenkombinationen
-----------------------|------------------
Entwicklertools umschalten | **F12**
Erneut laden                 | **ALT+STRG+R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
