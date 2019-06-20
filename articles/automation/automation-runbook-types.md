---
title: Azure Automation-Runbooktypen
description: 'Beschreibt die verschiedenen Runbooktypen, die Sie in Azure Automation verwenden können, sowie Aspekte, die Sie bei der Wahl des geeigneten Typs berücksichtigen sollten. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7a3990366814beda83852fc7c07c896445388c23
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737158"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-Runbooktypen

Azure Automation unterstützt verschiedene Runbooktypen, die in der folgenden Tabelle kurz beschrieben werden.  Die folgenden Abschnitte bieten weitere Informationen zu den einzelnen Typen und deren Einsatzbereichen.

| Type | BESCHREIBUNG |
|:--- |:--- |
| [Grafisch](#graphical-runbooks)|Basiert auf Windows PowerShell und wird vollständig im grafischen Editor im Azure-Portal erstellt und bearbeitet. |
| [PowerShell-Workflow, grafisch](#graphical-runbooks)|Basiert auf Windows PowerShell-Workflow und wird vollständig im grafischen Editor im Azure-Portal erstellt und bearbeitet. |
| [PowerShell](#powershell-runbooks) |Textrunbook, das auf einem Windows PowerShell-Skript basiert. |
| [PowerShell-Workflow](#powershell-workflow-runbooks)|Textrunbook, das auf einem Windows PowerShell-Workflow basiert. |
| [Python](#python-runbooks) |Auf Python basierendes Textrunbook |

## <a name="graphical-runbooks"></a>Grafische Runbooks

[Grafische](automation-runbook-types.md#graphical-runbooks) und grafische PowerShell-Workflow-Runbooks werden im grafischen Editor im Azure-Portal erstellt und bearbeitet.  Sie können in eine Datei exportiert und anschließend in ein anderes Automation-Konto importiert werden. Aber Sie können nicht mit einem anderen Tool erstellt oder bearbeitet werden. Grafische Runbooks generieren PowerShell-Code, aber Sie können den Code nicht direkt anzeigen oder ändern. Grafische Runbooks können weder in eines der [Textformate](automation-runbook-types.md) konvertiert werden, noch kann ein Textrunbook in das Grafikformat konvertiert werden. Grafische Runbooks können während des Imports in grafische PowerShell-Workflow-Runbooks konvertiert werden und umgekehrt.

### <a name="advantages"></a>Vorteile

* Visuelles Erstellungsmodell zum Einfügen/Verknüpfen/Konfigurieren  
* Schwerpunkt auf Datenfluss über das Gateway  
* Visuelle Darstellung von Verwaltungsprozessen  
* Einschließen weiterer Runbooks als untergeordnete Runbooks, um übergeordnete Workflows zu erstellen  
* Vereinfacht modulare Programmierung  

### <a name="limitations"></a>Einschränkungen

* Runbook kann nicht außerhalb des Azure-Portals bearbeitet werden.
* Erfordert möglicherweise Aktivität mit PowerShell-Code, um komplexe Logik auszuführen.
* Der vom grafischen Workflow erstellte PowerShell-Code kann nicht angezeigt oder direkt bearbeitet werden. Sie können den erstellten Code in allen Codeaktivitäten anzeigen.
* Auf einem Linux Hybrid Runbook Worker nicht ausführbar

## <a name="powershell-runbooks"></a>PowerShell-Runbooks

PowerShell-Runbooks basieren auf Windows PowerShell.  Sie bearbeiten den Code des Runbooks direkt mit dem Text-Editor im Azure-Portal.  Sie können auch einen beliebigen Offline-Texteditor verwenden und das [Runbook in Azure Automation importieren](manage-runbooks.md) .

### <a name="advantages"></a>Vorteile

* Implementierung der gesamten komplexen Logik mit PowerShell-Code ohne die zusätzliche Komplexität des PowerShell-Workflows.
* Runbook startet schneller als PowerShell-Workflow-Runbooks, da es nicht vor der Ausführung kompiliert werden muss.
* In Azure oder sowohl auf Linux als auch Windows Hybrid Runbook Worker ausführbar

### <a name="limitations"></a>Einschränkungen

* Autor muss mit PowerShell-Skripts vertraut sein.
* Eine [parallele Verarbeitung](automation-powershell-workflow.md#parallel-processing) zum gleichzeitigen Ausführen mehrerer Aktionen ist nicht möglich.
* Die Verwendung von [Prüfpunkten](automation-powershell-workflow.md#checkpoints) zum Fortsetzen des Runbooks bei einem Fehler ist nicht möglich.
* PowerShell-Workflow- und grafische Runbooks können nur mithilfe des Cmdlets „Start-AzureAutomationRunbook“ einbezogen werden, wodurch ein neuer Auftrag erstellt wird.

### <a name="known-issues"></a>Bekannte Probleme

Im Folgenden sind aktuell bekannte Probleme mit PowerShell-Runbooks aufgeführt.

* PowerShell-Runbooks können keine unverschlüsselten [Variablenassets](automation-variables.md) mit einem NULL-Wert abrufen.
* PowerShell-Runbooks können keine [Variablenassets](automation-variables.md) abrufen, deren Name *~* enthält.
* "Get-Process" in einer Schleife in einem PowerShell-Runbook kann nach etwa 80 Iterationen zum Absturz führen.
* Ein PowerShell-Runbook kann einen Fehler verursachen, wenn es versucht, eine große Datenmenge auf einmal in den Ausgabestream zu schreiben.   Sie können dieses Problem in der Regel vermeiden, indem Sie bei der Arbeit mit großen Objekten nur die benötigten Informationen ausgeben.  Statt beispielsweise *Get-Process* auszugeben, können Sie nur die erforderlichen Felder mit *Get-Process | Select ProcessName, CPU* ausgeben.

## <a name="powershell-workflow-runbooks"></a>PowerShell-Workflow-Runbooks

PowerShell-Workflow-Runbooks sind Textrunbooks, die auf einem [Windows PowerShell-Workflow](automation-powershell-workflow.md)basieren.  Sie bearbeiten den Code des Runbooks direkt mit dem Text-Editor im Azure-Portal.  Sie können auch einen beliebigen Offline-Texteditor verwenden und das [Runbook in Azure Automation importieren](manage-runbooks.md) .

### <a name="advantages"></a>Vorteile

* Implementierung der gesamten komplexen Logik mit PowerShell-Workflowcode.
* Verwendung von [Prüfpunkten](automation-powershell-workflow.md#checkpoints) zum Fortsetzen des Runbooks bei einem Fehler.
* Verwendung der [parallelen Verarbeitung](automation-powershell-workflow.md#parallel-processing) , um mehrere Aktionen gleichzeitig auszuführen.
* Kann andere grafische und PowerShell-Workflow-Runbooks als untergeordnete Runbooks integrieren, um übergeordnete Workflows zu erstellen.

### <a name="limitations"></a>Einschränkungen

* Autor muss mit PowerShell-Workflow vertraut sein.
* Die zusätzliche Komplexität des PowerShell-Workflows, z.B. [deserialisierte Objekte](automation-powershell-workflow.md#code-changes), müssen vom Runbook verarbeitet werden.
* Das Starten des Runbooks dauert länger als bei PowerShell-Runbooks, da es vor der Ausführung kompiliert werden muss.
* PowerShell-Runbooks können nur als untergeordnete Runbooks mithilfe des Cmdlets „Start-AzureAutomationRunbook“ einbezogen werden, wodurch ein neuer Auftrag erstellt wird.
* Auf einem Linux Hybrid Runbook Worker nicht ausführbar

## <a name="python-runbooks"></a>Python-Runbooks

Python-Runbooks werden unter Python-2 kompiliert.  Sie können den Code des Runbooks direkt mit einem Text-Editor im Azure-Portal oder mit einem beliebigen Text-Editor offline bearbeiten und [das Runbook in Azure Automation importieren](manage-runbooks.md).

### <a name="advantages"></a>Vorteile

* Verwendung der stabilen Python-Bibliotheken.
* In Azure oder auf beiden Linux Hybrid Runbook Workers ausführbar. Windows Hybrid Runbook Workers werden unterstützt, wenn [python2.7](https://www.python.org/downloads/release/latest/python2) installiert ist.

### <a name="limitations"></a>Einschränkungen

* Sie müssen mit Python-Skripts vertraut sein.
* Derzeit wird nur Python 2 unterstützt, die spezifischen Funktionen von Python 3 sind also nicht nutzbar.
* Um Bibliotheken von Drittanbietern nutzen zu können, müssen Sie [das Paket in das Automation-Konto importieren](python-packages.md), damit es verwendet wird.

## <a name="considerations"></a>Überlegungen

Wenn Sie festlegen, welchen Typ Sie für ein bestimmtes Runbook verwenden möchten, müssen Sie außerdem Folgendes berücksichtigen.

* Runbooks können nicht aus einem grafischen in einen textbasierten Typ oder umgekehrt konvertiert werden.
* Es gibt Einschränkungen bei der Verwendung von Runbooks verschiedener Typen als untergeordnete Runbooks. Weitere Informationen finden Sie unter [Untergeordnete Runbooks in Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Erstellen von grafischen Runbooks finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md)
* Informationen zu den Unterschieden zwischen PowerShell- und PowerShell-Workflow-Runbooks finden Sie unter [Grundlagen des Windows PowerShell-Workflows](automation-powershell-workflow.md)
* Weitere Informationen zum Erstellen oder Importieren eines Runbooks finden Sie unter [Erstellen oder Importieren eines Runbooks](manage-runbooks.md)

