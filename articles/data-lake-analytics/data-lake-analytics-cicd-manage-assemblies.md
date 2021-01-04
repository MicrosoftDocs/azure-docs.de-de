---
title: Verwalten von U-SQL-Assemblys in einer CI/CD-Pipeline – Azure Data Lake
description: Lernen Sie die Best Practices für die Verwaltung von U-SQL-C#-Assemblys in der CI/CD-Pipeline mit Azure DevOps kennen.
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: e88616f45c69d33234aa35333e0d82ad8cc59bb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015280"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Best Practices für die Verwaltung von U-SQL-Assemblys in einer CI/CD-Pipeline

In diesem Artikel erfahren Sie, wie Sie den Quellcode einer U-SQL-Assembly mit dem neu eingeführten U-SQL-Datenbankprojekt verwalten können. Sie erfahren auch, wie Sie mit Azure DevOps eine CI/CD-Pipeline (Continuous Integration und Continuous Deployment) für die Registrierung von Assemblys einrichten.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Verwenden des U-SQL-Datenbankprojekts zum Verwalten von Assemblyquellcode

Das [U-SQL-Datenbankprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md) ist ein Projekttyp in Visual Studio, mit dem Entwickler U-SQL-Datenbanken schnell und einfach entwickeln, verwalten und bereitstellen können. Sie können alle U-SQL-Datenbankobjekte (mit Ausnahme von Anmeldeinformationen) mit dem U-SQL-Datenbankprojekt verwalten.

Verwenden Sie Folgendes für die Verwaltung des Quellcodes der C#-Assembly und der U-SQL-Skripts für die Assemblyregistrierungs-DDL:

- U-SQL Datenbankprojekt zum Verwalten der U-SQL-Skripts für die Assemblyregistrierung.
- Klassenbibliothek (für U-SQL-Anwendungen) zum Verwalten des C#-Quellcodes und der Abhängigkeiten für benutzerdefinierte Operatoren, Funktionen und Aggregatoren (UDOs, UDFs und UDAGs).
- U-SQL-Datenbankprojekt zum Verweisen auf das Klassenbibliothekprojekt.

Ein U-SQL-Datenbankprojekt kann auf ein Klassenbibliothekprojekt (für U-SQL-Anwendungen) verweisen. Sie können Assemblys erstellen, die in der U-SQL-Datenbank registriert sind, indem Sie referenzierten C#-Quellcode aus diesem Klassenbibliothekprojekt (für U-SQL-Anwendungen) verwenden.

Führen Sie diese Schritte aus, um Projekte zu erstellen und Verweise hinzuzufügen.

1. Erstellen Sie ein Klassenbibliothekprojekt (für U-SQL-Anwendungen), indem Sie **Datei** > **Neu** > **Projekt** auswählen. Das Projekt befindet sich unter dem Knoten **Azure Data Lake > U-SQL**.

   ![Data Lake Tools für Visual Studio: Erstellen eines C#-Klassenbibliothekprojekts](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)

1. Fügen Sie Ihren benutzerdefinierten C#-Code in das Klassenbibliothekprojekt (für U-SQL-Anwendungen) ein.

1. Erstellen Sie ein U-SQL-Projekt, indem Sie **Datei** > **Neu** > **Projekt** auswählen. Das Projekt befindet sich unter dem Knoten **Azure Data Lake** > **U-SQL**.

   ![Data Lake Tools für Visual Studio: Erstellen eines U-SQL-Datenbankprojekts](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)

1. Fügen Sie einen Verweis auf das C#-Klassenbibliothekprojekt für das U-SQL-Datenbankprojekt hinzu.

   ![Data Lake Tools für Visual Studio Code: Hinzufügen eines Verweises](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png)

   ![Data Lake Tools für Visual Studio: Hinzufügen eines Verweises des U-SQL-Datenbankprojekts](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

1. Erstellen Sie ein Assemblyskript im U-SQL-Datenbankprojekt, indem Sie mit der rechten Maustaste auf das Projekt klicken und **Neues Element hinzufügen** auswählen.

   ![Data Lake Tools für Visual Studio: Hinzufügen eines Assemblyskripts](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Öffnen Sie das Assemblyskript in der Entwurfsansicht für die Assembly. Wählen Sie aus dem Dropdownmenü **Assembly aus Verweis erstellen** die referenzierte Assembly aus.

   ![Data Lake Tools für Visual Studio: Erstellen einer Assembly aus einem Verweis](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

1. Fügen Sie **verwaltete Abhängigkeiten** und **zusätzliche Dateien** hinzu, sofern vorhanden. Beim Hinzufügen von Dateien verwendet das Tool den relativen Pfad, um sicherzustellen, dass die Assemblys später auf Ihrem lokalen Computer und dem Buildcomputer gefunden werden.

**\@_DeployTempDirectory** unten im Editor-Fenster ist eine vordefinierte Variable, die das Tool auf den Buildausgabeordner verweist. Im Buildausgabeordner verfügt jede Assembly über einen Unterordner, der den gleichen Namen wie die Assembly besitzt. Alle DLLs und zusätzlichen Dateien befinden sich in diesem Unterordner.

## <a name="build-a-u-sql-database-project"></a>Erstellen eines U-SQL-Datenbankprojekts

Die Buildausgabe für ein U-SQL-Datenbankprojekt ist ein U-SQL-Datenbankbereitstellungspaket. Dieses Paket erhält das Suffix `.usqldbpack`. Beim `.usqldbpack`-Paket handelt es sich um eine ZIP-Datei, die alle DDL-Anweisungen in einem einzigen U-SQL-Skript im DDL-Ordner enthält. Alle integrierten DLL-Dateien und zusätzlichen Dateien für Assemblys befinden sich im temp-Ordner.

## <a name="deploy-a-u-sql-database"></a>Bereitstellen einer U-SQL-Datenbank

Das `.usqldbpack`-Paket kann in einem lokalen Konto oder in einem Azure Data Lake Analytics-Konto bereitgestellt werden. Verwenden Sie Visual Studio oder das Bereitstellungs-SDK.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Bereitstellen einer U-SQL-Datenbank in Visual Studio

Sie können eine U-SQL-Datenbank mithilfe eines U-SQL-Datenbankprojekts oder eines `.usqldbpack`-Pakets in Visual Studio bereitstellen.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Bereitstellen mithilfe eines U-SQL-Datenbankprojekts

1. Klicken Sie mit der rechten Maustaste auf das U-SQL-Datenbankprojekt, und wählen Sie dann **Bereitstellen** aus.

1. Wählen Sie im Assistenten **U-SQL-Datenbank bereitstellen** das **ADLA-Konto** aus, in dem Sie die Datenbank bereitstellen möchten. Sowohl lokale Konten als auch ADLA-Konten werden unterstützt.

1. Die **Datenbankquelle** wird automatisch ausgefüllt. Sie verweist auf das USQLDBPACK-Paket im Buildausgabeordner des Projekts.

1. Geben Sie unter **Datenbankname** einen Namen ein, um eine Datenbank zu erstellen. Wenn bereits eine Datenbank mit demselben Namen im Azure Data Lake Analytics-Zielkonto vorhanden ist, werden alle im Datenbankprojekt definierten Objekte erstellt, ohne dass die Datenbank neu erstellt wird.

1. Klicken Sie auf **Senden**, um die U-SQL-Datenbank bereitzustellen. Alle Ressourcen wie z.B. Assemblys und zusätzliche Dateien werden hochgeladen. Ein U-SQL-Auftrag, der alle DDL-Anweisungen enthält, wird übermittelt.

   ![Data Lake Tools für Visual Studio: Bereitstellen eines U-SQL-Datenbankprojekts](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake Tools für Visual Studio: Assistent zum Bereitstellen eines U-SQL-Datenbankprojekts](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Bereitstellen einer U-SQL-Datenbank in Azure DevOps

`PackageDeploymentTool.exe` enthält die Programmier- und Befehlszeilenschnittstellen, über die U-SQL-Datenbanken bereitgestellt werden können. Das SDK ist im [U-SQL SDK-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) enthalten und befindet sich unter `build/runtime/PackageDeploymentTool.exe`.

In Azure DevOps können Sie einen Befehlszeilentask und dieses SDK verwenden, um eine Automatisierungspipeline für die Aktualisierung der U-SQL-Datenbank einzurichten. [Erfahren Sie mehr über das SDK und das Einrichten einer CI/CD-Pipeline für die Bereitstellung von U-SQL-Datenbanken](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten einer CI/CD-Pipeline für Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Testen des Azure Data Lake Analytics-Codes](data-lake-analytics-cicd-test.md)
- [Ausführen von U-SQL-Skripts auf dem lokalen Computer](data-lake-analytics-data-lake-tools-local-run.md)
