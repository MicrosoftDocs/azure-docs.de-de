---
title: 'REST-API: Dateisystemvorgänge in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation'
description: Verwenden von WebHDFS-REST-APIs zum Ausführen von Dateisystemvorgängen in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 351c92f1e1a698893f61004d523ba79ebca253e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878782"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit der REST-API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

In diesem Artikel erfahren Sie, wie Sie mit WebHDFS-REST-APIs und Data Lake Storage Gen1-REST-APIs Dateisystemvorgänge in Azure Data Lake Storage Gen1 durchführen. Eine Anleitung zum Durchführen von Kontoverwaltungsvorgängen in Data Lake Storage Gen1 mit der REST-API finden Sie unter [Kontoverwaltungsvorgänge für Data Lake Storage Gen1 mit der REST-API](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Ein Azure Data Lake Storage Gen1-Konto**. Befolgen Sie die Anweisungen unter [Erste Schritte mit Azure Data Lake Storage Gen1 über das Azure-Portal](data-lake-store-get-started-portal.md).

* **[cURL](https://curl.haxx.se/)** . Dieser Artikel zeigt anhand von cURL, wie Sie REST-API-Aufrufe eines Data Lake Storage Gen1-Kontos ausführen.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Wie authentifiziere ich mich mithilfe von Azure Active Directory?
Zur Authentifizierung mit Azure Active Directory können Sie zwischen zwei Ansätzen wählen.

* Informationen zur Authentifizierung von Endbenutzern für Ihre Anwendung (interaktiv) finden Sie unter [Authentifizierung von Endbenutzern mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Informationen zur Dienst-zu-Dienst-Authentifizierung für Ihre Anwendung (nicht interaktiv) finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Erstellen Sie die Ordner.
Dieser Vorgang basiert auf dem [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)definierten WebHDFS-REST-API-Aufruf.

Verwenden Sie den folgenden cURL-Befehl. Ersetzen Sie **\<yourstorename>** durch Ihren Data Lake Storage Gen1-Kontonamen.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

Ersetzen Sie im vorherigen Befehl \<`REDACTED`\> durch das Autorisierungstoken, das Sie zuvor abgerufen haben. Dieser Befehl erstellt das Verzeichnis **mytempdir** im Stammordner Ihres Data Lake Storage Gen1-Kontos.

Wenn der Vorgang erfolgreich abgeschlossen wurde, sollte eine Antwort wie im folgenden Codeausschnitt angezeigt werden:

    {"boolean":true}

## <a name="list-folders"></a>Auflisten von Ordnern
Dieser Vorgang basiert auf dem [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)definierten WebHDFS-REST-API-Aufruf.

Verwenden Sie den folgenden cURL-Befehl. Ersetzen Sie **\<yourstorename>** durch Ihren Data Lake Storage Gen1-Kontonamen.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

Ersetzen Sie im vorherigen Befehl \<`REDACTED`\> durch das Autorisierungstoken, das Sie zuvor abgerufen haben.

Wenn der Vorgang erfolgreich abgeschlossen wurde, sollte eine Antwort wie im folgenden Codeausschnitt angezeigt werden:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Hochladen von Daten
Dieser Vorgang basiert auf dem [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)definierten WebHDFS-REST-API-Aufruf.

Verwenden Sie den folgenden cURL-Befehl. Ersetzen Sie **\<yourstorename>** durch Ihren Data Lake Storage Gen1-Kontonamen.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

In der vorherigen Syntax wird mit dem Parameter **-T** der Speicherort der hochgeladenen Datei angegeben.

Die Ausgabe sieht in etwa wie im folgenden Codeausschnitt aus:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Lesen von Daten
Dieser Vorgang basiert auf dem [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)definierten WebHDFS-REST-API-Aufruf.

Das Lesen von Daten aus einem Data Lake Storage Gen1-Konto ist ein zweistufiger Prozess.

* Sie müssen zuerst eine GET-Anforderung an den Endpunkt `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`senden. Mit diesem Aufruf wird ein Speicherort zurückgegeben, an den die nächste GET-Anforderung gesendet werden soll.
* Dann senden Sie die GET-Anforderung an den Endpunkt `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Mit diesem Aufruf wird der Inhalt der Datei angezeigt.

Da es in den Eingabeparametern keinen Unterschied zwischen dem ersten und zweiten Schritt gibt, können Sie mit dem `-L` -Parameter die erste Anforderung senden. Mit `-L` werden im Wesentlichen zwei Anforderungen in einer kombiniert, und es wird erreicht, dass cURL die Anforderung am neuen Speicherort noch einmal ausführt. Schließlich wird die Ausgabe aller Anforderungsaufrufe wie im folgenden Codeausschnitt dargestellt angezeigt. Ersetzen Sie **\<yourstorename>** durch Ihren Data Lake Storage Gen1-Kontonamen.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Die Ausgabe sollte in etwa wie folgender Codeausschnitt aussehen:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Umbenennen einer Datei
Dieser Vorgang basiert auf dem [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)definierten WebHDFS-REST-API-Aufruf.

Verwenden Sie zum Umbenennen einer Datei den folgenden cURL-Befehl. Ersetzen Sie **\<yourstorename>** durch Ihren Data Lake Storage Gen1-Kontonamen.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Die Ausgabe sollte in etwa wie folgender Codeausschnitt aussehen:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Löschen von Dateien
Dieser Vorgang basiert auf dem [hier](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)definierten WebHDFS-REST-API-Aufruf.

Verwenden Sie zum Löschen einer Datei den folgenden cURL-Befehl. Ersetzen Sie **\<yourstorename>** durch Ihren Data Lake Storage Gen1-Kontonamen.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Folgendes sollte angezeigt werden:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Nächste Schritte
* [Kontoverwaltungsvorgänge für Data Lake Storage Gen1 mit der REST-API](data-lake-store-get-started-rest-api.md)

## <a name="see-also"></a>Weitere Informationen
* [Azure Data Lake Storage Gen1 – REST-API-Referenz](https://docs.microsoft.com/rest/api/datalakestore/)
* [Mit Azure Data Lake Storage Gen1 kompatible Open-Source-Big Data-Anwendungen](data-lake-store-compatible-oss-other-applications.md)

