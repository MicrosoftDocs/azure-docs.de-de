---
title: '.NET SDK: Kontoverwaltungsvorgänge in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation'
description: Verwenden des Azure Data Lake Storage Gen1 .NET SDK, um Kontoverwaltungsvorgänge in Data Lake Storage Gen1 auszuführen
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8da40aa04381542c8c750c8d7e33c9a29879371d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900876"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 mithilfe des .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

In diesem Artikel erfahren Sie, wie Sie mithilfe des .NET SDK Kontoverwaltungsvorgänge in Azure Data Lake Storage Gen1 ausführen. Zu den Kontoverwaltungsvorgängen zählen unter anderem das Erstellen eines Data Lake Storage Gen1-Kontos, das Auflisten der Konten in einem Azure-Abonnement und das Löschen der Konten.

Eine Anleitung zum Durchführen von Datenverwaltungsvorgängen in Data Lake Storage Gen1 per .NET SDK finden Sie unter [Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit dem .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Voraussetzungen
* **Visual Studio 2013 und höher**. In der Anleitung unten wird Visual Studio 2019 verwendet.

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

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
5. Öffnen Sie **Program.cs**, löschen Sie den vorhandenen Code, und fügen Sie dann die folgenden Anweisungen ein, um Verweise auf Namespaces hinzuzufügen.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6. Deklarieren Sie die Variablen, und geben Sie die Werte für Platzhalter an. Stellen Sie außerdem sicher, dass der angegebene lokale Pfad und der Dateiname auf dem Computer vorhanden sind.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

In den restlichen Abschnitten dieses Artikels erfahren Sie, wie Sie die verfügbaren .NET-Methoden verwenden, um Vorgänge wie Authentifizierung, Dateiupload usw. durchzuführen.

## <a name="authentication"></a>Authentication

* Informationen zur Authentifizierung von Endbenutzern für Ihre Anwendung finden Sie unter [Authentifizierung von Endbenutzern mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Informationen zur Dienst-zu-Dienst-Authentifizierung für Ihre Anwendung finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Erstellen des Clientobjekts
Im folgenden Codeausschnitt wird das Data Lake Storage Gen1-Kontoclientobjekt erstellt, das verwendet wird, um Kontoverwaltungsanforderungen (etwa zum Erstellen oder Löschen eines Kontos) an den Dienst zu senden.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Erstellen eines Data Lake Storage Gen1-Kontos
Im folgenden Codeausschnitt wird ein Data Lake Storage Gen1-Konto in dem Azure-Abonnement erstellt, das Sie beim Erstellen des Data Lake Storage Gen1-Kontoclientobjekts angegeben haben.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Auflisten aller Data Lake Storage Gen1-Konten in einem Abonnement
Fügen Sie Ihrer Klassendefinition die folgende Methode hinzu: Mit dem folgenden Codeausschnitt werden alle Data Lake Storage Gen1-Konten innerhalb eines bestimmten Azure-Abonnements aufgelistet:

    // List all Data Lake Storage Gen1 accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-storage-gen1-account"></a>Löschen eines Data Lake Storage Gen1-Kontos
Im folgenden Codeausschnitt wird das zuvor erstellte Data Lake Storage Gen1-Konto gelöscht.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Weitere Informationen
* [Dateisystemvorgänge in Azure Data Lake Storage Gen1 mit dem .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [.NET SDK-Referenz zu Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Nächste Schritte
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
