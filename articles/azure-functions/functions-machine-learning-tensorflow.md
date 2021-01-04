---
title: Verwenden von Python und TensorFlow für maschinelles Lernen in Azure
description: Es wird beschrieben, wie Sie Python, TensorFlow und Azure Functions mit einem Machine Learning-Modell verwenden, um ein Bild anhand seines Inhalts zu klassifizieren.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: ca9ce27583168dfee1a597fce559afad38a3a8c7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994603"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Tutorial: Anwenden von Machine Learning-Modellen in Azure Functions mit Python und TensorFlow

In diesem Artikel wird beschrieben, wie Sie Python, TensorFlow und Azure Functions mit einem Machine Learning-Modell verwenden, um ein Bild anhand seines Inhalts zu klassifizieren. Da Sie alles lokal durchführen und keine Azure-Ressourcen in der Cloud erstellen, fallen für das Durcharbeiten dieses Tutorials keine Kosten an.

> [!div class="checklist"]
> * Initialisieren einer lokalen Umgebung zum Entwickeln von Azure Functions in Python
> * Importieren eines benutzerdefinierten TensorFlow-Machine Learning-Modells in eine Funktions-App
> * Erstellen einer serverlosen HTTP-API zum Klassifizieren eines Bilds als Hunde- bzw. Katzenbild
> * Verwenden der API aus einer Web-App

## <a name="prerequisites"></a>Voraussetzungen 

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 und Python 3.6.x wurden in Bezug auf Azure Functions überprüft, und Python 3.8 und höhere Versionen werden noch nicht unterstützt.)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Ein Code-Editor wie [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

1. Führen Sie in einem Terminal oder Befehlsfenster `func --version` aus, um zu überprüfen, ob mindestens Version 2.7.1846 der Azure Functions Core Tools verwendet wird.
1. Führen Sie `python --version` (Linux/Mac OS) oder `py --version` (Windows) aus, um zu überprüfen, ob Python-Version 3.7.x verwendet wird.

## <a name="clone-the-tutorial-repository"></a>Klonen des Tutorial-Repositorys

1. Klonen Sie in einem Terminal oder Befehlsfenster mit Git das folgende Repository:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Navigieren Sie in den Ordner, und überprüfen Sie dessen Inhalt.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *start*: Dies ist der Arbeitsordner für das Tutorial.
    - *end*: Dies ist das Endergebnis und die vollständige Implementierung zu Referenzzwecken.
    - *resources*: Enthält das Machine Learning-Modell und Hilfsbibliotheken.
    - *frontend*: Eine Website, über die die Funktions-App aufgerufen wird.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Erstellen und Aktivieren einer virtuellen Python-Umgebung

Navigieren Sie zum Ordner *start*, und führen Sie die folgenden Befehle aus, um eine virtuelle Umgebung mit dem Namen `.venv` zu erstellen und zu aktivieren. Achten Sie darauf, dass Sie Python 3.7 verwenden. Diese Version wird von Azure Functions unterstützt.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Führen Sie den folgenden Befehl aus, wenn über Python das venv-Paket auf Ihrer Linux-Distribution nicht installiert wurde:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -3.7 -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -3.7 -m venv .venv
```

```cmd
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

    Nach der Initialisierung enthält der Ordner *start* verschiedene Dateien für das Projekt, z. B. die Konfigurationsdateien [local.settings.json](functions-run-local.md#local-settings-file) und [host.json](functions-host-json.md). Da *local.settings.json* aus Azure heruntergeladene Geheimnisse enthalten kann, wird die Datei in der *GITIGNORE*-Datei standardmäßig aus der Quellcodeverwaltung ausgeschlossen.

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

1. Verwenden Sie **STRG**-**C**, um den Host zu beenden.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Importieren des TensorFlow-Modells und Hinzufügen von Hilfscode

Zum Ändern der `classify`-Funktion zum Klassifizieren eines Bilds anhand des Inhalts verwenden Sie ein vordefiniertes TensorFlow-Modell, das über den Custom Vision-Dienst von Azure trainiert und exportiert wurde. Mit dem Modell, das im Ordner *resources* des zuvor geklonten Beispiels enthalten ist, wird ein Bild in Abhängigkeit davon klassifiziert, ob es einen Hund oder eine Katze zeigt. Anschließend fügen Sie dem Projekt dann Hilfscode und Abhängigkeiten hinzu.

Um ein eigenes Modell unter dem Free-Tarif des Custom Vision-Diensts zu erstellen, können Sie die Anleitung im [Repository des Beispielprojekts](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md) verwenden.

> [!TIP]
> Wenn Sie Ihr TensorFlow-Modell unabhängig von der Funktions-App hosten möchten, können Sie stattdessen eine Dateifreigabe mit Ihrem Modell in Ihre Linux-Funktions-App einbinden. Weitere Informationen finden Sie unter [Einbinden einer Dateifreigabe in eine Python-Funktions-App mithilfe der Azure CLI](./scripts/functions-cli-mount-files-storage-linux.md).

1. Führen Sie im Ordner *start* den folgenden Befehl aus, um die Modelldateien in den Ordner *classify* zu kopieren. Stellen Sie sicher, dass Sie `\*` in den Befehl einfügen. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Vergewissern Sie sich, dass der Ordner *classify* Dateien mit dem Namen *model.pb* und *labels.txt* enthält. Falls nicht, sollten Sie überprüfen, ob Sie den Befehl im Ordner *start* ausgeführt haben.

1. Führen Sie im Ordner *start* den folgenden Befehl aus, um eine Datei mit Hilfscode in den Ordner *classify* zu kopieren:

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Vergewissern Sie sich, dass der Ordner *classify* jetzt eine Datei mit dem Namen *predict.py* enthält.

1. Öffnen Sie *start/requirements.txt* in einem Text-Editor, und fügen Sie die folgenden Abhängigkeiten hinzu, die für den Hilfscode benötigt werden:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Speichern Sie die Datei *requirements.txt*.

1. Installieren Sie die Abhängigkeiten, indem Sie den unten angegebenen Befehl im Ordner *start* ausführen. Die Installation kann einige Minuten dauern. Während dieses Vorgangs können Sie mit dem Ändern der Funktion im nächsten Abschnitt fortfahren.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    Unter Windows tritt unter Umständen ein Fehler der folgenden Art auf: „Pakete konnten nicht installiert werden. EnvironmentError: [Errno 2] Datei oder Verzeichnis nicht vorhanden:“. Anschließend folgt ein langer Pfadname zu einer Datei, z. B. *sharded_mutable_dense_hashtable.cpython-37.pyc*. Dieser Fehler tritt normalerweise auf, weil der Ordnerpfad eine zu große Tiefe hat. Legen Sie den Registrierungsschlüssel `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` in diesem Fall auf `1` fest, um lange Pfade zu aktivieren. Alternativ können Sie überprüfen, wo Ihr Python-Interpreter installiert ist. Falls dieser Speicherort über einen langen Pfad verfügt, sollten Sie versuchen, dafür eine erneute Installation in einem Ordner mit einem kürzeren Pfad durchzuführen.

> [!TIP]
> Beim Aufrufen von *predict.py* zum Treffen der ersten Vorhersage wird das TensorFlow-Modell mit der Funktion `_initialize` vom Datenträger geladen und in globalen Variablen zwischengespeichert. Diese Zwischenspeicherung sorgt für eine Beschleunigung der nachfolgenden Vorhersagen. Weitere Informationen zur Verwendung von globalen Variablen finden Sie im [Python-Entwicklerhandbuch für Azure Functions](functions-reference-python.md#global-variables).

## <a name="update-the-function-to-run-predictions"></a>Aktualisieren der Funktion zum Durchführen von Vorhersagen

1. Öffnen Sie *classify/\_\_init\_\_.py* in einem Text-Editor, und fügen Sie nach den vorhandenen `import`-Anweisungen die folgenden Zeilen hinzu, um die JSON-Standardbibliothek und die *predict*-Hilfsmethoden zu importieren:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Ersetzen Sie den gesamten Inhalt der Funktion `main` durch den folgenden Code:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Mit dieser Funktion wird eine Bild-URL in einem Abfragezeichenfolgen-Parameter mit dem Namen `img` empfangen. Anschließend wird `predict_image_from_url` aus der Hilfsbibliothek aufgerufen, um das Bild herunterzuladen und mit dem TensorFlow-Modell zu klassifizieren. Die Funktion gibt dann eine HTTP-Antwort mit den Ergebnissen zurück. 

    > [!IMPORTANT]
    > Da dieser HTTP-Endpunkt von einer Webseite aufgerufen wird, die in einer anderen Domäne gehostet wird, enthält die Antwort einen `Access-Control-Allow-Origin`-Header, um die CORS-Anforderungen (Cross-Origin Resource Sharing, Ressourcenfreigabe zwischen verschiedenen Ursprüngen) des Browsers zu erfüllen.
    >
    > Ändern Sie in einer Produktionsanwendung `*` in den spezifischen Ursprung der Webseite, um die Sicherheit zu erhöhen.

1. Speichern Sie Ihre Änderungen. Wenn die Installation der Abhängigkeiten abgeschlossen ist, können Sie den lokalen Funktionshost mit `func start` wieder starten. Achten Sie darauf, dass Sie den Host im Ordner *start* ausführen, während die virtuelle Umgebung aktiviert ist. Andernfalls wird der Host zwar gestartet, aber beim Aufrufen der Funktion treten Fehler auf.

    ```
    func start
    ```

1. Öffnen Sie in einem Browser die folgende URL, um die Funktion mit der URL eines Katzenbilds aufzurufen. Vergewissern Sie sich, dass das Bild vom zurückgegebenen JSON-Code als Bild mit einer Katze klassifiziert wird.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
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

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Wählen Sie **Senden** aus, um den Funktionsendpunkt zum Klassifizieren des Bilds aufzurufen.

    ![Screenshot: Abgeschlossenes Projekt](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Wenn im Browser beim Senden der Bild-URL ein Fehler gemeldet wird, sollten Sie das Terminal überprüfen, in dem Sie die Funktions-App ausführen. Falls ein Fehler der Art „Kein Modul gefunden: 'PIL'“ angezeigt wird, haben Sie die Funktions-App ggf. im Ordner *start* gestartet, ohne zuerst die zuvor erstellte virtuelle Umgebung zu aktivieren. Falls weiterhin Fehler angezeigt werden, sollten Sie `pip install -r requirements.txt` bei aktivierter virtueller Umgebung erneut ausführen und auf Fehler achten.

> [!NOTE]
> Mit dem Modell wird der Inhalt des Bilds immer als Katze oder Hund klassifiziert. Dies gilt auch, wenn das Bild keine Katze bzw. keinen Hund enthält (standardmäßig erfolgt in diesem Fall die Klassifizierung als Hund). Bilder mit Tigern oder Panthern werden beispielsweise normalerweise als „Katze“ klassifiziert, während Bilder mit Elefanten, Karotten oder Flugzeugen als „Hund“ klassifiziert werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Da dieses Tutorial vollständig lokal auf Ihrem Computer ausgeführt wird, müssen keine Azure-Ressourcen oder Dienste bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie einen HTTP-API-Endpunkt mit Azure Functions erstellen und anpassen, um mit einem TensorFlow-Modell Bilder zu klassifizieren. Es wurde auch beschrieben, wie Sie die API aus einer Web-App aufrufen. Sie können die Verfahren dieses Tutorials verwenden, um APIs mit beliebiger Komplexität zu erstellen, während Sie das von Azure Functions bereitgestellte serverlose Computemodell nutzen.

> [!div class="nextstepaction"]
> [Bereitstellen der Funktion für Azure Functions über den Azure CLI-Leitfaden](./functions-run-local.md#publish)

Weitere Informationen:

- [Tutorial: Erstellen und Bereitstellen von Azure Functions serverlos in Python mit Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Python-Entwicklerhandbuch für Azure Functions](./functions-reference-python.md)
- [Einbinden einer Dateifreigabe in eine Python-Funktions-App mithilfe der Azure CLI](./scripts/functions-cli-mount-files-storage-linux.md)
