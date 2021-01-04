---
title: Bereitstellen eines PyTorch-Modells als Azure Functions-Anwendung
description: Verwenden Sie ein vortrainiertes ResNet 18-Deep Neural Network von PyTorch mit Azure Functions, um einem Bild eine von 1.000 ImageNet-Bezeichnungen zuzuweisen.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 8891c29e5d8d06df6292d06ec06e5e57fb9880e7
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422840"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Tutorial: Bereitstellen eines vortrainierten Bildklassifizierungsmodells für Azure Functions mit PyTorch

In diesem Artikel erfahren Sie, wie Sie Python, PyTorch und Azure Functions verwenden, um ein vortrainiertes Modell für die Klassifizierung eines Bilds auf der Grundlage des Bildinhalts zu laden. Da Sie alles lokal durchführen und keine Azure-Ressourcen in der Cloud erstellen, fallen für das Durcharbeiten dieses Tutorials keine Kosten an.

> [!div class="checklist"]
> * Initialisieren einer lokalen Umgebung zum Entwickeln von Azure Functions in Python
> * Importieren eines vortrainierten PyTorch-Machine Learning-Modells in eine Funktions-App
> * Erstellen einer serverlosen HTTP-API, um ein Bild als eine von 1.000 ImageNet-[Klassen](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a) zu klassifizieren
> * Verwenden der API aus einer Web-App

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4 oder höher.](https://www.python.org/downloads/release/python-374/) (Python 3.8.x und Python 3.6.x können ebenfalls mit Azure Functions verwendet werden.)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Ein Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

1. Führen Sie in einem Terminal oder Befehlsfenster `func --version` aus, um zu überprüfen, ob mindestens Version 2.7.1846 der Azure Functions Core Tools verwendet wird.
1. Führen Sie `python --version` (Linux/Mac OS) oder `py --version` (Windows) aus, um zu überprüfen, ob Python-Version 3.7.x verwendet wird.

## <a name="clone-the-tutorial-repository"></a>Klonen des Tutorial-Repositorys

1. Klonen Sie in einem Terminal oder Befehlsfenster mit Git das folgende Repository:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Navigieren Sie in den Ordner, und überprüfen Sie dessen Inhalt.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *start* : Dies ist der Arbeitsordner für das Tutorial.
    - *end* : Dies ist das Endergebnis und die vollständige Implementierung zu Referenzzwecken.
    - *resources* : Enthält das Machine Learning-Modell und Hilfsbibliotheken.
    - *frontend* : Eine Website, über die die Funktions-App aufgerufen wird.

## <a name="create-and-activate-a-python-virtual-environment"></a>Erstellen und Aktivieren einer virtuellen Python-Umgebung

Navigieren Sie zum Ordner *start* , und führen Sie die folgenden Befehle aus, um eine virtuelle Umgebung mit dem Namen `.venv` zu erstellen und zu aktivieren.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Führen Sie den folgenden Befehl aus, wenn über Python das venv-Paket auf Ihrer Linux-Distribution nicht installiert wurde:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

Sie führen alle nachfolgenden Befehle in dieser aktivierten virtuellen Umgebung aus. (Führen Sie `deactivate` aus, um die virtuelle Umgebung zu beenden.)


## <a name="create-a-local-functions-project"></a>Erstellen eines lokalen Funktionsprojekts

In Azure Functions handelt es sich bei einem Funktionsprojekt um einen Container für eine oder mehrere individuelle Funktionen, die jeweils auf einen bestimmten Trigger reagieren. Für alle Funktionen eines Projekts werden die gleichen lokalen Konfigurationen und Hostkonfigurationen gemeinsam genutzt. In diesem Abschnitt erstellen Sie ein Funktionsprojekt mit einer Bausteinfunktion mit dem Namen `classify`, über die ein HTTP-Endpunkt bereitgestellt wird. In einem späteren Abschnitt fügen Sie spezifischeren Code hinzu.

1. Verwenden Sie im Ordner *start* die Azure Functions Core Tools, um eine Python-Funktions-App zu initialisieren:

    ```
    func init --worker-runtime python
    ```

    Nach der Initialisierung enthält der Ordner *start* verschiedene Dateien für das Projekt, z. B. die Konfigurationsdateien [local.settings.json](functions-run-local.md#local-settings-file) und [host.json](functions-host-json.md). Da *local.settings.json* aus Azure heruntergeladene Geheimnisse enthalten kann, wird die Datei in der *GITIGNORE* -Datei standardmäßig aus der Quellcodeverwaltung ausgeschlossen.

    > [!TIP]
    > Da ein Funktionsprojekt an eine bestimmte Runtime gebunden ist, müssen alle Funktionen des Projekts in derselben Sprache geschrieben werden.

1. Fügen Sie dem Projekt eine Funktion hinzu, indem Sie den unten angegebenen Befehl verwenden. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion, und mit dem `--template`-Argument wird der Trigger der Funktion angegeben. Mit `func new` wird ein Unterordner passend zum Funktionsnamen erstellt. Er enthält eine geeignete Codedatei für die gewählte Sprache des Projekts und eine Konfigurationsdatei mit dem Namen *function.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Mit diesem Befehl wird ein Ordner erstellt, der mit dem Namen der Funktion *classify* übereinstimmt. In diesem Ordner befinden sich zwei Dateien: *\_\_init\_\_.py* enthält den Funktionscode, und *function.json* enthält eine Beschreibung des Triggers der Funktion sowie der zugehörigen Eingabe- und Ausgabebindungen. Ausführliche Informationen zum Inhalt dieser Dateien finden Sie in der Python-Schnellstartanleitung unter [(Optional) Untersuchen des Dateiinhalts](./create-first-function-cli-python.md#optional-examine-the-file-contents).


## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

1. Starten Sie die Funktion, indem Sie den lokalen Azure Functions-Runtimehost im Ordner *start* starten:

    ```
    func start
    ```

1. Navigieren Sie zur URL ```http://localhost:7071/api/classify?name=Azure```, nachdem in der Ausgabe der `classify`-Endpunkt angezeigt wird. Die Meldung „Hello Azure!“ sollte in der Ausgabe angezeigt werden.

1. Verwenden Sie **STRG**-**C** , um den Host zu beenden.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Importieren des PyTorch-Modells und Hinzufügen von Hilfscode

Um die Funktion `classify` für die Klassifizierung eines Bilds auf der Grundlage des Bildinhalts anzupassen, wird ein vortrainiertes [ResNet](https://arxiv.org/abs/1512.03385)-Modell verwendet. Das vortrainierte Modell stammt von [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/) und klassifiziert ein Bild als eine von 1.000 [ImageNet-Klassen](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Anschließend fügen Sie dem Projekt dann Hilfscode und Abhängigkeiten hinzu.

1. Führen Sie im Ordner *start* den folgenden Befehl aus, um den Vorhersagecode und die Bezeichnungen in den Ordner *classify* zu kopieren:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Vergewissern Sie sich, dass der Ordner *classify* Dateien mit dem Namen *predict.py* und *labels.txt* enthält. Falls nicht, sollten Sie überprüfen, ob Sie den Befehl im Ordner *start* ausgeführt haben.

1. Öffnen Sie *start/requirements.txt* in einem Text-Editor, und fügen Sie die Abhängigkeiten hinzu, die für den Hilfscode benötigt werden. Diese sollten wie folgt aussehen:

    ```txt
    azure-functions
    requests
    -f https://download.pytorch.org/whl/torch_stable.html
    torch==1.5.0+cpu
    torchvision==0.6.0+cpu
    ```

1. Speichern Sie *requirements.txt* , und führen Sie anschließend im Ordner *start* den folgenden Befehl aus, um die Abhängigkeiten zu installieren:


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

Die Installation kann einige Minuten dauern. Während dieses Vorgangs können Sie mit dem Ändern der Funktion im nächsten Abschnitt fortfahren.
> [!TIP]
> >Unter Windows tritt unter Umständen ein Fehler der folgenden Art auf: „Pakete konnten nicht installiert werden. EnvironmentError: [Errno 2] Datei oder Verzeichnis nicht vorhanden:“. Anschließend folgt ein langer Pfadname zu einer Datei, z. B. *sharded_mutable_dense_hashtable.cpython-37.pyc*. Dieser Fehler tritt normalerweise auf, weil der Ordnerpfad eine zu große Tiefe hat. Legen Sie den Registrierungsschlüssel `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` in diesem Fall auf `1` fest, um lange Pfade zu aktivieren. Alternativ können Sie überprüfen, wo Ihr Python-Interpreter installiert ist. Falls dieser Speicherort über einen langen Pfad verfügt, sollten Sie versuchen, dafür eine erneute Installation in einem Ordner mit einem kürzeren Pfad durchzuführen.

## <a name="update-the-function-to-run-predictions"></a>Aktualisieren der Funktion zum Durchführen von Vorhersagen

1. Öffnen Sie *classify/\_\_init\_\_.py* in einem Text-Editor, und fügen Sie nach den vorhandenen `import`-Anweisungen die folgenden Zeilen hinzu, um die JSON-Standardbibliothek und die *predict* -Hilfsmethoden zu importieren:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Ersetzen Sie den gesamten Inhalt der Funktion `main` durch den folgenden Code:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Mit dieser Funktion wird eine Bild-URL in einem Abfragezeichenfolgen-Parameter mit dem Namen `img` empfangen. Anschließend wird `predict_image_from_url` aus der Hilfsbibliothek aufgerufen, um das Bild herunterzuladen und mit dem PyTorch-Modell zu klassifizieren. Die Funktion gibt dann eine HTTP-Antwort mit den Ergebnissen zurück.

    > [!IMPORTANT]
    > Da dieser HTTP-Endpunkt von einer Webseite aufgerufen wird, die in einer anderen Domäne gehostet wird, enthält die Antwort einen `Access-Control-Allow-Origin`-Header, um die CORS-Anforderungen (Cross-Origin Resource Sharing, Ressourcenfreigabe zwischen verschiedenen Ursprüngen) des Browsers zu erfüllen.
    >
    > Ändern Sie in einer Produktionsanwendung `*` in den spezifischen Ursprung der Webseite, um die Sicherheit zu erhöhen.

1. Speichern Sie Ihre Änderungen. Wenn die Installation der Abhängigkeiten abgeschlossen ist, können Sie den lokalen Funktionshost mit `func start` wieder starten. Achten Sie darauf, dass Sie den Host im Ordner *start* ausführen, während die virtuelle Umgebung aktiviert ist. Andernfalls wird der Host zwar gestartet, aber beim Aufrufen der Funktion treten Fehler auf.

    ```
    func start
    ```

1. Öffnen Sie in einem Browser die folgende URL, um die Funktion mit der URL eines Bilds aufzurufen, das einen Berner Sennenhund zeigt. Vergewissern Sie sich, dass das Bild vom zurückgegebenen JSON-Code als Bild eines Berner Sennenhunds klassifiziert wird.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. Behalten Sie die Ausführung des Hosts bei, da Sie ihn im nächsten Schritt benötigen.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Ausführen des lokalen Front-Ends der Web-App zum Testen der Funktion

Der Ordner *frontend* des Repositorys enthält eine einfache App, mit der das Aufrufen des Funktionsendpunkts aus einer anderen Web-App getestet werden kann.

1. Öffnen Sie ein neues Terminal bzw. eine Eingabeaufforderung, und aktivieren Sie die virtuelle Umgebung (wie weiter oben unter [Erstellen und Aktivieren einer virtuellen Python-Umgebung](#create-and-activate-a-python-virtual-environment) beschrieben).

1. Navigieren Sie zum Ordner *frontend* des Repositorys.

1. Starten Sie einen HTTP-Server mit Python:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. Navigieren Sie in einem Browser zu `localhost:8000`, und geben Sie dann eine der folgenden Foto-URLs in das Textfeld ein, oder verwenden Sie die URL eines beliebigen Bilds, das öffentlich zugänglich ist.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Wählen Sie **Senden** aus, um den Funktionsendpunkt zum Klassifizieren des Bilds aufzurufen.

    ![Screenshot: Abgeschlossenes Projekt](media/machine-learning-pytorch/screenshot.png)

    Wenn im Browser beim Senden der Bild-URL ein Fehler gemeldet wird, sollten Sie das Terminal überprüfen, in dem Sie die Funktions-App ausführen. Falls ein Fehler der Art „Kein Modul gefunden: 'PIL'“ angezeigt wird, haben Sie die Funktions-App ggf. im Ordner *start* gestartet, ohne zuerst die zuvor erstellte virtuelle Umgebung zu aktivieren. Falls weiterhin Fehler angezeigt werden, sollten Sie `pip install -r requirements.txt` bei aktivierter virtueller Umgebung erneut ausführen und auf Fehler achten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Da dieses Tutorial vollständig lokal auf Ihrem Computer ausgeführt wird, müssen keine Azure-Ressourcen oder Dienste bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie einen HTTP-API-Endpunkt mit Azure Functions erstellen und anpassen, um mit einem PyTorch-Modell Bilder zu klassifizieren. Es wurde auch beschrieben, wie Sie die API aus einer Web-App aufrufen. Sie können die Verfahren dieses Tutorials verwenden, um APIs mit beliebiger Komplexität zu erstellen, während Sie das von Azure Functions bereitgestellte serverlose Computemodell nutzen.

Weitere Informationen:

- [Tutorial: Erstellen und Bereitstellen von Azure Functions serverlos in Python mit Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Python-Entwicklerhandbuch für Azure Functions](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Bereitstellen der Funktion für Azure Functions über den Azure CLI-Leitfaden](./functions-run-local.md#publish)
