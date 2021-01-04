---
title: Herstellen einer Verbindung mit einer Compute-Instanz in Visual Studio Code (Vorschau)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie eine Verbindung mit einer Azure Machine Learning-Computeinstanz in Visual Studio Code herstellen, um interaktive Jupyter Notebook- und Remoteentwicklungsworkloads auszuführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jmartens
author: j-martens
ms.date: 11/16/2020
ms.openlocfilehash: 0afbf32f80a3d19f6db457f398a213a634cfa3d4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447052"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Herstellen einer Verbindung mit einer Azure Machine Learning-Compute-Instanz in Visual Studio Code (Vorschau)

In diesem Artikel erfahren Sie, wie Sie mithilfe von Visual Studio Code eine Verbindung mit einer Azure Machine Learning-Compute-Instanz herstellen.

Eine [Azure Machine Learning Compute-Instanz](concept-compute-instance.md) ist eine vollständig verwaltete cloudbasierte Arbeitsstation für Datenanalysten und bietet IT-Administratoren Funktionen für die Verwaltung und Unternehmensbereitschaft.

Es gibt zwei Möglichkeiten, wie Sie von Visual Studio Code aus eine Verbindung mit einer Compute-Instanz herstellen können:

* Über einen Jupyter Notebook-Remoteserver. Mit dieser Option können Sie eine Compute-Instanz als Jupyter Notebook-Remoteserver konfigurieren.
* Über die [Visual Studio Code-Remoteentwicklung](https://code.visualstudio.com/docs/remote/remote-overview). Mit der Visual Studio Code-Remoteentwicklung können Sie einen Container, einen Remotecomputer oder das Windows-Subsystem für Linux (WSL) als voll ausgestattete Entwicklungsumgebung verwenden.

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Konfigurieren der Compute-Instanz als Jupyter Notebook-Remoteserver

Sie benötigen folgende Voraussetzungen, um eine Compute-Instanz als Jupyter Notebook-Remoteserver konfigurieren zu können:

* Die Azure Machine Learning-Erweiterung für Visual Studio Code. Weitere Informationen finden Sie in der Anleitung zur Einrichtung der [Azure Machine Learning-Erweiterung für Visual Studio Code](tutorial-setup-vscode-extension.md).
* Einen Azure Machine Learning-Arbeitsbereich. [Verwenden Sie die Azure Machine Learning-Erweiterung für Visual Studio Code, um einen neuen Arbeitsbereich zu erstellen](how-to-manage-resources-vscode.md#create-a-workspace), wenn Sie noch nicht über einen verfügen.

So stellen Sie eine Verbindung mit einer Compute-Instanz her:

1. Öffnen Sie eine Jupyter Notebook-Instanz in Visual Studio Code.
1. Wählen Sie **Jupyter-Server** aus, wenn das integrierte Notebook geladen wird.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Jupyter Notebook-Remoteserver](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Alternativ können Sie auch die Befehlspalette verwenden:

    1. Öffnen Sie hierzu die Befehlspalette, indem Sie in der Menüleiste **Ansicht > Befehlspalette** auswählen.
    1. Geben Sie `Azure ML: Connect to Compute instance Jupyter server` in das Textfeld ein.

1. Wählen Sie `Azure ML Compute Instances` aus der Liste der Optionen für den Jupyter-Server aus.
1. Wählen Sie in der Liste Ihr Abonnement aus. Wenn Sie bereits Ihren Azure Machine Learning-Standardarbeitsbereich konfiguriert haben, wird dieser Schritt übersprungen.
1. Wählen Sie Ihren Arbeitsbereich aus.
1. Wählen Sie Ihre Compute-Instanz aus der Liste aus. Wenn Sie noch keine haben, klicken Sie auf **Create new Azure ML Compute Instance** (Neue Azure ML Compute-Instanz) erstellen, und befolgen Sie die Anweisungen, um eine Instanz zu erstellen.
1. Sie müssen Visual Studio Code erneut laden, damit die Änderungen wirksam werden.
1. Öffnen Sie ein Jupyter Notebook, und führen Sie eine Zelle aus.

> [!IMPORTANT]
> Sie **müssen** eine Zelle ausführen, um die Verbindung herzustellen.

Nun können Sie weitere Zellen in Ihrem Jupyter Notebook ausführen.

> [!TIP]
> Sie können auch mit Python-Skriptdateien (.py) arbeiten, die Jupyter-ähnliche Codezellen enthalten. Weitere Informationen finden Sie in der [Visual Studio Code-Dokumentation zu Python Interactive](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="configure-compute-instance-remote-development"></a>Konfigurieren der Remoteentwicklung von Compute-Instanzen

Für die vollständige Remoteentwicklung benötigen Sie einige Voraussetzungen:

* [die Remote-SSH-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)
* SSH-fähige Compute-Instanz. Weitere Informationen finden Sie in der Anleitung zum [Erstellen einer Compute-Instanz](how-to-create-manage-compute-instance.md).

> [!NOTE]
> Auf Windows-Plattformen müssen Sie ggf. [einem mit OpenSSH kompatiblen SSH-Client installieren](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client). PuTTY wird unter Windows nicht unterstützt, da sich der SSH-Befehl im Pfad befinden muss.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>Abrufen der IP-Adresse und des SSH-Ports für die Compute-Instanz

1. Wechseln Sie zum Azure Machine Learning-Studio unter https://ml.azure.com/.
2. Wählen Sie Ihren [Arbeitsbereich](concept-workspace.md) aus.
1. Aktualisieren der Registerkarte **Compute-Instanzen**.
1. Klicken Sie in der Spalte **Anwendungs-URI** auf den **SSH**-Link der Compute-Instanz, die Sie als Remotecompute-Instanz verwenden möchten. 
1. Notieren Sie die IP-Adresse und den SSH-Port aus dem angezeigten Dialogfeld. 
1. Speichern Sie den privaten Schlüssel im Verzeichnis „~/.ssh/“ auf dem lokalen Computer. Öffnen Sie beispielsweise einen Editor für eine neue Datei, und fügen Sie den Schlüssel in ein: 

   **Linux:**

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   Der private Schlüssel sieht in etwa wie folgt aus:

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. Ändern Sie die Berechtigungen für die Datei, um sicherzustellen, dass nur Sie die Datei lesen können.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>Hinzufügen von Instanz als Host

Öffnen Sie die Datei `~/.ssh/config` (unter Linux) oder `C:\Users<username>.ssh\config` (unter Windows) in einem Editor, und fügen Sie einen neuen Eintrag ähnlich dem folgenden Inhalt hinzu:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

Hier einige Details zu den Feldern:

|Feld|BESCHREIBUNG|
|----|---------|
|Host|Verwenden Sie einen beliebigen Kurznamen für die Compute-Instanz. |
|HostName|Dies ist die IP-Adresse der Compute-Instanz. |
|Port|Dies ist der im obigen SSH-Dialogfeld angezeigte Port. |
|Benutzer|Dies muss  `azureuser` sein. |
|IdentityFile|Muss auf die Datei verweisen, in der Sie den privaten Schlüssel gespeichert haben. |

Jetzt sollten Sie mithilfe des oben verwendeten Kurznamens `ssh azmlci1` eine SSH-Verbindung mit Ihrer Compute-Instanz herstellen können.

### <a name="connect-vs-code-to-the-instance"></a>Verbinden von VS Code mit der Instanz

1. Klicken Sie in der Visual Studio Code-Aktivitätsleiste auf das Remote-SSH-Symbol, um Ihre SSH-Konfigurationen anzuzeigen.

1. Klicken Sie mit der rechten Maustaste auf die soeben erstellte SSH-Hostkonfiguration.

1. Wählen Sie **Im aktuellem Fenster eine Verbindung mit dem Host herstellen** aus. 

Ab diesem Punkt arbeiten Sie komplett auf der Compute-Instanz und können nun Bearbeitungen durchführen, Debuggen, Git benutzen, Erweiterungen verwenden, etc. – genauso, wie das mit Ihrem lokalen Visual Studio Code möglich ist.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Visual Studio Code-Remoteerweiterung eingerichtet haben, können Sie eine Compute-Instanz als Remotecompute-Instanz aus Visual Studio Code verwenden, um den [Code interaktiv zu debuggen](how-to-debug-visual-studio-code.md).

[Tutorial: Trainieren Ihres ersten ML-Modells](tutorial-1st-experiment-sdk-train.md) zeigt, wie eine Compute-Instanz mit einem integrierten Notebook verwendet wird.
