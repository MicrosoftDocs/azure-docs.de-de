---
title: Informationen zu Azure Machine Learning-Umgebungen
titleSuffix: Azure Machine Learning
description: In diesem Artikel finden Sie Informationen über Machine Learning-Umgebungen, die reproduzierbare, überprüfbare und portierbare Machine Learning-Abhängigkeitsdefinitionen für verschiedene Computeziele ermöglichen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 11/16/2020
ms.openlocfilehash: 78f8d6d216659eaad01d512dd45696dd31035885
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695383"
---
# <a name="what-are-azure-machine-learning-environments"></a>Was sind Azure Machine Learning-Umgebungen?

Azure Machine Learning-Umgebungen sind eine Kapselung der Umgebung, in der Ihr Training für das maschinelle Lernen stattfindet. Sie geben die Python-Pakete, Umgebungsvariablen und Softwareeinstellungen bezüglich Ihrer Trainings- und Bewertungsskripts an. Sie geben auch Laufzeiten an (Python, Spark oder Docker). Die Umgebungen sind verwaltete und versionsverwaltete Entitäten innerhalb Ihres Machine Learning-Arbeitsbereichs, die reproduzierbare, überprüfbare und portierbare Machine Learning-Workflows über verschiedene Computeziele hinweg ermöglichen.

Sie können ein `Environment`-Objekt auf Ihrem lokalen Computer verwenden, um Folgendes zu erreichen:
* Entwickeln Sie Ihr Trainingsskript.
* Verwenden Sie dieselbe Umgebung in Azure Machine Learning Compute für das skalierte Modelltraining wieder.
* Stellen Sie Ihr Modell in derselben Umgebung bereit.
* Überprüfen Sie die Umgebung, in der ein bestehendes Modell trainiert wurde.

Das folgende Diagramm veranschaulicht, wie Sie ein einzelnes `Environment`-Objekt sowohl in Ihrer Ausführungskonfiguration (für Trainingszwecke) als auch in Ihrer Rückschluss- und Bereitstellungskonfiguration (für Webdienstbereitstellungen) verwenden können.

![Diagramm einer Umgebung im Machine Learning-Workflow](./media/concept-environments/ml-environment.png)

Die Umgebung, das Computeziel und das Trainingsskript bilden zusammen die Ausführungskonfiguration: die vollständige Spezifikation einer Trainingsausführung.

## <a name="types-of-environments"></a>Typen von Umgebungen

Umgebungen lassen sich weitgehend in drei Kategorien unterteilen: *zusammengestellt*, *benutzerverwaltet* und *systemverwaltet*.

Zusammengestellte Umgebungen werden von Azure Machine Learning bereitgestellt und sind standardmäßig in Ihrem Arbeitsbereich verfügbar. Sie sind zur sofortigen Verwendung gedacht und enthalten Sammlungen von Python-Paketen und -Einstellungen, die Ihnen den Einstieg in die verschiedenen Machine Learning-Frameworks erleichtern sollen. Diese vorab erstellten Umgebungen ermöglichen auch eine schnellere Bereitstellungszeit. Eine vollständige Liste finden Sie im [Artikel zu zusammengestellten Umgebungen](resource-curated-environments.md).

In benutzerverwalteten Umgebungen sind Sie für die Einrichtung Ihrer Umgebung und die Installation aller Pakete, die Ihr Trainingsskript auf dem Computeziel benötigt, verantwortlich. Conda wird Ihre Umgebung nicht überprüfen oder Installationen für Sie vornehmen. Beim Definieren einer eigenen Umgebung müssen Sie `azureml-defaults` mit der Version `>= 1.0.45` als Pip-Abhängigkeit auflisten. Dieses Paket enthält die erforderlichen Funktionen zum Hosten des Modells als Webdienst.

Sie verwenden systemverwaltete Umgebungen, wenn Sie möchten, dass [Conda](https://conda.io/docs/) die Python-Umgebung und die Skriptabhängigkeiten für Sie verwalten soll. Eine neue Conda-Umgebung wird auf der Grundlage des Conda-Abhängigkeitsobjekts erstellt. Der Azure Machine Learning Service setzt diesen Typ von Umgebung standardmäßig voraus, da er für Remotecomputeziele, die nicht manuell konfiguriert werden können, sehr praktisch ist.

## <a name="create-and-manage-environments"></a>Erstellen und Verwalten von Umgebungen

Sie können Umgebungen folgendermaßen erstellen:

* Durch Definieren von neuen `Environment`-Objekten, entweder mithilfe einer kuratierten Umgebung oder durch Definieren eigener Abhängigkeiten.
* Durch Verwenden vorhandener `Environment`-Objekte aus Ihrem Arbeitsbereich. Dieser Ansatz ermöglicht konsistente und reproduzierbare Abhängigkeiten.
* Durch Importieren aus einer vorhandenen Anaconda-Umgebungsdefinition.
* Verwenden der Azure Machine Learning-CLI
* [Verwenden der VS Code-Erweiterung](how-to-manage-resources-vscode.md#create-environment)

Bestimmte Codebeispiele finden Sie im Abschnitt „Erstellen einer Umgebung“ unter [Verwenden von Umgebungen](how-to-use-environments.md#create-an-environment). Umgebungen können auch problemlos über Ihren Arbeitsbereich verwaltet werden. Sie sollten folgende Funktionen umfassen:

* Umgebungen werden beim Übermitteln eines Experiments automatisch bei Ihrem Arbeitsbereich registriert. Sie können auch manuell registriert werden.
* Sie können Umgebungen aus Ihrem Arbeitsbereich abrufen, um sie zum Trainieren, Bereitstellen oder Vornehmen von Änderungen an der Umgebungsdefinition zu verwenden.
* Mit der Versionsverwaltung können Sie Änderungen an Ihren Umgebungen im Laufe der Zeit erkennen, was die Reproduzierbarkeit sicherstellt.
* Sie können Docker-Images automatisch aus Ihren Umgebungen erstellen.

Codebeispiele finden Sie im Abschnitt „Verwalten von Umgebungen“ unter [Verwenden von Umgebungen](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Erstellen, Zwischenspeichern und Wiederverwenden von Umgebungen

Der Azure Machine Learning-Dienst erstellt Umgebungsdefinitionen in Docker-Images und Conda-Umgebungen. Außerdem werden die Umgebungen zwischengespeichert, sodass sie bei späteren Trainingsdurchläufen und Bereitstellungen von Dienstendpunkten wiederverwendet werden können. Die Remoteausführung eines Trainingsskripts erfordert die Erstellung eines Docker-Images, während eine lokale Ausführung direkt eine Conda-Umgebung verwenden kann. 

### <a name="submitting-a-run-using-an-environment"></a>Übermitteln einer Ausführung mithilfe einer Umgebung

Der Azure Machine Learning Service ruft beim ersten Übermitteln einer Remoteausführung in einer Umgebung eine [ACR-Buildaufgabe](../container-registry/container-registry-tasks-overview.md) in der Azure Container Registry (ACR) auf, die dem Arbeitsbereich zugeordnet ist. Das erstellte Docker-Image wird dann in der ACR des Arbeitsbereichs zwischengespeichert. Zusammengestellte Umgebungen werden durch Docker-Images unterstützt, die in Global ACR zwischengespeichert werden. Zu Beginn der Ausführung wird das Image vom Computeziel von der relevanten ACR abgerufen.

Für lokale Ausführungen wird auf der Grundlage der Umgebungsdefinition eine Docker- oder Conda-Umgebung erstellt. Die Skripts werden dann auf dem Zielcomputer – einer lokalen Laufzeitumgebung oder einer lokalen Docker-Engine – ausgeführt.

### <a name="building-environments-as-docker-images"></a>Erstellen von Umgebungen als Docker-Images

Wenn die Umgebungsdefinition nicht bereits in der ACR des Arbeitsbereichs vorhanden ist, wird ein neues Image erstellt. Die Imageerstellung besteht aus zwei Schritten:

 1. Herunterladen eines Basisimages und Ausführen beliebiger Docker-Schritte
 2. Erstellen einer Conda-Umgebung gemäß den in der Umgebungsdefinition angegebenen Conda-Abhängigkeiten.

Der zweite Schritt entfällt, wenn Sie [vom Benutzer verwaltete Abhängigkeiten](/python/api/azureml-core/azureml.core.environment.pythonsection?preserve-view=true&view=azure-ml-py) angeben. In diesem Fall sind Sie für die Installation von Python-Paketen verantwortlich, indem Sie diese in Ihr Basisimage einbeziehen oder im ersten Schritt benutzerdefinierte Docker-Schritte festlegen. Sie sind auch dafür verantwortlich, den richtigen Speicherort für die ausführbare Python-Datei anzugeben. Es ist auch möglich, ein [benutzerdefiniertes Docker-Basisimage](how-to-deploy-custom-docker-image.md) zu verwenden.

### <a name="image-caching-and-reuse"></a>Zwischenspeicherung und Wiederverwendung von Images

Wenn Sie dieselbe Umgebungsdefinition für eine weitere Ausführung verwenden, verwendet der Azure Machine Learning-Dienst das zwischengespeicherte Image aus der ACR des Arbeitsbereichs wieder. 

Um die Details eines zwischengespeicherten Images anzuzeigen, verwenden Sie die Methode [Environment.get_image_details](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-image-details-workspace-).

Um zu bestimmen, ob ein zwischengespeichertes Image wiederverwendet oder ein neues erstellt werden soll, berechnet der Dienst [einen Hashwert](https://en.wikipedia.org/wiki/Hash_table) aus der Umgebungsdefinition und vergleicht ihn mit den Hashwerten bestehender Umgebungen. Der Hash basiert auf:
 
 * Eigenschaftswert des Basisimages
 * Eigenschaftswert für benutzerdefinierte Docker-Schritte
 * Liste der Python-Pakete in der Conda-Definition
 * Liste der Pakete in der Spark-Definition 

Der Hash hängt nicht vom Umgebungsnamen oder der Version ab. Wenn Sie Ihre Umgebung umbenennen oder eine neue Umgebung mit den genauen Eigenschaften und Paketen einer vorhandenen Umgebung erstellen, bleibt der Hashwert unverändert. Änderungen der Umgebungsdefinition, z. B. das Hinzufügen oder Entfernen eines Python-Pakets oder die Änderung der Paketversion, führen jedoch zu einer Änderung des Hashwerts. Das Ändern der Reihenfolge von Abhängigkeiten oder Kanälen in einer Umgebung führt zu einer neuen Umgebung und erfordert daher einen neuen Imagebuild. Es ist wichtig zu beachten, dass der Hash durch jede Änderung an einer zusammengestellten Umgebung ungültig wird und dies zu einer neuen „nicht zusammengestellten“ Umgebung führt.

Der berechnete Hashwert wird mit den Werten im Arbeitsbereich und in der globalen ACR (oder auf dem Computeziel für lokale Ausführungen) verglichen. Wenn es eine Übereinstimmung gibt, wird das zwischengespeicherte Image gepullt, andernfalls wird eine Imageerstellung ausgelöst. Die Dauer zum Abrufen eines zwischengespeicherten Images umfasst die Downloadzeit, während die Dauer zum Pullen eines neu erstellten Images sowohl die Erstellungszeit als auch die Downloadzeit umfasst. 

Das folgende Diagramm zeigt drei Umgebungsdefinitionen. Zwei von ihnen haben unterschiedliche Namen und Versionen, aber ein identisches Basisimage und identische Python-Pakete. Sie haben aber denselben Hashwert und entsprechen daher demselben zwischengespeicherten Image. Die dritte Umgebung weist verschiedene Python-Pakete und -Versionen auf und entspricht daher einem anderen zwischengespeicherten Image.

![Diagramm zur Zwischenspeicherung der Umgebung als Docker-Images](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Wenn Sie eine Umgebung mit losgelöster Paketabhängigkeit erstellen, z. B. ```numpy```, wird diese Umgebung weiterhin die Paketversion verwenden, _die zum Zeitpunkt der Erstellung der Umgebung installiert war_. Außerdem wird jede zukünftige Umgebung mit passender Definition weiterhin die alte Version verwenden. 

Geben Sie zum Aktualisieren des Pakets eine Versionsnummer an, um eine Neuerstellung des Images zu erzwingen, z. B. ```numpy==1.18.1```. Neue Abhängigkeiten, einschließlich geschachtelter Abhängigkeiten, werden installiert, die ein zuvor funktionierendes Szenario beeinträchtigen könnten. 

> [!WARNING]
>  Die Methode [Environment.build](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truebuild-workspace--image-build-compute-none-) erstellt das zwischengespeicherte Image neu, mit dem möglichen Nebeneffekt, dass losgelöste Pakete aktualisiert werden und die Reproduzierbarkeit für alle Umgebungsdefinitionen, die diesem zwischengespeicherten Image entsprechen, unterbrochen wird.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Umgebungen in Azure Machine Learning erstellen und verwenden](how-to-use-environments.md).
* Die [Umgebungsklasse](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) finden Sie in der Referenzdokumentation des Python SDK.
* [Umgebungen](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments) finden Sie in der Referenzdokumentation des R SDK.