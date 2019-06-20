---
title: Verwenden von Azure Storage mit einer Jenkins-Lösung für Continuous Integration
description: Dieses Lernprogramm zeigt, wie Sie den Azure-Blobdienst als Repository für Buildartefakte verwenden, die von einer Jenkins-Lösung für die fortlaufende Integration erstellt wurden.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
ms.service: storage
custom: jenkins
ms.date: 07/31/2018
ms.subservice: common
ms.openlocfilehash: d9ef6f5056fdbd7187c92c98d1c884a5314c29a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153667"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Verwenden von Azure Storage mit einer Jenkins-Lösung für Continuous Integration

Dieser Artikel zeigt die Verwendung von Blob-Speicher als Repository von Buildartefakten, die durch eine Jenkins Continuous Integration-Lösung (CI) erstellt wurden, oder als eine Quelle von herunterladbaren Dateien, die in einem Buildvorgang verwendet werden. Diese Lösung ist zum Beispiel dann hilfreich, wenn Sie in einer agilen Entwicklungsumgebung (in Java oder anderen Sprachen) programmieren, Builds auf Basis von Continuous Integration ausgeführt werden, und Sie ein Repository für Ihre Buildartefakte benötigen, sodass Sie sie beispielsweise mit anderen Mitgliedern der Organisation oder Ihren Kunden gemeinsam nutzen oder ein Archiv pflegen können. Ein anderes Szenario liegt vor, wenn für Ihren Buildauftrag an sich andere Dateien erforderlich sind, beispielsweise als Teil der Buildeingabe herunterzuladende Abhängigkeiten.

In diesem Lernprogramm verwenden Sie das Azure Storage-Plug-In für Jenkins CI, das von Microsoft zur Verfügung gestellt wird.

## <a name="jenkins-overview"></a>Jenkins-Übersicht
Jenkins ermöglicht die fortlaufende Integration (Continuous Integration, CI) eines Softwareprojekts, da Entwickler ihre Codeänderungen auf einfache Weise einbinden und Builds automatisch und häufig erstellen lassen können. Dadurch wird die Produktivität der Entwickler gesteigert. Builds werden mit Versionsangaben versehen, und Buildartefakte können in verschiedene Repositorys hochgeladen werden. In diesem Artikel wird gezeigt, wie Sie Azure Blob Storage als Repository für die Buildartefakte verwenden. Außerdem wird gezeigt, wie Abhängigkeiten aus Azure Blob Storage heruntergeladen werden.

Weitere Informationen zu Jenkins finden Sie unter [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Vorteile der Verwendung des Blob-Diensts
Die Verwendung des Blob-Dienstes zum Hosten Ihrer Buildartefakte aus der agilen Entwicklung hat folgende Vorteile:

* Hochverfügbarkeit Ihrer Buildartefakte und/oder herunterladbaren Abhängigkeiten.
* Schnelles Hochladen Ihrer Buildartefakte durch Jenkins CI
* Schnelles Herunterladen Ihrer Buildartefakte durch Kunden und Partner
* Kontrolle über Benutzerzugriffsrichtlinien, mit Wahlmöglichkeiten zwischen anonymer Zugriff, Zugriff per Shared Access Signature mit Ablaufdatum, privater Zugriff usw.

## <a name="prerequisites"></a>Voraussetzungen
* Eine Jenkins Lösung für Continuous Integration.
  
    Wenn Sie noch keine Jenkins CI-Lösung im Einsatz haben, können Sie eine Jenkins CI-Lösung mithilfe folgender Methode ausführen:
  
  1. Laden Sie für einen Java-fähigen Computer „jenkins.war“ von <https://jenkins-ci.org> herunter.
  2. Führen Sie in einer Eingabeaufforderung, die für den Ordner, der "jenkins.war" enthält, geöffnet wird, folgenden Befehl aus:
     
      `java -jar jenkins.war`

  3. Öffnen Sie in Ihrem Browser `http://localhost:8080/`, um das Jenkins-Dashboard zu öffnen, über das Sie das Azure Storage-Plug-In installieren und konfigurieren können.
     
      Eine typische Jenkins CI-Lösung würde zwar zur Ausführung als Service konfiguriert, die Ausführung von "jenkins.war" über die Befehlszeile reicht für dieses Lernprogramm jedoch aus.
* Ein Azure-Konto. Unter <https://www.azure.com> können Sie sich für ein Azure-Konto registrieren.
* Ein Azure-Speicherkonto. Wenn Sie noch kein Speicherkonto haben, können Sie eines erstellen, indem Sie die Schritte unter [Erstellen eines Speicherkontos](../common/storage-quickstart-create-account.md)befolgen.
* Vorkenntnisse der Jenkins CI-Lösung werden empfohlen, sind aber nicht zwingend erforderlich, da in den folgenden Abschnitten ein einfaches Beispiel verwendet wird, um zu zeigen, welche Schritte erforderlich sind, wenn Sie den Blob-Dienst als Repository für Jenking CI-Buildartefakte nutzen möchten.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Verwenden des Blob-Diensts mit Jenkins CI
Um den Blob-Dienst mit Jenkins verwenden zu können, müssen Sie das Azure-Speicher-Plug-In installieren, das Plug-In für die Verwendung Ihres Speicherkontos konfigurieren und dann eine Postbuildaktion erstellen, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt. Diese Schritte sind in den folgenden Abschnitten beschrieben.

## <a name="how-to-install-the-azure-storage-plugin"></a>Installieren des Azure-Speicher-Plug-Ins
1. Wählen Sie im Jenkins-Dashboard **Manage Jenkins** (Jenkins verwalten) aus.
2. Wählen Sie auf der Seite **Manage Jenkins** (Jenkins verwalten) die Option **Manage Plugins** (Plug-Ins verwalten).
3. Wählen Sie die Registerkarte **Available** aus.
4. Aktivieren Sie im Abschnitt **Artifact Uploaders** das Kontrollkästchen **Microsoft Azure Storage plugin**.
5. Wählen Sie entweder **Install without restart** (Ohne Neustart installieren) oder **Download now and install after restart** (Jetzt herunterladen und nach Neustart installieren) aus.
6. Starten Sie Jenkins neu.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Konfigurieren des Azure-Speicher-Plug-Ins für die Verwendung Ihres Speicherkontos
1. Wählen Sie im Jenkins-Dashboard **Manage Jenkins** (Jenkins verwalten) aus.
2. Klicken Sie auf der Seite **Manage Jenkins** (Jenkins verwalten) auf **Configure System** (System konfigurieren).
3. Führen Sie im Bereich **Microsoft Azure Storage Account Configuration** folgende Schritte aus:
   1. Geben Sie Ihren Speicherkontonamen ein, den Sie aus dem [Azure-Portal](https://portal.azure.com)abrufen können.
   2. Geben Sie Ihren Speicherkontoschlüssel ein, der ebenfalls über das [Azure-Portal](https://portal.azure.com)abrufbar ist.
   3. Verwenden Sie den Standardwert für **Blob Service Endpoint URL**, wenn Sie die globale Azure-Cloud verwenden. Wenn Sie mit einer anderen Azure-Cloud arbeiten, verwenden Sie den Endpunkt, der im [Azure-Portal](https://portal.azure.com) für Ihr Speicherkonto angegeben ist. 
   4. Klicken Sie auf **Validate storage credentials** (Speicheranmeldeinformationen überprüfen), um Ihr Speicherkonto zu validieren. 
   5. [Optional] Wenn Sie über weitere Speicherkonten verfügen, die Sie für Jenkins CI verfügbar machen möchten, wählen Sie **Add more Storage Accounts** (Weitere Speicherkonten hinzufügen) aus.
   6. Klicken Sie auf **Save** (Speichern), um Ihre Einstellungen zu speichern.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Erstellen einer Postbuildaktion, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt
Für das Lernprogramm müssen Sie zunächst einen Auftrag erstellen, der mehrere Dateien erstellen wird, und ihn dann zur Postbuildaktion hinzufügen, um die Dateien in Ihr Speicherkonto hochzuladen.

1. Wählen Sie im Jenkins-Dashboard **New Item** (Neues Element) aus.
2. Nennen Sie den Auftrag **MyJob**, klicken Sie auf **Build a free-style software project** (Freestyle-Softwareprojekt erstellen), und klicken Sie dann auf **OK**.
3. Wählen Sie in der Auftragskonfiguration im Bereich **Build** (Erstellen) die Option **Add build step** (Buildschritt hinzufügen), und wählen Sie dann **Execute Windows batch command** (Windows-Batchbefehl ausführen).
4. Verwenden Sie in **Command**folgende Befehle:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. Wählen Sie in der Auftragskonfiguration im Bereich **Postbuildaktionen** die Option **Postbuildaktion hinzufügen** aus, und wählen Sie **Artefakte in Azure Blob Storage hochladen**.
6. Wählen Sie unter **Storage account name**das zu verwendende Speicherkonto aus.
7. Geben Sie unter **Container name**den Containernamen ein. (Der Container wird erstellt, wenn er beim Hochladen der Buildartefakte noch nicht vorhanden ist.) Sie können Umgebungsvariablen verwenden. Geben Sie also für dieses Beispiel `${JOB_NAME}` als Containernamen ein.
   
    **Tipp**
   
    Unter dem Bereich **Command**, in dem Sie ein Skript für **Execute Windows batch command** eingegeben haben, befindet sich ein Link zu den von Jenkins erkannten Umgebungsvariablen. Wählen Sie diesen Link aus, um die Namen und Beschreibungen der Umgebungsvariablen anzuzeigen. Umgebungsvariablen, die Sonderzeichen enthalten, z.B. die Umgebungsvariable **BUILD_URL**, sind nicht als Containername oder gemeinsamer virtueller Pfad zulässig sind.
8. Wählen Sie für dieses Beispiel **Make new container public by default** (Neuen Container standardmäßig öffentlich machen) aus. (Wenn Sie einen privaten Container verwenden möchten, müssen Sie eine Shared Access Signature erstellen, um den Zugriff zu ermöglichen. Dies geht jedoch über den Rahmen dieses Artikels hinaus. Sie finden weitere Informationen zu Shared Access Signatures unter [Verwenden von Shared Access Signatures (SAS)](../storage-dotnet-shared-access-signature-part-1.md).
9. [Optional] Wählen Sie **Clean container before uploading** (Container vor dem Hochladen leeren), wenn die Inhalte aus dem Container gelöscht werden sollen, bevor die Buildartefakte hochgeladen (lassen Sie die Option deaktiviert, wenn die Inhalte nicht aus dem Container gelöscht werden sollen) werden.
10. Geben Sie unter **List of Artifacts to upload** (Liste der hochzuladenden Artefakte) die Zeichenfolge `text/*.txt` ein.
11. Geben Sie `${BUILD\_ID}/${BUILD\_NUMBER}` für die Zwecke dieses Tutorials unter **Common virtual path for uploaded artifacts** ein.
12. Klicken Sie auf **Save** (Speichern), um Ihre Einstellungen zu speichern.
13. Wählen Sie im Jenkins-Dashboard **Build Now** (Jetzt erstellen), um **MyJob** auszuführen. Prüfen Sie den Status in der Ausgabe der Konsole. Statusmeldungen für Azure-Speicher werden in die Ausgabe der Konsole aufgenommen, wenn die Postbuildaktion mit dem Hochladen von Buildartefakten beginnt.
14. Nach erfolgreichem Abschluss des Auftrags können Sie die Buildartefakte überprüfen, indem Sie den öffentlichen Blob öffnen.
    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
    2. Wählen Sie **Speicher**.
    3. Wählen Sie den Speicherkontonamen aus, den Sie für Jenkins verwendet haben.
    4. Wählen Sie **Container**aus.
    5. Wählen Sie den Container **myjob** aus. Dies ist die Version des Auftragsnamens, den Sie beim Erstellen des Jenkins-Auftrags zugewiesen haben, in Kleinbuchstaben. Containernamen und Blob-Namen bestehen im Azure-Speicher aus Kleinbuchstaben (es wird zwischen Groß- und Kleinschreibung unterschieden). In der Liste der Blobs für den Container **myjob** sollte **hello.txt** und **date.txt** angezeigt werden. Kopieren Sie die URL für beide Elemente, und öffnen Sie sie in Ihrem Browser. Sie sehen die Textdatei, die als Buildartefakt hochgeladen wurde.

Es kann nur eine Postbuildaktion pro Auftrag erstellt werden, die Artefakte in Azure Blob Storage hochlädt. Die einzelne „post-build“-Aktion zum Hochladen von Artefakten in Azure Blob Storage kann mithilfe eines Semikolons als Trennzeichen verschiedene Dateien (einschließlich Platzhalter) und Pfade zu Dateien in **List of Artifacts to upload** angeben. Wenn Ihr Jenkins-Build beispielsweise JAR- und TXT-Dateien im Ordner **build** Ihres Arbeitsbereichs erstellt und Sie beide in Azure Blob Storage hochladen möchten, verwenden Sie den folgenden Wert für die Option **Liste der hochzuladenden Artefakte**: `build/\*.jar;build/\*.txt`. Sie können auch eine Doppel-Doppelpunktsyntax verwenden, um einen im Blobnamen zu verwendenden Pfad anzugeben. Wenn beispielsweise die JAR-Dateien mithilfe von **binaries** im Blobpfad und die TXT-Dateien mithilfe von **notices** im Blobpfad hochgeladen werden sollen, verwenden Sie den folgenden Wert für die Option **List of Artifacts to upload** (Liste der hochzuladenden Artefakte): `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Erstellen eines Buildschritts für das Herunterladen aus Azure Blob Storage
Die folgenden Schritte veranschaulichen das Konfigurieren eines Buildschritts zum Herunterladen von Elementen aus dem Azure-Blobspeicher. Dies ist nützlich, wenn Sie Elemente in Ihren Build einbeziehen möchten. Ein Beispiel für die Verwendung dieses Musters sind die JAR-Dateien, die im Azure-Blobspeicher beibehalten werden sollten.

1. Wählen Sie in der Auftragskonfiguration im Abschnitt **Build** die Option **Add build step** (Buildschritt hinzufügen) aus, und wählen Sie dann **Download from Azure Blob storage** (Aus Azure-Blobspeicher herunterladen).
2. Wählen Sie unter **Storage account name**das zu verwendende Speicherkonto aus.
3. Geben Sie für **Container name**den Namen des Containers an, der die herunterzuladenden Blobs enthält. Sie können Umgebungsvariablen verwenden.
4. Geben Sie unter **Blob name** (Blobname) den Blobnamen ein. Sie können Umgebungsvariablen verwenden. Sie können auch ein Sternchen als einen Platzhalter verwenden, nachdem Sie den bzw. die Anfangsbuchstaben des Blobnamens angeben. Beispielsweise gibt **project\\** * alle Blobs an, deren Namen mit **project** beginnen.
5. [Optional] Geben Sie für **Downloadpfad** den Pfad auf dem Jenkins-Computer an, in den die Dateien aus Azure Blob Storage heruntergeladen werden sollen. Es können auch Umgebungsvariablen verwendet werden. (Wenn Sie keinen Wert für **Downloadpfad**angeben, werden die Dateien aus Azure Blob Storage in den Arbeitsbereich des Auftrags heruntergeladen.)

Wenn Sie zusätzliche Elemente aus Azure Blob Storage herunterladen möchten, können Sie zusätzliche Buildschritte erstellen.

Nach der Ausführung eines Builds können Sie die Build-Verlaufskonsolenausgabe prüfen oder den Downloadspeicherort aufrufen, um zu prüfen, ob die von Ihnen erwarteten Blobs erfolgreich heruntergeladen wurden.  

## <a name="components-used-by-the-blob-service"></a>Vom Blob-Dienst verwendete Komponenten
In diesem Abschnitt erhalten Sie einen Überblick über die Komponenten des Blob-Dienstes.

* **Storage Account** (Speicherkonto): Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Ein Speicherkonto ist die höchste Ebene des Namespaces für den Zugriff auf Blobs. Ein Konto kann eine beliebige Anzahl von Containern enthalten, solange deren Gesamtgröße 100 TB nicht überschreitet.
* **Container**: Ein Container dient zum Gruppieren mehrerer Blobs. Alle BLOBs müssen sich in Containern befinden. Ein Konto kann eine beliebige Anzahl von Containern enthalten. In einem Container kann eine beliebige Anzahl von BLOBs gespeichert sein.
* **Blob**: Eine Datei von beliebiger Art und Größe. Es gibt zwei Arten von Blobs, die im Azure-Speicher gespeichert werden können: Block- und Seitenblobs. Die meisten Dateien sind Block-BLOBs. Ein einzelner Block-Blob kann bis zu 200 GB groß sein. In diesem Tutorial werden Block-BLOBs verwendet. Der andere Blob-Typ, Seiten-Blobs, kann bis zu 1 TB groß sein und ist effizienter, wenn Byte-Bereiche in einer Datei häufig geändert werden. Weitere Informationen zu Blobs finden Sie unter [Understanding Block Blobs, Append Blobs, and Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) (Grundlegendes zu Block-, Anfüge- und Seitenblobs).
* **URL-Format:** Blobs können über das folgende URL-Format aufgerufen werden:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Das Format oben gilt für die globale Azure-Cloud. Wenn Sie mit einer anderen Azure-Cloud arbeiten, verwenden Sie den Endpunkt im [Azure-Portal](https://portal.azure.com) , um Ihren URL-Endpunkt zu bestimmen.)
  
    Bei obigem Format steht `storageaccount` für den Namen Ihres Speicherkontos, `container_name` für den Namen des Containers und `blob_name` für den Namen des Blobs. Der Containername kann mehrere Pfade umfassen, die durch einen Schrägstrich ( **/** ) getrennt sind. Der Beispielcontainername in diesem Tutorial war **MyJob**, und **${BUILD\_ID}/${BUILD\_NUMBER}** wurde für den gemeinsamen virtuellen Pfad verwendet. Der Blob hat also eine URL in folgendem Format:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Problembehandlung beim Jenkins-Plug-In

Wenn bei den Jenkins-Plug-Ins Fehler auftreten, melden Sie das Problem auf der [Jenkins-JIRA-Seite](https://issues.jenkins-ci.org/) für die jeweilige Komponente.

## <a name="next-steps"></a>Nächste Schritte
* [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Azure Storage-SDK für Java](https://github.com/azure/azure-storage-java)
* [Referenz für Azure Storage-Client-SDKs](http://dl.windowsazure.com/storage/javadoc/)
* [REST-API für Azure-Speicherdienste](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage-Teamblog](https://blogs.msdn.com/b/windowsazurestorage/)

Weitere Informationen finden Sie im Artikel [Azure für Java-Entwickler](/java/azure).
