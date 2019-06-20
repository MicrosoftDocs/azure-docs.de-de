---
title: Ausführen einer Image Factory aus Azure DevOps in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine benutzerdefinierte Image Factory in Azure DevTest Labs erstellen.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: abb85d568e26e4b6f85b960a2560aae570daf201
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61320605"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Ausführen einer Image Factory aus AzureDevOps
Dieser Artikel behandelt alle erforderlichen Vorbereitungsschritte, um die Image Factory aus Azure DevOps (früher Visual Studio Team Services) auszuführen.

> [!NOTE]
> Jede Orchestrierungs-Engine funktioniert! Azure DevOps ist nicht obligatorisch. Die Image Factory wird mithilfe von Azure PowerShell-Skripts ausgeführt, sodass sie auch manuell, mithilfe der Windows-Aufgabenplanung, mit anderen CI/CD-Systemen usw. ausgeführt werden könnte.

## <a name="create-a-lab-for-the-image-factory"></a>Erstellen eines Labs für die Image Factory
Der erste Schritt beim Einrichten der Image Factory besteht im Erstellen eines Labs in Azure DevTest Labs. Dieses Lab ist das Image Factory-Lab, in dem wir die virtuellen Computer erstellen und benutzerdefinierte Images speichern. Dieses Lab wird als Teil des Image Factory-Gesamtprozesses betrachtet. Nachdem Sie ein Lab erstellt haben, stellen Sie sicher, dass Sie sich den Namen merken, da Sie ihn später noch benötigen.

## <a name="scripts-and-templates"></a>Skripts und Vorlagen
Der nächste Schritt bei der Einführung der Image Factory für Ihr Team besteht darin zu verstehen, was alles verfügbar ist. Die Image Factory-Skripts und -Vorlagen stehen öffentlich im [DevTest Labs-GitHub-Repository](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory) zur Verfügung. Hier ist ein Überblick über die einzelnen Teile:

- Image Factory. Dies ist der Stammordner.
    - Konfiguration Die Eingaben für die Image Factory
        - GoldenImages. Dieser Ordner enthält die JSON-Dateien, die die Definitionen benutzerdefinierter Images darstellen.
        - Labs.json. Die Datei, bei der sich Teams registrieren, um spezifische Kundenimages zu erhalten.
- Skripts. Die Engine für die Image Factory.

Die Artikel in diesem Abschnitt bieten weitere Details zu diesen Skripts und Vorlagen.

## <a name="create-an-azure-devops-team-project"></a>Erstellen eines Azure DevOps-Teamprojekts
Azure DevOps ermöglicht Ihnen das Speichern des Quellcodes und das Ausführen der Azure PowerShell an einem Ort. Sie können sich wiederholende Ausführungen planen, um Images auf dem neuesten Stand zu halten. Es gibt gute Funktionen für die Protokollierung der Ergebnisse, um eventuelle Probleme zu diagnostizieren.  Die Verwendung von Azure DevOps ist jedoch keine Anforderung, denn Sie können jede Umgebung/Engine verwenden, die sich mit Azure verbinden und Azure PowerShell ausführen kann.

Wenn Sie ein vorhandenes DevOps-Konto oder Projekt besitzen, das Sie stattdessen verwenden möchten, überspringen Sie diesen Schritt.

Erstellen Sie zunächst ein kostenloses Konto in Azure DevOps. Besuchen Sie https://www.visualstudio.com/, und wählen Sie **Kostenlos einsteigen** direkt unter **Azure DevOps** (früher VSTS) aus. Sie müssen einen eindeutigen Kontonamen auswählen und sicherstellen, dass Sie Code mithilfe von Git verwalten. Sobald dies erstellt wurde, speichern Sie die URL für Ihr Teamprojekt. Hier ist eine Beispiel-URL: `https://<accountname>.visualstudio.com/MyFirstProject`.

## <a name="check-in-the-image-factory-to-git"></a>Einchecken der Image Factory bei Git
Die PowerShell, die Vorlagen und die Konfiguration für die Image Factory befinden sich im [öffentlichen DevTest Labs-GitHub-Repository](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Die schnellste Möglichkeit zum Abrufen des Codes in Ihr neues Teamprojekt besteht darin, ein Repository zu importieren. Hierdurch wird das gesamte DevTest Labs-Repository eingelesen (wodurch Sie zusätzliche Dokumente und Beispiele erhalten).

1. Besuchen Sie das Azure DevOps-Projekt, das Sie im vorherigen Schritt erstellt haben (die URL sieht wie folgt aus: **https:\//\<Kontoname>.visualstudio.com/MyFirstProject**).
2. Wählen Sie **Repository importieren** aus.
3. Geben Sie die **Klon-URL** für das DevTest-Labs-Repository ein: `https://github.com/Azure/azure-devtestlab`.
4. Wählen Sie **Importieren** aus.

    ![Importieren des Git-Repositorys](./media/set-up-devops-lab/import-git-repo.png)

Wenn Sie sich entscheiden, nur genau das einzuchecken, was benötigt wird (die Image Factory-Dateien), führen Sie [diese](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) Schritte aus, um das Git-Repository zu klonen und nur die im Verzeichnis **scripts/ImageFactory** befindlichen Dateien zu übertragen.

## <a name="create-a-build-and-connect-to-azure"></a>Erstellen eines Builds und Verbinden mit Azure
Zu diesem Zeitpunkt haben Sie die Quelldateien in einem Git-Repository in Azure DevOps gespeichert. Nun müssen Sie eine Pipeline einrichten, um die Azure PowerShell auszuführen. Es gibt viele Optionen, um diese Schritte auszuführen. In diesem Artikel verwenden Sie der Einfachheit halber Builddefinition, aber es funktioniert auch mit DevOps Build, DevOps Release (einzelne oder mehrere Umgebungen), anderen Ausführungs-Engines wie Windows-Aufgabenplanung oder jeder anderen Umgebung, die Azure PowerShell ausführen kann.

> [!NOTE]
> Ein wichtiger Punkt, den Sie bedenken sollten, ist, dass die Ausführung einiger der PowerShell-Dateien lange dauert, wenn es viele (mehr als 10) benutzerdefinierte Images zu erstellen gilt. Kostenlose gehostete DevOps Build/Release-Agents besitzen einen Timeout von30 Minuten, sodass Sie den kostenlosen gehosteten Agent nicht verwenden können, sobald Sie das Erstellen vieler Images gestartet haben. Diese Timeouteinschränkung gilt für jede beliebige Umgebung, die Sie verwenden möchten. Daher ist es ratsam, im Voraus zu überprüfen, ob Sie die typischen Timeouts für zeitintensive Azure PowerShell-Skripts verlängern können. Im Fall von Azure DevOps können Sie entweder kostenpflichtige gehostete Agents oder Ihren eigenen Build-Agent verwenden.

1. Wählen Sie für den Anfang **Build einrichten** auf der Startseite Ihres DevOps-Projekts aus:

    ![Schaltfläche „Build einrichten“](./media/set-up-devops-lab/setup-build-button.png)
2. Geben Sie einen **Namen** für den Build an (z.B.: Build), und liefern Sie Images an DevTest Labs.
3. Wählen Sie eine **leere** Builddefinition aus, und wählen **Anwenden** aus, um Ihren Build zu erstellen.
4. In dieser Phase können Sie als Build-Agent **Gehostet** auswählen.
5. **Speichern** Sie die Builddefinition.

    ![Builddefinition](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Konfigurieren der Buildvariablen
Um die Befehlszeilenparameter zu vereinfachen, kapseln Sie die Schlüsselwerte, die die Image Factory steuern, in einen Satz Buildvariablen. Wählen Sie die Registerkarte **Variablen** aus, wo eine Liste mehrerer Standardvariablen angezeigt wird. Dies ist die Liste der in Azure DevOps einzugebenden Variablen:


| Variablenname | Wert | Notizen |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Dies ist der vollständige Pfad im Repository zum Ordner **Configuration**. Wenn Sie oben das gesamte Repository importiert haben, ist der Wert auf der linken Seite zutreffend. Andernfalls aktualisieren Sie ihn so, dass er auf den Speicherort von „Configuration“ verweist. |
| DevTestLabName | MyImageFactory | Der Name des Labs in Azure DevTest Labs, das als Factory zur Erzeugung von Images verwendet wird. Wenn Sie keins haben, erstellen Sie eins. Stellen Sie sicher, dass sich das Lab im selben Abonnement befindet, auf das auch der Dienstendpunkt Zugriff hat. |
| ImageRetention | 1 | Die Anzahl der Images, die für jeden Typen gespeichert werden soll. Legen Sie den Standardwert auf 1 fest. |
| MachinePassword | ******* | Das integrierte Administratorkonto-Kennwort für die virtuellen Computer. Dies ist ein vorübergehendes Konto. Stellen Sie also sicher, dass es geschützt ist. Wählen Sie das kleinen Schlosssymbol auf der rechten Seite aus, um sicherzustellen, dass es sich um eine sichere Zeichenfolge handelt. |
| MachineUserName | ImageFactoryUser | Der integrierte Administratorkonto-Benutzername für die virtuellen Computer. Dies ist ein vorübergehendes Konto. |
| StandardTimeoutMinutes | 30 | Der Timeout, der für normale Azure-Vorgänge abgewartet werden soll. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | Die ID des Abonnements, in dem sich das Lab befindet, auf das auch der Dienstendpunkt Zugriff hat. |
| VMSize | Standard_A3 | Die Größe des virtuellen Computers, der für den Schritt **Erstellen** verwendet werden soll. Die erstellten VMs sind kurzlebig. Die Größe muss derjenigen entsprechen, die [für das Lab aktiviert](devtest-lab-set-lab-policy.md) ist. Bestätigen Sie, dass ein ausreichendes [Abonnement-Kontingent Speicherkerne](../azure-subscription-service-limits.md) vorhanden ist.

![Buildvariablen](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Herstellen einer Verbindung mit Azure
Der nächste Schritt besteht darin, einen Dienstprinzipal einzurichten. Dies ist eine Identität in Azure Active Directory, die es dem DevOps-Build-Agent ermöglicht, in Azure im Auftrag des Benutzers zu agieren. Um ihn einzurichten, beginnen Sie damit, dass Sie Ihren ersten Azure PowerShell-Buildschritt hinzufügen.

1. Wählen Sie **Aufgabe hinzufügen** aus.
2. Suchen Sie nach **Azure PowerShell**.
3. Sobald Sie sie gefunden haben, wählen Sie **Hinzufügen** aus, um die Aufgabe dem Build hinzuzufügen. Wenn Sie dies tun, sehen Sie, wie die Aufgabe mit dem Hinzufügen auf der linken Seite angezeigt wird.

![Einrichten eines PowerShell-Schritts](./media/set-up-devops-lab/set-up-powershell-step.png)

Die schnellste Möglichkeit zum Einrichten eines Dienstprinzipals besteht darin, dies von Azure DevOps erledigen zu lassen.

1. Wählen Sie die **Aufgabe** aus, die Sie gerade erstellt haben.
2. Wählen Sie als **Azure-Verbindungstyp** die Option **Azure Resource Manager** aus.
3. Wählen Sie den Link **Verwalten** aus, um den Dienstprinzipal einzurichten.

Weitere Informationen finden Sie in diesem [Blogbeitrag](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Wenn Sie den Link **Verwalten** auswählen, gelangen Sie zur richtigen Stelle in DevOps (zweiter Screenshot im Blogbeitrag), um die Verbindung mit Azure einzurichten. Achten Sie darauf, dass Sie **Azure Resource Manager-Dienstendpunkt** auswählen, wenn Sie dies einrichten.

## <a name="complete-the-build-task"></a>Abschließen der Buildaufgabe
Wenn Sie die Buildaufgabe auswählen, werden alle Details im rechten Bereich angezeigt, die eingetragen werden sollten.

1. Als Erstes benennen Sie die Buildaufgabe: **Erstellen Sie virtuelle Computer**.
2. Wählen Sie den von Ihnen erstellten **Dienstprinzipal** aus, indem Sie **Azure Resource Manager** auswählen.
3. Wählen Sie den **Dienstendpunkt** aus.
4. Wählen Sie als **Skriptpfad** die Option **...** (Auslassungspunkte) auf der rechten Seite aus.
5. Navigieren Sie zum Skript **MakeGoldenImageVMs.ps1**.
6. Die Skriptparameter sollten wie folgt aussehen: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Abschließen der Builddefinition](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Einstellen des Builds in die Warteschlange
Lassen Sie uns überprüfen, ob Sie alles richtig eingerichtet haben, indem wir einen neuen Build in die Warteschlange stellen. Wechseln Sie während der Ausführung des Builds zum [Azure-Portal](https://portal.azure.com), und aktivieren Sie **Alle virtuellen Computer** in Ihrem Image Factory-Lab, um sicherzustellen, dass alles ordnungsgemäß funktioniert. Sie sollten sehen, wie drei virtuellen Computer im Lab erstellt werden.

![VMs im Lab](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Nächste Schritte
Der erste Schritt des Einrichtens der Image Factory, basierend auf Azure DevTest Labs, ist abgeschlossen. Im nächsten Artikel aus dieser Reihe zeigen wir Ihnen, wie Sie diese virtuellen Computer generalisieren und in benutzerdefinierten Images speichern. Danach lassen Sie sie an alle Ihre anderen Labs verteilen. Lesen Sie den nächsten Artikel aus dieser Reihe: [Speichern von benutzerdefinierten Images und Verteilen an mehrere Labs](image-factory-save-distribute-custom-images.md).
