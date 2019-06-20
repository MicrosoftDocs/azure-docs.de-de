---
title: Integrieren von Umgebungen in Azure Pipelines in Azure DevTest Labs | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure DevTest Labs-Umgebungen in Ihre CI- und CD-Pipelines (Continuous Integration und Continuous Delivery) von Azure DevOps integrieren.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61318385"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrieren von Umgebungen in Ihre Azure DevOps-CI/CD-Pipelines
Mithilfe der in Azure DevOps Services (ehemals Visual Studio Team Services) installierten Erweiterung „Azure DevTest Labs Tasks“ können Sie Ihre Build- und Releasepipeline mit Continuous Integration (CI)/Continuous Delivery (CD) problemlos in Azure DevTest Labs integrieren. Diese Erweiterungen vereinfachen beispielsweise die schnelle Bereitstellung einer [Umgebung](devtest-lab-test-env.md) für eine bestimmte Testaufgabe und die anschließende Löschung der Umgebung nach Abschluss des Tests. 

In diesem Artikel wird gezeigt, wie Sie im Rahmen einer vollständigen Pipeline eine Umgebung erstellen und bereitstellen und die Umgebung anschließend wieder löschen. In Ihrer eigenen benutzerdefinierten Build-, Test- und Bereitstellungspipeline müssten diese Aufgaben üblicherweise einzeln ausgeführt werden. Die Erweiterungen in diesem Artikel werden zusätzlich zu den folgenden [Aufgaben zum Erstellen/Löschen eines virtuellen DTL-Computers](devtest-lab-integrate-ci-cd-vsts.md) verwendet:

- Erstellen einer Umgebung
- Löschen einer Umgebung

## <a name="before-you-begin"></a>Voraussetzungen
Um Ihre CI/CD-Pipeline in Azure DevTest Labs integrieren zu können, müssen Sie die Erweiterung [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) aus dem Visual Studio Marketplace installieren. 

## <a name="create-and-configure-the-lab-for-environments"></a>Erstellen und Konfigurieren des Labs für Umgebungen
In diesem Abschnitt erfahren Sie, wie Sie ein Lab für die Bereitstellung der Azure-Umgebung erstellen und konfigurieren.

1. [Erstellen Sie ein Lab](devtest-lab-create-lab.md), falls Sie noch keines besitzen. 
2. Konfigurieren Sie das Lab, und erstellen Sie eine Umgebungsvorlage, wie im folgenden Artikel beschrieben: [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md).
3. Verwenden Sie für dieses Beispiel die bereits vorhandene Azure-Schnellstartvorlage [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Kopieren Sie den Ordner **201-web-app-redis-cache-sql-database** in den Ordner **ArmTemplate** des Repositorys, das Sie in Schritt 2 konfiguriert haben.

## <a name="create-a-release-definition"></a>Erstellen einer Releasedefinition
So erstellen Sie die Releasedefinition

1.  Wählen Sie im Hub **Build und Release** auf der Registerkarte **Releases** die Schaltfläche **mit dem Pluszeichen (+)** aus.
2.  Wählen Sie im Fenster **Releasedefinition erstellen** die Vorlage **Leer** aus, und klicken Sie dann auf **Weiter**.
3.  Klicken Sie auf **Später auswählen** und dann auf **Erstellen**, um eine neue Releasedefinition mit einer Standardumgebung ohne verknüpfte Artefakte zu erstellen.
4.  Klicken Sie zum Öffnen des Kontextmenüs in der neuen Releasedefinition auf die Auslassungspunkte ( **...** ) neben dem Umgebungsnamen, und wählen Sie dann **Variablen konfigurieren** aus.
5.  Geben Sie im Fenster **Konfigurieren – Umgebung** die folgenden Werte für die Variablen ein, die Sie in den Releasedefinitionsaufgaben verwenden:
1.  Geben Sie für **administratorLogin** den Anmeldenamen des SQL-Administrators ein.
2.  Geben Sie für **administratorLoginPassword** das gewünschte Kennwort für die SQL-Administratoranmeldung ein. Verwenden Sie das Schlosssymbol, um das Kennwort auszublenden und zu schützen.
3.  Geben Sie für **databaseName** den Namen der SQL-Datenbank ein.
4.  Diese Variablen sind spezifisch für die Beispielumgebung. Andere Umgebungen besitzen ggf. andere Variablen.

## <a name="create-an-environment"></a>Erstellen einer Umgebung
In der nächsten Bereitstellungsphase wird die Umgebung erstellt, die zu Entwicklungs- oder Testzwecken verwendet wird.

1. Wählen Sie in der Releasedefinition **Aufgaben hinzufügen** aus.
2. Fügen Sie auf der Registerkarte **Aufgaben** eine Azure DevTest Labs-Aufgabe zum Erstellen einer Umgebung hinzu. Konfigurieren Sie die Aufgabe wie folgt:
    1. Wählen Sie für **Azure RM-Abonnement** in der Liste **Verfügbare Azure-Dienstverbindungen** eine Verbindung aus, oder erstellen Sie eine Verbindung mit eingeschränkteren Berechtigungen für Ihr Azure-Abonnement. Weitere Informationen finden Sie unter [Azure Resource Manager-Dienstendpunkt](/azure/devops/pipelines/library/service-endpoints).
2. Wählen Sie als **Lab-Name** den Namen der Instanz aus, die Sie zuvor erstellt haben.*
3. Wählen Sie unter **Repositoryname** das Repository aus, in das die Resource Manager-Vorlage (201) gepusht wurde.*
4. Wählen Sie unter **Vorlagenname** den Namen der Umgebung aus, den Sie in Ihrem Quellcoderepository gespeichert haben.* 
5. **Labname**, **Repositoryname** und **Vorlagenname** sind benutzerfreundliche Darstellungen der Azure-Ressourcen-IDs. Wählen Sie diese Angaben mithilfe der Dropdownlisten aus. Bei manueller Eingabe des Anzeigenamens tritt ein Fehler auf.
6. Geben Sie unter **Umgebungsname** einen Namen ein, der die Umgebungsinstanz innerhalb des Labs eindeutig identifiziert.  Dieser Wert muss innerhalb des Labs eindeutig sein.
7. Mithilfe der **Parameterdatei** und der **Parameter** können benutzerdefinierte Parameter an die Umgebung übergeben werden. Die Parameterwerte können mithilfe einer der beiden Optionen oder mithilfe beider Optionen festgelegt werden. In diesem Beispiel wird der Parameterabschnitt verwendet. Verwenden Sie die Namen der Variablen, die Sie in der Umgebung definiert haben, z. B.: `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. Informationen in der Umgebungsvorlage können im Ausgabeabschnitt der Vorlage übergeben werden. Aktivieren Sie das Kontrollkästchen **Create output variables based on the environment template output** (Ausgabevariablen auf der Grundlage der Ausgabe der Umgebungsvorlage erstellen), damit die Daten von anderen Aufgaben genutzt werden können. `$(Reference name.Output Name)` ist das zu verwendende Muster. Wenn der Verweisname also beispielsweise „DTL“ und der Ausgabename in der Vorlage „location“ lautet, ergibt sich folgende Variable: `$(DTL.location)`.

## <a name="delete-the-environment"></a>Löschen der Umgebung
In der letzten Phase wird die Umgebung, die Sie in Ihrer Azure DevTest Labs-Instanz bereitgestellt haben, wieder gelöscht. Eine Umgebung wird in der Regel gelöscht, nachdem Sie die erforderlichen Entwicklungsaufgaben oder Tests auf den bereitgestellten Ressourcen ausgeführt haben.

Wählen Sie in der Releasedefinition **Aufgaben hinzufügen** aus, und fügen Sie dann auf der Registerkarte **Bereitstellen** eine **Azure DevTest Labs-Aufgabe** um Löschen einer Umgebung hinzu. Konfigurieren Sie ihn wie folgt:

1. Informationen zum Löschen des virtuellen Computers finden Sie unter [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Wählen Sie für **Azure RM-Abonnement** in der Liste **Verfügbare Azure-Dienstverbindungen** eine Verbindung aus, oder erstellen Sie eine Verbindung mit eingeschränkteren Berechtigungen für Ihr Azure-Abonnement. Weitere Informationen finden Sie unter [Azure Resource Manager-Dienstendpunkt](/azure/devops/pipelines/library/service-endpoints).
    2. Wählen Sie unter **Labname** das Lab aus, in dem sich die Umgebung befindet.
    3. Geben Sie unter **Umgebungsname** den Namen der zu entfernenden Umgebung ein.
2. Geben Sie einen Namen für die Releasedefinition ein, und speichern Sie sie.

## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln: 
- [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md)
- Resource Manager-Schnellstartvorlagen für die DevTest Labs-Automatisierung aus dem [GitHub-Repository für DevTest Labs](https://github.com/Azure/azure-quickstart-templates)
- [VSTS Troubleshooting page (Seite zur Problembehandlung bei VSTS)](/azure/devops/pipelines/troubleshooting)

