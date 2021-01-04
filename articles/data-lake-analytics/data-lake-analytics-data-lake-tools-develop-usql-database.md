---
title: Entwickeln eines U-SQL-Datenbankprojekts – Azure Data Lake
description: Erfahren Sie, wie Sie eine U-SQL-Datenbank mithilfe von Azure Data Lake Tools für Visual Studio entwickeln.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 706457a602e20dd37e64e5f389948b351ac8ebcf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015246"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Entwickeln einer U-SQL-Datenbank für Azure Data Lake mithilfe eines U-SQL-Datenbankprojekts

U-SQL-Datenbank ermöglicht strukturierte Ansichten von unstrukturierten Daten und verwalteten strukturierten Daten in Tabellen. Darüber hinaus umfasst U-SQL-Datenbank ein allgemeines Metadatenkatalogsystem zum Organisieren von strukturierten Daten und benutzerdefiniertem Code. Über die Datenbank werden diese verbundenen Objekte konzeptionell gruppiert.

Erfahren Sie mehr über die [U-SQL-Datenbank und Data Definition Language (DDL)](/u-sql/data-definition-language-ddl-statements).

Das U-SQL-Datenbankprojekt ist ein Projekttyp in Visual Studio, mit dem Entwickler U-SQL-Datenbanken schnell und einfach entwickeln, verwalten und bereitstellen können.

## <a name="create-a-u-sql-database-project"></a>Erstellen eines U-SQL-Datenbankprojekts

In Azure Data Lake Tools für Visual Studio nach Version 2.3.3000.0 wurde eine neue Projektvorlage für U-SQL-Datenbankprojekte hinzugefügt. Klicken Sie zum Erstellen eines U-SQL-Projekts auf **Datei > Neu > Projekt**. Das U-SQL-Datenbankprojekt befindet sich unter **Azure Data Lake > Knoten „U-SQL“** .

![Data Lake Tools für Visual Studio: Erstellen eines U-SQL-Datenbankprojekts](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png)

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Entwickeln von U-SQL-Datenbankobjekten mithilfe eines Datenbankprojekts

Klicken Sie mit der rechten Maustaste auf das U-SQL-Datenbankprojekt. Klicken Sie anschließend auf **Hinzufügen > Neues Element**. Sie finden alle neuen unterstützten Objekttypen im Assistenten **Neues Element hinzufügen**.

Für ein Nicht-Assemblyobjekt (etwa eine Tabellenwertfunktion) wird nach dem Hinzufügen eines neuen Elements ein neues U-SQL-Skript erstellt. Dann können Sie die DDL-Anweisung für das Objekt im Editor entwickeln.

Für ein Assemblyobjekt umfasst das Tool einen benutzerfreundlichen Benutzeroberflächen-Editor, in dem Sie die Assembly registrieren und DLL-Dateien und andere zusätzliche Dateien bereitstellen können. Die folgenden Schritte veranschaulichen, wie Sie dem U-SQL-Datenbankprojekt eine Assemblyobjektdefinition hinzufügen:

1. Fügen Sie Verweise zum C#-Projekt hinzu, die UDO/UDAG/UDF für das U-SQL-Datenbankprojekt enthalten.

   ![Data Lake Tools für Visual Studio Code: Hinzufügen eines Verweises](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png)

   ![Data Lake Tools für Visual Studio: Hinzufügen eines Verweises des U-SQL-Datenbankprojekts](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2. Wählen Sie in der Entwurfsansicht der Assembly im Dropdownmenü **Assembly aus Verweis erstellen** die referenzierte Assembly aus.

   ![Data Lake Tools für Visual Studio: Erstellen einer Assembly aus einem Verweis](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3. Fügen Sie **verwaltete Abhängigkeiten** und **zusätzliche Dateien** hinzu, sofern vorhanden. Beim Hinzufügen zusätzlicher Dateien verwendet das Tool den relativen Pfad, um sicherzustellen, dass die Assemblys später auf Ihrem lokalen Computer und dem Buildcomputer gefunden werden.

@_DeployTempDirectory ist eine vordefinierte Variable, die das Tool auf den Buildausgabeordner verweist. Im Buildausgabeordner verfügt jede Assembly über einen Unterordner, der den gleichen Namen wie die Assembly besitzt. Alle DLLs und zusätzlichen Dateien befinden sich in diesem Unterordner.

## <a name="build-a-u-sql-database-project"></a>Erstellen eines U-SQL-Datenbankprojekts

Die Buildausgabe für ein U-SQL-Datenbankprojekt ist ein U-SQL-Datenbankbereitstellungspaket, das mit dem Suffix `.usqldbpack` benannt ist. Das Paket `.usqldbpack` ist eine ZIP-Datei und enthält alle DDL-Anweisungen in einem einzelnen U-SQL-Skript im Ordner **DDL** sowie alle DLLs und zusätzlichen Dateien für Assemblys im Ordner **Temp**.

Erfahren Sie mehr über das [Erstellen eines U-SQL-Datenbankprojekts mit der MSBuild-Befehlszeile und einer Azure DevOps Services-Buildaufgabe](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Bereitstellen einer U-SQL-Datenbank

Das Paket vom Typ „.usqldbpack“ kann mit Visual Studio oder dem Bereitstellungs-SDK in einem lokalen Konto oder in einem Azure Data Lake Analytics-Konto bereitgestellt werden.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Bereitstellen einer U-SQL-Datenbank in Visual Studio

Sie können eine U-SQL-Datenbank über ein U-SQL-Datenbankprojekt oder ein .usqldbpack-Paket in Visual Studio bereitstellen.

#### <a name="deploy-through-a-u-sql-database-project"></a>Bereitstellen über ein U-SQL-Datenbankprojekt

1. Klicken Sie mit der rechten Maustaste auf das U-SQL-Datenbankprojekt, und wählen Sie dann **Bereitstellen** aus.

1. Wählen Sie im Assistenten **U-SQL-Datenbank bereitstellen** das **ADLA-Konto** aus, in dem Sie die Datenbank bereitstellen möchten. Sowohl lokale Konten als auch ADLA-Konten werden unterstützt.

1. **Datenbankquelle** wird automatisch ausgefüllt und verweist auf das Paket vom Typ „.usqldbpack“ im Buildausgabeordner des Projekts.

1. Geben Sie unter **Datenbankname** einen Namen ein, um eine Datenbank zu erstellen. Wenn bereits eine Datenbank mit demselben Namen im Azure Data Lake Analytics-Zielkonto vorhanden ist, werden alle im Datenbankprojekt definierten Objekte erstellt, ohne dass die Datenbank neu erstellt wird.

1. Klicken Sie auf **Senden**, um die U-SQL-Datenbank bereitzustellen. Alle Ressourcen (Assemblys und zusätzliche Dateien) werden hochgeladen, und ein U-SQL-Auftrag mit allen gesendeten DDL-Anweisungen wird gesendet.

   ![Data Lake Tools für Visual Studio: Bereitstellen eines U-SQL-Datenbankprojekts](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake Tools für Visual Studio: Assistent zum Bereitstellen eines U-SQL-Datenbankprojekts](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Bereitstellen über ein U-SQL-Datenbankbereitstellungspaket

1. Öffnen Sie **Server-Explorer**. Erweitern Sie anschließend das **Azure Data Lake Analytics-Konto**, in dem Sie die Datenbank bereitstellen möchten.

1. Klicken Sie mit der rechten Maustaste auf **U-SQL-Datenbanken**, und wählen Sie **Datenbank bereitstellen** aus.

1. Legen Sie **Datenbankquelle** auf den Pfad des U-SQL-Datenbankbereitstellungspakets (.usqldbpack-Datei) fest.

1. Geben Sie unter **Datenbankname** den Namen ein, um eine Datenbank zu erstellen. Wenn bereits eine Datenbank mit demselben Namen im Azure Data Lake Analytics-Zielkonto vorhanden ist, werden alle im Datenbankprojekt definierten Objekte erstellt, ohne dass die Datenbank neu erstellt wird.

   ![Data Lake Tools für Visual Studio: Bereitstellen eines U-SQL-Datenbankpakets](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

   ![Data Lake Tools für Visual Studio: Assistent zum Bereitstellen eines U-SQL-Datenbankpakets](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Bereitstellen einer U-SQL-Datenbank mit dem SDK

`PackageDeploymentTool.exe` enthält die Programmier- und Befehlszeilenschnittstellen, über die U-SQL-Datenbanken bereitgestellt werden können. Das SDK ist im [U-SQL SDK-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) enthalten und befindet sich unter `build/runtime/PackageDeploymentTool.exe`.

[Erfahren Sie mehr über das SDK und zum Einrichten einer CI/CD-Pipeline für die Bereitstellung von U-SQL-Datenbanken](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Verweisen auf ein U-SQL-Datenbankprojekt

Ein U-SQL-Projekt kann auf ein U-SQL-Datenbankprojekt verweisen. Der Verweis wirkt sich auf zwei Workloads aus:

- *Projekterstellung:* Richten Sie die Umgebungen für die referenzierten Datenbanken vor dem Erstellen der U-SQL-Skripts ein.
- *Lokale Ausführung im Konto „(Local-project)“:* Die referenzierten Datenbankumgebungen werden vor dem Erstellen der U-SQL-Skripts im Konto „(Local-project)“ bereitgestellt. [Hier](data-lake-analytics-data-lake-tools-local-run.md) erfahren Sie mehr über die lokale Ausführung und den Unterschied zwischen dem Konto „(Local-machine)“ und dem Konto „(Local-project)“.

### <a name="how-to-add-a-u-sql-database-reference"></a>So fügen Sie einen U-SQL-Datenbankverweis hinzu

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das U-SQL-Projekt, und wählen Sie **U-SQL-Datenbankverweis hinzufügen** aus.

    ![Data Lake Tools für Visual Studio: Hinzufügen eines Datenbankprojektverweises](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfigurieren Sie den Datenbankverweis aus einem U-SQL-Datenbankprojekt in der aktuellen Projektmappe oder einer U-SQL-Datenbankpaketdatei.

3. Geben Sie den Namen für die Datenbank an.

    ![Data Lake Tools für Visual Studio, Assistent zum Hinzufügen eines Datenbankprojektverweises](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten einer CI/CD-Pipeline für Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Testen des Azure Data Lake Analytics-Codes](data-lake-analytics-cicd-test.md)
- [Ausführen von U-SQL-Skripts auf dem lokalen Computer](data-lake-analytics-data-lake-tools-local-run.md)
