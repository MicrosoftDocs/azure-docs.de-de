---
title: 'Endbenutzerauthentifizierung: .NET SDK mit Azure Data Lake Storage Gen1 mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie die Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory mit dem .NET SDK umsetzen.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 215b839c21c2590c08ac2f4250086eaf97914ce1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243711"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe des .NET SDK
> [!div class="op_single_selector"]
> * [Verwenden von Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Verwenden des .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Verwenden von Python](data-lake-store-end-user-authenticate-python.md)
> * [Verwenden der REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

In diesem Artikel erfahren Sie, wie Sie mithilfe des .NET SDK die Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 durchführen. Informationen zur Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe des .NET SDK finden Sie unter [Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe des .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Voraussetzungen
* **Visual Studio 2013 und höher**. In der Anleitung unten wird Visual Studio 2019 verwendet.

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Erstellen einer nativen Azure Active Directory-Anwendung.** Sie müssen die Schritte unter [Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) ausgeführt haben.

## <a name="create-a-net-application"></a>Erstellen einer .NET-Anwendung
1. Wählen Sie in Visual Studio das Menü **Datei**, dann die Option **Neu** und anschließend **Projekt** aus.
2. Wählen Sie **Konsolen-App (.NET Framework)** und dann **Weiter** aus.
3. Geben Sie unter **Projektname** `CreateADLApplication` ein, und wählen Sie dann **Erstellen** aus.

4. Fügen Sie Ihrem Projekt die NuGet-Pakete hinzu.

   1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und dann auf **NuGet-Pakete verwalten**.
   2. Stellen Sie auf der Registerkarte **NuGet-Paket-Manager** sicher, dass **Paketquelle** auf **nuget.org** festgelegt und das Kontrollkästchen **Vorabversion einbeziehen** aktiviert ist.
   3. Suchen und installieren Sie die folgenden NuGet-Pakete:

      * `Microsoft.Azure.Management.DataLake.Store`: In diesem Tutorial wird „v2.1.3-preview“ verwendet.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`: In diesem Tutorial wird „v2.2.12“ verwendet.

        ![Hinzufügen einer NuGet-Quelle](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Erstellen eines neuen Azure Data Lake-Kontos")
   4. Schließen Sie den **NuGet-Paket-Manager**.

5. Öffnen Sie **Program.cs**.
6. Ersetzen Sie die „using“-Anweisungen durch die folgenden Zeilen:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Authentifizierung von Endbenutzern
Fügen Sie diesen Codeausschnitt der .NET-Clientanwendung hinzu. Ersetzen Sie die Platzhalterwerte mit den aus einer (als Voraussetzung angegebenen) nativen Azure AD-Anwendung abgerufenen Werten. Mit diesem Codeausschnitt können Sie Ihre Anwendung **interaktiv** bei Data Lake Storage Gen1 authentifizieren, d.h., Sie werden aufgefordert, Ihre Azure-Anmeldeinformationen einzugeben.

Zur Vereinfachung werden im folgenden Codeausschnitt Standardwerte für Client-ID und Umleitungs-URI verwendet, die für jedes Azure-Abonnement gültig sind. Im folgenden Codeausschnitt müssen Sie nur den Wert für Ihre Mandanten-ID bereitstellen. Sie können mithilfe der Anweisungen unter [Abrufen der Mandanten-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) die Mandanten-ID abrufen.
    
- Ersetzen Sie die Main()-Funktion durch den folgenden Code:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Im Anschluss folgen einige wissenswerte Informationen zu obigem Codeausschnitt:

* Im vorangehenden Ausschnitt werden die Hilfsfunktionen `GetTokenCache` und `GetCreds_User_Popup` verwendet. Den Code für diese Hilfsfunktionen finden Sie [hier auf GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Zur Vereinfachung des Tutorials wird in dem Codeausschnitt eine native Anwendungsclient-ID verwendet, die standardmäßig für alle Azure-Abonnements verfügbar ist. Dadurch können Sie **diesen Codeausschnitt unverändert in Ihrer Anwendung verwenden**.
* Wenn Sie allerdings Ihre eigene Azure AD-Domäne und Anwendungsclient-ID verwenden möchten, müssen Sie eine native Azure AD-Anwendung erstellen und dann die Azure AD-Mandanten-ID, die Client-ID und den Umleitungs-URI für die erstellte Anwendung verwenden. Eine entsprechende Anleitung finden Sie unter [Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

  
## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierung von Endbenutzern verwenden, um sich mithilfe des .NET SDK bei Azure Data Lake Storage Gen1 zu authentifizieren. In den folgenden Artikeln wird erörtert, wie Sie das .NET SDK mit Azure Data Lake Storage Gen1 verwenden.

* [Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 mit dem .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit dem .NET SDK](data-lake-store-data-operations-net-sdk.md)

