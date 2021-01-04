---
title: 'Erstellen von Apache Hadoop-Clustern mithilfe von Vorlagen: Azure HDInsight'
description: Erfahren Sie, wie Sie Cluster für HDInsight mit Azure Resource Manager-Vorlagen erstellen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/07/2020
ms.openlocfilehash: aa42e9691eac223e7e72da0d792d14262470e15c
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748769"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Erstellen von Apache Hadoop-Clustern in HDInsight mit Resource Manager-Vorlagen

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

In diesem Artikel lernen Sie verschiedene Möglichkeiten zum Erstellen von Azure HDInsight-Clustern mit [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/deploy-powershell.md). Informationen über weitere Tools und Features zur Clustererstellung finden Sie, indem Sie oben auf dieser Seite auf die Registerkartenauswahl klicken. Siehe auch [Methoden zur Clustererstellung](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Resource Manager-Vorlagen

Eine Resource Manager-Vorlage vereinfacht das Erstellen der folgenden Ressourcen für Ihre Anwendung in einem einzelnen koordinierten Vorgang:

* HDInsight-Cluster und ihre abhängigen Ressourcen (z.B. das Standardspeicherkonto).
* Andere Ressourcen (z.B. Azure SQL-Datenbank zur Verwendung von [Apache Sqoop](https://sqoop.apache.org/)).

In der Vorlage definieren Sie die Ressourcen, die für die Anwendung erforderlich sind. Sie geben außerdem die Bereitstellungsparameter zur Eingabe von Werten für unterschiedliche Umgebungen an. Die Vorlage besteht aus JSON-Code und Ausdrücken, mit denen Sie Werte für Ihre Bereitstellung erstellen.

Sie finden Beispiele für HDInsight-Vorlagen unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?term=hdinsight). Verwenden Sie den plattformübergreifenden [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) mit der [Resource Manager-Erweiterung](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) oder einem Text-Editor, um die Vorlage in einer Datei auf Ihrer Arbeitsstation zu speichern.

Weitere Informationen über Resource Manager-Vorlagen finden Sie in den folgenden Artikeln und Beispielen:

* [Erstellen von Azure Resource-Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md)
* [Bereitstellen einer Anwendung mit Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)-Vorlagenreferenz
* [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generieren von Vorlagen

Mit Resource Manager können Sie eine Resource Manager-Vorlage aus vorhandenen Ressourcen in Ihrem Abonnement mithilfe unterschiedlicher Tools exportieren. Auf der Grundlage dieser generierten Vorlage können Sie sich über die Vorlagensyntax informieren oder ggf. die erneute Bereitstellung Ihrer Lösung automatisieren. Weitere Informationen finden Sie unter [Exportieren von Vorlagen](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Bereitstellen mit dem Portal

Sie können eine Resource Manager-Vorlage über das Azure-Portal bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Bereitstellen mit PowerShell

Sie können eine Resource Manager-Vorlage mithilfe von Azure PowerShell bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) und [Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

Sie können eine Resource Manager-Vorlage mit der Azure-Befehlszeilenschnittstelle bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/templates/deploy-cli.md) und [Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Bereitstellen mit der REST-API

Sie können eine Resource Manager-Vorlage mit der REST-API bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Resource Manager-REST-API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Bereitstellen mit Visual Studio 2013

 Verwenden Sie Visual Studio, um ein Ressourcengruppenprojekt zu erstellen und über die Benutzeroberfläche in Azure bereitzustellen. Sie wählen den Typ der Ressourcen aus, die in Ihr Projekt einbezogen werden sollen. Diese Ressourcen werden automatisch der Resource Manager-Vorlage hinzugefügt. Das Projekt enthält auch ein PowerShell-Skript zum Bereitstellen der Vorlage.

Eine Einführung in die Verwendung von Visual Studio mit Ressourcengruppen finden Sie unter [Erstellen und Bereitstellen von Azure-Ressourcengruppen über Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](hdinsight-hadoop-customize-cluster-linux.md#access-control) an.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehrere Möglichkeiten zum Erstellen von HDInsight-Clustern kennengelernt. Weitere Informationen erhalten Sie in den folgenden Artikeln:

* Weitere HDInsight-bezogene Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Ein Beispiel für die Bereitstellung von Ressourcen über die .NET-Clientbibliothek finden Sie unter [Bereitstellen von Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Ein ausführliches Beispiel für die Bereitstellung einer Anwendung finden Sie unter [Vorhersagbares Bereitstellen von Microservices in Azure](../app-service/deploy-complex-application-predictably.md).
* Informationen zum Bereitstellen der Lösung in andere Umgebungen finden Sie unter [Entwicklungs- und Testumgebungen in Microsoft Azure](../devtest-labs/devtest-lab-overview.md).
* Informationen zu den Abschnitten der Azure Resource Manager-Vorlage finden Sie unter [Erstellen von Vorlagen](../azure-resource-manager/templates/template-syntax.md).
* Unter [Vorlagenfunktionen](../azure-resource-manager/templates/template-functions.md)finden Sie eine Liste der Funktionen, die Sie in einer Azure Resource Manager-Vorlage verwenden können.
