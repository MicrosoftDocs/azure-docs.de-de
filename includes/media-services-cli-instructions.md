---
title: include file
description: include file
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 1327ac49920af353d6adbbe592ab4189417e60e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996894"
---
## <a name="use-cli-shell"></a>Verwenden der CLI-Shell

Es wird empfohlen, [Azure Cloud Shell](../articles/cloud-shell/overview.md?view=azure-cli-latest) zu verwenden, um CLI-Befehle auszuführen. **Cloud Shell** ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Allgemeine Tools sind in Cloud Shell vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Es bietet Ihnen die Flexibilität, die Shell-Benutzeroberfläche auszuwählen, die sich am besten für Ihre Arbeitsweise eignet. Linux-Benutzer können Bash-Benutzeroberflächen verwenden, während Windows-Benutzer PowerShell nutzen können.

Sie können die CLI auch lokal installieren. Anweisungen für Ihre Plattform finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

### <a name="sign-in"></a>Anmelden

Wenn Sie eine lokale Installation der CLI verwenden, müssen Sie sich bei Azure anmelden. Dieser Schritt ist für Azure Cloud Shell nicht erforderlich. Melden Sie sich mit dem Befehl `az login` an.

Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Anmeldeseite. Andernfalls müssen Sie eine Browserseite öffnen und die Anweisungen zur Befehlszeile befolgen, um einen Autorisierungscode einzugeben, nachdem Sie in Ihrem Browser zu https://aka.ms/devicelogin navigiert sind.

### <a name="specify-location-of-files"></a>Angeben des Speicherorts für Dateien

Bei vielen Media Services-CLI-Befehlen können Sie einen Parameter mit einem Dateinamen übergeben. Bei Verwendung von **Cloud Shell** können Sie die Datei auf Ihr Clouddrive hochladen (mithilfe von Bash oder PowerShell). 

![Hochladen von Dateien]

Gleich, ob Sie eine lokale Befehlszeilenschnittstelle oder **Cloud Shell** verwenden, Sie müssen den Dateipfad gemäß dem verwendeten Betriebssystem bzw. der Cloud Shell (Bash oder PowerShell) angeben. Im Anschluss finden Sie einige Beispiele:

Relativer Pfad zur Datei (alle Betriebssysteme)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absoluter Dateipfad unter den Betriebssystemen Linux/Mac und Windows

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Verwenden Sie `{file}`, wenn der Befehl nach einem Pfad zur Datei fragt. Beispiel: `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Verwenden Sie `@{file}`, wenn der Befehl die angegebene Datei lädt. Beispiel: `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Hochladen von Dateien]: ./media/media-services-cli/upload-download-files.png