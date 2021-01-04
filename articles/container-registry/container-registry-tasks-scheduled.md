---
title: Tutorial – Planen eines ACR Tasks
description: In diesem Tutorial erfahren Sie, wie Sie einen Azure Container Registry Task nach einem definierten Zeitplan auszuführen, indem Sie einen oder mehrere Zeitgebertrigger festlegen.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 13a4ccac4ea97538583c1c063a6dc61e4d25686a
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030610"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Tutorial: Ausführen eines ACR Tasks nach einem definierten Zeitplan

Dieses Tutorial zeigt Ihnen, wie Sie einen [ACR Task](container-registry-tasks-overview.md) nach einem Zeitplan ausführen. Planen Sie einen Task, indem Sie einen oder mehrere *Zeitgebertrigger* einrichten. Zeitgebertrigger können allein oder in Kombination mit anderen Tasktriggern verwendet werden.

In diesem Tutorial erfahren Sie mehr über das Planen von Tasks und:

> [!div class="checklist"]
> * Erstellen eines Tasks mit einem Zeitgebertrigger
> * Verwalten von Zeitgebertriggern

Das Planen eines Tasks ist für Szenarien wie die folgenden nützlich:

* Ausführen einer Containerworkload für geplante Wartungsarbeiten. Führen Sie beispielsweise eine containerisierte Anwendung aus, um nicht benötigte Images aus Ihrer Registrierung zu entfernen.
* Führen Sie während des Arbeitstages im Rahmen Ihrer Liveüberwachung eine Reihe von Tests an einem Produktionsimage durch.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Informationen zum Planen eines Tasks

* **Trigger mit Cron-Ausdruck** – Der Zeitgebertrigger für einen Task verwendet einen *Cron-Ausdruck*. Der Ausdruck ist eine Zeichenfolge mit fünf Feldern, die die Minute, Stunde, den Tag, Monat und Wochentag angeben, um den Task auszulösen. Es werden Häufigkeiten von bis zu einmal pro Minute unterstützt.

  Beispielsweise löst der Ausdruck `"0 12 * * Mon-Fri"` an jedem Wochentag um 12 Uhr UTC einen Task aus. Informationen finden Sie unter den [Details](#cron-expressions) weiter unten in diesem Artikel.
* **Mehrere Zeitgebertrigger** – Das Hinzufügen mehrerer Zeitgeber zu einem Task ist erlaubt, solange die Zeitpläne unterschiedlich sind.
    * Geben Sie beim Erstellen des Tasks mehrere Zeitgebertrigger an, oder fügen Sie sie später hinzu.
    * Benennen Sie optional die Trigger zur einfacheren Verwaltung, oder ACR Tasks stellt standardmäßig Triggernamen bereit.
    * Wenn sich Zeitgeberzeitpläne bei einer Zeit überschneiden, löst ACR Tasks den Task zum geplanten Zeitpunkt für den jeweiligen Zeitgeber aus.
* **Andere Task-Trigger** – In einem per Zeitgeber ausgelösten Task können Sie auch Trigger aktivieren, die auf [Quellcode-Commit](container-registry-tutorial-build-task.md) oder [Basisimage-Updates](container-registry-tutorial-base-image-update.md) basieren. Wie andere ACR-Tasks auch, können Sie einen geplanten Task auch [manuell ausführen][az-acr-task-run].

## <a name="create-a-task-with-a-timer-trigger"></a>Erstellen eines Tasks mit einem Zeitgebertrigger

### <a name="task-command"></a>Aufgabenbefehl

Füllen Sie zunächst die folgenden Shell-Umgebungsvariablen mit geeigneten Werten für Ihre Umgebung auf. Dieser Schritt ist zwar nicht zwingend erforderlich, vereinfacht aber das Ausführen der mehrzeiligen Azure CLI-Befehle in diesem Tutorial. Wenn Sie die Umgebungsvariable nicht angeben, müssen Sie später jedes Mal die einzelnen Werte manuell ersetzen, wenn sie in einem der Beispielbefehle vorkommt.

[![Start einbetten](https://shell.azure.com/images/launchcloudshell.png "Starten von Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

Wenn Sie einen Task mit dem Befehl [az acr task create][az-acr-task-create] erstellen, können Sie optional einen Zeitgebertrigger hinzufügen. Fügen Sie den `--schedule`-Parameter hinzu,m und übergeben Sie einen Cron-Ausdruck für den Zeitgeber.

Als einfaches Beispiel löst der folgende Task die tägliche Ausführung des `hello-world`-Images aus der Microsoft Container Registry um 21:00 Uhr (UTC) aus. Der Task läuft ohne Quellcodekontext.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Führen Sie den Befehl [az acr task show][az-acr-task-show] aus, um zu sehen, ob der Timertrigger konfiguriert ist. Standardmäßig ist auch der Trigger für die Aktualisierung des Basisimages aktiviert.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Auslösen des Tasks

Lösen Sie den Task manuell mit [az acr task run][az-acr-task-run] aus, um sicherzustellen, dass er richtig eingerichtet ist:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Wenn der Container erfolgreich ausgeführt wird, ähnelt die Ausgabe der folgenden. Die Ausgabe ist zusammengefasst, um die wichtigsten Schritte darzustellen.

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Führen Sie nach der geplanten Zeit den Befehl [az acr task list-runs][az-acr-task-list-runs] aus, um sicherzustellen, dass der Zeitgeber den Task wie erwartet ausgelöst hat:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Wenn der Zeitgeber erfolgreich ist, lautet die Ausgabe wie folgt:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Verwalten von Zeitgebertriggern

Verwenden Sie die [az acr task timer][az-acr-task-timer]-Befehle, um die Zeitgebertrigger für einen ACR Task zu verwalten.

### <a name="add-or-update-a-timer-trigger"></a>Hinzufügen oder Aktualisieren eines Zeitgebertriggers

Nachdem ein Task erstellt wurde, fügen Sie optional einen Zeitgebertrigger hinzu, indem Sie den Befehl [az acr task timer add][az-acr-task-timer-add] verwenden. Im folgenden Beispiel wird dem zuvor erstellten *timertask* der Zeitgebertriggername *timer2* hinzugefügt. Dieser Zeitgeber löst den Task jeden Tag um 10:30 UTC aus.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Aktualisieren Sie den Zeitplan eines vorhandenen Triggers, oder ändern Sie seinen Status, indem Sie den Befehl [az acr task timer update][az-acr-task-timer-update] verwenden. Aktualisieren Sie beispielsweise den Trigger mit dem Namen *timer2*, um den Task um 11:30 UTC auszulösen:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Auflisten von Zeitgebertriggern

Der Befehl [az acr task timer list][az-acr-task-timer-list] zeigt die für einen Task eingerichteten Zeitgebertrigger an:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
```

Beispielausgabe:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Entfernen eines Zeitgebertriggers

Verwenden Sie den Befehl [az acr task timer remove][az-acr-task-timer-remove], um einen Zeitgebertrigger aus einem Task zu entfernen. Das folgende Beispiel entfernt den Trigger *timer2* aus *timertask*:

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-Ausdrücke

ACR Tasks verwendet die [NCronTab](https://github.com/atifaziz/NCrontab)-Bibliothek, um Cron-Ausdrücke zu interpretieren. Unterstützte Ausdrücke in ACR Tasks haben fünf Pflichtfelder, die durch Leerzeichen getrennt sind:

`{minute} {hour} {day} {month} {day-of-week}`

Die für die Cron-Ausdrücke verwendete Zeitzone ist Coordinated Universal Time (UTC). Stunden werden im 24-Stunden-Format angegeben.

> [!NOTE]
> ACR Tasks unterstützt das Feld `{second}` oder `{year}` in Cron-Ausdrücken nicht. Wenn Sie einen Cron-Ausdruck kopieren, der in einem anderen System verwendet wird, müssen Sie diese Felder entfernen, falls sie verwendet werden.

Jedes Feld kann einen der folgenden Werttypen aufweisen:

|type  |Beispiel  |Auslösung  |
|---------|---------|---------|
|Ein bestimmter Wert |<nobr>`"5 * * * *"`</nobr>|Stündliche Ausführung jeweils 5 Minuten nach der vollen Stunde|
|Alle Werte (`*`)|<nobr>`"* 5 * * *"`</nobr>|Jede Minute der Stunde ab 5:00 Uhr UTC (60-mal pro Tag)|
|Ein Bereich (`-`-Operator)|<nobr>`"0 1-3 * * *"`</nobr>|3-mal täglich, um 1:00, 2:00 und 3:00 Uhr UTC|
|Eine Gruppe von Werten (`,`-Operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3-mal pro Stunde, bei 20 Minuten, 30 Minuten und 40 Minuten nach der Stunde|
|Ein Intervallwert (`/`-Operator)|<nobr>`"*/10 * * * *"`</nobr>|6-mal pro Stunde, bei 10 Minuten, 20 Minuten usw. nach der Stunde

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron-Beispiele

|Beispiel|Auslösung  |
|---------|---------|
|`"*/5 * * * *"`|einmal alle fünf Minuten|
|`"0 * * * *"`|einmal zu jeder vollen Stunde|
|`"0 */2 * * *"`|einmal alle zwei Stunden|
|`"0 9-17 * * *"`|zwischen 9:00 und 17:00 Uhr UTC jeweils einmal pro Stunde|
|`"30 9 * * *"`|täglich um 9:30 Uhr UTC|
|`"30 9 * * 1-5"`|werktags um 9:30 Uhr UTC|
|`"30 9 * Jan Mon"`|jeden Montag im Januar um 9:30 UTC|

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie die folgenden Befehle aus, um alle Ressourcen zu entfernen, die Sie im Rahmen dieser Tutorialreihe erstellt haben (einschließlich der Containerregistrierung(en), der Containerinstanz, des Schlüsseltresors und des Dienstprinzipals):

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie Azure Container Registry Tasks erstellen, die automatisch von einem Zeitgeber ausgelöst werden. 

Ein Beispiel für die Verwendung einer geplanten Aufgabe zum Bereinigen von Repositorys in einer Registrierung finden Sie unter [Automatisches Bereinigen von Images aus einer Azure-Containerregistrierung](container-registry-auto-purge.md).

Beispiele für Tasks, die durch Quellcode-Commits oder Basisimage-Updates ausgelöst werden, finden Sie in anderen Artikeln der [Tutorialserie zu ACR Tasks](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
