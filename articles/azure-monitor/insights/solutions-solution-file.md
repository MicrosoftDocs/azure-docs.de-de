---
title: Erstellen einer Verwaltungslösungsdatei in Azure | Microsoft-Dokumentation
description: Verwaltungslösungen beinhalten gebündelte Verwaltungsszenarien, die Kunden zu ihrer Azure-Umgebung hinzufügen können.  Diese Artikel beschreibt, wie Sie Verwaltungslösungen erstellen, die Sie in Ihrer Umgebung verwenden oder Ihren Kunden zur Verfügung stellen möchten.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e5c27911fe86a6916235014f8602327df929e20
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595763"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Erstellen einer Verwaltungslösungsdatei in Azure (Vorschau)
> [!NOTE]
> Dies ist die vorläufige Dokumentation für das Erstellen von Verwaltungslösungen in Azure, die sich derzeit in der Vorschau befinden. Jedes unten beschriebene Schema kann sich ändern.  

Verwaltungslösungen in Azure werden als [Resource Manager-Vorlagen](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) implementiert.  Wenn Sie lernen möchten, wie Sie Verwaltungslösungen erstellen, ist der wichtigste Punkt das [Erstellen einer Vorlage](../../azure-resource-manager/resource-group-authoring-templates.md).  In diesem Artikel erhalten Sie wichtige Informationen zu Vorlagen, die für Lösungen verwendet werden, und erfahren, wie Sie typische Lösungsressourcen konfigurieren.


## <a name="tools"></a>Tools

Können Sie einen beliebigen Texteditor zum Arbeiten mit Lösungsdateien verwenden, aber es wird empfohlen, die in Visual Studio oder Visual Studio Code bereitgestellten Funktionen zu nutzen, wie in den folgenden Artikeln beschrieben.

- [Erstellen und Bereitstellen von Azure-Ressourcengruppen mit Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Verwenden von Azure Resource Manager-Vorlagen in Visual Studio Code](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Strukturdefinition
Die grundlegende Struktur einer Verwaltungslösung entspricht einer [Resource Manager-Vorlage](../../azure-resource-manager/resource-group-authoring-templates.md#template-format), die wie folgt aussieht.  Jeder der folgenden Abschnitte beschreibt die Elemente auf oberster Ebene und die zugehörigen Inhalte in einer Lösung.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parameter
[Parameter](../../azure-resource-manager/resource-group-authoring-templates.md#parameters) sind Werte, die Sie von den Benutzern benötigen, wenn diese die Verwaltungslösung installieren.  Es gibt Standardparameter, über die alle Lösungen verfügen werden, und Sie können nach Bedarf zusätzliche Parameter zu Ihrer bestimmten Lösung hinzufügen.  Wie Benutzer die Parameterwerte bei der Installation Ihrer Lösung bereitstellen, hängt vom bestimmten Parameter ab und wie die Lösung installiert wird.

Wenn Benutzer [Ihre Verwaltungslösung installieren](solutions.md#install-a-monitoring-solution) (über Azure Marketplace oder Azure-Schnellstartvorlagen), werden sie aufgefordert, einen [Log Analytics-Arbeitsbereich und ein Automation-Konto](solutions.md#log-analytics-workspace-and-automation-account) auszuwählen.  Diese werden verwendet, um die Werte jedes Standardparameters aufzufüllen.  Der Benutzer wird nicht dazu aufgefordert, direkt Werte für die Standardparameter bereitzustellen, jedoch wird er aufgefordert, Werte für jeden zusätzlichen Parameter bereitzustellen.


Ein Beispielparameter ist unten dargestellt.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

In der folgenden Tabelle werden die Attribute eines Parameters beschrieben.

| Attribut | BESCHREIBUNG |
|:--- |:--- |
| type |Der Datentyp für den Parameter. Das für den Benutzer angezeigte Eingabesteuerelement hängt vom Datentyp ab.<br><br>bool – Dropdownfeld<br>string – Textfeld<br>int – Textfeld<br>securestring – Kennwortfeld<br> |
| category |Optionale Kategorie für den Parameter.  Parameter in der gleichen Kategorie werden gruppiert. |
| control |Zusätzliche Funktionen für Abfrageparameter<br><br>datetime – die Datetime-Steuerung wird angezeigt<br>guid – der GUID-Wert wird automatisch generiert, und er Parameter wird nicht angezeigt. |
| description |Optionale Beschreibung für den Parameter.  Wird in einer Informationssprechblase neben dem Parameter angezeigt. |

### <a name="standard-parameters"></a>Standardparameter
In der folgenden Tabelle werden die Standardparameter für alle Verwaltungslösungen aufgelistet.  Diese Werte werden für den Benutzer aufgefüllt, es gibt keine Aufforderung, wenn Ihre Lösung vom Azure Marketplace oder von Schnellstartvorlagen installiert wird.  Der Benutzer muss Werte für diese bereitstellen, wenn die Lösung mit einer anderen Methode installiert wurde.

> [!NOTE]
> Die Benutzeroberfläche im Azure Marketplace und für Schnellstartvorlagen erwartet in der Tabelle die Namen der Parameter.  Wenn Sie unterschiedliche Parameternamen verwenden, dann wird der Benutzer für diese aufgefordert, und sie werden nicht automatisch aufgefüllt.
>
>

| Parameter | Type | BESCHREIBUNG |
|:--- |:--- |:--- |
| .<Name der Region |Zeichenfolge |Azure Automation-Kontoname |
| pricingTier |Zeichenfolge |Der Tarif für den Log Analytics-Arbeitsbereich und das Azure Automation-Konto |
| regionId |Zeichenfolge |Region des Azure Automation-Kontos |
| solutionName |Zeichenfolge |Name der Lösung  Wenn Sie die Lösung über Schnellstartvorlagen bereitstellen, sollten Sie „solutionName“ als Parameter definieren, damit Sie eine Zeichenfolge definieren können und dies nicht durch den Benutzer erfolgen muss. |
| workspaceName |Zeichenfolge |Name des Log Analytics-Arbeitsbereichs. |
| workspaceRegionId |Zeichenfolge |Region des Azure Log Analytics-Arbeitsbereichs |


Unten stehend finden Sie die Struktur der Standardparameter, die Sie kopieren und in Ihrer Lösungsdatei einfügen können.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Sie verweisen in anderen Elementen der Lösung mit der Syntax **parameters('parameter name')** auf die Parameterwerte.  Um z.B. auf den Namen des Arbeitsbereichs zuzugreifen, verwenden Sie **parameters('workspaceName')** .

## <a name="variables"></a>Variables
[Variablen](../../azure-resource-manager/resource-group-authoring-templates.md#variables) sind Werte, die Sie in der restlichen Verwaltungslösung verwenden.  Diese Werte werden dem Benutzer, der die Lösung installiert, nicht verfügbar gemacht.  Sie stellen dem Ersteller einen zentralen Ort bereit, an dem dieser Werte verwalten kann, die in der Lösung möglicherweise mehrfach verwendet werden. Platzieren Sie beliebige Werte für Ihre Lösung in Variablen anstatt sie im **resources**-Element hartzucodieren.  Dadurch ist der Code besser lesbar und Sie können diese Werte einfach in späteren Versionen ändern.

Im Folgenden finden Sie ein Beispiel eines **variables**-Elements mit typischen Parametern, die in Lösungen verwendet werden.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Sie verweisen in der Lösung mit der Syntax **variables('variable name')** auf Variablenwerte.  Um beispielsweise auf die SolutionName-Variable zuzugreifen, verwenden Sie **variables('SolutionName')** .

Sie können auch komplexe Variablen mit mehreren Gruppen von Werten definieren.  Diese sind in Verwaltungslösungen besonders nützlich, wenn Sie mehrere Eigenschaften für verschiedene Arten von Ressourcen definieren.  Sie könnten beispielsweise die oben aufgeführten Lösungsvariablen die folgendermaßen umstrukturieren.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Sie verweisen in diesem Fall in der Lösung mit der Syntax **variables('variable name').property** auf Variablenwerte.  Um beispielsweise auf die SolutionName-Variable zuzugreifen, verwenden Sie **variables('Solution').Name**.

## <a name="resources"></a>Ressourcen
[Ressourcen](../../azure-resource-manager/resource-group-authoring-templates.md#resources) definieren die verschiedenen Ressourcen, die von Ihrer Verwaltungslösung installiert und konfiguriert werden.  Dies ist der größte und komplexeste Teil der Vorlage.  Sie erhalten die Struktur und die vollständige Beschreibung der Ressourcenelemente unter [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/resource-group-authoring-templates.md#resources).  Verschiedene Ressourcen, die Sie in der Regel definieren, werden in anderen Artikeln in dieser Dokumentation beschrieben. 


### <a name="dependencies"></a>Abhängigkeiten
Das **dependsOn**-Element gibt eine [Abhängigkeit](../../azure-resource-manager/resource-group-define-dependencies.md) von einer anderen Ressource an.  Wenn die Lösung installiert ist, wird keine Ressource erstellt, bis nicht alle Abhängigkeiten erstellt wurden.  Beispielsweise kann Ihre Lösung möglicherweise [ein Runbook starten](solutions-resources-automation.md#runbooks), wenn sie mithilfe einer [Auftragsressource](solutions-resources-automation.md#automation-jobs) erstellt wurde.  Die Auftragsressource würde dann von der Runbookressource abhängig sein, um sicherzustellen, dass das Runbook erstellt wird, bevor der Auftrag erstellt wird.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-Arbeitsbereich und Automation-Konto
Verwaltungslösungen erfordern einen [Log Analytics-Arbeitsbereich](../../azure-monitor/platform/manage-access.md) für Ansichten und ein [Automation-Konto](../../automation/automation-security-overview.md#automation-account-overview) für Runbooks und zugehörige Ressourcen.  Diese müssen verfügbar sein, bevor die Ressourcen in der Lösung erstellt werden und sie sollten nicht in der Lösung selbst definiert werden.  Der Benutzer wird [einen Arbeitsbereich und ein Konto angeben](solutions.md#log-analytics-workspace-and-automation-account), wenn dieser Ihre Lösung bereitstellt. Als Autor sollten Sie aber die nachstehenden Punkte beachten.


## <a name="solution-resource"></a>Lösungsressource
Jede Lösung erfordert einen Ressourceneintrag im Element **resources**, der die Lösung selbst definiert.  Der Eintrag weist den Typ **Microsoft.OperationsManagement/solutions** und die folgende Struktur auf. Dies schließt [Standardparameter](#parameters) und [Variablen](#variables) ein, die in der Regel verwendet werden, um Eigenschaften der Lösung zu definieren.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Abhängigkeiten
Die Lösungsressource muss über eine [Abhängigkeit](../../azure-resource-manager/resource-group-define-dependencies.md) auf jeder anderen Ressource in der Lösung verfügen, da sie vorhanden sein müssen, bevor die Lösung erstellt werden kann.  Hierzu fügen Sie im Element **dependsOn** einen Eintrag für jede Ressource hinzu.

### <a name="properties"></a>Eigenschaften
Die Lösungsressource weist die Eigenschaften in der folgenden Tabelle auf.  Dies umfasst die Ressourcen, die in der Lösung enthalten sind oder von dieser referenziert werden. Dadurch wird definiert, wie die Ressource nach Installation der Lösung verwaltet wird.  Jede Ressource in der Lösung muss entweder in der Eigenschaft **referencedResources** oder in der Eigenschaft **containedResources** aufgelistet werden.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| workspaceResourceId |ID des Log Analytics-Arbeitsbereichs im Format *\<Ressourcengruppen-ID>/providers/Microsoft.OperationalInsights/workspaces/\<Workspace Name\>* . |
| referencedResources |Liste der Ressourcen in der Lösung, die nicht entfernt werden sollen, wenn die Lösung entfernt wird. |
| containedResources |Liste der Ressourcen in der Lösung, die entfernt werden sollen, wenn die Lösung entfernt wird. |

Das obige Beispiel ist für eine Lösung mit einem Runbook, einem Zeitplan und einer Anzeige vorgesehen.  Zeitplan und Runbook werden in im Element **properties** (Eigenschaften) referenziert (*referenced*) und daher beim Entfernen der Lösung nicht entfernt.  Die Ansicht ist enthalten (*contained*) und wird daher beim Entfernen der Lösung ebenfalls entfernt.

### <a name="plan"></a>Plan
Die Entität **Plan** der Lösungsressource weist die Eigenschaften in der folgenden Tabelle auf.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| name |Name der Lösung |
| Version |Die Version der Lösung, wie vom Autor festgelegt. |
| product |Eindeutige Zeichenfolge zum Identifizieren der Lösung. |
| Herausgeber |Herausgeber der Lösung |



## <a name="next-steps"></a>Nächste Schritte
* [Hinzufügen gespeicherter Suchvorgänge und Warnungen](solutions-resources-searches-alerts.md) zu Ihrer Verwaltungslösung
* [Hinzufügen von Ansichten](solutions-resources-views.md) zu Ihrer Verwaltungslösung
* [Hinzufügen von Runbooks und anderen Automation-Ressourcen](solutions-resources-automation.md) zu Ihrer Verwaltungslösung
* Erfahren Sie Näheres zum [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/resource-group-authoring-templates.md).
* Durchsuchen Sie die [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates), um Beispiele für verschiedene Resource Manager-Vorlagen zu finden.
