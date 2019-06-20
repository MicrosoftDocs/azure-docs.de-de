---
title: Übersicht über das SDK für grafische Azure Automation-Runbooks
description: Dieser Artikel beschreibt, wie Sie das SDK für grafische Azure Automation-Runbooks verwenden.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc9f1feff6d8d45e52c8621d1ec4b36e0a4a4a76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61303205"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Verwenden des SDK für grafische Azure Automation-Runbooks

[Grafische Runbooks](automation-graphical-authoring-intro.md) sind Runbooks, mit deren Hilfe Sie die Komplexität des zugrunde liegenden Windows PowerShell- oder PowerShell-Workflowcodes verwalten können. Mit dem Grafikerstellungs-SDK für Microsoft Azure Automation können Entwickler grafische Runbooks für den Azure Automation-Dienst erstellen und bearbeiten. Die folgenden Codeausschnitte zeigen den grundlegenden Ablauf beim Erstellen eines grafischen Runbooks aus Ihrem Code.

## <a name="pre-requisites"></a>Voraussetzungen

Importieren Sie zuerst das Paket `Microsoft.Azure.Automation.GraphicalRunbook.Model` in Ihr Projekt.

## <a name="create-a-runbook-object-instance"></a>Erstellen einer Runbookobjektinstanz

Verweisen Sie auf die Assembly `Orchestrator.GraphRunbook.Model`, und erstellen Sie eine Instanz der Klasse `Orchestrator.GraphRunbook.Model.GraphRunbook`:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Hinzufügen von Runbookparametern

Instanziieren Sie `Orchestrator.GraphRunbook.Model.Parameter`-Objekte, und fügen Sie sie dem Runbook hinzu:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Hinzufügen von Aktivitäten und Links

Instanziieren Sie Aktivitäten der entsprechenden Typen, und fügen Sie sie dem Runbook hinzu:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Aktivitäten werden von den folgenden Klassen im Namespace `Orchestrator.GraphRunbook.Model` implementiert:

|Klasse  |Aktivität  |
|---------|---------|
|CommandActivity     | Ruft einen PowerShell-Befehl auf (Cmdlet, Funktion usw.).        |
|InvokeRunbookActivity     | Ruft ein anderes Runbook inline auf.        |
|JunctionActivity     | Wartet darauf, dass alle eingehenden Verzweigungen fertig gestellt werden.        |
|WorkflowScriptActivity     | Führt einen PowerShell-Block oder einen PowerShell-Workflowcodeblock (abhängig vom Runbooktyp) im Rahmen des Runbooks aus. Diese effiziente Aktivität sollten Sie nicht zu oft ausführen: Die Benutzeroberfläche zeigt diesen Skriptblock als Text an. Die Ausführungs-Engine behandelt den bereitgestellten Block als Blackbox und versucht nicht, den Inhalt zu analysieren – mit Ausnahme einer grundlegenden Syntaxprüfung. Wenn Sie nur einen einzelnen PowerShell-Befehl aufrufen möchten, verwenden Sie „CommandActivity“.        |

> [!NOTE]
> Leiten Sie keine eigenen Aktivitäten aus den bereitgestellten Klassen ab: Azure Automation kann keine Runbooks mit benutzerdefinierten Aktivitätstypen verwenden.

„CommandActivity“- und „InvokeRunbookActivity“-Parameter müssen als Wertdeskriptoren bereitgestellt werden, nicht als direkte Werte. Wertdeskriptoren geben an, wie die tatsächlichen Parameterwerte erstellt werden. Derzeit sind die folgenden Wertdeskriptoren verfügbar:


|Deskriptor  |Definition  |
|---------|---------|
|ConstantValueDescriptor     | Verweist auf einen hartcodierten konstanten Wert.        |
|RunbookParameterValueDescriptor     | Verweist auf einen Runbookparameter anhand des Namens.        |
|ActivityOutputValueDescriptor     | Verweist auf eine Upstreamaktivitätausgabe, die einer Aktivität erlaubt, Daten zu „abonnieren“, die von einer anderen Aktivität erzeugt wurden.        |
|AutomationVariableValueDescriptor     | Verweist auf ein Automation-Variablenasset anhand des Namens.         |
|AutomationCredentialValueDescriptor     | Verweist auf ein Automation-Zertifikatasset anhand des Namens.        |
|AutomationConnectionValueDescriptor     | Verweist auf ein Automation-Verbindungsasset anhand des Namens.        |
|PowerShellExpressionValueDescriptor     | Gibt einen PowerShell-Freiformausdruck an, der kurz vor Aktivitätsaufruf ausgewertet wird.  <br/>Diese effiziente Aktivität sollten Sie nicht zu oft ausführen: Die Benutzeroberfläche zeigt diesen Ausdruck als Text an. Die Ausführungs-Engine behandelt den bereitgestellten Block als Blackbox und versucht nicht, den Inhalt zu analysieren – mit Ausnahme einer grundlegenden Syntaxprüfung. Verwenden Sie sofern möglich spezifischere Wertdeskriptoren.      |

> [!NOTE]
> Leiten Sie keine eigenen Wertdeskriptoren aus den bereitgestellten Klassen ab: Azure Automation kann keine Runbooks mit benutzerdefinierten Wertdeskriptortypen verwenden.

Instanziieren Sie Links, die Aktivitäten verbinden, und fügen Sie sie dem Runbook hinzu:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Speichern des Runbooks in einer Datei

Verwenden Sie `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer`, um ein Runbooks mit einer Zeichenfolge zu serialisieren:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Diese Zeichenfolge kann in einer Datei mit der Erweiterung **.graphrunbook** gespeichert werden. Diese Datei lässt sich in Azure Automation importieren.
Das serialisierte Format kann sich in künftigen Versionen von `Orchestrator.GraphRunbook.Model.dll` ändern. Wir garantieren Abwärtskompatibilität: Jedes Runbook, das mit einer älteren Version von `Orchestrator.GraphRunbook.Model.dll` serialisiert wurde, kann mit jeder neueren Version deserialisiert werden. Aufwärtskompatibilität ist nicht gewährleistet: Ein mit einer neueren Version serialisiertes Runbook ist möglicherweise nicht mit älteren Versionen deserialisierbar.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu grafischen Runbooks in Azure Automation finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md).

