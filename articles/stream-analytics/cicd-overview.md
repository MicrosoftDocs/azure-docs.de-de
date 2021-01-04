---
title: Continuous Integration und Continuous Deployment für Azure Stream Analytics
description: Dieser Artikel bietet eine Übersicht über eine CI/CD-Pipeline (Continuous Integration/Continuous Deployment) für Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: b5057eb8c84e839f504060228986ea759c8bdc3d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123166"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Continuous Integration und Continuous Deployment (CI/CD) für Azure Stream Analytics

Sie können Ihren Azure Stream Analytics-Auftrag mithilfe der Integration der Quellcodeverwaltung kontinuierlich bereitstellen. Die Integration der Quellcodeverwaltung ermöglicht einen Workflow, bei dem eine Codeaktualisierung eine Ressourcenbereitstellung in Azure auslöst. In diesem Artikel werden die grundlegenden Schritte zum Erstellen einer CI/CD-Pipeline (Continuous Integration/Continuous Deployment) beschrieben.

Wenn Sie noch nicht mit Azure Stream Analytics vertraut sind, beginnen Sie mit der [Schnellstartanleitung zu Azure Stream Analytics](stream-analytics-quick-create-portal.md).

## <a name="create-a-cicd-pipeline"></a>Erstellen einer CI/CD-Pipeline

Führen Sie die Schritte in dieser Anleitung aus, um eine CI/CD-Pipeline für Stream Analytics zu erstellen.

1. Entwickeln Sie eine Azure Stream Analytics-Abfrage.

   Verwenden Sie Azure Stream Analytics-Tools für [Visual Studio Code](./quick-create-visual-studio-code.md) oder [Visual Studio](stream-analytics-quick-create-vs.md), um [Abfragen lokal zu entwickeln und zu testen](develop-locally.md). Sie können auch [einen vorhandenen Auftrag in ein lokales Projekt exportieren](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project).

2. Committen Sie Ihre Azure Stream Analytics-Projekte in das Quellcodeverwaltungssystem, z. B. in ein Git-Repository.

3. Verwenden Sie [Azure Stream Analytics-CI/CD-Tools](cicd-tools.md), um die Projekte zu erstellen und Azure Resource Manager-Vorlagen für die Bereitstellung zu generieren.

4. Führen Sie [Skripts für automatisierte Tests](cicd-tools.md#automated-test) für Qualitätsregressionstests aus.

5. [Stellen Sie den Auftrag automatisch in Azure bereit](cicd-tools.md#deploy-to-azure).

## <a name="auto-build-test-and-deploy"></a>Automatisches Erstellen, Testen und Bereitstellen

Sie können zum automatischen Erstellen, Testen und Bereitstellen die Befehlszeile und [Azure Stream Analytics-CI/CD-Tools](cicd-tools.md) verwenden. Sie können auch in [Azure Pipelines](set-up-cicd-pipeline.md) eine CI/CD-Pipeline einrichten. Azure Pipelines bieten erweiterte Funktionen, z. B. Pipelineverwaltung, Visualisierung und Trigger.

## <a name="next-steps"></a>Nächste Schritte

* [Automatisieren von Builds, Tests und Bereitstellungen eines Azure Stream Analytics-Auftrags mit CI/CD-Tools](cicd-tools.md)
* [Einrichten einer CI/CD-Pipeline für einen Stream Analytics-Auftrag mithilfe von Azure Pipelines](set-up-cicd-pipeline.md)