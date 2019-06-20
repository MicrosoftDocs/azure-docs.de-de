---
title: Verhindern von Speicherlimits und Experimentlatenz mit Eingabe- und Ausgabeverzeichnissen
description: In diesem Artikel erfahren Sie, wo Sie Ihre Experimenteingabedateien speichern sollten, und wohin Sie Ausgabedateien schreiben sollten, um Speicherlimits und Experimentlatenz zu vermeiden.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 28d8c47db8ea9c8a51bc8e9deb0a689eb0b20177
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392907"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Verzeichnisse zum Speichern und Schreiben von Dateien für Azure Machine Learning-Experimente

In diesem Artikel erfahren Sie, wo Sie Ihre Eingabedateien speichern sollten, und wohin Sie Ausgabedateien Ihrer Experimente schreiben sollten, um Speicherlimitfehler und Experimentlatenz zu vermeiden.

Bei Beginn von Trainingsausführungen auf einem [Computeziel](how-to-set-up-training-targets.md) sind sie von externen Umgebungen isoliert. Der Zweck dieses Entwurfs besteht darin, die Reproduzierbarkeit und Portabilität des Experiments sicherzustellen. Wenn Sie dasselbe Skript auf demselben bzw. einem anderen Computeziel zweimal ausführen, erhalten Sie die gleichen Ergebnisse. Mit diesem Entwurf können Sie Computeziele als zustandslose Berechnungsressourcen betrachten, die jeweils keine Affinität zu den Aufträgen aufweisen, die nach deren Abschluss ausgeführt werden.

## <a name="where-to-save-input-files"></a>Speicherort von Eingabedateien

Bevor Sie ein Experiment auf einem Computeziel oder Ihrem lokalen Computer initiieren können, müssen Sie sicherstellen, dass die erforderlichen Dateien, z.B. Abhängigkeitsdateien und Datendateien, die Ihr Code zum Ausführen benötigt, für das Computeziel verfügbar sind.

Azure Machine Learning führt Trainingsskripts durch Kopieren des gesamten Skriptordners in den Computezielkontext aus und erstellt anschließend eine Momentaufnahme. Das Speicherlimit für Momentaufnahmen des Experiments liegt bei 300 MB und/oder 2000 Dateien.

Aus diesem Grund empfehlen wir Folgendes:

* **Speichern Sie Ihre Dateien in einem Azure Machine Learning-[Datenspeicher](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Dadurch werden Probleme mit Wartezeiten beim Experiment vermieden, und Sie erhalten die Vorteile des Datenzugriffs von einem Remotecomputeziel aus, was bedeutet, dass Funktionen wie die Authentifizierung und die Einbindung von Azure Machine Learning Service verwaltet werden. Weitere Informationen zum Angeben eines Datenspeichers als Ihr Quellverzeichnis und Hochladen von Dateien in Ihren Datenspeicher finden Sie im Artikel [Zugreifen auf Daten aus Ihren Datenspeichern](how-to-access-data.md).

* **Wenn Sie nur wenige Datendateien und Abhängigkeitsskripts benötigen und keinen Datenspeicher verwenden können,** legen Sie die Dateien in demselben Ordnerverzeichnis wie Ihr Trainingsskript ab. Geben Sie diesen Ordner direkt in Ihrem Trainingsskript als Ihr `source_directory` an, oder in dem Code, der Ihr Trainingsskript aufruft.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Speicherlimits von Experimentmomentaufnahmen

Für Experimente erstellt Azure Machine Learning automatisch eine Experimentmomentaufnahme Ihres Codes basierend auf dem Verzeichnis, das Sie bei der Konfiguration der Ausführung vorschlagen. Hierfür gilt ein Grenzwert von insgesamt 300MB und/oder 2.000 Dateien. Wenn Sie diese Grenze überschreiten, sehen Sie folgende Fehlermeldung:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Speichern Sie Ihre Experimentdateien in einem Datenspeicher, um diesen Fehler zu beheben. Wenn Sie keinen Datenspeicher verwenden können, bietet die folgende Tabelle mögliche alternative Lösungen.

Experimentbeschreibung|Lösung zum Speicherlimit
---|---
Weniger als 2000 Dateien und Verwendung eines Datenspeichers nicht möglich| Setzen Sie die Größenbeschränkung für die Momentaufnahme folgendermaßen außer Kraft: <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Dies kann je nach Anzahl und Größe der Dateien mehrere Minuten dauern.
Spezifisches Skriptverzeichnis muss verwendet werden| Erstellen Sie eine `.amlignore`-Datei zum Ausschließen von Dateien aus der Momentaufnahme des Experiments, die nicht Teil des Quellcodes sind. Fügen Sie der `.amlignore`-Datei die Dateinamen hinzu, und legen Sie die Datei in demselben Verzeichnis wie Ihr Trainingsskript ab. Für die `.amlignore`-Datei werden [dieselbe Syntax und dieselben Muster](https://git-scm.com/docs/gitignore) wie für eine `.gitignore`-Datei verwendet.
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

* Erfahren Sie mehr über das [Einrichten von Computezielen für das Modelltraining](how-to-set-up-training-targets.md).
