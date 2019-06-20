---
title: Erstellen von benutzerdefinierten Artefakten für Ihren virtuellen DevTest Labs-Computer | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre eigenen Artefakte für die Verwendung mit Azure DevTest Labs erstellen.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 0d1e269a1818f013bc14842bc541216d7f31bc84
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116825"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Erstellen benutzerdefinierter Artefakte für Ihren virtuellen DevTest Labs-Computer

Das folgende Video enthält einen Überblick über die in diesem Artikel beschriebenen Schritte:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Übersicht
Sie können nach der Bereitstellung einer VM *Artefakte* bereitstellen und Ihre Anwendung einrichten. Ein Artefakt umfasst eine Artefaktdefinitionsdatei und andere Skriptdateien, die in einem Ordner in einem Git-Repository gespeichert sind. Artefaktdefinitionsdateien bestehen aus JSON und Ausdrücken, mit denen Sie angeben können, was Sie auf einem virtuellen Computer installieren möchten. Sie können beispielsweise den Namen eines Artefakts, einen auszuführenden Befehl sowie Parameter, die beim Ausführen des Befehls verfügbar sind, definieren. Sie können auf andere Skriptdateien innerhalb der Artefaktdefinitionsdatei anhand ihres Namens verweisen.

## <a name="artifact-definition-file-format"></a>Format von Artefaktdefinitionsdateien
Das folgende Beispiel zeigt die Abschnitte, die die grundlegende Struktur einer Definitionsdatei bilden:

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Elementname | Erforderlich? | BESCHREIBUNG |
| --- | --- | --- |
| $schema |Nein  |Speicherort der JSON-Schemadatei Mithilfe der JSON-Schemadatei können Sie die Gültigkeit der Definitionsdatei testen. |
| title |Ja |Der Name des im Lab angezeigten Artefakts. |
| description |Ja |Die Beschreibung des im Lab angezeigten Artefakts. |
| iconUri |Nein  |Der URI des im Lab angezeigten Symbols |
| targetOsType |Ja |Das Betriebssystem der VM, auf der das Artefakt installiert ist. Unterstützte Optionen sind „Windows“ und „Linux“. |
| parameters |Nein  |Werte, die bereitgestellt werden, wenn der Artefaktinstallationsbefehl auf einem Computer ausgeführt wird. Dieser ermöglicht die Anpassung Ihres Artefakts. |
| runCommand |Ja |Artefaktinstallationsbefehl, der auf einem virtuellen Computer ausgeführt wird. |

### <a name="artifact-parameters"></a>Artefaktparameter
Geben Sie im Parameterabschnitt der Definitionsdatei an, welche Werte ein Benutzer beim Installieren eines Artefakts eingeben kann. Auf diese Werte können Sie im Artefaktinstallationsbefehl verweisen.

Sie definieren Parameter mit der folgenden Struktur:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Elementname | Erforderlich? | BESCHREIBUNG |
| --- | --- | --- |
| type |Ja |Der Typ des Parameterwerts. In der folgenden Liste finden Sie die zulässigen Typen. |
| displayName |Ja |Der Name des Parameters, der einem Benutzer im Labor angezeigt wird. |
| description |Ja |Die Beschreibung des Parameters, der im Labor angezeigt wird. |

Folgende Typen sind zulässig:

* „string“ (eine beliebige gültige JSON-Zeichenfolge)
* „int“ (eine gültige JSON-Ganzzahl)
* „bool“ (ein gültiger boolescher JSON-Wert)
* „array“ (ein gültiges JSON-Array)

## <a name="artifact-expressions-and-functions"></a>Ausdrücke und Funktionen für Artefakte
Sie können zum Erstellen des Artefaktinstallationsbefehls Ausdrücke und Funktionen verwenden.
Ausdrücke werden in Klammern eingeschlossen ([ und ]) und beim Installieren des Artefakts ausgewertet. Ausdrücke können an einer beliebigen Stelle in einem JSON-Zeichenfolgenwert angezeigt werden. Ausdrücke geben stets einen anderen JSON-Wert zurück. Wenn Sie ein Zeichenfolgenliteral verwenden müssen, das mit einer eckigen Klammer ([) beginnt, müssen Sie zwei eckige Klammern verwenden ([[).
In der Regel verwenden Sie Ausdrücke mit Funktionen, um einen Wert zu erstellen. Genau wie in JavaScript haben Funktionsaufrufe das Format **functionName(arg1, arg2, arg3)**.

Die folgende Liste zeigt häufig verwendete Funktionen:

* **parameters(parameterName)**: Gibt einen Parameterwert zurück, der beim Ausführen des Artefaktbefehls bereitgestellt wird.
* **concat(arg1, arg2, arg3,….. )**: Kombiniert mehrere Zeichenfolgenwerte. Diese Funktion kann eine Vielzahl an Argumenten entgegennehmen.

Das folgende Beispiel zeigt, wie Sie mit Ausdrücken und Funktionen einen Wert erstellen:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Erstellen eines benutzerdefinierten Artefakts

1. Installieren Sie einen JSON-Editor. Sie benötigen zum Bearbeiten von Artefaktdefinitionsdateien einen JSON-Editor. Empfehlenswert ist [Visual Studio Code](https://code.visualstudio.com/), der für Windows, Linux und OS X verfügbar ist.
2. Rufen Sie die Beispieldefinitionsdatei „artifactfile.json“ ab. Sehen Sie sich die Artefakte in unserem [GitHub-Repository](https://github.com/Azure/azure-devtestlab) an, die vom DevTest Labs-Team erstellt wurden. Wir haben eine umfangreiche Bibliothek an Artefakten erstellt, mit der Sie eigene Artefakte erstellen können. Laden Sie eine Artefaktdefinitionsdatei herunter, und nehmen Sie an dieser Änderungen vor, um eigene Artefakte zu erstellen.
3. Verwenden Sie IntelliSense. Zeigen Sie mithilfe von IntelliSense gültige Elemente an, die zum Erstellen einer Artefaktdefinitionsdatei verwendet werden können. Sie können auch die verschiedenen Optionen für die Werte eines Elements sehen. Beispielsweise zeigt IntelliSense beim Bearbeiten des **targetOsType**-Elements die beiden Wahlmöglichkeiten für Windows und Linux an.
4. Speichern Sie das Artefakt im [öffentlichen Git-Repository für DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) oder in [Ihrem eigenen Git-Repository](devtest-lab-add-artifact-repo.md). Im öffentlichen Repository können Sie Artefakte anzeigen, die von anderen Benutzern gemeinsam verwendet werden und die Sie direkt verwenden oder entsprechend Ihren Anforderungen anpassen können.
   
   1. Erstellen Sie ein separates Verzeichnis für jedes Artefakt. Der Verzeichnisname muss mit dem Artefaktnamen identisch sein.
   2. Speichern Sie die Artefaktdefinitionsdatei („artifactfile.json“) in dem erstellten Verzeichnis.
   3. Speichern Sie die Skripts, auf die im Artefaktinstallationsbefehl verwiesen wird.
      
      Ein Beispiel für den möglichen Inhalt eines Artefaktordners:
      
      ![Beispiel für einen Artefaktordner](./media/devtest-lab-artifact-author/git-repo.png)
5. Wenn Sie zum Speichern von Artefakten Ihr eigenes Repository verwenden, fügen Sie das Repository zum Lab hinzu, wie in diesem Artikel beschrieben: [Hinzufügen eines Git-Repositorys für Artefakte und Vorlagen](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Verwandte Artikel
* [Diagnostizieren von Artefaktfehlern in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Einbinden einer VM in eine vorhandene Active Directory-Domäne mithilfe einer Resource Manager-Vorlage in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie einem [ein Git-Artefaktrepository zu einem Lab hinzufügen](devtest-lab-add-artifact-repo.md).
