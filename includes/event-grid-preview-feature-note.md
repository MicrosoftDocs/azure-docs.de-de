---
title: Includedatei
description: Includedatei
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 85a1579e32b4c216f234f77c76316bedeaea77b0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119542"
---
Dieses Feature befindet sich in der Vorschauphase. Sie müssen eine Vorschauerweiterung oder ein Vorschaumodul installieren, um es zu verwenden.

### <a name="install-extension-for-azure-cli"></a>Installieren der Erweiterung für Azure CLI

Für die Azure CLI benötigen Sie die [Event Grid-Erweiterung](/cli/azure/azure-cli-extensions-list).

In [CloudShell](/azure/cloud-shell/quickstart):

* Wenn Sie die Erweiterung bereits installiert haben, aktualisieren Sie sie mit `az extension update -n eventgrid`.
* Wenn Sie die Erweiterung noch nicht installiert haben, installieren Sie sie mit `az extension add -n eventgrid`.

Für eine lokale Installation:

1. Deinstallieren Sie die Azure CLI lokal.
1. Installieren Sie die [neueste Version](/cli/azure/install-azure-cli) der Azure CLI.
1. Starten Sie das Befehlsfenster.
1. Deinstallieren Sie frühere Versionen der Erweiterung `az extension remove -n eventgrid`.
1. Installieren Sie die Erweiterung `az extension add -n eventgrid`.

### <a name="install-module-for-powershell"></a>Installieren des Moduls für PowerShell

Für PowerShell benötigen Sie das [AzureRM.EventGrid-Modul](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

In [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Installieren Sie das Modul `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

Für eine lokale Installation:

1. Öffnen Sie die PowerShell-Konsole als Administrator.
1. Installieren Sie das Modul `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

Wenn der `-AllowPrerelease`-Parameter nicht verfügbar ist, führen Sie die folgenden Schritte aus:

1. Führen Sie `Install-Module PowerShellGet -Force` aus.
1. Führen Sie `Update-Module PowerShellGet` aus.
1. Schließen Sie die PowerShell-Konsole.
1. Starten Sie PowerShell als Administrator neu.
1. Installieren Sie das Modul `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.
