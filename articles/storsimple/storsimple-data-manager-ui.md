---
title: Microsoft Azure StorSimple Data Manager-Benutzeroberfläche
description: Erfahren Sie, wie Sie die Benutzeroberfläche von StorSimple Data Manager verwenden können, um Daten zu transformieren, die sich auf den Geräten der StorSimple 8000-Serie befinden.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 802f732e8d62f5df861be525316b3c31ab4d0655
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957927"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Verwalten des StorSimple Data Manager-Diensts im Azure-Portal

In diesem Artikel wird erläutert, wie Sie die Benutzeroberfläche von StorSimple Data Manager verwenden können, um Daten zu transformieren, die sich auf den Geräten der StorSimple 8000-Serie befinden. Die transformierten Daten können von anderen Azure-Diensten, z. B. Azure Media Services, Azure HDInsight, Azure Machine Learning und Azure Cognitive Search, genutzt werden.


## <a name="use-storsimple-data-transformation"></a>Verwenden der StorSimple-Datentransformation

StorSimple Data Manager ist die Ressource, in der die Datentransformation instanziiert wird. Mit dem Datentransformationsdienst können Sie Daten aus dem StorSimple-Format in das native Format in Blobs oder Azure Files transformieren. Damit Sie im nativen Format vorliegende StorSimple-Daten transformieren können, müssen Sie die Details zu Ihrem Gerät der StorSimple 8000-Serie sowie die für Sie interessanten Daten angeben, die Sie transformieren möchten.

### <a name="create-a-storsimple-data-manager-service"></a>Erstellen eines StorSimple Data Manager-Diensts

Führen Sie die folgenden Schritte aus, um einen StorSimple Data Manager-Dienst zu erstellen.

1. Melden Sie sich mithilfe Ihrer Microsoft-Kontoanmeldeinformationen beim [Azure-Portal](https://portal.azure.com/)an.

2. Klicken Sie auf **+ Ressource erstellen**, und suchen Sie nach StorSimple Data Manager.

    ![Erstellen eines StorSimple Data Manager-Diensts 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klicken Sie auf „StorSimple Data Manager“, und klicken Sie dann auf **Erstellen**.
    
    ![Erstellen eines StorSimple Data Manager-Diensts 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Geben Sie für den neuen Dienst Folgendes an:

   1. Geben Sie einen eindeutigen **Dienstnamen** für den StorSimple Data Manager an. Dies ist ein Anzeigename, der zum Identifizieren des Diensts verwendet werden kann. Der Name kann zwischen 3 und 24 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.

   2. Wählen Sie ein **Abonnement** in der Dropdownliste aus. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. Dieses Feld wird automatisch ausgefüllt (und ist nicht auswählbar), wenn Sie nur ein Abonnement haben.

   3. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Gruppe. Weitere Informationen finden Sie unter [Azure resource groups (Azure-Ressourcengruppen)](../azure-resource-manager/management/manage-resource-groups-portal.md).

   4. Geben Sie den **Standort** für Ihren Dienst an, an dem sich Ihre Speicherkonten und Ihr StorSimple Data Manager-Dienst befinden. Ihr StorSimple-Geräte-Manager-Dienst, Data Manager-Dienst und die zugeordneten Speicherkonten sollten sich alle in den unterstützten Regionen befinden.
    
   5. Um einen Link zu diesem Dienst auf Ihrem Dashboard zu erhalten, wählen Sie **An Dashboard anheften** aus.
    
   6. Klicken Sie auf **Erstellen**.

      ![Erstellen eines StorSimple Data Manager-Diensts 3](./media/storsimple-data-manager-ui/create-service-4.png)

Die Diensterstellung dauert einige Minuten. Sie sehen eine Benachrichtigung, nachdem der Dienst erfolgreich erstellt wurde, und der neue Dienst wird angezeigt.

### <a name="create-a-data-transformation-job-definition"></a>Erstellen einer Auftragsdefinition für die Datentransformation

Sie müssen innerhalb eines StorSimple Data Manager-Diensts eine Auftragsdefinition für die Datentransformation erstellen. Eine Auftragsdefinition gibt Details zu den StorSimple-Daten im nativen Format an, die Sie in ein Speicherkonto verschieben möchten. Nachdem Sie eine Auftragsdefinition erstellt haben, können Sie diesen Auftrag mit anderen Laufzeiteinstellungen erneut ausführen.

Führen Sie die folgenden Schritte aus, um eine Auftragsdefinition zu erstellen.

1. Navigieren Sie zu dem Dienst, den Sie erstellt haben. Wechseln Sie zu **Verwaltung > Auftragsdefinitionen**.

2. Klicken Sie auf **+ Auftragsdefinition**.

    ![Auf +Auftragsdefinition klicken](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Geben Sie einen Namen für Ihre Auftragsdefinition ein. Der Name kann 3 bis 63 Zeichen enthalten. Der Name darf nur Groß- und Kleinbuchstaben, Ziffern und Bindestriche enthalten.

4. Geben Sie einen Standort an, an dem Ihr Auftrag ausgeführt wird. Dieser Standort kann sich von dem Standort unterscheiden, an dem der Dienst bereitgestellt wird.

5. Klicken Sie auf **Quelle**, um das Quelldatenrepository anzugeben.

    ![Konfigurieren des Quelldatenrepositorys](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Da es sich um einen neuen Data Manager-Dienst handelt, werden keine Datenrepositorys konfiguriert. Geben Sie in **Datenquelle konfigurieren** die Details des Geräts der StorSimple 8000-Serie und der für Sie interessanten Daten an.

   Klicken Sie in der Dropdownliste im Datenrepository auf **Neu hinzufügen**, und anschließend auf **Add Data Repository** (Datenrepository hinzufügen), um Ihren StorSimple-Geräte-Manager als Datenrepository hinzuzufügen.

    ![Hinzufügen eines neuen Datenrepositorys](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Wählen Sie **StorSimple 8000-Serie Manager** als Datenrepositorytyp aus.
    
   2. Geben Sie einen Anzeigenamen für Ihr Quelldatenrepository ein.
    
   3. Wählen Sie in der Dropdownliste ein Abonnement aus, das Ihrem StorSimple-Geräte-Manager-Dienst zugeordnet ist.
    
   4. Geben Sie den Namen des StorSimple-Geräte-Managers für die **Ressource** ein.

   5. Geben Sie die **Dienstdatenverschlüsselungs**-Schlüssel für den StorSimple-Geräte-Manager-Dienst ein. 

      ![Konfigurieren des Quelldatenrepositorys 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Klicken Sie nach Abschluss des Vorgangs auf **OK**. Dadurch wird Ihr Datenrepository gespeichert. Sie können diesen StorSimple-Geräte-Manager in anderen Auftragsdefinitionen wiederverwenden, ohne diese Parameter erneut eingeben zu müssen. Nachdem Sie auf **OK** geklickt haben, dauert es einige Sekunden, bis das neu erstellte Quelldatenrepository in der Dropdownliste angezeigt wird.

7. Wählen Sie in der Dropdownliste für **Datenrepository** das Datenrepository aus, das Sie erstellt haben. 

   1. Geben Sie den Namen des Geräts der StorSimple 8000-Serie ein, das die gewünschten Daten enthält.

   2. Geben Sie den Namen des Volumes an, das sich auf dem StorSimple-Gerät befindet und das die von Ihnen gewünschten Daten enthält.

   3. Geben Sie im Unterabschnitt **Filter** das Stammverzeichnis, das die von Ihnen gewünschten Daten enthält, im Format _\MeinStammverzeichnis\Daten_ ein. Laufwerkbuchstaben, etwa _\C:\Daten_, werden nicht unterstützt. Sie können hier auch Dateifilter hinzufügen.

   4. Der Datentransformationsdienst funktioniert mit den aktuellsten Momentaufnahmen der an Azure gepushten Daten.

   5. Klicken Sie auf **OK**.

      ![Konfigurieren des Quelldatenrepositorys 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Als Nächstes muss das Zieldatenrepository konfiguriert werden. Wählen Sie Speicherkonten aus, wenn Sie Dateien in Blobs in diesem Konto ablegen möchten. Wählen Sie in der Dropdownliste **Neu hinzufügen** aus, und dann **Konfigurieren von Einstellungen**.

9. Wählen Sie den Typ des Zielrepositorys, das Sie hinzufügen möchten, und die anderen Parameter aus, die dem Repository zugeordnet werden sollen.

    Wenn Sie für das Ziel einen Speicherkontotyp auswählen, können Sie einen Anzeigenamen, ein Abonnement (wählen dasjenige, das für den Dienst verwendet wird, oder ein anderes aus) und ein Speicherkonto angeben.
        ![Konfigurieren des Zieldatenrepositorys 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Bei der Ausführung des Auftrags wird eine Speicherwarteschlange erstellt. Diese Warteschlange wird mit Nachrichten über transformierte Blobs aufgefüllt, wenn diese bereit sind. Der Name dieser Warteschlange entspricht dem Namen der Auftragsdefinition.
    
10. Nachdem Sie das Datenrepository hinzugefügt haben, warten Sie einige Minuten.
    
    1. Wählen Sie das Repository, das Sie als Ziel erstellt haben, in der Dropdownliste im Feld **Zielkontoname** aus.

    2. Wählen Sie den Speichertyp als „Blobs“ oder „Dateien“ aus. Geben Sie den Namen des Speichercontainers an, in dem sich die transformierten Daten befinden. Klicken Sie auf **OK**.

        ![Konfigurieren des Speicherkontos für das Zieldatenrepository](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Sie können auch die Option aktivieren, dass eine Schätzung der Auftragsdauer angezeigt wird, bevor Sie den Auftrag ausführen. Klicken Sie auf **OK**, um die Auftragsdefinition zu erstellen. Die Auftragsdefinition ist jetzt vollständig. Sie können diese Auftragsdefinition über die Benutzeroberfläche mehrfach mit unterschiedlichen Laufzeiteinstellungen verwenden.

    ![Abschließen der Auftragsdefinition](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Die neu erstellte Auftragsdefinition wird zur Liste der Auftragsdefinitionen für diesen Dienst hinzugefügt.

### <a name="run-the-job-definition"></a>Ausführen der Auftragsdefinition

Immer wenn Sie Daten aus StorSimple in das Speicherkonto verschieben möchten, das Sie in der Auftragsdefinition angegeben haben, müssen Sie diese ausführen. Zur Laufzeit können einige Parameter anders angegeben werden. Die Schritte lauten wie folgt:

1. Wählen Sie Ihren StorSimple Data Manager-Dienst aus, und wechseln Sie dann zu **Verwaltung > Auftragsdefinitionen**. Klicken Sie auf die Auftragsdefinition aus, die Sie ausführen möchten.
     
     ![Starten der Auftragsausführung 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klicken Sie auf **Jetzt ausführen**.
     
     ![Starten der Auftragsausführung 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Klicken Sie auf **Ausführungseinstellungen**, um Einstellungen zu modifizieren, die Sie möglicherweise bei dieser Ausführung des Auftrags ändern möchten. Klicken Sie auf **OK**, und klicken Sie dann auf **Ausführen**, um Ihren Auftrag auszuführen.

    ![Starten der Auftragsausführung 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Wechseln Sie zu **Aufträge** in Ihrem StorSimple Data Manager, um diesen Auftrag zu überwachen. Zusätzlich zur Überwachung können Sie auf dem Blatt **Aufträge** außerdem die Speicherwarteschlange abhören. Hier wird jedes Mal eine Nachricht hinzugefügt, wenn eine Datei aus StorSimple in das Speicherkonto verschoben wird.

    ![Starten der Auftragsausführung 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Anzeigen von Protokollen nach Auftragsabschluss

Nachdem Sie einen Auftrag abgeschlossen haben, können Sie seinen Status anzeigen. Der Auftragsstatus kann **Erfolgreich**, **Teilweise erfolgreich** oder **Fehlerhaft** sein. Sie können die Liste der erfolgreich kopierten Dateien und der Dateien, die nicht kopiert werden konnten, anzeigen. Diese Listen sind in einem Container mit dem Namen **storsimple-data-manager-joblogs** in Ihrem Zielspeicherkonto verfügbar. Sie können in diesem Container nach einem Ordner mit dem gleichen Namen wie die Auftragsdefinition suchen. Darin wird ein Ordner für jede Auftragsausführung erstellt, der Ihre Listen enthält. Der Name dieses Ordners ist die GUID des Auftrags, die Sie auf der Seite mit den Auftragsdetails finden. Alternativ wird in den meisten Fällen ein Link für die Kopierprotokolle auf der Seite „Aufträge“ angezeigt.
In diesem Ordner werden zwei Gruppen von CSV-Dateien angezeigt. Alle Dateien, die mit **copiedfilelist...** beginnen, enthalten die Liste der erfolgreich kopierten Dateien. Alle Dateien, die mit **failedfilelist...** beginnen, enthalten Dateien, die nicht kopiert werden konnten, sowie eine Fehlermeldung.


## <a name="next-steps"></a>Nächste Schritte

[Use .NET SDK to launch StorSimple Data Manager jobs (Verwenden von .NET SDK zum ausführen von StorSimple Data Manager-Aufträgen)](storsimple-data-manager-dotnet-jobs.md).