---
title: Ausführen von Skripts in einer Azure Linux-VM
description: In diesem Thema wird das Ausführen von Skripts auf einem virtuellen Computer beschrieben.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 3881a6d0ee1139fa481908f09b6e96efda24a5cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60542542"
---
# <a name="run-scripts-in-your-linux-vm"></a>Ausführen von Skripts in Ihrer Linux-VM

Beim Automatisieren von Aufgaben oder Beheben von Problemen kann es erforderlich sein, Befehle in einer VM auszuführen. Der folgende Artikel enthält eine kurze Übersicht der Funktionen, die zum Ausführen von Skripts und Befehlen in Ihren VMs zur Verfügung stehen.

## <a name="custom-script-extension"></a>Benutzerdefinierte Skripterweiterung

Die [benutzerdefinierte Skripterweiterung](../extensions/custom-script-linux.md) dient in erster Linie zur Konfiguration nach der Bereitstellung und zur Softwareinstallation.

* Herunterladen und Ausführen von Skripts auf virtuellen Azure-Computern.
* Kann mithilfe von Azure Resource Manager-Vorlagen, der Azure CLI, REST-API, PowerShell oder dem Azure-Portal ausgeführt werden.
* Skriptdateien können bei der Ausführung aus dem Azure-Portal von Azure Storage oder GitHub heruntergeladen oder von Ihrem PC bereitgestellt werden.
* Führen Sie auf Windows-Computern PowerShell-Skripts und auf Linux-Computern Bash-Skripts aus.
* Nützlich für die Konfiguration nach der Bereitstellung, bei der Softwareinstallation und bei anderen Konfigurations- oder Verwaltungsaufgaben.

## <a name="run-command"></a>Befehl ausführen

Die Funktion [Befehl ausführen](run-command.md) ermöglicht die Verwaltung und Problembehandlung von virtuellen Computern mithilfe von Skripts und ist selbst dann verfügbar, wenn der Computer nicht erreicht werden kann, etwa weil in der Gastfirewall der RDP- oder SSH-Port nicht geöffnet ist.

* Ausführen von Skripts auf virtuellen Azure-Computern.
* Kann mithilfe des [Azure-Portals](run-command.md), der [REST-API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), des [Azure CLIs](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) oder von [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) ausgeführt werden.
* Führen Sie im Azure-Portal schnell ein Skript aus, zeigen Sie die Ausgabe an, und wiederholen Sie bei Bedarf.
* Skripts können direkt eingegeben werden, oder Sie können eins der integrierten Skripts ausführen.
* Führen Sie auf Windows-Computern PowerShell-Skripts und auf Linux-Computern Bash-Skripts aus.
* Nützlich für die Verwaltung von virtuellen Computern und Anwendungen und zum Ausführen von Skripts auf virtuellen Computern, die nicht erreichbar sind.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Der [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) stellt allgemeine Verwaltung für Computer, Anwendungen und Umgebungen mit benutzerdefinierten Skripts zur Verfügung, die in einem Automatisierungskonto gespeichert sind.

* Ausführen von Skripts auf Azure-Computern und Computern, die nicht unter Azure ausgeführt werden.
* Kann mithilfe des Azure-Portals, des Azure CLIs, der REST-API, von PowerShell oder einem Webhook ausgeführt werden.
* In einem Automatisierungskonto gespeicherte und verwaltete Skripts.
* Ausführen von PowerShell-, PowerShell Workflow-, Python- oder Graphical-Runbooks
* Keine zeitliche Begrenzung der Laufzeit von Skripts.
* Mehrere Skripts können parallel ausgeführt werden.
* Die vollständige Skriptausgabe wird zurückgegeben und gespeichert.
* Der Auftragsverlauf steht 90 Tage lang zur Verfügung.
* Skripts können als lokales System oder mit vom Benutzer bereitgestellten Anmeldeinformationen ausgeführt werden.
* Erfordert [manuelle Installation](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Serielle Konsole

Die [serielle Konsole](serial-console.md) stellt direkten Zugriff auf eine VM zur Verfügung, ähnlich, als wäre eine Tastatur mit der VM verbunden.

* Ausführen von Befehlen auf virtuellen Azure-Computern.
* Kann mithilfe einer textbasierten Konsole für den Computer im Azure-Portal ausgeführt werden.
* Melden Sie sich mit einem lokalen Benutzerkonto beim Computer an.
* Nützlich, wenn Zugriff auf den virtuellen Computer unabhängig von dessen Netzwerk- oder Betriebssystemstatus erforderlich ist.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die verschiedenen Funktionen, die zur Ausführung von Skripts und Befehlen innerhalb Ihrer VMs zur Verfügung stehen.

* [Benutzerdefinierte Skripterweiterung](../extensions/custom-script-linux.md)
* [Befehl ausführen](run-command.md)
* [Hybrid-Runbook-Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Serielle Konsole](serial-console.md)