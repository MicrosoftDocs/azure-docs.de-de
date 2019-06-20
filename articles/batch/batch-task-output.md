---
title: Beibehalten von Ergebnissen oder Protokollen von abgeschlossenen Aufträgen und Tasks in einem Datenspeicher – Azure Batch | Microsoft-Dokumentation
description: Lernen Sie verschiedene Optionen zum Beibehalten von Ausgabedaten von Batch-Tasks und -Aufträgen kennen. Sie können Daten in Azure Storage oder in einem anderen Datenspeicher beibehalten.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc579cd372616563b61e5ba04fe32612f3efb1c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549943"
---
# <a name="persist-job-and-task-output"></a>Persistente Aufträge und Aufgabenausgabe

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Einige gängige Beispiele der Taskausgabe sind unter anderem:

- Erstellte Dateien, wenn die Aufgabe Eingabedaten verarbeitet
- Protokolldateien, die mit der Taskausführung in Verbindung stehen

In diesem Artikel werden verschiedene Optionen für die Beibehaltung der Taskausgabe beschrieben.

## <a name="options-for-persisting-output"></a>Optionen für das Beibehalten der Ausgabe

Je nach Szenario gibt es ein paar unterschiedliche Ansätze, die Sie ergreifen können, um die Taskausgabe beizubehalten:

- [Verwenden der Batch-Dienst-API](batch-task-output-files.md)  
- [Verwenden der Batch-Dateikonventionenbibliothek für .NET](batch-task-output-file-conventions.md)  
- Implementieren Sie den Batch-Dateikonventionenstandard in Ihrer Anwendung.
- Implementieren Sie eine benutzerdefinierte Dateibewegungslösung.

Die folgenden Abschnitte enthalten eine kurze Erläuterung des jeweiligen Ansatzes sowie allgemeine Entwurfsüberlegungen für die Beibehaltung der Ausgabe.

### <a name="use-the-batch-service-api"></a>Verwenden der Batch-Dienst-API

Der Batch-Dienst unterstützt die Angabe von Ausgabedateien in Azure Storage für Taskdaten, wenn Sie [einen Task einem Auftrag hinzufügen](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) oder [eine Sammlung von Tasks einem Auftrag hinzufügen](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Weitere Informationen zum Beibehalten der Taskausgabe mit der Batch-Dienst-API, finden Sie unter [Persist task data to Azure Storage with the Batch service API (Beibehalten von Taskdaten in Azure Storage mit der Batch-Dienst-API)](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Verwenden der Batch-Dateikonventionenbibliothek für .NET

Batch definiert einen optionalen Satz von Konventionen zum Benennen von Taskausgabedateien in Azure Storage. Der [Batch-Dateikonventionenstandard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) beschreibt diese Konventionen. Der Dateikonventionenstandard bestimmt die Namen des Zielcontainers und den Blobpfad in Azure Storage für eine gegebene Ausgabedatei, die auf den Namen des Auftrags und der Aufgabe basiert.

Es liegt an Ihnen, ob Sie Dateikonventionenstandard für die Benennung Ihrer Ausgabedatendateien verwenden. Sie können auch den Zielcontainer und -blob benennen, wie Sie möchten. Wenn sie den Dateikonventionenstandard zum Benennen von Ausgabedateien verwenden, sind Ihre Ausgabedateien zur Anzeige im [Azure-Portal][portal] verfügbar.

Entwickler, die Batch-Lösungen mit C# und .NET erstellen, können die [Dateikonventionenbibiothek für .NET][nuget_package] verwenden, um Taskdaten in einem Azure Storage-Konto auf Grundlage des [Batch-Dateikonventionenstandards](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) beizubehalten. Die Dateikonventionenbibliothek behandelt das Verschieben von Ausgabedateien zu Azure Storage und das Benennen von Zielcontainern und Blobs auf bekannte Weise.

Informationen zum Beibehalten von Taskausgaben mit der Dateikonventionenbibliothek für .NET finden Sie unter [Beibehalten von Auftrags- und Taskdaten in Azure Storage mit der Batch-Dateikonventionenbibliothek für .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementieren des Batch-Konventionenstandards

Wenn Sie eine andere Sprache als .NET verwenden, können Sie den [Batch-Dateikonventionenstandard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) in Ihrer eigenen Anwendung implementieren.

Möglicherweise möchten Sie den Dateikonventionen-Benennungsstandard selbst implementieren, wenn Sie ein bewährtes Benennungsschema wollen oder die Taskausgabe im Azure-Portal anzeigen möchten.

### <a name="implement-a-custom-file-movement-solution"></a>Implementieren einer benutzerdefinierten Lösung zur Dateiverschiebung

Sie können auch Ihre eigene vollständige Lösung zur Dateiverschiebung implementieren. Nutzen Sie dieses Verfahren, wenn:

- Sie Taskdaten in einem Datenspeicher beibehalten möchten, der nicht Azure Storage ist. Um Dateien in einen Datenspeicher wie Azure SQL oder Azure Data Lake hochzuladen, können Sie ein benutzerdefiniertes Skript oder eine ausführbare Datei erstellen, um Dateien in diesen Speicherort zu laden. Sie können sie dann über die Befehlszeile aufrufen, nachdem Sie Ihre erste ausführbare Datei ausgeführt haben. Sie können z.B. auf einem Windows-Knoten möglicherweise diese beiden Befehle aufrufen: `doMyWork.exe && uploadMyFilesToSql.exe`
- Sie Prüfungspunkte oder einen frühen Upload erster Ergebnisse ausführen möchten.
- Sie präzise Kontrolle über die Fehlerbehandlung beibehalten möchten. Sie möchten z.B. Ihre eigene Lösung implementieren, wenn sie Taskabhängigkeitsaktionen verwenden wollen, um bestimmte Uploadaktionen auf Grundlage bestimmter Exitcodes für Tasks auszuführen. Weitere Informationen zu Taskabhängigkeitsaktionen finden Sie unter [Erstellen von Taskabhängigkeiten zum Ausführen von Tasks, die von anderen Tasks abhängen](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Entwurfsüberlegungen zum Beibehalten der Ausgabe

Berücksichtigen Sie beim Entwerfen Ihrer Batch-Lösung folgende Faktoren, die die Ausgabe von Aufträgen und Tasks betreffen.

- **Lebensdauer der Computeknoten**: Computeknoten sind häufig vorübergehender Natur, insbesondere in Pools mit aktivierter automatischer Skalierung. Die Ausgabe einer Aufgabe, die auf einem Knoten ausgeführt wird, ist nur verfügbar, solange der Knoten vorhanden ist, und nur für die Dateiaufbewahrungszeit, die Sie für den Task festgelegt haben. Wenn eine Aufgabe eine Ausgabe produziert, die möglicherweise nach Abschluss der Aufgabe erforderlich ist, muss die Aufgabe diese Ausgabedateien in einen permanenten Speicher hochladen, z.B. Azure Storage.

- **Ausgabespeicher**: Azure Storage wird als Datenspeicher für die Taskausgabe empfohlen, aber Sie können auch jeden anderen persistenten Speicher verwenden. Das Schreiben der Taskausgabe in Azure Storage ist in der Batch-Dienst-API enthalten. Wenn Sie eine andere Form des permanenten Speichers verwenden, müssen Sie die Anwendungslogik selbst schreiben, um die Taskausgabe beizubehalten.

- **Abrufen der Ausgabe**: Sie können die Taskausgabe direkt aus den Computeknoten im Pool oder von Azure Storage einem anderen Datenspeicher beibehalten, wenn Sie über eine persistente Taskausgabe verfügen. Um die Ausgabe eines Tasks direkt von einem Computeknoten abzurufen, benötigen Sie den Dateinamen und den Ausgabespeicherort auf dem Knoten. Wenn Sie die Taskausgabe in Azure Storage beibehalten, benötigen Sie den vollständigen Pfad zur Datei in Azure Storage, um die Ausgabedateien mit dem Azure Storage SDK herunterzuladen.

- **Anzeigen der Ausgabe**: Wenn Sie im Azure-Portal zu einem Batch-Task navigieren und **Dateien auf Knoten** auswählen, werden Ihnen alle Dateien angezeigt, die mit dem Task verknüpft sind (nicht nur die Ausgabedateien, die Sie interessieren). Wie bereits erwähnt, sind Dateien auf Computeknoten nur verfügbar, solange der Knoten vorhanden ist, und nur für die Dateiaufbewahrungszeit, die Sie für den Task festgelegt haben. Um die Taskausgabe anzuzeigen, die Sie in Azure Storage beibehalten haben, können Sie das Azure-Portal oder eine Azure Storage-Clientanwendung wie z.B. den [Azure Storage-Explorer][storage_explorer] verwenden. Um Ausgabedaten in Azure Storage mit dem Portal oder einem anderen Tool anzuzeigen, müssen Sie den Speicherort der Datei kennen und direkt dorthin navigieren.

## <a name="next-steps"></a>Nächste Schritte

- Untersuchen Sie mithilfe der neuen Funktionen in der Batch-Dienst-API das Beibehalten von Daten unter [Persist task data to Azure Storage with the Batch service API (Beibehalten von Taskdaten in Azure Storage mit der Batch-Dienst-API)](batch-task-output-files.md).
- Informationen zur Verwendung der Batch-Dateikonventionenbibliothek für .NET finden Sie unter [Beibehalten von Auftrags- und Taskdaten in Azure Storage mit der Batch-Dateikonventionenbibliothek für .NET](batch-task-output-file-conventions.md).
- Sehen Sie sich das Beispielprojekt [PersistOutputs][github_persistoutputs] auf GitHub an, das zeigt, wie die Batch-Clientbibliothek für .NET und die Dateikonventionenbibliothek für .NET verwendet werden, um die Taskausgabe im dauerhaften Speicher beizubehalten.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
