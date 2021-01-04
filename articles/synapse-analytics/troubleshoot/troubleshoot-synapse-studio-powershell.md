---
title: Beheben von Synapse Studio-Konnektivitätsproblemen
description: Problembehandlung bei der Azure Synapse Studio-Konnektivität mit PowerShell
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 5f0dc73877d7742d4fc6a0c5b9bcf0529d475e0a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445268"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>Beheben von Synapse Studio-Konnektivitätsproblemen mit PowerShell

Azure Synapse Studio hängt von einer Reihe von ordnungsgemäß funktionierenden Web-API-Endpunkten ab. Dieser Leitfaden wird Ihnen helfen, die Ursachen von Konnektivitätsproblemen in folgenden Situationen zu erkennen:
- Konfigurieren des lokalen Netzwerks (z. B. Netzwerk hinter einer Unternehmensfirewall) für den Zugriff auf Azure Synapse Studio.
- Auftreten von Konnektivitätsproblemen mit Azure Synapse Studio.

## <a name="prerequisite"></a>Voraussetzung

* PowerShell 5.0 oder höhere Version unter Windows oder
* PowerShell Core 6.0 oder höhere Version unter Windows oder Linux

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Klicken Sie mit der rechten Maustaste auf den folgenden Link, und wählen Sie „Ziel speichern unter“ aus:

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Alternativ dazu können Sie den Link direkt öffnen und die geöffnete Skriptdatei speichern. Speichern Sie die Adresse des obigen Links nicht, da sie sich zukünftig ändern kann.

Klicken Sie im Datei-Explorer mit der rechten Maustaste auf die heruntergeladene Skriptdatei, und wählen Sie „Mit PowerShell ausführen“ aus.

![Ausführen heruntergeladener Skriptdateien mit PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Wenn Sie dazu aufgefordert werden, geben Sie den Namen des Azure Synapse-Arbeitsbereichs ein, bei dem derzeit ein Problem auftritt oder dessen Konnektivität Sie testen möchten, und drücken Sie dann die EINGABETASTE.

![Arbeitsbereichsnamen eingeben](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Die Diagnosesitzung wird gestartet. Warten Sie, bis die Überprüfung abgeschlossen ist.

![Warten auf den Abschluss der Diagnose](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Am Ende wird eine Zusammenfassung der Diagnose angezeigt. Wenn Ihr PC keine Verbindung mit einem oder mehreren Endpunkten herstellen kann, werden im Abschnitt „Zusammenfassung“ einige Vorschläge angezeigt.

![Überprüfen der Diagnosezusammenfassung](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Zusätzlich wird eine Diagnoseprotokolldatei für diese Sitzung im gleichen Ordner wie das Skript für die Problembehandlung erstellt. Sein Speicherort ist im Abschnitt „Allgemeine Tipps“ (`D:\TestAzureSynapse_2020....log`) angegeben. Sie können diese Datei bei Bedarf an den technischen Support senden.

Wenn Sie ein Netzwerkadministrator sind und die Firewallkonfiguration für Azure Synapse Studio optimieren, können die technischen Details, die über den Abschnitt „Zusammenfassung“ angezeigt werden, hilfreich sein.

* Alle Testelemente (Anforderungen), die mit „Erfolgreich“ gekennzeichnet sind, bedeuten, dass sie die Konnektivitätstests bestanden haben, unabhängig vom HTTP-Statuscode.
 Bei den fehlerhaften Anforderungen wird der Grund gelb dargestellt, z. B. `NamedResolutionFailure` oder `ConnectFailure`. Diese Gründe können Ihnen helfen herauszufinden, ob es Konfigurationsfehler in Ihrer Netzwerkumgebung gibt.


## <a name="next-steps"></a>Nächste Schritte
Wenn die vorherigen Schritte nicht zur Lösung Ihres Problems beitragen, [erstellen Sie ein Supportticket](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
