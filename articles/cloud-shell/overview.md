---
title: Übersicht über Azure Cloud Shell | Microsoft-Dokumentation
description: Übersicht über Azure Cloud Shell.
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 5608b3e0f9b98db62d22245de5a864f757f48799
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199668"
---
# <a name="overview-of-azure-cloud-shell"></a>Übersicht über Azure Cloud Shell
Azure Cloud Shell ist eine interaktive, über den Browser zugängliche Shell für die Verwaltung von Azure-Ressourcen.
Es bietet Ihnen die Flexibilität, die Shell-Benutzeroberfläche auszuwählen, die sich am besten für Ihre Arbeitsweise eignet.
Linux-Benutzer können Bash-Benutzeroberflächen verwenden, während Windows-Benutzer PowerShell nutzen können.

Versuchen Sie es über „shell.azure.com“, indem Sie unten klicken.

[![Eingebetteter Start](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

Verwenden Sie das Cloud Shell-Symbol, um es über das Azure-Portal zu versuchen:

![Start über das Portal](media/overview/portal-launch-icon.png)

## <a name="features"></a>Features

### <a name="browser-based-shell-experience"></a>Browserbasierte Shelloberfläche
Cloud Shell ermöglicht den Zugriff auf eine browserbasierte Befehlszeilenumgebung, die extra auf Azure-Verwaltungsaufgaben ausgerichtet ist.
Nutzen Sie Cloud Shell, um unabhängig von einem lokalen Computer in einer Weise zu arbeiten, die Ihnen nur die Cloud bieten kann.

### <a name="choice-of-preferred-shell-experience"></a>Auswahl bevorzugter Shell-Benutzeroberfläche
Benutzer können in der Dropdownliste der Shell zwischen Bash und PowerShell wählen.

![Bash in Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell in Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Authentifizierte und konfigurierte Azure-Arbeitsstation
Cloud Shell wird von Microsoft verwaltet und verfügt somit über gängige Befehlszeilentools und Sprachunterstützung. Darüber hinaus führt Cloud Shell automatisch eine sichere Authentifizierung durch, sodass Sie über die Azure CLI oder über Azure PowerShell-Cmdlets sofort auf Ihre Ressourcen zugreifen können.

Zeigen Sie die vollständige [Liste der in Cloud Shell installierten Tools](features.md#tools) an.

### <a name="integrated-cloud-shell-editor"></a>Integrierter Cloud Shell-Editor
Cloud Shell bietet einen integrierten grafischen Text-Editor, der auf dem Open-Source-Monaco-Editor basiert. Erstellen und bearbeiten Sie Konfigurationsdateien einfach durch Ausführung von `code .` zur nahtlosen Bereitstellung über die Azure CLI oder Azure PowerShell.

[Erfahren Sie mehr über den Cloud Shell-Editor](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>In „docs.microsoft.com“ integriert

Sie können Cloud Shell direkt über die Dokumentation nutzen, die auf [docs.microsoft.com](https://docs.microsoft.com) gehostet wird. Das Tool ist in die Dokumentation zu [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) und [Azure CLI](https://docs.microsoft.com/cli/azure) integriert. Klicken Sie in einem Codeausschnitt auf die Schaltfläche „Ausprobieren“, um die immersive Shell-Benutzeroberfläche zu öffnen. 

### <a name="multiple-access-points"></a>Mehrere Zugriffspunkte
Cloud Shell ist ein flexibles Tool mit verschiedenen Zugriffsmöglichkeiten:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure)
* [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/overview)
* [Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Azure-Kontoerweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Herstellen einer Verbindung mit dem Microsoft Azure Files-Speicher
Cloud Shell-Computer sind temporär und erfordern die Einbindung einer neuen oder vorhandenen Azure Files-Freigabe als `clouddrive`, um Ihre Dateien dauerhaft zu speichern.

Beim ersten Start werden Sie von Cloud Shell darauf hingewiesen, dass für Sie eine Ressourcengruppe, ein Speicherkonto und eine Azure Files-Freigabe erstellt werden. Dieser Schritt ist nur einmal erforderlich. Diese Komponenten werden für alle Sitzungen automatisch angefügt. Es kann eine einzelne Dateifreigabe zugeordnet werden, die sowohl von Bash als auch von PowerShell in Cloud-Shell verwendet wird.

Erfahren Sie, wie Sie ein [neues oder vorhandenes Speicherkonto](persisting-shell-storage.md) einbinden.

## <a name="concepts"></a>Konzepte
* Cloud Shell wird auf einem temporären Host ausgeführt, der pro Sitzung und pro Benutzer bereitgestellt wird.
* Nach 20 Minuten ohne interaktive Aktivitäten tritt für Cloud Shell ein Timeout ein.
* Für Cloud Shell muss eine Azure-Dateifreigabe bereitgestellt werden.
* Cloud Shell verwendet dieselbe Azure-Dateifreigabe für Bash und PowerShell.
* Cloud Shell wird ein Computer pro Benutzerkonto zugewiesen.
* Cloud Shell stellt $HOME dauerhaft unter Verwendung eines 5-GB-Images bereit, das sich in Ihrer Dateifreigabe befindet.
* Berechtigungen werden auf einen normalen Linux-Benutzer in Bash festgelegt.

Weitere Informationen erhalten Sie unter [Bash in Cloud Shell](features.md) und [PowerShell in Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Preise
Der Computer, auf dem Cloud Shell gehostet wird, ist kostenlos. Als Voraussetzung muss eine Azure Files-Freigabe eingebunden sein. Es gelten die üblichen Speicherkosten.

## <a name="next-steps"></a>Nächste Schritte
[Schnellstart für Bash in Azure Cloud Shell](quickstart.md) <br>
[Schnellstart für PowerShell in Cloud Shell](quickstart-powershell.md)
