---
title: Bereitstellen geschachtelter Resource Manager-Vorlagenumgebungen in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie geschachtelte Azure Resource Manager-Vorlagen bereitstellen, um Umgebungen mit Azure DevTest Labs bereitzustellen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835252"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Bereitstellen geschachtelter Azure Resource Manager-Vorlagen für Testumgebungen
Mithilfe einer geschachtelten Bereitstellung können Sie andere Azure Resource Manager-Vorlagen der wichtigsten Resource Manager-Vorlage ausführen. Sie können die Bereitstellung in mehrere gezielte und zweckgebundene Vorlagen zerlegen. Dies bietet Vorteile für das Testen, die Wiederverwendung und die Lesbarkeit. Der Artikel [Verwenden verknüpfter Vorlagen bei der Bereitstellung von Azure-Ressourcen](../azure-resource-manager/resource-group-linked-templates.md) bietet einen guten Überblick über diese Projektmappe mit mehreren Beispielcodes. Dieser Artikel enthält ein Beispiel, das für Azure DevTest Labs gilt. 

## <a name="key-parameters"></a>Schlüsselparameter
Zwar können Sie Ihre eigene Resource Manager-Vorlage von Grund auf neu erstellen, allerdings empfehlen wir Ihnen, das [Azure-Ressourcengruppenprojekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) in Visual Studio zu verwenden, das das Entwickeln und Debuggen von Vorlagen vereinfacht. Wenn Sie „azuredeploy.json“ eine geschachtelte Bereitstellungsressource hinzufügen, fügt Visual Studio einige Elemente hinzu, um die Vorlage flexibler zu gestalten. Diese Elemente enthalten den Unterordner mit der zweiten Vorlagen- und Parameterdatei, dem Variablennamen in der Hauptvorlagendatei und zwei Parametern für den Speicherort der neuen Datei. **_artifactsLocation** und **_artifactsLocationSasToken** sind die von DevTest Labs verwendeten Schlüsselparameter. 

Wenn Sie nicht mit der Funktionsweise von DevTest Labs mit Umgebungen vertraut sind, finden Sie weitere Informationen unter [Create multi-VM environments and PaaS resources with Azure Resource Manager templates (Erstellen mehrerer VM-Umgebungen und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen)](devtest-lab-create-environment-from-arm.md). Die Vorlagen werden in dem mit dem Lab in DevTest Labs verknüpften Repository gespeichert. Wenn Sie mit diesen Vorlagen eine neue Umgebung erstellen, werden die Dateien in einen Azure Storage-Container im Lab verschoben. DevTest Labs identifiziert die Parameter „_artifactsLocation“ und „_artifactsLocationSasToken“ und kopiert die Unterordner in den Speichercontainer, um die geschachtelten Dateien zu identifizieren und zu kopieren. Anschließend wird das Speicherort- und Shared Access Signature-Token (SaS) automatisch in die Parameter eingefügt. 

## <a name="nested-deployment-example"></a>Beispiel für die geschachtelte Bereitstellung
Hier finden Sie ein einfaches Beispiel für eine geschachtelte Bereitstellung:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

Der Ordner im Repository, der diese Vorlage enthält, hat einen Unterordner (`nestedtemplates`) mit den Dateien **NestOne.json** und **NestOne.parameters.json**. In der **azuredeploy.json**-Vorlage wird der URI für die Vorlage mithilfe des Artefaktspeicherorts, dem geschachtelten Vorlagenordner und der geschachtelten Dateinamenvorlage erstellt. Auf ähnliche Weise wird der URI für diese Parameter mithilfe des Artefaktspeicherorts, dem geschachtelten Vorlagenordner und der Parameterdatei für die geschachtelte Vorlage erstellt. 

So sieht die gleiche Projektstruktur in Visual Studio aus: 

![Projektstruktur in Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Sie können im ersten Ordner zwar zusätzliche Ordner hinzufügen, allerdings nur eine einzelne Ebene. 

## <a name="next-steps"></a>Nächste Schritte
Die folgenden Artikel enthalten ausführliche Informationen zu Umgebungen: 

- [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md)
- [Konfigurieren und Verwenden von öffentlichen Umgebungen in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Herstellen einer Verbindung zwischen einer Umgebung und dem virtuellen Netzwerk Ihres Labs in Azure DevTest Labs](connect-environment-lab-virtual-network.md)