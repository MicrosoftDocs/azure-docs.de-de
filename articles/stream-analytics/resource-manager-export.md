---
title: Exportieren einer Azure Resource Manager-Vorlage für einen Azure Stream Analytics-Auftrag
description: In diesem Artikel erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage für Ihren Azure Stream Analytics-Auftrag exportieren.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 2afebe61c4b2998692c823e66d8fc73584ec1dc2
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125647"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Exportieren einer Azure Resource Manager-Vorlage für einen Azure Stream Analytics-Auftrag

[Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md) ermöglichen die Implementierung von Infrastruktur als Code. Bei der Vorlage handelt es sich um eine JSON-Datei (JavaScript Object Notation), die die Infrastruktur und die Konfiguration für Ihre Ressourcen definiert. Sie geben die bereitzustellenden Ressourcen und die Eigenschaften für diese Ressourcen an.

Die Azure Resource Manager-Vorlage kann zur erneuten Bereitstellung eines Azure Stream Analytics-Auftrags exportiert werden.

## <a name="open-a-job-in-vs-code"></a>Öffnen eines Auftrags in VS Code

Um eine Vorlage exportieren zu können, müssen Sie zunächst einen vorhandenen Stream Analytics-Auftrag in Visual Studio Code öffnen. 

Wenn Sie einen Auftrag in ein lokales Projekt exportieren möchten, navigieren Sie im Azure-Portal im **Stream Analytics-Explorer** zu dem zu exportierenden Auftrag. Wählen Sie auf der Seite **Abfrage** die Option **In Visual Studio öffnen** aus. Wählen Sie anschließend **Visual Studio Code** aus.

![Öffnen eines Stream Analytics-Auftrags in Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Weitere Informationen zur Verwaltung von Stream Analytics-Aufträgen mit Visual Studio Code finden Sie unter [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags in Visual Studio Code (Vorschauversion)](quick-create-visual-studio-code.md).

## <a name="compile-the-script"></a>Kompilieren des Skripts 

Als Nächstes muss das Auftragsskript als Azure Resource Manager-Vorlage kompiliert werden. Vergewissern Sie sich vor dem Kompilieren des Skripts, dass für den Auftrag mindestens eine Eingabe und eine Ausgabe konfiguriert sind. Sollte keine Eingabe oder Ausgabe konfiguriert sein, müssen diese zuerst konfiguriert werden.

1. Navigieren Sie in Visual Studio Code zur Datei *Transformation.asaql* Ihres Auftrags.

   ![Datei „Transformation.asaql“ in Visual Studio Code](./media/resource-manager-export/transformation-asaql.png)

1. Klicken Sie mit der rechten Maustaste auf die Datei *Transformation.asaql* , und wählen Sie **ASA: Skript kompilieren** aus dem Menü aus.

1. Daraufhin wird im Arbeitsbereich Ihres Stream Analytics-Auftrags ein Ordner namens **Deploy** angezeigt.

1. Sehen Sie sich die Datei *JobTemplate.json* an. Dabei handelt es sich um die für die Bereitstellung verwendete Azure Resource Manager-Vorlage.

## <a name="complete-the-parameters-file"></a>Bearbeiten der Parameterdatei

Bearbeiten Sie als Nächstes die Parameterdatei für die Azure Resource Manager-Vorlage.

1. Öffnen Sie die Datei *JobTemplate.parameters.json* im Ordner **Deploy** des Arbeitsbereichs Ihres Stream Analytics-Auftrags in Visual Studio Code.

1. Wie Sie sehen, sind die Eingabe- und Ausgabeschlüssel NULL. Ersetzen Sie die NULL-Werte durch die tatsächlichen Zugriffsschlüssel für Ihre Eingabe- und Ausgaberessourcen.

1. Speichern Sie die Parameterdatei.

## <a name="deploy-using-templates"></a>Bereitstellen mithilfe von Vorlagen

Ihr Azure Stream Analytics-Auftrag kann nun mithilfe der Azure Resource Manager-Vorlagen bereitgestellt werden, die Sie im vorherigen Abschnitt generiert haben.

Führen Sie in einem PowerShell-Fenster den folgenden Befehl aus. Ersetzen Sie dabei *ResourceGroupName* , *TemplateFile* und *TemplateParameterFile* durch den tatsächlichen Ressourcengruppennamen bzw. durch die vollständigen Dateipfade der Dateien *JobTemplate.json* und *JobTemplate.parameters.json* im Ordner **Deploy** des Arbeitsbereichs Ihres Auftrags.

Sollte Azure PowerShell bei Ihnen nicht konfiguriert sein, führen Sie die Schritte zum [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) aus.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Nächste Schritte

* [Lokales Testen von Azure Stream Analytics-Aufträgen mit Liveeingabe unter Verwendung von Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Erkunden von Azure Stream Analytics-Aufträgen mit Visual Studio Code (Vorschauversion)](visual-studio-code-explore-jobs.md)