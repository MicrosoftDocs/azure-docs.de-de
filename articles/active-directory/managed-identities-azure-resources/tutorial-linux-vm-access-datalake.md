---
title: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Data Lake Store
description: Dieses Tutorial veranschaulicht die Verwendung einer systemseitig verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474165219d7740e63bb5e0a62d08069109a52c31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66236131"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Tutorial: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für einen virtuellen Linux-Computer für den Zugriff auf Azure Data Lake Storage verwenden. Folgendes wird vermittelt: 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Gewähren des Zugriffs auf Azure Data Lake Store durch Ihren virtuellen Computer
> * Abrufen eines Zugriffstokens mithilfe der systemseitig zugewiesenen verwalteten Identität eines virtuellen Computers zum Zugreifen auf Azure Data Lake Store

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Gewähren des Zugriffs auf Azure Data Lake Store durch Ihre VM

Sie können Ihrem virtuellen Computer nun Zugriff auf Dateien und Ordner in Azure Data Lake Store gewähren. Für diesen Schritt können Sie eine vorhandene Data Lake Store-Instanz verwenden oder eine neue erstellen. Um im Azure-Portal eine Data Lake Store-Instanz zu erstellen, führen Sie diesen [Schnellstart zu Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) durch. Es gibt in der [Dokumentation zu Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) auch Schnellstarts, in denen die Azure-Befehlszeilenschnittstelle und Azure PowerShell verwendet werden.

Erstellen Sie in Data Lake Store einen neuen Ordner, und gewähren Sie für die systemseitig zugewiesene verwaltete Identität des virtuellen Linux-Computers die Berechtigungen zum Lesen, Schreiben und Ausführen von Dateien in diesem Ordner:

1. Wählen Sie im Azure-Portal im linken Bereich **Data Lake Store**.
2. Wählen Sie die Data Lake Store-Instanz aus, die Sie verwenden möchten.
3. Klicken Sie auf der Befehlsleiste auf **Daten-Explorer**.
4. Der Stammordner der Data Lake Store-Instanz ist ausgewählt. Klicken Sie auf der Befehlsleiste auf **Zugriff**.
5. Wählen Sie **Hinzufügen**.  Geben Sie im Feld **Auswählen** den Namen Ihres virtuellen Computers ein, z.B. **DevTestVM**. Klicken Sie in den Suchergebnissen auf Ihren virtuellen Computer und dann auf **Auswählen**.
6. Klicken Sie auf **Berechtigungen auswählen**.  Wählen Sie **Lesen** und **Ausführen** aus, und fügen Sie die Berechtigungen **diesem Ordner** und **nur als Zugriffsberechtigung** hinzu. Klicken Sie auf **OK**.  Die Berechtigung sollte erfolgreich hinzugefügt werden.
7. Schließen Sie den Bereich **Zugriff**.
8. Erstellen Sie für dieses Tutorial einen neuen Ordner. Klicken Sie auf der Befehlsleiste auf **Neuer Ordner**, und nennen Sie den neuen Ordner beispielsweise **TestFolder**.  Klicken Sie auf **OK**.
9. Wählen Sie den erstellten Ordner aus, und klicken Sie dann auf der Befehlsleiste auf **Zugriff**.
10. Klicken Sie wie in Schritt 5 auf **Hinzufügen**. Geben Sie im Feld **Auswählen** den Namen Ihres virtuellen Computers ein. Klicken Sie in den Suchergebnissen auf Ihren virtuellen Computer und dann auf **Auswählen**.
11. Klicken Sie wie in Schritt 6 auf **Berechtigungen auswählen**. Wählen Sie **Lesen**, **Schreiben** und **Ausführen** aus, und fügen Sie diese Berechtigungen **diesem Ordner** als **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** hinzu. Klicken Sie auf **OK**.  Die Berechtigung sollte erfolgreich hinzugefügt werden.

Verwaltete Identitäten für Azure-Ressourcen können nun alle Vorgänge für Dateien in dem von Ihnen erstellten Ordner ausführen. Weitere Informationen zum Verwalten des Zugriffs auf Data Lake Store finden Sie unter [Zugriffssteuerung in Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Abrufen eines Zugriffstokens und Aufrufen des Data Lake Store-Dateisystems

Azure Data Lake Store unterstützt die Azure AD-Authentifizierung nativ, sodass Zugriffstoken, die mit verwalteten Identitäten für Azure-Ressourcen abgerufen wurden, direkt angenommen werden können. Für die Authentifizierung im Data Lake Store-Dateisystem übermitteln Sie ein Zugriffstoken, das von Azure AD für Ihren Endpunkt des Data Lake Store-Dateisystems ausgestellt wurde. Das Zugriffstoken ist in einem Autorisierungsheader im Format „Bearer \<ZUGRIFFSTOKENWERT\>“ enthalten.  Weitere Informationen zur Unterstützung von Data Lake Store für die Azure AD-Authentifizierung finden Sie unter [Authentifizierung bei Data Lake Store mithilfe von Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

In diesem Tutorial authentifizieren Sie sich bei der REST-API für das Data Lake Store-Dateisystem mit REST-Anforderungen in cURL.

> [!NOTE]
> Die Client-SDKs für das Data Lake Store-Dateisystem unterstützen noch keine verwalteten Identitäten für Azure-Ressourcen.

Zum Ausführen dieser Schritte benötigen Sie einen SSH-Client. Wenn Sie Windows verwenden, können Sie den SSH-Client im [Windows-Subsystem für Linux](https://msdn.microsoft.com/commandline/wsl/about) verwenden. Wenn Sie Hilfe beim Konfigurieren der SSH-Clientschlüssel benötigen, finden Sie unter [Verwenden von SSH-Schlüsseln mit Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) oder [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) weitere Informationen.

1. Navigieren Sie im Portal zu Ihrem virtuellen Linux-Computer. Klicken Sie unter **Übersicht** auf **Verbinden**.  
2. Stellen Sie mithilfe des gewünschten SSH-Clients eine Verbindung mit dem virtuellen Computer her. 
3. Senden Sie im Terminalfenster mit cURL eine Anforderung an den lokalen Endpunkt der verwalteten Identitäten für Azure-Ressourcen, um ein Zugriffstoken für das Data Lake Store-Dateisystem abzurufen. Der Ressourcenbezeichner für Data Lake Storage lautet „`https://datalake.azure.net/`“.  Es ist wichtig, den abschließenden Schrägstrich in den Ressourcenbezeichner einzuschließen.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Eine erfolgreiche Antwort gibt das Zugriffstoken zurück, das Sie zur Authentifizierung bei Data Lake Store verwenden:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Stellen Sie mithilfe von cURL eine Anforderung an den REST-Endpunkt Ihres Data Lake Store-Dateisystems, um die Ordner im Stammordner aufzulisten. Damit können Sie ganz einfach überprüfen, ob alles richtig konfiguriert ist. Kopieren Sie den Wert des Zugriffstokens aus dem vorherigen Schritt. Es ist wichtig, dass die Zeichenfolge „Bearer“ im Autorisierungsheader mit dem Großbuchstaben „B“ beginnt. Sie finden den Namen Ihrer Data Lake Store-Instanz im Abschnitt **Übersicht** im Bereich **Data Lake Store** im Azure-Portal.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Eine erfolgreiche Antwort sieht wie folgt aus:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Jetzt können Sie versuchen, eine Datei in Ihre Data Lake Store-Instanz hochzuladen. Erstellen Sie zunächst eine Datei zum Hochladen.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Stellen Sie mit cURL eine Anforderung an den REST-Endpunkt Ihres Data Lake Store-Dateisystems, um die Datei in den zuvor erstellten Ordner hochzuladen. Zum Upload gehört eine Umleitung, der cURL automatisch folgt. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Eine erfolgreiche Antwort sieht wie folgt aus:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Mithilfe anderer APIs für das Data Lake Store-Dateisystem können Sie Dateien anfügen und herunterladen und viele andere Aufgaben durchführen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene verwaltete Identität eines virtuellen Linux-Computers verwenden, um auf Azure Data Lake Store zuzugreifen. Weitere Informationen zu Azure Data Lake Store finden Sie hier:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
