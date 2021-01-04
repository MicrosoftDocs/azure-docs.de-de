---
title: Exportieren einer Vorlage im Azure-Portal
description: Verwenden Sie das Azure-Portal, um eine Azure Resource Manager-Vorlage aus Ressourcen in Ihrem Abonnement zu exportieren.
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: ee97953a337bbb7cc9a8d1f042a3beae7bccdcae
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185690"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal

Um bei der Erstellung von Azure Resource Manager-Vorlagen zu helfen, können Sie eine Vorlage aus vorhandenen Ressourcen exportieren. Die exportierte Vorlage hilft Ihnen, die JSON-Syntax und die Eigenschaften zu verstehen, die Ihre Ressourcen bereitstellen. Zum Automatisieren zukünftiger Bereitstellungen beginnen Sie mit der exportierten Vorlage und passen Sie sie an Ihr Szenario an.

Mit Resource Manager können Sie eine oder mehrere Ressourcen für den Export in eine Vorlage auswählen. Sie können sich auf genau die Ressourcen konzentrieren, die in der Vorlage erforderlich sind.

In diesem Artikel wird das Exportieren von Vorlagen über das Portal veranschaulicht. Sie können auch die [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) oder die [REST-API](/rest/api/resources/resourcegroups/exporttemplate) verwenden.

## <a name="choose-the-right-export-option"></a>Auswählen der richtigen Exportoption

Eine Vorlage kann auf zwei Arten exportiert werden:

* **Export aus Ressourcengruppe oder Ressource**. Diese Option generiert eine neue Vorlage aus vorhandenen Ressourcen. Die exportierte Vorlage ist eine „Momentaufnahme“ des aktuellen Zustands der Ressourcengruppe. Sie können eine gesamte Ressourcengruppe oder bestimmte Ressourcen innerhalb dieser Ressourcengruppe exportieren.

* **Export vor der Bereitstellung oder über den Verlauf**. Diese Option ruft eine exakte Kopie einer für die Bereitstellung verwendeten Vorlage ab.

Je nach gewählter Option haben die exportierten Vorlagen unterschiedliche Qualitäten.

| Aus Ressourcengruppe oder Ressource | Vor der Bereitstellung oder über den Verlauf |
| --------------------- | ----------------- |
| Die Vorlage ist eine Momentaufnahme des aktuellen Zustands der Ressourcen. Sie umfasst alle manuellen Änderungen, die Sie nach der Bereitstellung vorgenommen haben. | Die Vorlage zeigt nur den Zustand der Ressourcen zum Zeitpunkt der Bereitstellung an. Alle manuellen Änderungen, die Sie nach der Bereitstellung vorgenommen haben, sind nicht enthalten. |
| Sie können auswählen, welche Ressourcen aus einer Ressourcengruppe exportiert werden sollen. | Alle Ressourcen für eine bestimmte Bereitstellung sind enthalten. Sie können keine Teilmenge dieser Ressourcen auswählen oder Ressourcen hinzufügen, die zu einem anderen Zeitpunkt hinzugefügt wurden. |
| Die Vorlage enthält alle Eigenschaften für die Ressourcen, einschließlich einiger Eigenschaften, die Sie normalerweise während der Bereitstellung nicht festlegen würden. Möglicherweise möchten Sie diese Eigenschaften entfernen oder bereinigen, bevor Sie die Vorlage wiederverwenden. | Die Vorlage enthält nur die für die Bereitstellung erforderlichen Eigenschaften. Die Vorlage ist einsatzbereit. |
| Die Vorlage enthält möglicherweise nicht alle für die Wiederverwendung erforderlichen Parameter. Die meisten Eigenschaftswerte sind in der Vorlage hartcodiert. Um die Vorlage in anderen Umgebungen neu bereitzustellen, müssen Sie Parameter hinzufügen, die die Möglichkeit zur Konfiguration der Ressourcen erhöhen.  Sie können die Auswahl von **Parameter einschließen** auch aufheben, sodass Sie Ihre eigenen Parameter erstellen können. | Die Vorlage enthält Parameter, die es einfach gestalten, sie in verschiedenen Umgebungen neu bereitzustellen. |

Exportieren Sie die Vorlage aus einer Ressourcengruppe oder Ressource, wenn Folgendes zutrifft:

* Sie müssen Änderungen an den Ressourcen erfassen, die nach der ursprünglichen Bereitstellung vorgenommen wurden.
* Sie möchten die zu exportierenden Ressourcen auswählen.

Exportieren Sie die Vorlage vor der Bereitstellung oder über den Verlauf, wenn Folgendes zutrifft:

* Sie benötigen eine einfach wiederzuverwendende Vorlage.
* Sie müssen keine Änderungen einbeziehen, die Sie nach der ursprünglichen Bereitstellung vorgenommen haben.

## <a name="limitations"></a>Einschränkungen

Beim Exportieren aus einer Ressourcengruppe oder Ressource wird die exportierte Vorlage aus den [veröffentlichten Schemas](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) für jeden Ressourcentyp generiert. Gelegentlich weist das Schema nicht die neueste Version für einen Ressourcentyp auf. Überprüfen Sie die exportierte Vorlage, um sicherzustellen, dass sie die benötigten Eigenschaften enthält. Bearbeiten Sie bei Bedarf die exportierte Vorlage so, dass sie die benötigte API-Version verwendet.

Die Funktion „Vorlage exportieren“ unterstützt nicht das Exportieren von Azure Data Factory-Ressourcen. Informationen zum Exportieren von Data Factory-Ressourcen finden Sie unter [Kopieren oder Klonen einer Data Factory in Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Um über das klassische Bereitstellungsmodell erstellte Ressourcen zu exportieren, müssen Sie [sie zum Resource Manager-Bereitstellungsmodell migrieren](../../virtual-machines/migration-classic-resource-manager-overview.md).

Wenn beim Exportieren einer Vorlage eine Warnung angezeigt wird, die angibt, dass ein Ressourcentyp nicht exportiert wurde, können Sie die Eigenschaften für diese Ressource immer noch ermitteln. Weitere Informationen zu den verschiedenen Optionen zum Anzeigen von Ressourceneigenschaften finden Sie unter [Ermitteln von Ressourceneigenschaften](view-resources.md). Sie können den Ressourcentyp auch in der [Azure-REST-API](/rest/api/azure/) nachschlagen.

Die Ressourcengruppe, für die Sie die exportierte Vorlage erstellen, ist auf 200 Ressourcen begrenzt. Wenn Sie versuchen, eine Ressourcengruppe mit mehr als 200 Ressourcen zu exportieren, wird die Fehlermeldung `Export template is not supported for resource groups more than 200 resources` angezeigt.

## <a name="export-template-from-a-resource-group"></a>Exportieren einer Vorlage aus einer Ressourcengruppe

So exportieren Sie eine oder mehrere Ressourcen aus einer Ressourcengruppe

1. Wählen Sie die Ressourcengruppe aus, die die zu exportierenden Ressourcen enthält.

1. Wählen Sie mithilfe der Kontrollkästchen mindestens eine Ressource aus.  Wenn Sie alle Kontrollkästchen aktivieren möchten, aktivieren Sie das Kontrollkästchen links neben **Name**. Das Menüelement **Vorlage exportieren** wird erst aktiviert, wenn Sie mindestens eine Ressource ausgewählt haben.

   ![Exportieren aller Ressourcen](./media/export-template-portal/select-all-resources.png)

    Auf dem Screenshot ist nur das Speicherkonto ausgewählt.
1. Wählen Sie **Vorlage exportieren** aus.

1. Die exportierte Vorlage wird angezeigt und steht zum Herunterladen und Bereitstellen bereit.

   ![Anzeigen der Vorlage](./media/export-template-portal/show-template.png)

   **Parameter einschließen** ist standardmäßig ausgewählt.  Wenn diese Option ausgewählt ist, werden alle Vorlagenparameter eingeschlossen, wenn die Vorlage generiert wird. Wenn Sie Ihre eigenen Parameter erstellen möchten, deaktivieren Sie dieses Kontrollkästchen, um sie nicht einzuschließen.

## <a name="export-template-from-a-resource"></a>Exportieren einer Vorlage aus einer Ressource

So exportieren Sie eine Ressource

1. Wählen Sie die Ressourcengruppe aus, die die zu exportierende Ressource enthält.

1. Wählen Sie die zu exportierende Ressource aus, um sie zu öffnen.

1. Wählen Sie für diese Ressource im linken Bereich **Vorlage exportieren** aus.

   ![Exportieren einer Ressource](./media/export-template-portal/export-single-resource.png)

1. Die exportierte Vorlage wird angezeigt und steht zum Herunterladen und Bereitstellen bereit. Die Vorlage enthält nur die einzelne Ressource. **Parameter einschließen** ist standardmäßig ausgewählt.  Wenn diese Option ausgewählt ist, werden alle Vorlagenparameter eingeschlossen, wenn die Vorlage generiert wird. Wenn Sie Ihre eigenen Parameter erstellen möchten, deaktivieren Sie dieses Kontrollkästchen, um sie nicht einzuschließen.

## <a name="export-template-before-deployment"></a>Exportieren einer Vorlage vor der Bereitstellung

1. Wählen Sie den bereitzustellenden Azure-Dienst aus.

1. Geben Sie die Werte für den neuen Dienst ein.

1. Wählen Sie nach erfolgreicher Validierung, aber vor Beginn der Bereitstellung, **Vorlage zur Automatisierung herunterladen** aus.

   ![Herunterladen der Vorlage](./media/export-template-portal/download-before-deployment.png)

1. Die Vorlage wird angezeigt und steht zum Herunterladen und Bereitstellen bereit.


## <a name="export-template-after-deployment"></a>Exportieren der Vorlage nach der Bereitstellung

Sie können die Vorlage exportieren, die für die Bereitstellung vorhandener Ressourcen verwendet wurde. Die Vorlage, die Sie erhalten, ist genau dieselbe, die für die Bereitstellung verwendet wurde.

1. Wählen Sie die zu exportierende Ressourcengruppe aus.

1. Wählen Sie den Link unter **Bereitstellungen** aus.

   ![Auswählen des Bereitstellungsverlaufs](./media/export-template-portal/select-deployment-history.png)

1. Wählen Sie eine der Bereitstellungen aus dem Bereitstellungsverlauf aus.

   ![Auswählen der Bereitstellung](./media/export-template-portal/select-details.png)

1. Wählen Sie **Vorlage** aus. Die für diese Bereitstellung verwendete Vorlage wird angezeigt und steht zum Herunterladen zur Verfügung.

   ![Auswählen der Vorlage](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Exportieren von Vorlagen mit der [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) oder der [REST-API](/rest/api/resources/resourcegroups/exporttemplate).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](template-syntax.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](../index.yml).
- Informationen zum Anzeigen der Vorlagenschemas für Azure Resource Manager finden Sie in der [Referenz zu Vorlagen](/azure/templates/).