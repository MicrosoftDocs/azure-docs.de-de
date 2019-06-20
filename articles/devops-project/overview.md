---
title: Übersicht über Azure DevOps Projects | Microsoft-Dokumentation
description: Grundlegendes zum Nutzen von Azure DevOps Projects
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: 6ae2d0723b195ed59f8c6a7320245509145094df
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "60925946"
---
# <a name="overview-of-azure-devops-projects"></a>Übersicht über Azure DevOps Projects

 Azure DevOps Projects erleichtert die ersten Schritte mit Azure. Mit Azure DevOps Projects können Sie Ihre bevorzugte App für den Azure-Dienst Ihrer Wahl in wenigen kurzen Schritten über das Azure-Portal starten. 

 Azure DevOps Projects richtet alles ein, was Sie zum Entwickeln, Bereitstellen und Überwachen Ihrer Anwendung benötigen. Über das Dashboard von Azure DevOps Projects können Sie Codecommits, Buildvorgänge und Bereitstellungen in einer zentralen Ansicht im Azure-Portal überwachen.

## <a name="why-should-i-use-devops-projects"></a>Gründe für die Verwendung von DevOps Projects

  DevOps Projects automatisiert die Einrichtung einer gesamten CI- und CD-Pipeline (Continuous Integration und Continuous Delivery) für Azure.  Sie können bereits vorhandenen Code nutzen oder eine der bereitgestellten Beispielanwendungen verwenden. Anschließend können Sie die Anwendung schnell für verschiedene Azure-Dienste wie Virtual Machines, App Service, Azure Kubernetes Service (AKS), Azure SQL-Datenbank oder Azure Service Fabric bereitstellen.  

  DevOps Projects nimmt Ihnen die gesamte Erstkonfiguration einer DevOps-Pipeline ab – von der Einrichtung des anfänglichen Git-Repositorys und der Konfiguration der CI/CD-Pipeline über die Erstellung einer Application Insights-Ressource für die Überwachung bis hin zur Bereitstellung einer zentralen Ansicht der gesamten Lösung durch Erstellung eines DevOps Projects-Dashboards im Azure-Portal.

DevOps Projects ermöglicht Folgendes:

* Schnelles Bereitstellen Ihrer Anwendung in Azure
* Automatisieren der Einrichtung einer CI/CD-Pipeline
* Anzeigen und Nachvollziehen der ordnungsgemäßen Einrichtung einer CI/CD-Pipeline
* Weiteres Anpassen der Releasepipelines auf der Grundlage Ihres spezifischen Szenarios

## <a name="how-do-i-use-devops-projects"></a>Verwenden von DevOps Projects

  DevOps Projects steht über das Azure-Portal zur Verfügung. Eine DevOps Projects-Ressource wird im Portal auf die gleiche Weise erstellt wie jede andere Azure-Ressource. DevOps Projects bietet einen Assistenten zum Durchlaufen der verschiedenen Konfigurationsoptionen.  

Bei der Ersteinrichtung müssen mehrere Konfigurationsoptionen ausgewählt werden. Die Optionen umfassen:

* Verwenden der bereitgestellten Beispiel-App oder Ihres eigenen Codes
* Auswählen einer App-Sprache
* Auswählen eines App-Frameworks auf der Grundlage der Sprache
* Auswählen eines Azure-Diensts (Bereitstellungsziel)
* Erstellen einer neuen Azure DevOps-Organisation oder Verwenden einer bereits vorhandenen Organisation 
* Auswählen Ihres Azure-Abonnements
* Auswählen des Standorts von Azure-Diensten
* Auswählen eines Tarifs für Azure-Dienste

Nach Verwendung von DevOps Projects können die Ressourcen auch alle von einem zentralen Ort aus über das DevOps Projects-Dashboard im Azure-Portal gelöscht werden.

## <a name="devops-projects-and-azure-devops-integration"></a>DevOps Projects- und Azure DevOps-Integration

DevOps Projects basiert auf Azure DevOps. DevOps Projects automatisiert sämtliche Aufgaben, die in Azure Pipelines zum Einrichten einer CI/CD-Pipeline ausgeführt werden müssen. Es erstellt ein Git-Repository in einer neuen oder vorhandenen Azure DevOps-Organisation und committet dann eine Beispielanwendung oder Ihren vorhandenen Code in einem neuen Git-Repository.  

Die Automatisierung richtet auch einen CI-Trigger für den Build ein, sodass nach jedem neuen Codecommit ein Buildvorgang initiiert wird. DevOps Projects erstellt einen CD-Trigger und stellt jeden neuen erfolgreichen Build für den Azure-Dienst Ihrer Wahl bereit.  

Build- und Releasepipelines können für zusätzliche Szenarien angepasst werden. Darüber hinaus können Sie die Build- und die Releasepipelines für die Verwendung in anderen Projekten klonen.

Nach der Erstellung des DevOps-Projekts haben Sie folgende Möglichkeiten:

* Anpassen der Build- und Releasepipeline
* Verwenden von Pullanforderungen, um den Codefluss zu verwalten und eine hohe Qualität zu gewährleisten
* Testen und Erstellen jedes Commits vor dem Zusammenführen Ihres Codes zur Erhöhung der Qualität
* Direktes Nachverfolgen von Backlog und Problemen zusammen mit Ihrer Anwendung

## <a name="how-do-i-start-using-devops-projects"></a>Informationen zum Einstieg in die Verwendung von DevOps Projects

* [Tutorial: Erstellen einer CI/CD-Pipeline für vorhandenen Code mit dem Azure DevOps-Projekt](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>Videos zu DevOps Projects

* [Creating your CI/CD Pipeline with VSTS into Azure](https://channel9.msdn.com/Events/Connect/2017/T174/player/) (Erstellen Ihrer CI/CD-Pipeline mit VSTS für Azure)
