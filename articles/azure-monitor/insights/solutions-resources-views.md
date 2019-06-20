---
title: Ansichten in Verwaltungslösungen | Microsoft-Dokumentation
description: 'Verwaltungslösungen enthalten in der Regel eine oder mehrere Ansichten zum Visualisieren von Daten.  Dieser Artikel beschreibt die Vorgehensweise beim Exportieren einer Ansicht, die vom Ansicht-Designer erstellt und in einer Verwaltungslösung eingeschlossen wurde. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: cefb83d5336bb99fd09001b5ea369a0b8fc4b942
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60596627"
---
# <a name="views-in-management-solutions-preview"></a>Ansichten in Verwaltungslösungen (Vorschau)
> [!NOTE]
> Dies ist die vorläufige Dokumentation für das Erstellen von Verwaltungslösungen, die sich derzeit in der Vorschau befinden. Jedes unten beschriebene Schema kann sich ändern.    


[Verwaltungslösungen](solutions.md) enthalten in der Regel eine oder mehrere Ansichten zum Visualisieren von Daten.  Dieser Artikel beschreibt die Vorgehensweise beim Exportieren einer Ansicht, die vom [Ansicht-Designer](../../azure-monitor/platform/view-designer.md) erstellt und in einer Verwaltungslösung eingeschlossen wurde.  

> [!NOTE]
> Die Beispiele in diesem Artikel verwenden Parameter und Variablen, die entweder erforderlich sind oder für Verwaltungslösungen gelten und unter [Entwerfen und Erstellen einer Verwaltungslösung in Azure](solutions-creating.md) beschrieben sind.
>
>

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie schon mit der [Erstellung einer Verwaltungslösung](solutions-creating.md) und der Struktur einer Lösungsdatei vertraut sind.

## <a name="overview"></a>Übersicht
Um eine Ansicht in eine Verwaltungslösung aufzunehmen, erstellen Sie dafür eine **Ressource** in der [Lösungsdatei](solutions-creating.md).  Die JSON, die die detaillierte Konfiguration der Ansicht beschreibt, ist normalerweise sehr komplex und nicht etwas, das ein typischer Lösungsautor manuell erstellen könnte.  Die am häufigsten verwendete Methode ist die Erstellung der Ansicht mit den [Ansicht-Designer](../../azure-monitor/platform/view-designer.md), diese zu exportieren, und dann deren ausführliche Konfiguration der Lösung hinzuzufügen.

Die grundlegenden Schritte zum Hinzufügen einer Ansicht zu einer Lösung sind wie folgt.  Jeder Schritt wird in den nachstehenden Abschnitten ausführlicher beschrieben.

1. Exportieren der Ansicht in eine Datei
2. Erstellen der Ansichtsressource in der Lösung
3. Hinzufügen der Anzeigedetails

## <a name="export-the-view-to-a-file"></a>Exportieren der Ansicht in eine Datei
Führen Sie die Schritte unter [Log Analytics View Designer (Log Analytics-Ansicht-Designer)](../../azure-monitor/platform/view-designer.md), um eine Ansicht in eine Datei zu exportieren.  Die exportierte Datei wird im JSON-Format mit den gleichen [Elementen wie die der Lösungsdatei](solutions-solution-file.md) dargestellt.  

Das Element **resources** (Ressourcen) der Ansichtsdatei erhält eine Ressource mit einem Typ **Microsoft.OperationalInsights/workspaces**, der den Log Analytics-Arbeitsbereich darstellt.  Dieses Element erhält ein Unterelement mit einem Typ **views** (Ansichten), der die Ansicht darstellt und die ausführliche Konfiguration enthält.  Kopieren Sie die Details dieses Elements, und kopieren Sie diese dann in Ihre Lösung.

## <a name="create-the-view-resource-in-the-solution"></a>Erstellen der Ansichtsressource in der Lösung
Fügen Sie die folgende Ansichtsressource zum Element **resources** (Ressourcen) Ihrer Lösungsdatei.  Diese verwendet Variablen, die unten beschrieben sind, die Sie ebenfalls hinzufügen müssen.  Beachten Sie, dass die **Dashboard**- und **OverviewTile**-Eigenschaften Platzhalter sind, die Sie mit den entsprechenden Eigenschaften der exportierten Ansichtsdatei überschreiben.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Fügen Sie die folgenden Variablen dem variables-Element der Projektmappendatei hinzu, und ersetzen Sie die Werte durch die für Ihre Lösung.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Beachten Sie, dass Sie die gesamte Ansichtsressource aus Ihrer exportierten Ansichtsdatei kopieren können, Sie müssen jedoch die folgenden Änderungen durchführen, damit dies in Ihrer Lösung funktioniert.  

* Der **Typ** für die Ansichtsressource muss von **views** (Ansichten) auf **Microsoft.OperationalInsights/workspaces** geändert werden.
* Die Eigenschaft **name** (Name) für die Ansichtsressource muss geändert werden, um den Namen des Arbeitsbereichs einzuschließen.
* Die Abhängigkeit auf dem Arbeitsbereich muss entfernt werden, da die Arbeitsbereichsressource nicht in der Lösung definiert ist.
* Die Eigenschaft **DisplayName** muss der Ansicht hinzugefügt werden.  Die Eigenschaften **id**, **name** und **DisplayName** müssen gleich sein.
* Parameternamen müssen geändert werden, damit sie den erforderlichen Parametern entsprechen.
* Variablen sollten in der Lösung definiert und in den entsprechenden Eigenschaften verwendet werden.

### <a name="log-analytics-api-version"></a>Log Analytics-API-Version
Alle in einer Resource Manager-Vorlage definierten Log Analytics-Ressourcen verfügen über die Eigenschaft **apiVersion**, die die Version der API definiert, die von der Ressource verwendet werden soll.  Diese Version unterscheidet sich für Ansichten mit Abfragen, die die [ältere und die aktualisierte Abfragesprache](../../azure-monitor/log-query/log-query-overview.md) verwenden.  

 In der folgenden Tabelle werden die Log Analytics-API-Versionen für Ansichten in älteren und aktualisierten Arbeitsbereichen angegeben: 

| Arbeitsbereichsversion | API-Version | Abfragen |
|:---|:---|:---|
| V1 (Legacy)   | 2015-11-01-preview | Altes Format.<br> Beispiel: Type=Event EventLevelName = Error  |
| V2 (Upgrade) | 2015-11-01-preview | Altes Format.  Bei der Installation in das aktualisierte Format konvertiert.<br> Beispiel: Type=Event EventLevelName = Error<br>Konvertiert in: Event &#124; where EventLevelName == "Error"  |
| V2 (Upgrade) | 2017-03-03-preview | Aktualisiertes Format. <br>Beispiel: Event &#124; where EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>Hinzufügen der Ansichtsdetails
Die Ansichtsressource in der exportierten Ansichtsdatei wird zwei Elemente im Element **properties** (Eigenschaften) namens **Dashboard** und **OverviewTile** enthalten, die die ausführliche Konfiguration der Ansicht enthalten.  Kopieren Sie diese beiden Elemente und deren Inhalte in das **properties**-Element der Ansichtsressource in Ihrer Lösungsdatei.

## <a name="example"></a>Beispiel
Im folgenden Beispiel wird z.B. eine einfache Lösungsdatei mit einer Ansicht gezeigt.  Aus Platzgründen werden Auslassungspunkte (...) für die Inhalte der Elemente **Dashboard** und **OverviewTile** angezeigt.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie ausführlich, wie Sie [Verwaltungslösungen](solutions-creating.md) erstellen.
* Einschließen von [Automations-Runbooks in Ihrer Verwaltungslösung](solutions-resources-automation.md).
