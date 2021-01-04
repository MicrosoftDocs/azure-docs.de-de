---
title: Speicherorte zum Speichern und Schreiben von Experimentdateien
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wo Sie Ihre Eingabe- und Ausgabedateien speichern sollten, um Speicherlimits einzuhalten und die Latenz von Experimenten zu minimieren.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 49e1e9efbd6f59bd037a8033f83836bf7fc71c43
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630327"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Verzeichnisse zum Speichern und Schreiben von Dateien für Azure Machine Learning-Experimente


In diesem Artikel erfahren Sie, wo Sie Ihre Eingabedateien speichern sollten, und wohin Sie Ausgabedateien Ihrer Experimente schreiben sollten, um Speicherlimitfehler und Experimentlatenz zu vermeiden.

Bei Beginn von Trainingsausführungen auf einem [Computeziel](concept-compute-target.md) sind sie von externen Umgebungen isoliert. Der Zweck dieses Entwurfs besteht darin, die Reproduzierbarkeit und Portabilität des Experiments sicherzustellen. Wenn Sie dasselbe Skript auf demselben bzw. einem anderen Computeziel zweimal ausführen, erhalten Sie die gleichen Ergebnisse. Mit diesem Entwurf können Sie Computeziele als zustandslose Berechnungsressourcen betrachten, die jeweils keine Affinität zu den Aufträgen aufweisen, die nach deren Abschluss ausgeführt werden.

## <a name="where-to-save-input-files"></a>Speicherort von Eingabedateien

Bevor Sie ein Experiment auf einem Computeziel oder Ihrem lokalen Computer initiieren können, müssen Sie sicherstellen, dass die erforderlichen Dateien, z.B. Abhängigkeitsdateien und Datendateien, die Ihr Code zum Ausführen benötigt, für das Computeziel verfügbar sind.

Zum Ausführen von Trainingsskripts wird von Azure Machine Learning das gesamte Quellverzeichnis kopiert. Sind vertrauliche Daten vorhanden, die nicht hochgeladen werden sollen, verwenden Sie die [IGNORE-Datei](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots), oder platzieren Sie sie nicht im Quellverzeichnis. Greifen Sie stattdessen mit einem [Datenspeicher](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py) auf Ihre Daten zu.

Das Speicherlimit für Momentaufnahmen des Experiments liegt bei 300 MB und/oder 2000 Dateien.

Aus diesem Grund empfehlen wir Folgendes:

* **Speichern Sie Ihre Dateien in einem Azure Machine Learning-[Datenspeicher](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py).** Dadurch werden Probleme mit Wartezeiten beim Experiment vermieden, und Sie erhalten die Vorteile des Datenzugriffs von einem Remotecomputeziel aus, was bedeutet, dass Funktionen wie die Authentifizierung und die Einbindung von Azure Machine Learning verwaltet werden. Weitere Informationen zum Angeben eines Datenspeichers als Ihr Quellverzeichnis und Hochladen von Dateien in Ihren Datenspeicher finden Sie im Artikel [Zugreifen auf Daten aus Ihren Datenspeichern](how-to-access-data.md).

* **Wenn Sie nur wenige Datendateien und Abhängigkeitsskripts benötigen und keinen Datenspeicher verwenden können,** legen Sie die Dateien in demselben Ordnerverzeichnis wie Ihr Trainingsskript ab. Geben Sie diesen Ordner direkt in Ihrem Trainingsskript als Ihr `source_directory` an, oder in dem Code, der Ihr Trainingsskript aufruft.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Speicherlimits von Experimentmomentaufnahmen

Für Experimente erstellt Azure Machine Learning automatisch eine Experimentmomentaufnahme Ihres Codes basierend auf dem Verzeichnis, das Sie bei der Konfiguration der Ausführung vorschlagen. Hierfür gilt ein Grenzwert von insgesamt 300MB und/oder 2.000 Dateien. Wenn Sie diese Grenze überschreiten, sehen Sie folgende Fehlermeldung:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Speichern Sie Ihre Experimentdateien in einem Datenspeicher, um diesen Fehler zu beheben. Wenn Sie keinen Datenspeicher verwenden können, bietet die folgende Tabelle mögliche alternative Lösungen.

Experimentbeschreibung &nbsp;|Lösung zum Speicherlimit
---|---
Weniger als 2000 Dateien und Verwendung eines Datenspeichers nicht möglich| Setzen Sie die Größenbeschränkung für die Momentaufnahme folgendermaßen außer Kraft: <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Dies kann je nach Anzahl und Größe der Dateien mehrere Minuten dauern.
Spezifisches Skriptverzeichnis muss verwendet werden| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Pipeline|Verwenden Sie für jeden Schritt ein anderes Unterverzeichnis.
Jupyter Notebooks| Erstellen Sie eine `.amlignore`-Datei, oder verschieben Sie Ihr Notebook in ein neues, leeres Unterverzeichnis, und führen Sie den Code erneut aus.

## <a name="where-to-write-files"></a>Speicherort zum Schreiben von Dateien

Aufgrund der Isolation von Trainingsexperimenten werden die während Ausführungen auftretenden Änderungen an Dateien nicht unbedingt außerhalb Ihrer Umgebung beibehalten. Wenn Ihr Skript die lokalen zu berechnenden Dateien ändert, werden die Änderungen für Ihre nächste Experimentausführung nicht beibehalten und auch nicht automatisch an den Clientcomputer zurückgeleitet. Darum sollten die während der ersten Ausführung des Experiments vorgenommenen Änderungen sich nicht auf die Änderungen der zweiten Ausführung auswirken.

Wenn Sie Änderungen vornehmen, wird empfohlen, Dateien in einen Azure Machine Learning-Datenspeicher zu schreiben. Weitere Informationen finden Sie unter [Zugreifen auf Daten aus Ihren Datenspeichern](how-to-access-data.md).

Wenn Sie keinen Datenspeicher benötigen, schreiben Sie Dateien in die Ordner `./outputs` und/oder `./logs`.

>[!Important]
> Zwei Ordner, *outputs* und *logs*, erhalten eine besondere Behandlung durch Azure Machine Learning. Wenn Sie während des Trainings Dateien in die Ordner `./outputs` und `./logs` schreiben, werden diese Dateien automatisch in Ihren Ausführungsverlauf hochgeladen, damit Sie Zugriff darauf haben, wenn die Ausführung abgeschlossen ist.

* **Für Ausgabe wie Statusmeldungen oder Bewertungsergebnisse** schreiben Sie Dateien in den Ordner `./outputs`, sodass diese als Artefakte im Verlauf beibehalten werden. Achten Sie auf die Anzahl und Größe der Dateien, die in diesen Ordner geschrieben werden, weil beim Hochladen der Inhalte in den Ausführungsverlauf Wartezeiten entstehen können. Wenn die Wartezeit ein Problem darstellt, wird das Schreiben von Dateien in einen Datenspeicher empfohlen.

* **Um geschriebene Dateien als Protokolle im Ausführungsverlauf zu speichern**, schreiben Sie Dateien in den Ordner `./logs`. Die Protokolle werden in Echtzeit hochgeladen, sodass diese Methode für das Streamen von Liveupdates von einer Remoteausführung geeignet ist.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Zugreifen auf Daten aus Ihren Datenspeichern](how-to-access-data.md).

* Erfahren Sie mehr über das [Erstellen von Computezielen für Modelltraining und -bereitstellung](how-to-create-attach-compute-studio.md)