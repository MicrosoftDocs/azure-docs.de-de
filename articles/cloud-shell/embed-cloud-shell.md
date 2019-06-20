---
title: Einbetten von Azure Cloud Shell | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Einbetten von Azure Cloud Shell.
services: cloud-shell
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
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614311"
---
# <a name="embed-azure-cloud-shell"></a>Einbetten von Azure Cloud Shell

Das Einbetten von Cloud Shell ermöglicht Entwicklern und Inhaltsentwicklern Cloud Shell unter einer dedizierten URL, [shell.azure.com](https://shell.azure.com), direkt zu öffnen. Dadurch erhalten Ihre Benutzer sofort Zugriff auf den gesamten Leistungsumfang bei Authentifizierung, Tools und aktuellen Azure CLI/Azure PowerShell-Tools in Cloud Shell.

Normale große Schaltfläche

[![Normal starten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

Große Schaltfläche

[![Groß starten](https://shell.azure.com/images/launchcloudshell@2x.png "Azure Cloud Shell starten")](https://shell.azure.com)

## <a name="how-to"></a>Vorgehensweise

Integrieren Sie die Start-Schaltfläche von Cloud Shell in Markdowndateien, indem Sie Folgendes kopieren:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Den HTML-Code zum Einbetten einer Popup-Cloud Shell finden Sie unten:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Anpassen der Benutzererfahrung

Legen Sie eine bestimmte Shell-Benutzererfahrung durch Erweitern der URL fest.

|Benutzererfahrung   |URL   |
|---|---|
|Zuletzt verwendete Shell   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Nächste Schritte
[Schnellstart für Bash in Azure Cloud Shell](quickstart.md)<br>
[Schnellstart für PowerShell in Cloud Shell](quickstart-powershell.md)
