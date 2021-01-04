---
title: CI/CD für Azure API Management mit ARM-Vorlagen
description: Einführung in API DevOps mit Azure API Management, wobei Azure Resource Manager-Vorlagen zum Verwalten von API-Bereitstellungen in einer CI/CD-Pipeline verwendet werden.
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 62f163b9ce649cd5ddb52b4325682570633dfb92
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183157"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>CI/CD für API Management mit Azure Resource Manager-Vorlagen

In diesem Artikel wird gezeigt, wie Sie API DevOps mit Azure API Management über Azure Resource Manager-Vorlagen verwenden. Aufgrund des strategischen Nutzens von APIs ist die CI/CD-Pipeline (Continuous Integration/Continuous Deployment) zu einem wichtigen Aspekt der API-Entwicklung geworden. Sie ermöglicht Organisationen die Automatisierung der Bereitstellung von API-Änderungen ohne fehleranfällige manuelle Schritte, die frühzeitigere Erkennung von Problemen und letztendlich einen schnelleren Nutzen für Benutzer. 

Ausführliche Informationen, Tools und Codebeispiele zur Implementierung des in diesem Artikel beschriebenen DevOps-Ansatzes finden Sie auf GitHub unter dem [Azure API Management DevOps Resource Kit (Open Source)](https://github.com/Azure/azure-api-management-devops-resource-kit). Da Kunden über viele unterschiedliche technische Ansätze und bereits vorhandene Automatisierungslösungen verfügen, ist der Ansatz keine universelle Lösung.

## <a name="the-problem"></a>Das Problem

Organisationen verfügen heute normalerweise über mehrere Bereitstellungsumgebungen (z. B. für Entwicklung, Test und Produktion) und verwenden für jede Umgebung separate API Management-Instanzen. Einige Instanzen werden von mehreren Entwicklungsteams gemeinsam verwendet, wobei die Teams für unterschiedliche APIs mit verschiedenen Releaseintervallen verantwortlich sind.

Dies hat zur Folge, dass Kunden Antworten auf die folgenden Fragen finden müssen:

* Wie kann ich die Bereitstellung von APIs in API Management automatisieren?
* Wie kann ich Konfigurationen von einer Umgebung zu einer anderen migrieren?
* Wie kann ich Beeinträchtigungen zwischen den verschiedenen Entwicklungsteams verhindern, die dieselbe API Management-Instanz nutzen?

## <a name="manage-configurations-in-resource-manager-templates"></a>Verwalten von Konfigurationen in Resource Manager-Vorlagen

Die vorgeschlagene Vorgehensweise ist in der folgenden Abbildung dargestellt: 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="Diagramm: DevOps mit API Management":::

In diesem Beispiel werden zwei Bereitstellungsumgebungen verwendet: eine für die *Entwicklung* (Development) und eine für die *Produktion* (Production). Jede verfügt über eine eigene API Management-Instanz. 

* API-Entwickler haben Zugriff auf die Instanz „Entwicklung“ und können sie zum Entwickeln und Testen ihrer APIs verwenden. 
* Ein zugeordnetes Team mit dem Namen *API publishers* (API-Herausgeber) verwaltet die Instanz „Produktion“.

Der entscheidende Punkt bei diesem vorgeschlagenen Ansatz ist, alle API Management-Konfigurationen in [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md) aufzubewahren. Die Organisation sollte diese Vorlagen in einem System für die Quellcodeverwaltung (etwa Git) vorhalten. Wie in der Abbildung dargestellt, enthält das Repository „Publisher“ (Herausgeber) alle Konfigurationen der API Management-Instanz „Produktion“ als Vorlagensammlung:

|Vorlage  |BESCHREIBUNG  |
|---------|---------|
|Dienstvorlage     | Dienstebenenkonfigurationen der API Management-Instanz, z. B. Tarif und benutzerdefinierte Domänen         |
|Freigegebene Vorlagen     |  Gemeinsam genutzte Ressourcen einer API Management-Instanz, z. B. Gruppen, Produkte und Protokollierungen    |
|API-Vorlagen     |  Konfigurationen von APIs und deren Unterressourcen: Vorgänge, Richtlinien, Diagnoseeinstellungen        |
|Mastervorlage (Hauptvorlage)     |   Vereint alle Elemente, indem eine [Verknüpfung](../azure-resource-manager/templates/linked-templates.md) mit allen Vorlagen erstellt wird und diese in der entsprechenden Reihenfolge bereitgestellt werden. Stellen Sie die Hauptvorlage bereit, um die Bereitstellung aller Konfigurationen für eine API Management-Instanz durchzuführen. Sie können jede Vorlage auch einzeln bereitstellen.       |

API-Entwickler forken das Repository „Publisher“ (Herausgeber) in ein Repository „Developer“ (Entwickler) und arbeiten an den Änderungen für ihre APIs. In den meisten Fällen konzentrieren sie sich auf die API-Vorlagen für ihre APIs und müssen die freigegebenen Vorlagen oder Dienstvorlagen nicht ändern.

## <a name="migrate-configurations-to-templates"></a>Migrieren von Konfigurationen zu Vorlagen
Die Verwendung von Resource Manager-Vorlagen stellt API-Entwickler vor Herausforderungen:

* API-Entwickler nutzen häufig die [OpenAPI-Spezifikation](https://github.com/OAI/OpenAPI-Specification) und sind ggf. nicht mit Resource Manager-Schemas vertraut. Die manuelle Erstellung von Vorlagen kann fehleranfällig sein. 

   Ein Tool mit dem Namen [Creator](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) im Resource Kit ist hilfreich beim Automatisieren der Erstellung von API-Vorlagen basierend auf einer OpenAPI-Spezifikationsdatei. Darüber hinaus können Entwickler auch API Management-Richtlinien für eine API im XML-Format bereitstellen. 

* Für Kunden, die API Management bereits nutzen, ist eine weitere Hürde das Extrahieren vorhandener Konfigurationen in Resource Manager-Vorlagen. Für diese Kunden kann das Tool [Extractor](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) aus dem Resource Kit als Hilfe beim Generieren von Vorlagen dienen, indem Konfigurationen aus ihren API Management-Instanzen extrahiert werden.  

## <a name="workflow"></a>Workflow

* Nachdem API-Entwickler die Entwicklung und die Durchführung von Tests für eine API abgeschlossen und die API-Vorlagen generiert haben, können sie einen Pull Request übermitteln, um die Änderungen für das Repository „Herausgeber“ zusammenzuführen. 

* API-Herausgeber können den Pull Request überprüfen und sicherstellen, dass die Änderungen sicher und konform sind. Beispielsweise können sie überprüfen, ob für die Kommunikation mit der API nur HTTPS zulässig ist. Die meisten Überprüfungen können als Schritt in der CI/CD-Pipeline automatisiert werden.

* Nachdem die Änderungen genehmigt wurden und die Zusammenführung erfolgreich abgeschlossen wurde, können die API-Herausgeber angeben, dass sie entweder nach einem Zeitplan oder nach Bedarf für die Instanz „Produktion“ bereitgestellt werden sollen. Die Bereitstellung der Vorlagen kann mit [GitHub Actions](https://github.com/Azure/apimanagement-devops-samples), [Azure Pipelines](/azure/devops/pipelines), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), der [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) oder anderen Tools automatisiert werden.


Bei dieser Vorgehensweise kann eine Organisation die Bereitstellung von API-Änderungen in API Management-Instanzen automatisieren, und es ist einfach, Änderungen von einer Umgebung in eine andere weiterzugeben. Da unterschiedliche API-Entwicklungsteams an verschiedenen Gruppen von API-Vorlagen und Dateien arbeiten, kommt es nicht zu Beeinträchtigungen zwischen den Teams.

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen, Tools und Beispielvorlagen finden Sie im [Azure API Management DevOps Resource Kit (Open Source)](https://github.com/Azure/azure-api-management-devops-resource-kit).