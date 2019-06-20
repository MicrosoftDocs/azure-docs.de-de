---
title: Fehlerbehandlung in grafischen Azure Automation-Runbooks
description: In diesem Artikel wird beschrieben, wie Sie die Fehlerbehandlungslogik in grafischen Azure Automation-Runbooks implementieren.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d7fe38334b71334d4dae9235643117efdf5fbd5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61233085"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Fehlerbehandlung in grafischen Azure Automation-Runbooks

Eine wichtige Regel, die Sie beim Entwerfen von Runbooks berücksichtigen sollten, ist die Identifizierung verschiedener möglicher Probleme für Runbooks, z. B. Erfolg, erwartete Fehlerzustände und unerwartete Fehlerbedingungen.

Runbooks sollten über eine entsprechende Fehlerbehandlung verfügen. Wenn Sie die Ausgabe einer Aktivität überprüfen oder einen Fehler bei grafischen Runbooks behandeln möchten, können Sie eine Windows PowerShell-Codeaktivität verwenden, eine bedingte Logik für den Ausgabelink der Aktivität definieren oder eine andere Methode anwenden.          

Wenn bei der Ausführung von Runbooks für eine Aktivität ein Fehler ohne Abbruch auftritt, wird die darauffolgende Aktivität häufig trotzdem verarbeitet. Wahrscheinlich generiert der Fehler eine Ausnahme, aber die Ausführung der nächsten Aktivität wird dennoch zugelassen. Auf diese Weise behandelt PowerShell Fehler.    

Die PowerShell-Fehler, die während der Ausführung auftreten können, können mit oder ohne Abbruch erfolgen. Fehler mit und ohne Abbruch unterscheiden sich folgendermaßen:

* **Fehler mit Abbruch**: Ein schwerwiegender Fehler während der Ausführung, durch den der Befehl (bzw. die Skriptausführung) vollständig unterbrochen wird. Beispiele hierfür sind nicht vorhandene Cmdlets, Syntaxfehler, die die Ausführung eines Cmdlets verhindern, oder andere schwerwiegende Fehler.

* **Fehler ohne Abbruch**: Ein nicht schwerwiegender Fehler, bei dem die Ausführung fortgesetzt werden kann. Beispiele hierfür sind Ablauffehler wie „Datei nicht gefunden“, Berechtigungsprobleme usw.

Grafische Azure Automation-Runbooks wurden verbessert und um die Fehlerbehandlung erweitert. Sie können Ausnahmen jetzt in Fehler ohne Abbruch ändern und Fehlerlinks zwischen Aktivitäten erstellen. So können Autoren von Runbooks Fehler abfangen und erkannte bzw. unerwartete Bedingungen verwalten.  

## <a name="when-to-use-error-handling"></a>Einsatzgebiete der Fehlerbehandlung

Wenn bei einer kritischen Aktivität ein Fehler oder eine Ausnahme ausgelöst wird, können Sie das Verarbeiten der nächsten Aktivität im Runbook verhindern und den Fehler entsprechend behandeln. Dies ist besonders wichtig, wenn Sie mit Ihren Runbooks einen Unternehmens- oder Dienstleistungsprozess unterstützen.

Für jede Aktivität, die einen Fehler erzeugt kann, kann der Autor des Runbooks einen Fehlerlink zu einer anderen Aktivität hinzufügen. Die Zielaktivität kann einen beliebigen Typ aufweisen: Codeaktivitäten, Aufruf eines Cmdlets, Aufruf eines anderen Runbooks usw.

Außerdem kann die Zielaktivität auch über ausgehende Links verfügen. Dies können reguläre Links oder Fehlerlinks sein. Der Autor des Runbooks kann somit eine komplexe Fehlerbehandlungslogik implementieren, ohne eine Codeaktivität einbinden zu müssen. Wir empfehlen, ein Runbook für die Fehlerbehandlung mit allgemeiner Funktionalität zu erstellen. Dies ist jedoch nicht zwingend erforderlich. Eine Fehlerbehandlungslogik in einer PowerShell-Codeaktivität ist nicht die einzige Option.  

Möglich wäre auch ein Runbook, das versucht, einen virtuellen Computer zu starten und darauf eine Anwendung zu installieren. Wenn der virtuelle Computer nicht richtig gestartet wird, führt er zwei Aktionen durch:

1. Er sendet eine Benachrichtigung zu diesem Problem.
2. Er startet ein anderes Runbook, mit dem automatisch ein neuer virtueller Computer als Ersatz bereitgestellt wird.

Eine Lösung besteht darin, einen Fehlerlink zu verwenden, der auf eine Aktivität zur Behandlung von Schritt 1 zeigt. Sie könnten z. B. das **Write-Warning**-Cmdlet, mit einer Aktivität für Schritt 2 verbinden, z. B. das **Start-AzureRmAutomationRunbook**-Cmdlet.

Sie können dieses Verhalten auch für die Verwendung in vielen Runbooks verallgemeinern, indem Sie diese beiden Aktivitäten in ein separates Runbook für die Fehlerbehandlung einfügen und die zuvor beschriebenen Anweisungen befolgen. Vor dem Aufrufen dieses Runbooks für die Fehlerbehandlung können Sie beispielsweise aus den Daten im Originalrunbook eine benutzerdefinierte Nachricht erstellen und diese dann als Parameter an das Runbook für die Fehlerbehandlung übergeben.

## <a name="how-to-use-error-handling"></a>Verwenden der Fehlerbehandlung

Jede Aktivität verfügt über eine Konfigurationseinstellung, die Ausnahmen in Fehler ohne Abbruch umwandelt. Diese Einstellung ist standardmäßig deaktiviert. Wir empfehlen Ihnen, diese Einstellung für alle Aktivitäten zu aktivieren, für die Sie Fehler behandeln möchten.  

Durch das Aktivieren dieser Konfiguration stellen Sie sicher, dass sowohl Fehler mit und ohne Abbruch in der Aktivität als Fehler ohne Abbruch behandelt werden und per Fehlerlink verarbeitet werden können.  

Nach dem Konfigurieren dieser Einstellung erstellen Sie eine Aktivität zum Behandeln des Fehlers. Wenn eine Aktivität einen beliebigen Fehler erzeugt, werden die ausgehenden Fehlerlinks verwendet. Die regulären Links werden auch dann nicht verwendet, wenn mit der Aktivität auch eine reguläre Ausgabe erzeugt wurde.<br><br> ![Fehlerlink für Automation-Runbook – Beispiel](media/automation-runbook-graphical-error-handling/error-link-example.png)

Im folgenden Beispiel ruft ein Runbook eine Variable auf, die den Computernamen eines virtuellen Computers enthält. Diese versucht, den virtuellen Computer mit der nächsten Aktivität zu starten.<br><br> ![Fehlerbehandlung mit Automation-Runbook – Beispiel](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Die **Get-AutomationVariable**-Aktivität und **Start-AzureRmVm** werden konfiguriert, um Ausnahmen in Fehler zu verwandeln. Falls beim Abrufen der Variablen oder Starten des virtuellen Computers Probleme auftreten, werden Fehler generiert.<br><br> ![Fehlerbehandlung mit Automation-Runbook – Aktivitätseinstellungen](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Fehlerlinks zeigen von diesen Aktivitäten auf eine Aktivität zur **Fehlerverwaltung** (Codeaktivität), die mit einem einfachen PowerShell-Ausdruck konfiguriert ist. Das Schlüsselwort *Throw* wird verwendet, um die Verarbeitung zu beenden, und mit *$Error.Exception.Message* wird die Meldung abgerufen, in der die aktuelle Ausnahme beschrieben wird.<br><br> ![Fehlerbehandlung mit Automation-Runbook – Codebeispiel](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Links und Linktypen in grafischen Runbooks finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* Weitere Informationen zum Ausführen von Runbooks, zum Überwachen von Runbookaufträgen sowie andere technische Details finden Sie unter [Verfolgen eines Runbookauftrags](automation-runbook-execution.md).

