---
title: Erstellen eines Log Analytics-Arbeitsbereichs mithilfe der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle einen Log Analytics-Arbeitsbereich erstellen, um Verwaltungslösungen und die Datensammlung in Ihren cloudbasierten und lokalen Umgebungen zu aktivieren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 54d1d8a29c87f8d129c0ea5b29973c4fef0e6f7a
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888996"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Erstellen eines Log Analytics-Arbeitsbereichs mit Azure CLI 2.0

Azure CLI 2.0 dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In diesem Schnellstart wird gezeigt, wie Sie über Azure CLI 2.0 einen Log Analytics-Arbeitsbereich in Azure Monitor bereitstellen. Ein Log Analytics-Arbeitsbereich ist eine spezifische Umgebung für Azure Monitor-Protokolldaten. Jeder Arbeitsbereich verfügt über ein eigenes Datenrepository und eine eigene Konfiguration. Datenquellen und Lösungen sind so konfiguriert, dass die zugehörigen Daten in einem bestimmten Arbeitsbereich gespeichert werden. Ein Log Analytics-Arbeitsbereich ist erforderlich, wenn Sie Daten aus den folgenden Quellen erfassen möchten:

* Azure-Ressourcen im Abonnement  
* Lokale Computer, die von System Center Operations Manager überwacht werden  
* Gerätesammlungen aus Configuration Manager  
* Diagnose- oder Protokolldaten aus dem Azure-Speicher  

Informationen zu anderen Quellen in Ihrer Umgebung (etwa virtuelle Azure-Computer und virtuelle Windows- oder Linux-Computer) finden Sie in folgenden Themen:

* [Sammeln von Daten von virtuellen Azure-Computern](./quick-collect-azurevm.md)
* [Sammeln von Daten von Linux-Hybridcomputern](./quick-collect-linux-computer.md)
* [Sammeln von Daten von Windows-Hybridcomputern](quick-collect-windows-computer.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.30 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs
Erstellen Sie mit [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) einen Arbeitsbereich. Das folgende Beispiel erstellt einen Arbeitsbereich am Standort *eastus* (USA, Osten) mithilfe einer Resource Manager-Vorlage von Ihrem lokalen Computer. Die JSON-Vorlage ist so konfiguriert, dass Sie nur zur Eingabe des Namens für den Arbeitsbereich aufgefordert werden und Standardwerte für die anderen Parameter angegeben werden, die wahrscheinlich als Standardkonfiguration in Ihrer Umgebung verwendet werden. Für den gemeinsamen Zugriff in Ihrer Organisation können Sie die Vorlage auch in einem Azure Storage-Konto speichern. Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und der Azure-Befehlszeilenschnittstelle](../../azure-resource-manager/templates/deploy-cli.md).

Weitere Informationen zu den unterstützten Regionen finden Sie auf der Seite zur [Verfügbarkeit von Log Analytics in den einzelnen Regionen](https://azure.microsoft.com/regions/services/), indem Sie im Feld **Nach einem Produkt suchen** nach Azure Monitor suchen.

Für folgende Parameter wird ein Standardwert festgelegt:

* Standort: Als Standardwert wird „USA, Osten“ verwendet.
* SKU: Als Standardwert wird der neue, im Preismodell von April 2018 veröffentlichte Tarif pro GB verwendet.

>[!WARNING]
>Wenn Sie einen Log Analytics-Arbeitsbereich in einem Abonnement mit dem neuen Preismodell von April 2018 erstellen oder konfigurieren, ist **PerGB2018** als einziger gültiger Log Analytics-Tarif verfügbar.
>

### <a name="create-and-deploy-template"></a>Erstellen und Bereitstellen der Vorlage

1. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in Ihre Datei ein:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Bearbeiten Sie die Vorlage entsprechend Ihren Anforderungen. Informationen zu den unterstützten Eigenschaften und Werten finden Sie in der Referenz [Microsoft.OperationalInsights/workspaces template](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces).
3. Speichern Sie diese Datei unter dem Namen **deploylaworkspacetemplate.json** in einem lokalen Ordner.   
4. Nun können Sie die Vorlage bereitstellen. Führen Sie die folgenden Befehle in dem Ordner mit der Vorlage aus. Wenn Sie zur Eingabe eines Arbeitsbereichsnamens aufgefordert werden, geben Sie einen Namen an, der in allen Azure-Abonnements global eindeutig ist.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Die Bereitstellung kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung ähnlich der folgenden mit dem Ergebnis angezeigt:

![Beispielergebnis nach abgeschlossener Bereitstellung](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>Problembehandlung
Wenn Sie einen Arbeitsbereich erstellen, der in den letzten 14 Tagen gelöscht wurde und sich im [Zustand des vorläufigen Löschens](../platform/delete-workspace.md#soft-delete-behavior) befindet, kann der Vorgang je nach Arbeitsbereichskonfiguration ein anderes Ergebnis aufweisen:
1. Wenn Sie denselben Arbeitsbereichsnamen, dieselbe Ressourcengruppe, dasselbe Abonnement und dieselbe Region wie beim gelöschten Arbeitsbereich angeben, wird der Arbeitsbereich einschließlich Daten, Konfiguration und verbundener Agents wiederhergestellt.
2. Wenn Sie denselben Arbeitsbereichsnamen, aber andere Werte für Ressourcengruppe, Abonnement oder Region verwenden, erhalten Sie eine Fehlermeldung des Typs *Der Arbeitsbereichsname „Arbeitsbereichname“ ist nicht eindeutig* oder *Konflikt*. Wenn Sie das vorläufige Löschen außer Kraft setzen, den Arbeitsbereich dauerhaft löschen und einen neuen, gleichnamigen Arbeitsbereich erstellen möchten, gehen Sie folgendermaßen vor, um den Arbeitsbereich zunächst wiederherzustellen und dann dauerhaft zu löschen:
   * [Wiederherstellen](../platform/delete-workspace.md#recover-workspace) Ihres Arbeitsbereichs
   * [Dauerhaftes Löschen](../platform/delete-workspace.md#permanent-workspace-delete) Ihres Arbeitsbereichs
   * Erstellen eines neuen Arbeitsbereichs mit demselben Arbeitsbereichsnamen

## <a name="next-steps"></a>Nächste Schritte
Sie besitzen einen verfügbaren Arbeitsbereich und können nun die Erfassung von Überwachungstelemetrie konfigurieren, Protokollsuchen zum Analysieren dieser Daten ausführen und eine Verwaltungslösung hinzufügen, um weitere daten- und analysebasierte Einblicke zu gewinnen.  

* Informationen zum Aktivieren der Datensammlung aus Azure-Ressourcen mit Azure-Diagnose oder Azure Storage finden Sie unter [Sammeln von Azure-Dienstprotokollen und Metriken zur Verwendung in Log Analytics](../platform/resource-logs.md#send-to-log-analytics-workspace).  
* [Fügen Sie System Center Operations Manager als Datenquelle hinzu](../platform/om-agents.md), um Daten von Agents zu sammeln, die an die Operations Manager-Verwaltungsgruppe berichten, und sie im Log Analytics-Arbeitsbereich zu speichern.  
* Stellen Sie eine Verbindung mit [Configuration Manager](../platform/collect-sccm.md) her, um Computer zu importieren, die Sammlungen in der Hierarchie angehören.  
* Sehen Sie sich die verfügbaren [Überwachungslösungen](../insights/solutions.md) sowie Informationen dazu an, wie Sie eine Lösung Ihrem Arbeitsbereich hinzufügen bzw. daraus entfernen.

