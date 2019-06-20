---
title: Exportieren von Azure Resource Manager-Vorlagen über das Azure-Portal
description: Verwenden Sie das Azure-Portal, um eine Azure Resource Manager-Vorlage aus Ressourcen in Ihrem Abonnement zu exportieren.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: ea9499da3dac67635a48704f439f6592c6ed467e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65515066"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Exportieren von einzelnen und mehreren Ressourcen in eine Vorlage im Azure-Portal

Um bei der Erstellung von Azure Resource Manager-Vorlagen zu helfen, können Sie eine Vorlage aus vorhandenen Ressourcen exportieren. Die exportierte Vorlage hilft Ihnen, die JSON-Syntax und die Eigenschaften zu verstehen, die Ihre Ressourcen bereitstellen. Zum Automatisieren zukünftiger Bereitstellungen beginnen Sie mit der exportierten Vorlage und passen Sie sie an Ihr Szenario an.

Mit Resource Manager können Sie eine oder mehrere Ressourcen für den Export in eine Vorlage auswählen. Sie können sich auf genau die Ressourcen konzentrieren, die in der Vorlage erforderlich sind.

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
| Die Vorlage enthält möglicherweise nicht alle für die Wiederverwendung erforderlichen Parameter. Die meisten Eigenschaftswerte sind in der Vorlage hartcodiert. Um die Vorlage in anderen Umgebungen neu bereitzustellen, müssen Sie Parameter hinzufügen, die die Möglichkeit zur Konfiguration der Ressourcen erhöhen. | Die Vorlage enthält Parameter, die es einfach gestalten, sie in verschiedenen Umgebungen neu bereitzustellen. |

Exportieren Sie die Vorlage aus einer Ressourcengruppe oder Ressource, wenn Folgendes zutrifft:

* Sie müssen Änderungen an den Ressourcen erfassen, die nach der ursprünglichen Bereitstellung vorgenommen wurden.
* Sie möchten die zu exportierenden Ressourcen auswählen.

Exportieren Sie die Vorlage vor der Bereitstellung oder über den Verlauf, wenn Folgendes zutrifft:

* Sie benötigen eine einfach wiederzuverwendende Vorlage.
* Sie müssen keine Änderungen einbeziehen, die Sie nach der ursprünglichen Bereitstellung vorgenommen haben.

## <a name="export-template-from-resource-group"></a>Exportieren einer Vorlage aus der Ressourcengruppe

So exportieren Sie eine oder mehrere Ressourcen aus einer Ressourcengruppe

1. Wählen Sie die Ressourcengruppe aus, die die zu exportierenden Ressourcen enthält.

1. Wählen Sie zum Exportieren aller Ressourcen in der Ressourcengruppe alle Ressourcen und dann **Vorlage exportieren** aus. Die Option **Vorlage exportieren** wird erst aktiviert, wenn Sie mindestens eine Ressource ausgewählt haben.

   ![Exportieren aller Ressourcen](./media/export-template-portal/select-all-resources.png)

1. Um bestimmte Ressourcen für den Export auszuwählen, aktivieren Sie die Kontrollkästchen neben diesen Ressourcen. Wählen Sie dann **Vorlage exportieren** aus.

   ![Auswählen der zu exportierenden Ressourcen](./media/export-template-portal/select-resources.png)

1. Die exportierte Vorlage wird angezeigt und steht zum Herunterladen zur Verfügung.

   ![Anzeigen der Vorlage](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Exportieren der Vorlage aus einer Ressource

So exportieren Sie eine Ressource

1. Wählen Sie die Ressourcengruppe aus, die die zu exportierende Ressource enthält.

1. Wählen Sie die zu exportierende Ressource aus.

   ![Auswählen einer Ressource](./media/export-template-portal/select-link-resource.png)

1. Wählen Sie für diese Ressource im linken Bereich **Vorlage exportieren** aus.

   ![Exportieren einer Ressource](./media/export-template-portal/export-single-resource.png)

1. Die exportierte Vorlage wird angezeigt und steht zum Herunterladen zur Verfügung. Die Vorlage enthält nur die einzelne Ressource.

## <a name="export-template-before-deployment"></a>Exportieren einer Vorlage vor der Bereitstellung

1. Wählen Sie den bereitzustellenden Azure-Dienst aus.

1. Geben Sie die Werte für den neuen Dienst ein.

1. Wählen Sie nach erfolgreicher Validierung, aber vor Beginn der Bereitstellung, **Vorlage zur Automatisierung herunterladen** aus.

   ![Herunterladen der Vorlage](./media/export-template-portal/download-before-deployment.png)

1. Die Vorlage wird angezeigt und steht zum Herunterladen bereit.

   ![Anzeigen der Vorlage](./media/export-template-portal/show-template-before-deployment.png)

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

- Weitere Informationen zu Azure Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](./resource-group-overview.md).
- Informationen zur Resource Manager-Vorlagensyntax finden Sie unter [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](./resource-group-authoring-templates.md).
- Informationen zum Entwickeln von Vorlagen finden Sie in den [Schritt-für-Schritt-Tutorials](/azure/azure-resource-manager/).
- Informationen zum Anzeigen der Vorlagenschemas für Azure Resource Manager finden Sie in der [Referenz zu Vorlagen](/azure/templates/).