---
title: Tutorial`:` Verwenden einer verwalteten Identität zum Zugreifen auf Azure Data Lake Store – Windows – Azure AD
description: Dieses Tutorial veranschaulicht die Verwendung einer systemseitig verwalteten Identität eines virtuellen Windows-Computers für den Zugriff auf Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908fb1ac869ec2b22085af2e07ced6ff64229308
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592482"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Tutorial: Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Windows-Computers für den Zugriff auf Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für einen virtuellen Windows-Computer für den Zugriff auf Azure Data Lake Store verwenden. Verwaltete Dienstidentitäten werden von Azure automatisch verwaltet und ermöglichen Ihnen die Authentifizierung für Dienste, die die Azure AD-Authentifizierung unterstützen, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Gewähren des Zugriffs auf eine Azure Data Lake Store-Instanz durch Ihre VM
> * Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens für den Zugriff auf Azure Data Lake Store

## <a name="prerequisites"></a>Voraussetzungen

- Kenntnisse im Bereich verwaltete Identitäten. Wenn Sie mit der Funktion für verwaltete Identitäten für Azure-Ressourcen nicht vertraut sind, finden Sie hier eine [Übersicht](overview.md). 
- Ein Azure-Konto. [Registrieren Sie sich für ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/)
- Berechtigungen vom Typ „Besitzer“ für den entsprechenden Bereich (Ihr Abonnement oder die Ressourcengruppe), um die erforderlichen Schritte zur Ressourcenerstellung und Rollenverwaltung durchführen zu können. Wenn Sie Unterstützung bei der Rollenzuweisung benötigen, finden Sie weitere Informationen unter [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](../../role-based-access-control/role-assignments-portal.md).
- Außerdem benötigen Sie einen virtuellen Windows-Computer, auf dem systemseitig zugewiesene verwaltete Identitäten aktiviert sind.
  - Wenn Sie einen virtuellen Computer für dieses Tutorial erstellen müssen, können Sie den Artikel [Aktivieren einer vom System zugewiesenen verwalteten Identität beim Erstellen eines virtuellen Computers](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) durcharbeiten.




## <a name="enable"></a>Aktivieren

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Gewähren von Zugriff

Sie können Ihrem virtuellen Computer nun Zugriff auf Dateien und Ordner in einer Azure Data Lake Store-Instanz gewähren.  Für diesen Schritt können Sie eine vorhandene Data Lake Store-Instanz verwenden oder eine neue erstellen.  Um im Azure-Portal eine neue Data Lake Store-Instanz zu erstellen, führen Sie diesen [Schnellstart zu Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) durch. Es gibt in der [Dokumentation zu Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md) auch Schnellstarts, in denen die Azure-Befehlszeilenschnittstelle und Azure PowerShell verwendet werden.

Erstellen Sie in Data Lake Store einen neuen Ordner, und gewähren Sie für die systemseitig zugewiesene Identität des virtuellen Computers die Berechtigungen zum Lesen, Schreiben und Ausführen von Dateien in diesem Ordner:

1. Klicken Sie im Azure-Portal im linken Navigationsbereich auf **Data Lake Store**.
2. Klicken Sie auf die Data Lake Store-Instanz, die Sie für dieses Tutorial verwenden möchten.
3. Klicken Sie auf der Befehlsleiste auf **Daten-Explorer**.
4. Der Stammordner der Data Lake Store-Instanz ist ausgewählt.  Klicken Sie auf der Befehlsleiste auf **Zugriff**.
5. Klicken Sie auf **Hinzufügen**.  Geben Sie im Feld **Auswählen** den Namen Ihres virtuellen Computers ein, z.B. **DevTestVM**.  Klicken Sie in den Suchergebnissen auf Ihren virtuellen Computer, um ihn zu markieren, und klicken Sie dann auf **Auswählen**.
6. Klicken Sie auf **Berechtigungen auswählen**.  Wählen Sie **Lesen** und **Ausführen** aus, und fügen Sie die Berechtigungen **diesem Ordner** und **nur als Zugriffsberechtigung** hinzu.  Klicken Sie auf **OK**.  Die Berechtigung sollte erfolgreich hinzugefügt werden.
7. Schließen Sie das Blatt **Zugriff**.
8. Erstellen Sie für dieses Tutorial einen neuen Ordner.  Klicken Sie auf der Befehlsleiste auf **Neuer Ordner**, und benennen Sie den neuen Ordner z.B. mit **TestFolder**.  Klicken Sie auf **OK**.
9. Klicken Sie auf den Ordner, den Sie erstellt haben, und klicken Sie auf der Befehlsleiste auf **Zugriff**.
10. Klicken Sie wie in Schritt 5 auf **Hinzufügen**, geben Sie im Feld **Auswählen** den Namen Ihres virtuellen Computers ein, wählen Sie ihn aus, und klicken Sie dann auf **Auswählen**.
11. Klicken Sie wie in Schritt 6 auf **Berechtigungen auswählen**, wählen Sie **Lesen**, **Schreiben** und **Ausführen** aus, und fügen Sie diese Berechtigungen **diesem Ordner** als **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** hinzu.  Klicken Sie auf **OK**.  Die Berechtigung sollte erfolgreich hinzugefügt werden.

Ihre systemseitig zugewiesene verwaltete Identität des virtuellen Computers kann nun alle Vorgänge für Dateien in dem erstellten Ordner ausführen.  Weitere Informationen zum Verwalten des Zugriffs auf Data Lake Store finden Sie in diesem Artikel zur [Zugriffssteuerung in Data Lake Store](../../data-lake-store/data-lake-store-access-control.md).

## <a name="access-data"></a>Zugriff auf Daten

Azure Data Lake Store unterstützt die Azure AD-Authentifizierung nativ, sodass Zugriffstoken, die mit verwalteten Identitäten für Azure-Ressourcen abgerufen wurden, direkt angenommen werden können.  Für die Authentifizierung im Data Lake Store-Dateisystem übermitteln Sie ein Zugriffstoken, das von Azure AD für Ihren Endpunkt des Data Lake Store-Dateisystems ausgestellt wurde, in einem Autorisierungsheader im Format „Bearer <ZUGRIFFSTOKENWERT>“.  Weitere Informationen zur Unterstützung von Data Lake Store für die Azure AD-Authentifizierung finden Sie unter [Authentifizierung bei Data Lake Store mithilfe von Azure Active Directory](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).

> [!NOTE]
> Die Client-SDKs für das Data Lake Store-Dateisystem unterstützen noch keine verwalteten Identitäten für Azure-Ressourcen.  Dieses Tutorial wird aktualisiert, wenn die Unterstützung im SDK hinzugefügt wird.

In diesem Tutorial authentifizieren Sie sich bei der REST-API für das Data Lake Store-Dateisystem mit REST-Anforderungen in PowerShell. Um die systemseitig zugewiesene verwaltete Identität des virtuellen Computers für die Authentifizierung zu verwenden, müssen Sie die Anforderungen auf dem virtuellen Computer auszuführen.

1. Navigieren Sie im Portal zu **Virtuelle Computer**, wechseln Sie dann zu Ihrem virtuellen Windows-Computer, und klicken Sie in der **Übersicht** auf **Verbinden**.
2. Geben Sie Ihren **Benutzernamen** und Ihr **Kennwort** ein, das Sie beim Erstellen des virtuellen Windows-Computers hinzugefügt haben. 
3. Sie haben nun eine **Remotedesktopverbindung** mit dem virtuellen Computer erstellt. Öffnen Sie jetzt **PowerShell** in der Remotesitzung. 
4. Erstellen Sie mithilfe des PowerShell-Befehls `Invoke-WebRequest` eine Anforderung an den lokalen Endpunkt für die verwalteten Identitäten für Azure-Ressourcen, um ein Zugriffstoken für Azure Data Lake Store zu erhalten.  Der Ressourcenbezeichner für Data Lake Store lautet `https://datalake.azure.net/`.  Data Lake führt eine Prüfung auf genaue Übereinstimmung der Ressourcen-ID durch, bei der auf der abschließende Schrägstrich wichtig ist.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Konvertieren Sie die Antwort aus einem JSON-Objekt in ein PowerShell-Objekt. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extrahieren Sie das Zugriffstoken aus der Antwort.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Führen Sie mithilfe des PowerShell-Befehls „Invoke-WebRequest“ eine Anforderung an den REST-Endpunkt Ihrer Data Lake Store-Instanz durch, um die Ordner im Stammordner aufzulisten.  Damit können Sie ganz einfach überprüfen, ob alles richtig konfiguriert ist.  Es ist wichtig, dass die Zeichenfolge „Bearer“ im Autorisierungsheader mit dem Großbuchstaben „B“ beginnt.  Sie finden den Namen Ihrer Data Lake Store-Instanz im Abschnitt **Übersicht** auf dem Data Lake Store-Blatt im Azure-Portal.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Eine erfolgreiche Antwort sieht wie folgt aus:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Jetzt können Sie versuchen, eine Datei in Ihre Data Lake Store-Instanz hochzuladen.  Erstellen Sie zunächst eine Datei zum Hochladen.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Erstellen Sie mit dem PowerShell-Befehl `Invoke-WebRequest` eine Anforderung an den REST-Endpunkt Ihrer Data Lake Store-Instanz, um die Datei in den zuvor erstellten Ordner hochzuladen.  Diese Anforderung wird in zwei Schritten durchgeführt.  Im ersten Schritt übermitteln Sie die Anforderung und erhalten eine Umleitung an den Speicherort, an den die Datei hochgeladen werden soll.  Im zweiten Schritt führen Sie den eigentlichen Upload der Datei durch.  Denken Sie daran, den Namen des Ordners und der Datei ordnungsgemäß anzupassen, wenn Sie andere Werte als in diesem Tutorial verwenden. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Wenn Sie den Wert von `$HdfsRedirectResponse` untersuchen, sollte dieser der folgenden Antwort ähneln:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Schließen Sie den Upload ab, indem Sie eine Anforderung an den Umleitungsendpunkt senden:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Eine erfolgreiche Antwort sieht wie folgt aus:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Mithilfe anderer APIs für das Data Lake Store-Dateisystem können Sie Dateien anfügen und herunterladen und viele andere Aufgaben durchführen.


## <a name="disable"></a>Disable

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene verwaltete Identität für einen virtuellen Windows-Computer verwenden, um auf eine Azure Data Lake Store-Instanz zuzugreifen. Weitere Informationen zu Azure Data Lake Store finden Sie hier:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)