---
title: Einrichten eines Labs zum Vermitteln von Data Science mit Python und Jupyter Notebook | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Lab einrichten, um Data Science mit Python und Jupyter Notebook zu vermitteln.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648020"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Einrichten eines Labs zum Vermitteln von Data Science mit Python und Jupyter Notebook
In diesem Artikel wird erläutert, wie eine Vorlage eines virtuellen Computers (VM) in Lab Services mit den Tools eingerichtet wird, die erforderlich sind, um den Kursteilnehmern die Verwendung von [Jupyter Notebook](http://jupyter-notebook.readthedocs.io/) beizubringen, und wie die Kursteilnehmer eine Verbindung zu ihren Notebook-Instanzen in ihren VMs herstellen können.

Jupyter Notebook ist ein Open-Source-Projekt, mit dem Sie problemlos Rich-Text und ausführbaren Python-Quellcode in einem einzelnen Zeichenbereich kombinieren können, der als Notebook bezeichnet wird. Das Ausführen einer Notebook-Instanz führt zu einem linearen Datensatz von Eingaben und Ausgaben. Diese Ausgaben können Text, Tabellen mit Informationen, Punktdiagramme und mehr enthalten.

## <a name="set-up-the-lab"></a>Einrichten des Labs

### <a name="lab-configuration"></a>Labkonfiguration
Zum Einrichten dieses Labs müssen Sie auf ein Azure-Abonnement und ein Lab-Konto zugreifen können. Besprechen Sie mit dem Administrator Ihrer Organisation, ob Sie Zugang zu einem vorhandenen Azure-Abonnement erhalten können. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Sobald Sie über ein Azure-Abonnement verfügen, erstellen Sie entweder ein neues Lab-Konto in Azure Lab Services oder folgen den Anweisungen in diesem Tutorial: [Einrichten eines Lab-Kontos](tutorial-setup-lab-account.md). Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen
Aktivieren Sie die Einstellungen für das Lab-Konto, die in der nachfolgenden Tabelle beschrieben werden. Weitere Informationen zum Aktivieren von Marketplace-Images finden Sie unter [Angeben von für Lab-Ersteller verfügbare Marketplace-Images](specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| ------------------- | ------------ |
| Marketplace-Image | Aktivieren Sie in Ihrem Lab-Konto eines der Azure Marketplace-Images entsprechend den Anforderungen Ihres Betriebssystems: <br/><ul><li>Data Science Virtual Machine – Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18.04</li></ul> |

> [!NOTE]
> In diesem Artikel werden die im Azure Marketplace verfügbaren Images für Data Science Virtual Machine verwendet, da sie mit Jupyter Notebook vorkonfiguriert sind. Diese Images enthalten jedoch auch zahlreiche andere Entwicklungs- und Modellierungstools für Data Science. Wenn Sie diese zusätzlichen Tools nicht wünschen und ein einfaches Setup mit ausschließlich Jupyter Notebook-Instanzen möchten, erstellen Sie ein benutzerdefiniertes VM-Image. Ein Beispiel ist das [Installieren von JupyterHub in Azure](http://tljh.jupyter.org/en/latest/install/azure.html). Nachdem das benutzerdefinierte Image erstellt wurde, können Sie es in einen freigegebenen Imagekatalog hochladen, um es in Azure Lab Services zu verwenden. Weitere Informationen finden Sie unter [Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services](how-to-attach-detach-shared-image-gallery.md). 

### <a name="lab-settings"></a>Lab-Einstellungen
Konfigurieren Sie beim Einrichten eines Classroom-Labs die Einstellungen **Größe des virtuellen Computers** und **Virtuelles Computerimage** wie in der folgenden Tabelle gezeigt. Anweisungen zum Erstellen eines Classroom-Labs finden Sie unter [Einrichten eines Classroom-Labs](tutorial-setup-classroom-lab.md).

| Lab-Einstellungen | Wert/Anweisungen |
| ------------ | ------------------ | 
| Größe des virtuellen Computers | <p>Die Größe, die Sie hier auswählen, hängt von der Arbeitsauslastung ab, die Sie ausführen möchten:</p><ul><li>Klein oder Mittel – gut für eine grundlegende Einrichtung des Zugriffs auf Jupyter Notebook</li><li>Kleine GPU (Compute) – eignet sich am besten für rechenintensive und netzwerkintensive Anwendungen wie künstliche Intelligenz und Deep Learning</li></ul> | 
| VM-Image | <p>Wählen Sie je nach den Anforderungen Ihres Betriebssystems eines der folgenden Images aus:</p><ul><li>[Data Science Virtual Machine – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Vorlage für virtuelle Maschinen
Nachdem Sie ein Lab erstellt haben, wird eine Vorlagen-VM basierend auf der Größe des virtuellen Computers und dem Image erstellt, das Sie ausgewählt haben. Sie konfigurieren die Vorlagen-VM mit allem, was Sie den Kursteilnehmern für diese Klasse zur Verfügung stellen möchten. Weitere Informationen finden Sie unter [Erstellen und Verwalten einer Classroom-Vorlage in Azure Lab Services](how-to-create-manage-template.md). 

Die Data Science VM-Images werden standardmäßig mit vielen Data Science-Frameworks und -Tools bereitgestellt, die für diese Art von Klasse erforderlich sind. Die Images umfassen beispielsweise Folgendes:

- [Jupyter Notebook](http://jupyter-notebook.readthedocs.io/): Eine Webanwendung, mit der Datenanalysten Berechnungen an Rohdaten durchführen und die Ergebnisse in derselben Umgebung anzeigen können. Sie wird lokal auf der Vorlagen-VM ausgeführt.  
- [Visual Studio Code](https://code.visualstudio.com/): Eine IDE, die eine umfangreiche interaktive Benutzeroberfläche zum Schreiben und Testen einer Notebook-Instanz bietet. Weitere Informationen finden Sie unter [Arbeiten mit Jupyter-Notebooks in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

### <a name="provide-notebooks-for-the-class"></a>Bereitstellen von Notebook-Instanzen für die Klasse
Die nächste Aufgabe besteht darin, für Kursteilnehmer Notebook-Instanzen bereitzustellen, die verwendet werden sollen. Zum Bereitstellen eigener Notebook-Instanzen können Sie diese lokal in der Vorlagen-VM speichern. 

Beispiele für Notebook-Instanzen aus Azure Machine Learning finden Sie unter [Konfigurieren einer Umgebung mit Jupyter Notebook](../machine-learning/how-to-configure-environment.md#jupyter). 

### <a name="optional-enable-graphical-desktop-for-linux"></a>Optional: Aktivieren von grafischem Desktop für Linux 
Das Image **Data Science Virtual Machine – Ubuntu** wird bereits mit dem X2GO-Server bereitgestellt und ist für die Herstellung von Clientverbindungen konfiguriert. Beim Einrichten der Vorlagen-VM sind keine weiteren Schritte erforderlich. 

### <a name="publish-the-template-machine"></a>Veröffentlichen der Vorlage-VM
Wenn Sie die Vorlage veröffentlichen, erhält jeder in Ihrem Lab registrierte Kursteilnehmer eine Kopie der Vorlagen-VM mit allen lokalen Tools und Notebook-Instanzen, die Sie darauf eingerichtet haben.

## <a name="how-students-connect-to-jupyter-notebooks"></a>Wie stellen Kursteilnehmer eine Verbindung zu Jupyter Notebook her?
Nachdem Sie die Vorlage veröffentlicht haben, hat jeder Kursteilnehmer Zugriff auf eine VM mit allem, was Sie für die Klasse vorkonfiguriert haben, einschließlich der Jupyter Notebook-Instanzen. In den folgenden Abschnitten werden verschiedene Möglichkeiten aufgezeigt, wie Kursteilnehmer eine Verbindung zu Jupyter Notebook-Instanzen herstellen können. 

### <a name="for-windows-vms"></a>Für Windows-VMs
Wenn Sie den Kursteilnehmern Windows-VMs zur Verfügung gestellt haben, müssen sie sich mit ihren VMs verbinden und Jupyter Notebook-Instanzen verwenden, die darauf lokal verfügbar sind. 

Zum Herstellen einer Verbindung mit einer Windows-VM kann ein Kursteilnehmer eine Remotedesktopverbindung (RDP) verwenden. Einzelheiten hierzu finden Sie unter [Zugreifen auf ein Classroom-Lab](how-to-use-classroom-lab.md). 

Kursteilnehmer mit einem Mac oder Chromebook können die Anweisungen aus den folgenden Artikeln ausführen, um eine Verbindung mit der Data Science-Windows-VM herzustellen. 

- [Herstellen einer RDP-Verbindung mit einem virtuellen Computer auf einem Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Herstellen einer Verbindung zu einer VM mithilfe des Remotedesktopprotokolls auf einem Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Für Linux-VMs
Wenn Sie den Kursteilnehmern Linux-VMs bereitgestellt haben, haben die Kursteilnehmer mehrere Möglichkeiten, sich mit ihren Jupyter Notebook-Instanzen in den VMs zu verbinden:

- Lokaler Zugriff auf Jupyter Notebook-Instanzen nach Verbindung mit der VM
    - SSH-Verbindung mit der VM für Terminalsitzungen
     - X2Go-Verbindung mit der VM für grafische Sitzungen
- Verwenden Sie einen SSH-Tunnel, um eine direkte Verbindung zwischen dem lokalen Computer des Kursteilnehmers und dem Jupyter-Server auf dem virtuellen Computer herzustellen. 

Die folgenden Abschnitte enthalten Details zu den Verbindungsmöglichkeiten mit Jupyter Notebook-Instanzen. 

#### <a name="ssh-to-virtual-machine"></a>SSH-Verbindung mit virtuellem Computer
Kursteilnehmer können von einer Terminalsitzung aus über SSH eine Verbindung mit ihren Linux-VMs herstellen. Einzelheiten hierzu finden Sie unter [Zugreifen auf ein Classroom-Lab](how-to-use-classroom-lab.md). Wenn sie einen Windows-Clientcomputer verwenden, müssen sie einen SSH-Client aktivieren, indem sie [PuTTY](https://www.putty.org/) herunterladen oder [OpenSSH in Windows](/windows-server/administration/openssh/openssh_install_firstuse) von der Eingabeaufforderung in SSH aktivieren. 

1.  Starten Sie den virtuellen Computer.
2.  Sobald die VM ausgeführt wird, klicken Sie auf **Verbinden**. Daraufhin wird ein Dialogfeld mit der SSH-Befehlszeichenfolge eingeblendet, die wie im folgenden Beispiel aussieht:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Wechseln Sie zur Eingabeaufforderung oder zum Terminal, fügen Sie diesen Befehl ein, und drücken Sie dann die **EINGABETASTE**.
4.  Geben Sie das Kennwort zur Anmeldung bei der VM ein. 

Sobald die Kursteilnehmer mit den VMs verbunden sind, können sie lokal auf Jupyter Notebook-Instanzen zugreifen und diese ausführen.

#### <a name="x2go-to-virtual-machine"></a>X2Go-Verbindung zu virtuellem Computer
Das Image **Data Science Virtual Machine – Ubuntu** wird bereits mit dem X2GO-Server bereitgestellt und ist für die Herstellung von Clientverbindungen konfiguriert. Um eine Verbindung mit dem grafischen Desktop des Linux-Computers herzustellen, müssen die Kursteilnehmer diese Schritte einmalig ausführen, um X2Go auf ihren Clientcomputern einzurichten:

1.  Laden Sie den [X2Go-Client](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) für Ihre Clientplattform herunter, und installieren Sie ihn.
2.  Stellen Sie im [Azure Lab Services-Portal](https://labs.azure.com) sicher, dass die Linux-VM, mit der Sie eine Verbindung herstellen möchten, gestartet ist.
3.  Sobald die VM ausgeführt wird, klicken Sie auf **Verbinden**. Daraufhin wird ein Dialogfeld mit der SSH-Befehlszeichenfolge eingeblendet, die wie im folgenden Beispiel aussieht:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. Nachdem Sie diese Informationen erhalten haben, öffnen Sie die X2Go-Client-App, und erstellen Sie eine neue Sitzung. 
5.  Geben Sie die folgenden Werte in den Bereich **Sitzungseinstellungen** ein:
    - **Sitzungsname:** Sie können einen beliebigen Namen eingeben, aber es wird empfohlen, den Namen Ihrer Lab-VM einzugeben.
     - **Host**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **Anmeldung**: Kursteilnehmer
     - **SSH-Port**: 12345
     - **Sitzungstyp**: XFCE
6. Klicken Sie auf **OK**. 

    > [!NOTE]
     > Stellen Sie beim Erstellen einer neuen X2Go-Sitzung sicher, dass Sie den SSH-Port verwenden, **nicht** den RDP-Port.

Führen Sie die folgenden Schritte aus, um eine Verbindung mit dem virtuellen Computer herzustellen:    

1.  Doppelklicken Sie im X2Go-Client auf die VM, mit der Sie eine Verbindung herstellen möchten. 

    ![X2Go-Client](./media/class-type-jupyter-notebook/x2go-client.png)
2. Geben Sie das Kennwort zum Herstellen einer Verbindung mit der VM ein. (Möglicherweise müssen Sie X2Go die Berechtigung zum Umgehen Ihrer Firewall erteilen, um den Verbindungsvorgang abzuschließen.)
3.  Nun sollte die grafische Benutzeroberfläche für Ihre Data Science-Ubuntu-VM angezeigt werden.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>SSH-Tunnel zu Jupyter-Server in der VM
Einige Kursteilnehmer möchten möglicherweise von ihrem lokalen Computer aus eine direkte Verbindung mit dem Jupyter-Server in ihren VMs herstellen. Das SSH-Protokoll ermöglicht die Portweiterleitung zwischen dem lokalen Computer und einem Remoteserver (in unserem Fall die Lab-VM des Kursteilnehmers), sodass eine Anwendung, die auf einem bestimmten Port auf dem Server läuft, auf den Zuordnungsport auf dem lokalen Computer **getunnelt** wird. Die Kursteilnehmer sollten die folgenden Schritte ausführen, um einen SSH-Tunnel zum Jupyter-Server in ihren Labor-VMs einzurichten:

1.  Stellen Sie im [Azure Lab Services-Portal](https://labs.azure.com) sicher, dass die Linux-VM, mit der Sie eine Verbindung herstellen möchten, gestartet ist.
2.  Sobald die VM ausgeführt wird, klicken Sie auf **Verbinden**. Daraufhin wird ein Dialogfeld mit der SSH-Befehlszeichenfolge eingeblendet, die wie in der folgenden Zeichenfolge aussieht:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. Starten Sie auf Ihrem lokalen Computer ein Terminal oder eine Eingabeaufforderung, und kopieren Sie die SSH-Verbindungszeichenfolge hinein. Fügen Sie dann `-L 8888:localhost:8888` zur Befehlszeichenfolge hinzu, wodurch der **Tunnel** zwischen den Ports erstellt wird. Die endgültige Zeichenfolge sollte wie folgt aussehen:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. Drücken Sie die **EINGABETASTE**, um den Befehl auszuführen. 
5.  Wenn Sie dazu aufgefordert werden, geben Sie das Kennwort zum Herstellen einer Verbindung mit dem virtuellen Lab ein. 
6.  Sobald die Verbindung mit der VM hergestellt ist, starten Sie den Jupyter-Server mit diesem Befehl: 

    ```bash
     jupyter notebook
     ```
7. Wenn Sie den Befehl ausführen, wird Ihnen eine URL im Terminal oder in der Befehlszeile angezeigt. Die URL sollte wie folgt aussehen:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Fügen Sie diese URL in einen Browser auf Ihrem lokalen Computer ein, um eine Verbindung herzustellen und auf Ihrem Jupyter Notebook zu arbeiten. 

    > [!NOTE]
    > Visual Studio Code bietet auch eine großartige [Jupyter Notebook-Bearbeitungsoberfläche](https://code.visualstudio.com/docs/python/jupyter-support). Sie können den Anweisungen in der Anleitung zum [Herstellen einer Verbindung zu einem Jupyter-Remoteserver](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) folgen und dieselbe URL aus dem vorherigen Schritt verwenden, um eine Verbindung über VS Code statt über den Browser herzustellen. 


## <a name="cost-estimate"></a>Kostenschätzung
Betrachten wir eine mögliche Kostenschätzung für diesen Kurs. Wir setzen einen Kurs mit 25 Kursteilnehmern an. Es ist eine Kursdauer von 20 Stunden geplant. Zudem erhält jeder Kursteilnehmer ein Kontingent von 10 Stunden für Hausaufgaben und Aufgaben außerhalb der regulären Kurszeiten. Als Größe des virtuellen Computers wurde „Kleine GPU (Compute)“ ausgewählt, was 139 Lab-Einheiten entspricht. Wenn Sie die kleine (20 Lab-Einheiten) oder die mittlere Größe (42 Lab-Einheiten) verwenden möchten, können Sie die entsprechenden Lab-Einheiten in der unten stehenden Gleichung durch die richtige Anzahl ersetzen.  

Hier ist ein Beispiel für eine mögliche Kostenschätzung für diesen Kurs: 25 Kursteilnehmer * (20 geplante Stunden + 10 Kontingentstunden) * 139 Lab-Einheiten * 0,01 USD pro Stunde = 1042,5 USD

Weitere Informationen zu den Preisen finden Sie unter [Azure Lab Services-Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Zusammenfassung
In diesem Artikel haben wir Sie durch die Schritte zum Erstellen eines Labs für einen Jupyter Notebook-Kurs geführt. Sie können ein ähnliches Setup für andere Machine Learning-Kurse verwenden.

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Erstellen und Verwalten einer Vorlage](how-to-create-manage-template.md)
- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)