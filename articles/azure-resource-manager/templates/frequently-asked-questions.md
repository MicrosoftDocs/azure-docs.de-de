---
title: Häufig gestellte Fragen zu Resource Manager-Vorlagen
description: Häufig gestellte Fragen (FAQ) zu Azure Resource Manager-Vorlagen (ARM-Vorlagen).
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 757dfa119649e21c6e1787671afa61ee18184300
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561901"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>Häufig gestellte Fragen zu Resource Manager-Vorlagen

In diesem Artikel finden Sie Antworten auf häufig gestellte Fragen zu Azure Resource Manager-Vorlagen (ARM-Vorlagen).

## <a name="getting-started"></a>Erste Schritte

* **Was sind Resource Manager-Vorlagen, und warum sollte ich sie verwenden?**

  Resource Manager-Vorlagen sind JSON-Dateien, in denen Sie definieren, was Sie in Azure bereitstellen möchten. Vorlagen helfen Ihnen dabei, eine Infrastructure-as-Code-Lösung für Azure zu implementieren. Ihre Organisation kann die damit erforderliche Infrastruktur wiederholt und zuverlässig in verschiedenen Umgebungen bereitstellen.

  Weitere Informationen zur Unterstützung von Resource Manager-Vorlagen bei der Verwaltung Ihrer Azure-Infrastruktur finden Sie unter [Was sind ARM-Vorlagen?](overview.md)

* **Wie beginne ich mit der Nutzung von Vorlagen?**

  Um die Erstellung von Resource Manager-Vorlagen zu vereinfachen, benötigen Sie die richtigen Tools. Wir empfehlen die Installation von [Visual Studio Code](https://code.visualstudio.com/) und der [Erweiterung „Azure Resource Manager-Tools“](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Eine kurze Einführung in diese Tools finden Sie unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

  Wenn Sie sich über das Erstellen von Resource Manager-Vorlagen informieren möchten, arbeiten Sie die [Tutorial-Reihe zu Resource Manager-Vorlagen für Einsteiger](template-tutorial-create-first-template.md) durch. Diese Tutorials führen Sie Schritt für Schritt durch das Erstellen einer Resource Manager-Vorlage. Sie erfahren mehr über die verschiedenen Abschnitte einer Vorlage und deren Zusammenarbeit. Diese Informationen sind auch als [Microsoft Learn-Modul](/learn/modules/authoring-arm-templates/) verfügbar.

* **Sollte ich Resource Manager-Vorlagen oder Terraform für die Bereitstellung in Azure verwenden?**

  Verwenden Sie die für Sie am besten geeignete Option. Beide Dienste unterstützen Sie bei der Automatisierung von Bereitstellungen in Azure.

  Wir sind davon überzeugt, dass Resource Manager-Vorlagen gegenüber anderen Infrastructure-as-Code-Diensten eine Reihe von Vorteilen bieten. Weitere Informationen zu diesen Vorteilen finden Sie unter [Warum ARM-Vorlagen verwenden?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Build 2020

* **Ich habe die Präsentation auf der Microsoft Build 2020 verpasst. Kann ich sie trotzdem ansehen?**

  Ja, [Sie können sie hier jederzeit ansehen](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions).

* **Wo erhalte ich weitere Informationen zu den neuen Features, die auf der Build angekündigt wurden?**

  Allgemeine Informationen zu den Funktionen, an denen wir gerade arbeiten, finden Sie in der [Azure Advisors-Yammer-Gruppe zu Bereitstellungen](https://aka.ms/ARMMeet).

  Wenn Sie mehr über die neue Vorlagensprache erfahren möchten, [melden Sie sich für Benachrichtigungen an](https://aka.ms/armLangUpdates).

  Informationen zu den Vorlagenspezifikationen finden Sie unter [Vorlagenspezifikationen im Azure Resource Manager (Vorschau)](template-specs.md).

## <a name="creating-and-testing-templates"></a>Erstellen und Testen von Vorlagen

* **Wo finde ich Informationen zu bewährten Methoden für Resource Manager-Vorlagen?**

  Empfehlungen zum Implementieren Ihrer Vorlagen finden Sie unter [Bewährte Methoden für ARM-Vorlagen](template-best-practices.md). Nachdem Sie eine Vorlage erstellt haben, führen Sie das [ARM-Testtoolkit](https://github.com/azure/arm-ttk) aus. Hiermit wird überprüft, ob Ihre Vorlage den empfohlenen Methoden entspricht.

* **Ich habe meine Umgebung über das Portal eingerichtet. Gibt es eine Möglichkeit, die Vorlage aus einer vorhandenen Ressourcengruppe zu übernehmen?**

  Ja, Sie können die [Vorlage aus einer Ressourcengruppe exportieren](export-template-portal.md). Die exportierte Vorlage ist ein guter Ausgangspunkt, um sich mit Vorlagen vertraut zu machen. Sie sollten sie allerdings überarbeiten, bevor Sie sie in einer Produktionsumgebung verwenden.

  Beim Exportieren der Vorlage können Sie auswählen, welche Ressourcen Sie in die Vorlage einschließen möchten.

* **Kann ich eine Ressourcengruppe in einer Resource Manager-Vorlage erstellen und Ressourcen darin bereitstellen?**

  Ja, Sie können eine Ressourcengruppe in einer Vorlage erstellen, wenn Sie die Vorlage auf der Ebene Ihres Azure-Abonnements bereitstellen. Ein Beispiel für das Erstellen einer Ressourcengruppe und das Bereitstellen von Ressourcen finden Sie unter [Ressourcengruppen und Ressourcen](deploy-to-subscription.md#resource-groups).

* **Kann ich ein Abonnement in einer Resource Manager-Vorlage erstellen?**

  Ja. Weitere Informationen finden Sie unter [Programmgesteuertes Erstellen von Azure-Abonnements mit den neuesten APIs](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md).

* **Wie kann ich meine Vorlage vor der Bereitstellung testen?**

  Es wird empfohlen, das [ARM-Testtoolkit](https://github.com/azure/arm-ttk) und den [Was-wäre-wenn-Vorgang](template-deploy-what-if.md) für Ihre Vorlagen auszuführen, bevor Sie sie bereitstellen. Mit dem Testtoolkit wird überprüft, ob in der Vorlage die empfohlenen Vorgehensweisen verwendet werden. Sie erhalten eine Warnung, wenn Änderungen erkannt werden, die vor der Implementierung Ihrer Vorlage noch optimiert werden könnten.

  Mit dem Was-wäre-wenn-Vorgang werden die Änderungen gezeigt, die die Vorlage an Ihrer Umgebung vornimmt. So können Sie unbeabsichtigte Änderungen sehen, bevor sie bereitgestellt werden. Der Was-wäre-wenn-Vorgang gibt außerdem alle Fehler zurück, die während der Preflightvalidierung erkannt werden. Wenn Ihre Vorlage beispielsweise einen syntaktischen Fehler enthält, wird dieser Fehler zurückgegeben. Außerdem werden alle Fehler zurückgegeben, die im Bezug auf den endgültigen Zustand der bereitgestellten Ressourcen erkannt werden können. Wenn mit Ihrer Vorlage beispielsweise ein Speicherkonto mit einem Namen bereitgestellt wird, der bereits verwendet wird, gibt der Was-wäre-wenn-Vorgang diesen Fehler zurück.

* **Wo finde ich Informationen zu den Eigenschaften, die für die einzelnen Ressourcentypen verfügbar sind?**

  VS Code bietet IntelliSense zum Arbeiten mit den Ressourceneigenschaften. Eigenschaften und Beschreibungen finden Sie außerdem in der [Vorlagenreferenz](/azure/templates/).

* **Ich möchte mehrere Instanzen eines Ressourcentyps erstellen. Wie erstelle ich einen Iterator in meiner Vorlage?**

  Verwenden Sie das „copy“-Element, um mehr als eine Instanz anzugeben. Sie können „copy“ für [Ressourcen](copy-resources.md), [Eigenschaften](copy-properties.md), [Variablen](copy-variables.md) und [Ausgaben](copy-outputs.md) verwenden.

## <a name="template-language"></a>Vorlagensprache

* **Ich habe gehört, dass Sie an einer neuen Vorlagensprache arbeiten. Wo finde ich weitere Informationen?**

  Informationen zum Anzeigen einer Vorschau der neuen Sprache finden Sie im [Repository zu Project Bicep](https://github.com/Azure/bicep). Wenn Sie in Bezug auf die neue Vorlagensprache auf dem Laufenden bleiben möchten, [melden Sie sich für Benachrichtigungen an](https://aka.ms/armLangUpdates).

* **Soll das Erstellen von Vorlagen in YAML unterstützt werden?**

  Derzeit ist nicht geplant, YAML zu unterstützen. Wir sind der Meinung, dass die neue Vorlagensprache eine Lösung bietet, die benutzerfreundlicher als YAML oder JSON ist.

* **Kann ich nach der Veröffentlichung der neuen Vorlagensprache weiterhin Vorlagen in JSON schreiben?**

  Ja, Sie können weiterhin JSON-Vorlagen verwenden.

* **Werden Sie ein Tool anbieten, um JSON-Vorlagen in die neue Vorlagensprache zu konvertieren?**

  Ja.

## <a name="template-specs"></a>Vorlagenspezifikationen

* **Wie kann ich die Vorschauversion von Vorlagenspezifikationen verwenden?**

  Installieren Sie die aktuelle Version von PowerShell oder der Azure CLI. Verwenden Sie [mindestens Version 5.0.0](/powershell/azure/install-az-ps) von Azure PowerShell. Verwenden Sie [mindestens Version 2.14.2](/cli/azure/install-azure-cli) der Azure CLI.

* **Was ist der Zusammenhang zwischen Vorlagenspezifikationen und Azure Blueprints?**

  Azure Blueprints verwendet Vorlagenspezifikationen bei der Implementierung, indem die `blueprint definition`-Ressource durch eine `template spec`-Ressource ersetzt wird. Wir werden einen Migrationspfad bereitstellen, über den die Blaupausendefinition in eine Vorlagenspezifikation konvertiert werden kann. Die Blaupausendefinitions-APIs werden aber weiterhin unterstützt. Es gibt keine Änderungen an der `blueprint assignment`-Ressource. Blueprints bleibt als Benutzerumgebung zum Erstellen einer verwalteten Umgebung in Azure erhalten.

* **Ersetzen Vorlagenspezifikationen verknüpfte Vorlagen?**

  Nein, aber Vorlagenspezifikationen sind für die Zusammenarbeit mit verknüpften Vorlagen konzipiert. Sie müssen die verknüpfte Vorlage nicht auf einen öffentlich zugänglichen Endpunkt verschieben, bevor Sie die übergeordnete Vorlage bereitstellen. Stattdessen packen Sie die übergeordnete Vorlage und ihre Artefakte beim Erstellen der Vorlagenspezifikation.

* **Können Vorlagenspezifikationen abonnementsübergreifend verwendet werden?**

  Ja, sie können über mehrere Abonnements hinweg verwendet werden, solange der Benutzer über Lesezugriff auf die Vorlagenspezifikation verfügt. Vorlagenspezifikationen können nicht über mehrere Mandanten hinweg verwendet werden.

## <a name="scripts-in-templates"></a>Skripts in Vorlagen

* **Kann ich ein Skript in meine Vorlage einschließen, um Aufgaben auszuführen, die in einer Vorlage nicht möglich sind?**

  Ja, verwenden Sie hierfür [Bereitstellungsskripts](deployment-script-template.md). Sie können Azure PowerShell- oder Azure CLI-Skripts in Ihre Vorlagen einschließen. Dieses Feature befindet sich in der Vorschau.

* **Können weiterhin benutzerdefinierte Skripterweiterungen und DSC-Konfigurationen (Desired State Configuration) verwendet werden?**

  Diese Optionen sind weiterhin verfügbar und wurden nicht geändert. Bereitstellungsskripts sind für die Ausführung von Aktionen konzipiert, die nicht im Zusammenhang mit dem VM-Gast stehen. Wenn Sie ein Skript auf einem Hostbetriebssystem auf einem virtuellen Computer ausführen müssen, ist die benutzerdefinierte Skripterweiterung und/oder DSC eine bessere Wahl. Bereitstellungsskripts bieten jedoch Vorteile, z. B. das Festlegen der Timeoutdauer.

* **Werden Bereitstellungsskripts in Azure Government unterstützt?**

  Ja, Sie können Bereitstellungsskripts in US Gov Arizona und US Gov Virginia verwenden.

## <a name="preview-changes-before-deployment"></a>Vorschau der Änderungen vor der Bereitstellung

* **Kann ich vor dem Bereitstellen einer Vorlage eine Vorschau der Änderungen anzeigen, die vorgenommen werden?**

  Ja, verwenden Sie hierfür die [Was-wäre-wenn-Funktion](template-deploy-what-if.md). Hiermit wird der aktuelle Zustand Ihrer Umgebung ausgewertet und mit dem Zustand nach der Bereitstellung verglichen. Sie können die zusammengefassten Änderungen überprüfen, um sicherzustellen, dass die Vorlage nicht zu unerwarteten Ergebnissen führt.

* **Kann ich die Was-wäre-wenn-Funktion im inkrementellen und im vollständigen Modus verwenden?**

  Ja, beide [Bereitstellungsmodi](deployment-modes.md) werden unterstützt. Ein Beispiel für die Verwendung des inkrementellen Modus finden Sie unter [Ausführen des Was-wäre-wenn-Vorgangs](template-deploy-what-if.md#run-what-if-operation). Ein Beispiel für die Verwendung des vollständigen Modus finden Sie unter [Löschvorgang bestätigen](template-deploy-what-if.md#confirm-deletion).

* **Funktioniert der Was-wäre-wenn-Vorgang mit verknüpften Vorlagen?**

  Ja, hierbei werden der Zustand der übergeordneten Vorlage und ihrer verknüpften Vorlagen auswertet.

* **Kann ich den Was-wäre-wenn-Vorgang in einer Azure-Pipeline verwenden?**

  Ja, Sie können mit dem Was-wäre-wenn-Vorgang überprüfen, ob die Pipeline fortgesetzt werden soll.

* **Wenn ich den Was-wäre-wenn-Vorgang verwende, werden Änderungen bei Eigenschaften angezeigt, die nicht in meiner Vorlage enthalten sind. Muss mit solchen Fehlern gerechnet werden?**

  Wir arbeiten daran, die Fehlerrate zu verringern. Sie können uns bei der Verbesserung helfen, indem Sie Probleme über unser GitHub-Repository melden: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Schnellansicht für Vorlagen

* **Gibt es eine Möglichkeit zum Visualisieren meiner Resource Manager-Vorlage und ihrer Ressourcen?**

  Es gibt eine [von der Community bereitgestellte VS Code-Erweiterung](https://aka.ms/ARMVisualizer), mit der Sie Ihre Resource Manager-Vorlage anzeigen können. Sie zeigt die Ressourcen, die Sie bereitstellen, sowie die Beziehungen zwischen diesen.

* **Kann ich die Vorlagenschnellansicht außerhalb von VS Code verwenden?**

  Die Vorlagenschnellansicht wird im Portal als Vorschauversion angezeigt. Weitere Informationen finden Sie [in dieser kurzen Build-Sitzung](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions).

## <a name="deployment-limits"></a>Bereitstellungslimits

* **Wie viele Ressourcengruppen kann ich in einem Bereitstellungsvorgang bereitstellen?**

  In der Vergangenheit gab es eine Beschränkung auf fünf Ressourcengruppen. Diese wurde vor Kurzem auf 800 Ressourcengruppen erweitert. Weitere Informationen finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](deploy-to-subscription.md).

* **Im Bereitstellungsverlauf wird ein Fehler angezeigt, dass eine Einschränkung auf 800 Bereitstellungen gilt. Wie soll ich vorgehen?**

  Es gibt Änderungen an der Verwaltung des Bereitstellungsverlaufs einer Ressourcengruppe. In der Vergangenheit mussten Sie manuell Bereitstellungen aus dem Verlauf löschen, um diesen Fehler zu vermeiden. Ab Juni 2020 werden Bereitstellungen automatisch aus dem Verlauf gelöscht, wenn Sie den Grenzwert fast erreicht haben. Weitere Informationen finden Sie unter [Automatische Löschungen aus dem Bereitstellungsverlauf](deployment-history-deletions.md).

  Das Löschen einer Bereitstellung aus dem Verlauf wirkt sich nicht auf die bereitgestellten Ressourcen aus.

## <a name="templates-and-devops"></a>Vorlagen und DevOps

* **Kann ich Resource Manager-Vorlagen in Azure Pipelines integrieren?**

  Ja. Eine Erläuterung zur Verwendung von Vorlagen und Pipelines finden Sie unter [Tutorial: Continuous Integration von ARM-Vorlagen mit Azure Pipelines](deployment-tutorial-pipeline.md) und [Integrieren von ARM-Vorlagen in Azure Pipelines](add-template-to-azure-pipelines.md).

* **Kann ich GitHub Actions für die Bereitstellung einer Vorlage verwenden?**

  Ja, siehe [Bereitstellen von ARM-Vorlagen mithilfe von GitHub Actions](deploy-github-actions.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Einführung in Resource Manager-Vorlagen finden Sie unter [Was sind ARM-Vorlagen?](overview.md)
