---
title: 'REST-API: Kontoverwaltungsvorgänge in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie Azure Data Lake Storage Gen1 und die WebHDFS-REST-API verwenden, um im Data Lake Storage Gen1-Konto Kontoverwaltungsvorgänge auszuführen.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60877104"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 mit der REST-API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

In diesem Artikel wird beschrieben, wie Sie Kontoverwaltungsvorgänge in Azure Data Lake Storage Gen1 mit der REST-API durchführen. Zu den Kontoverwaltungsvorgängen gehören das Erstellen eines Data Lake Storage Gen1-Kontos, das Löschen eines Data Lake Storage Gen1-Kontos usw. Eine Anleitung, wie Sie Dateisystemvorgänge in Data Lake Storage Gen1 mit der REST-API durchführen, finden Sie unter [Dateisystemvorgänge in Data Lake Storage Gen1 mit der REST-API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)** . Dieser Artikel zeigt anhand von cURL, wie Sie REST-API-Aufrufe eines Data Lake Storage Gen1-Kontos ausführen.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Wie authentifiziere ich mich mithilfe von Azure Active Directory?
Zur Authentifizierung mit Azure Active Directory können Sie zwischen zwei Ansätzen wählen.

* Informationen zur Authentifizierung von Endbenutzern für Ihre Anwendung (interaktiv) finden Sie unter [Authentifizierung von Endbenutzern mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Informationen zur Dienst-zu-Dienst-Authentifizierung für Ihre Anwendung (nicht interaktiv) finden Sie unter [Dienst-zu-Dienst-Authentifizierung mit Data Lake Storage Gen1 über das .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Erstellen eines Data Lake Storage Gen1-Kontos
Dieser Vorgang basiert auf dem [hier](https://docs.microsoft.com/rest/api/datalakestore/accounts/create)definierten REST-API-Aufruf.

Verwenden Sie den folgenden cURL-Befehl. Ersetzen Sie **\<yourstoragegen1name>** durch Ihren Data Lake Storage Gen1-Namen.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Ersetzen Sie im Befehl oben \<`REDACTED`\> durch das Autorisierungstoken, das Sie zuvor abgerufen haben. Die Anforderungsnutzlast für diesen Befehl ist in der Datei **input.json** enthalten, die für den oben genannten `-d`-Parameter bereitgestellt wird. Der Inhalt der Datei „input.json“ ähnelt dem folgenden Codeausschnitt:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Löschen eines Data Lake Storage Gen1-Kontos
Dieser Vorgang basiert auf dem [hier](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete)definierten REST-API-Aufruf.

Verwenden Sie den folgenden cURL-Befehl zum Löschen eines Data Lake Storage Gen1-Kontos. Ersetzen Sie **\<yourstoragegen1name>** durch Ihren Data Lake Storage Gen1-Kontonamen.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Die Ausgabe sollte in etwa wie im folgenden Codeausschnitt aussehen:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Nächste Schritte
* [Dateisystemvorgänge in Data Lake Storage Gen1 mit der REST-API](data-lake-store-data-operations-rest-api.md)

## <a name="see-also"></a>Weitere Informationen
* [Azure Data Lake Storage Gen1 – REST-API-Referenz](https://docs.microsoft.com/rest/api/datalakestore/)
* [Mit Azure Data Lake Storage Gen1 kompatible Open-Source-Big Data-Anwendungen](data-lake-store-compatible-oss-other-applications.md)

