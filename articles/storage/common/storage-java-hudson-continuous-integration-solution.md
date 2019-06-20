---
title: Verwenden von Hudson mit Blobspeicher | Microsoft Docs
description: Beschreibt die Verwendung von Hudson mit Azure Blob Storage als Repository für Buildartefakte.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 4b47af857fada453e36fcb0c23e6d89e5ad90e42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154341"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Verwenden von Azure-Speicher mit einer Hudson-Lösung für die fortlaufende Integration
## <a name="overview"></a>Übersicht
Die folgenden Informationen zeigen die Verwendung von Blob Storage als Repository von Buildartefakten, die durch eine Hudson Continuous Integration-Lösung (CI) erstellt wurden, oder als eine Quelle von herunterladbaren Dateien, die in einem Buildvorgang verwendet werden. Dies ist zum Beispiel dann hilfreich, wenn Sie in einer agilen Entwicklungsumgebung (in Java oder anderen Sprachen) programmieren, Builds auf Basis der fortlaufenden Integration ausgeführt werden, und Sie ein Repository für Ihre Buildartefakte benötigen, sodass Sie sie beispielsweise mit anderen Mitgliedern der Organisation oder Ihren Kunden gemeinsam nutzen oder ein Archiv pflegen können.  Ein anderes Szenario liegt vor, wenn für Ihren Buildauftrag an sich andere Dateien erforderlich sind, beispielsweise als Teil der Buildeingabe herunterzuladende Abhängigkeiten.

In diesem Lernprogramm verwenden Sie das Azure-Speicher-Plug-In für Hudson CI, das von Microsoft zur Verfügung gestellt wird.

## <a name="introduction-to-hudson"></a>Einführung in Hudson
Hudson ermöglicht die fortlaufende Integration (Continuous Integration, CI) eines Softwareprojekts, da Entwickler ihre Codeänderungen auf einfache Weise einbinden und Builds automatisch und häufig erstellen lassen können. Dadurch wird die Produktivität der Entwickler gesteigert. Builds werden mit Versionsangaben versehen, und Buildartefakte können in verschiedene Repositorys hochgeladen werden. In diesem Artikel wird gezeigt, wie Sie Azure Blob Storage als Repository für die Buildartefakte verwenden. Außerdem wird gezeigt, wie Abhängigkeiten aus Azure Blob Storage heruntergeladen werden.

Weitere Informationen zu Hudson finden Sie unter [Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Vorteile der Verwendung des Blob-Diensts
Die Verwendung des Blob-Dienstes zum Hosten Ihrer Buildartefakte aus der agilen Entwicklung hat folgende Vorteile:

* Hochverfügbarkeit Ihrer Buildartefakte und/oder herunterladbaren Abhängigkeiten.
* Schnelles Hochladen Ihrer Buildartefakte durch Hudson CI.
* Schnelles Herunterladen Ihrer Buildartefakte durch Kunden und Partner
* Kontrolle über Benutzerzugriffsrichtlinien, mit Wahlmöglichkeiten zwischen anonymer Zugriff, Zugriff per Shared Access Signature mit Ablaufdatum, privater Zugriff usw.

## <a name="prerequisites"></a>Voraussetzungen
Sie müssen folgende Voraussetzungen erfüllen, um den Blob-Dienst mit Ihrer Hudson CI-Lösung zu verwenden:

* Eine Hudson-Lösung für die fortlaufende Integration.
  
    Wenn Sie noch keine Hudson CI-Lösung im Einsatz haben, können Sie eine Hudson CI-Lösung auf folgende Weise ausführen:
  
  1. Laden Sie für einen Java-fähigen Computer unter <http://hudson-ci.org/> die Hudson-WAR-Datei herunter.
  2. Führen Sie in einer Eingabeaufforderung im Ordner, der Hudson.WAR enthält, den folgenden Befehl aus: Falls Sie zum Beispiel Version 3.1.2 heruntergeladen haben:
     
      `java -jar hudson-3.1.2.war`

  3. Öffnen Sie `http://localhost:8080/`in Ihrem Browser . Das Hudson-Dashboard wird geöffnet.
  4. Schließen Sie vor der ersten Verwendung von Hudson die initiale Einrichtung unter `http://localhost:8080/`ab.
  5. Nach Abschluss der Ersteinrichtung können Sie die laufende Instanz der Hudson-WAR-Datei anhalten und neu starten und das Hudson-Dashboard unter `http://localhost:8080/` erneut öffnen. Dort können Sie nun das Plug-In für den Azure-Speicher installieren und konfigurieren.
     
      Eine typische Hudson CI-Lösung würde zwar zur Ausführung als Service konfiguriert, die Ausführung von "hudson.war" über die Befehlszeile reicht für dieses Lernprogramm jedoch aus.
* Ein Azure-Konto. Unter <https://www.azure.com> können Sie sich für ein Azure-Konto registrieren.
* Ein Azure-Speicherkonto. Wenn Sie noch kein Speicherkonto haben, können Sie eines erstellen, indem Sie die Schritte unter [Erstellen eines Speicherkontos](../common/storage-quickstart-create-account.md)befolgen.
* Vorkenntnisse der Hudson CI-Lösung werden empfohlen, sind aber nicht zwingend erforderlich, da in den folgenden Abschnitten ein einfaches Beispiel verwendet wird, um zu zeigen, welche Schritte erforderlich sind, wenn Sie den Blob-Dienst als Repository für Hudson CI-Buildartefakte nutzen möchten.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Verwenden des Blob-Dienstes mit Hudson CI
Um den Blob-Dienst mit Hudson verwenden zu können, müssen Sie das Azure-Speicher-Plug-In installieren, das Plug-In für die Verwendung Ihres Speicherkontos konfigurieren und dann eine Postbuildaktion erstellen, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt. Diese Schritte sind in den folgenden Abschnitten beschrieben.

## <a name="how-to-install-the-azure-storage-plugin"></a>Installieren des Azure-Speicher-Plug-Ins
1. Klicken Sie im Hudson-Dashboard auf **Manage Hudson**.
2. Klicken Sie auf der Seite **Manage Hudson** (Hudson verwalten) auf **Manage Plugins** (Plug-Ins verwalten).
3. Klicken Sie auf die Registerkarte **Available** .
4. Klicken Sie auf **Others**.
5. Wählen Sie im Abschnitt **Artifact Uploaders** (Artefaktuploadprogramme) die Option **Microsoft Azure Storage-Plug-In**.
6. Klicken Sie auf **Installieren**.
7. Starten Sie Hudson nach Abschluss der Installation neu.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Konfigurieren des Azure-Speicher-Plug-Ins für die Verwendung Ihres Speicherkontos
1. Klicken Sie im Hudson-Dashboard auf **Manage Hudson**.
2. Klicken Sie auf der Seite **Manage Hudson** (Hudson verwalten) auf **Configure System** (System konfigurieren).
3. Führen Sie im Bereich **Microsoft Azure Storage Account Configuration** folgende Schritte aus:
   
    a. Geben Sie Ihren Speicherkontonamen ein, den Sie aus dem [Azure-Portal](https://portal.azure.com)abrufen können.
   
    b. Geben Sie Ihren Speicherkontoschlüssel ein, der ebenfalls über das [Azure-Portal](https://portal.azure.com)abrufbar ist.
   
    c. Verwenden Sie den Standardwert für **Blob Service Endpoint URL** , wenn Sie die öffentliche Azure-Cloud verwenden. Wenn Sie mit einer anderen Azure-Cloud arbeiten, verwenden Sie den Endpunkt, der im [Azure-Portal](https://portal.azure.com) für Ihr Speicherkonto angegeben ist.
   
    d. Klicken Sie auf **Validate storage credentials** , um Ihr Speicherkonto zu validieren.
   
    e. [Optional] Wenn Sie über weitere Speicherkonten verfügen, die Sie für Hudson CI verfügbar machen möchten, klicken Sie auf **Weitere Speicherkonten hinzufügen**.
   
    f. Klicken Sie auf **Save**, um Ihre Einstellungen zu speichern.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Erstellen einer Postbuildaktion, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt
Für das Lernprogramm müssen wir zunächst einen Auftrag erstellen, der mehrere Dateien erstellen wird, und ihn dann zur Postbuildaktion hinzufügen, um die Dateien in Ihr Speicherkonto hochzuladen.

1. Klicken Sie im Hudson-Dashboard auf **New Job**.
2. Nennen Sie den Auftrag **MyJob**, klicken Sie auf **Build a free-style software project** (Benutzerdefiniertes Softwareprojekt erstellen), und klicken Sie dann auf **OK**.
3. Klicken Sie in der Auftragskonfiguration im Bereich **Build** auf **Add build step** (Buildschritt hinzufügen), und wählen Sie dann **Execute Windows batch command** (Windows-Batchbefehl ausführen) aus.
4. Verwenden Sie in **Command**folgende Befehle:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. Klicken Sie in der Auftragskonfiguration im Bereich **Post-build Actions** (Aktionen nach dem Erstellen) auf **Upload artifacts to Microsoft Azure Blob storage** (Artefakte in Microsoft Azure Blob Storage hochladen).
6. Wählen Sie unter **Storage Account Name**das zu verwendende Speicherkonto aus.
7. Geben Sie unter **Container Name**den Containernamen ein. (Der Container wird erstellt, wenn er beim Hochladen der Buildartefakte noch nicht vorhanden ist.) Sie können Umgebungsvariablen verwenden. Geben Sie also für dieses Beispiel **${JOB_NAME}** als Containernamen ein.
   
    **Tipp**
   
    Unterhalb des Bereichs **Command** (Befehl), in dem Sie ein Skript für **Execute Windows batch command** (Windows-Batchbefehl ausführen) eingegeben haben, befindet sich ein Link zu den von Hudson erkannten Umgebungsvariablen. Klicken Sie auf diesen Link, um die Namen und Beschreibungen der Umgebungsvariablen anzuzeigen. Beachten Sie, dass Umgebungsvariablen, die Sonderzeichen enthalten, z.B. die Umgebungsvariable **BUILD_URL**, nicht als Containername oder gemeinsamer virtueller Pfad zulässig sind.
8. Klicken Sie für dieses Beispiel auf **Make new container public by default** . (Wenn Sie einen privaten Container verwenden möchten, müssen Sie eine Shared Access Signature erstellen, um den Zugriff zu ermöglichen. Dies geht jedoch über den Rahmen dieses Artikels hinaus. Sie finden weitere Informationen zu Shared Access Signatures unter [Verwenden von Shared Access Signatures (SAS)](../storage-dotnet-shared-access-signature-part-1.md).
9. [Optional] Klicken Sie auf **Clean container before uploading** , wenn die Inhalte aus dem Container gelöscht werden sollen, bevor die Buildartefakte hochgeladen (lassen Sie die Option deaktiviert, wenn die Inhalte nicht aus dem Container gelöscht werden sollen) werden.
10. Geben Sie unter **List of Artifacts to upload** (Liste der hochzuladenden Artefakte) die Zeichenfolge **text/*.txt** ein.
11. Geben Sie unter **Common virtual path for uploaded artifacts** (Gemeinsamer virtueller Pfad für hochgeladene Artefakte) die Zeichenfolge **${BUILD\_ID}/${BUILD\_NUMBER}** ein.
12. Klicken Sie auf **Save** , um Ihre Einstellungen zu speichern.
13. Klicken Sie im Hudson-Dashboard auf **Build Now** (Jetzt erstellen), um **MyJob** auszuführen. Prüfen Sie den Status in der Ausgabe der Konsole. Statusmeldungen für Azure Storage werden in die Ausgabe der Konsole aufgenommen, wenn die Postbuildaktion mit dem Hochladen von Buildartefakten beginnt.
14. Nach erfolgreichem Abschluss des Auftrags können Sie die Buildartefakte überprüfen, indem Sie den öffentlichen Blob öffnen.
    
    a. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
    
    b. Klicken Sie auf **Storage**.
    
    c. Klicken Sie auf den Speicherkontonamen, den Sie für Hudson verwendet haben.
    
    d. Klicken Sie auf **Container**.
    
    e. Klicken Sie auf den Container **myjob**. Dies ist die Version des Auftragsnamens, den Sie beim Erstellen des Hudson-Auftrags zugewiesen haben, in Kleinbuchstaben. Containernamen und Blobnamen bestehen in Azure Storage aus Kleinbuchstaben (es wird zwischen Groß- und Kleinschreibung unterschieden). In der Liste der Blobs für den Container **myjob** sollte **hello.txt** und **date.txt** angezeigt werden. Kopieren Sie die URL für beide Elemente, und öffnen Sie sie in Ihrem Browser. Sie sehen die Textdatei, die als Buildartefakt hochgeladen wurde.

Es kann nur eine Postbuildaktion pro Auftrag erstellt werden, die Artefakte in Azure Blob Storage hochlädt. Beachten Sie, dass die einzelne Postbuild-Aktion zum Hochladen von Artefakten in Azure Blob Storage mithilfe eines Semikolons als Trennzeichen verschiedene Dateien (einschließlich Platzhalter) und Pfade zu Dateien in **Liste der hochzuladenden Artefakte** angeben kann. Wenn Ihr Hudson-Build beispielsweise JAR- und TXT-Dateien im Ordner **build** Ihres Arbeitsbereichs erstellt und Sie beide in Azure Blob Storage hochladen möchten, verwenden Sie Folgendes für den Wert **List of Artifacts to upload** (Liste der hochzuladenden Artefakte): **build/\*.jar;build/\*.txt**. Sie können auch eine Doppel-Doppelpunktsyntax verwenden, um einen im Blobnamen zu verwendenden Pfad anzugeben. Wenn beispielsweise die JAR-Dateien mithilfe von **binaries** im Blobpfad und die TXT-Dateien mithilfe von **notices** im Blobpfad hochgeladen werden sollen, verwenden Sie Folgendes für den Wert **List of Artifacts to upload** (Liste der hochzuladenden Artefakte): **build/\*.jar::binaries;build/\*.txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Erstellen eines Buildschritts für das Herunterladen aus Azure Blob Storage
Die folgenden Schritte zeigen, wie Sie einen Buildschritt konfigurieren, damit Elemente aus Azure Blob Storage heruntergeladen werden. Dies ist hilfreich, wenn Sie Elemente in Ihren Build einbeziehen möchten, beispielsweise JAR-Dateien, die in Azure Blob Storage gespeichert sind.

1. Klicken Sie in der Auftragskonfiguration im Abschnitt **Build** auf **Add build step** (Buildschritt hinzufügen), und wählen Sie dann **Download from Azure Blob storage** (Aus Azure Blob Storage herunterladen) aus.
2. Wählen Sie unter **Storage account name**das zu verwendende Speicherkonto aus.
3. Geben Sie für **Container name**den Namen des Containers an, der die herunterzuladenden Blobs enthält. Sie können Umgebungsvariablen verwenden.
4. Geben Sie unter **Blob name** (Blobname) den Blobnamen ein. Sie können Umgebungsvariablen verwenden. Sie können auch ein Sternchen als einen Platzhalter verwenden, nachdem Sie den bzw. die Anfangsbuchstaben des Blobnamens angeben. Beispielsweise würde **project\\** * alle Blobs angeben, deren Name mit **project** beginnt.
5. [Optional] Geben Sie für **Downloadpfad** den Pfad auf dem Hudson-Computer an, in den die Dateien aus Azure Blob Storage heruntergeladen werden sollen. Es können auch Umgebungsvariablen verwendet werden. (Wenn Sie keinen Wert für **Downloadpfad** angeben, werden die Dateien aus Azure Blob Storage in den Arbeitsbereich des Auftrags heruntergeladen.)

Wenn Sie zusätzliche Elemente aus Azure Blob Storage herunterladen möchten, können Sie zusätzliche Buildschritte erstellen.

Nach der Ausführung eines Builds können Sie die Build-Verlaufskonsolenausgabe prüfen oder den Downloadspeicherort aufrufen, um zu prüfen, ob die von Ihnen erwarteten Blobs erfolgreich heruntergeladen wurden.

## <a name="components-used-by-the-blob-service"></a>Vom Blob-Dienst verwendete Komponenten
Im Folgenden erhalten Sie einen Überblick über die Komponenten des Blob-Dienstes.

* **Speicherkonto**: Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Dies ist die höchste Ebene des Namespaces für den Zugriff auf Blobs. Ein Konto kann eine beliebige Anzahl von Containern enthalten, solange deren Gesamtgröße 100 TB nicht überschreitet.
* **Container**: Ein Container dient zum Gruppieren mehrerer Blobs. Alle BLOBs müssen sich in Containern befinden. Ein Konto kann eine beliebige Anzahl von Containern enthalten. In einem Container kann eine beliebige Anzahl von BLOBs gespeichert sein.
* **Blob**: Eine Datei von beliebiger Art und Größe. Es gibt zwei Arten von Blobs, die im Azure-Speicher gespeichert werden können: Block- und Seitenblobs. Die meisten Dateien sind Block-BLOBs. Ein einzelner Block-Blob kann bis zu 200 GB groß sein. In diesem Tutorial werden Block-BLOBs verwendet. Der andere Blob-Typ, Seiten-Blobs, kann bis zu 1 TB groß sein und ist effizienter, wenn Byte-Bereiche in einer Datei häufig geändert werden. Weitere Informationen zu Blobs finden Sie unter [Understanding Block Blobs, Append Blobs, and Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) (Grundlegendes zu Block-, Anfüge- und Seitenblobs).
* **URL-Format:** Blobs können über das folgende URL-Format aufgerufen werden:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Das Format oben gilt für die öffentliche Azure-Cloud. Wenn Sie mit einer anderen Azure-Cloud arbeiten, verwenden Sie den Endpunkt im [Azure-Portal](https://portal.azure.com) , um Ihren URL-Endpunkt zu bestimmen.)
  
    Bei obigem Format steht `storageaccount` für den Namen Ihres Speicherkontos, `container_name` für den Namen des Containers und `blob_name` für den Namen des Blobs. Der Containername kann mehrere Pfade umfassen, die durch einen Schrägstrich ( **/** ) getrennt sind. Der Beispielcontainername in diesem Lernprogramm war **MyJob**, und **${BUILD\_ID}/${BUILD\_NUMBER}** wurde für den gemeinsamen virtuellen Pfad verwendet. Der Blob hat also eine URL in folgendem Format:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Nächste Schritte
* [Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage-SDK für Java](https://github.com/azure/azure-storage-java)
* [Referenz für Azure Storage-Client-SDKs](http://dl.windowsazure.com/storage/javadoc/)
* [REST-API für Azure-Speicherdienste](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage-Teamblog](https://blogs.msdn.com/b/windowsazurestorage/)

Weitere Informationen finden Sie im Artikel [Azure für Java-Entwickler](/java/azure).
