---
title: Azure Cloud Shell für Windows-Benutzer | Microsoft-Dokumentation
description: Anleitung für Benutzer, die mit Linux-Systemen nicht vertraut sind
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 109067899884c04cbd283b5eef1d7e0089556b07
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301725"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell in Azure Cloud Shell für Windows-Benutzer

Im Mai 2018 wurden Änderungen für PowerShell in Azure Cloud Shell [angekündigt](https://azure.microsoft.com/blog/pscloudshellrefresh/).
Als PowerShell-Oberfläche in Azure Cloud Shell wird jetzt [PowerShell Core 6](https://github.com/powershell/powershell) in einer Linux-Umgebung ausgeführt.
Die PowerShell-Umgebung in Cloud Shell weist im Zuge dieser Änderung möglicherweise einige Unterschiede im Vergleich zu einer Windows PowerShell-Oberfläche auf.

## <a name="file-system-case-sensitivity"></a>Groß-/Kleinschreibung bei Dateisystemen

Beim Dateisystem unter Windows wird die Groß-/Kleinschreibung nicht berücksichtigt, wohingegen unter Linux Gegenteiliges der Fall ist.
Zuvor galten `file.txt` und `FILE.txt` als selbe Datei, jetzt jedoch gelten diese als unterschiedliche Dateien.
Bei `tab-completing` im Dateisystem muss die richtige Groß- und Kleinschreibung verwendet werden.
Bei PowerShell-spezifischen Elementen (z.B. Cmdlet `tab-completing`, Namen, Parameter und Werte) wird die Groß- und Kleinschreibung nicht berücksichtigt.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell-Aliase im Vergleich zu Linux-Hilfsprogrammen

Einige vorhandene PowerShell-Aliase weisen die gleichen Namen auf wie integrierte Linux-Befehle (z.B. `cat`,`ls`, `sort`, `sleep`). In PowerShell Core 6 wurden Aliase, die mit integrierten Linux-Befehlen in Konflikt stehen, entfernt.
Im Folgenden werden die allgemeine Aliase, die entfernt wurden, und die entsprechenden Befehle aufgeführt:  

|Entfernter Alias   |Entsprechender Befehl   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>Beibehalten von $HOME

Vorher konnten Benutzer lediglich Skripts und andere Dateien im Cloudlaufwerk beibehalten.
Jetzt wird das $HOME-Verzeichnis des Benutzers auch sitzungsübergreifend beibehalten.

## <a name="powershell-profile"></a>PowerShell-Profil

Standardmäßig wird kein PowerShell-Profil für einen Benutzer erstellt.
Zum Erstellen Ihres Profils erstellen Sie unter `$HOME/.config` ein `PowerShell`-Verzeichnis.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

Unter `$HOME/.config/PowerShell` können Sie Ihre Profildateien `profile.ps1` und/oder `Microsoft.PowerShell_profile.ps1` erstellen.

## <a name="whats-new-in-powershell-core-6"></a>Neuerungen in PowerShell Core 6

Informationen zu den Neuerungen in PowerShell Core 6 finden Sie in der [PowerShell-Dokumentation](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1) und im Blogbeitrag [Getting Started with PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) (Erste Schritte mit PowerShell Core).