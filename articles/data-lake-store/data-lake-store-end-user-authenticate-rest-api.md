---
title: 'Authentifizierung von Endbenutzern: REST-API mit Azure Data Lake Storage Gen1 mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie die Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory und der REST-API umsetzen.
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
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60877821"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe der REST-API
> [!div class="op_single_selector"]
> * [Verwenden von Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Verwenden des .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Verwenden von Python](data-lake-store-end-user-authenticate-python.md)
> * [Verwenden der REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

In diesem Artikel erfahren Sie, wie Sie mithilfe der REST-API die Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 durchführen. Informationen zur Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe der REST-API finden Sie unter [Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe der REST-API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Erstellen einer nativen Azure Active Directory-Anwendung.** Sie müssen die Schritte unter [Authentifizierung von Endbenutzern bei Azure Data Lake Storage Gen1 mithilfe von Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) ausgeführt haben.

* **[cURL](https://curl.haxx.se/)** . Dieser Artikel zeigt anhand von cURL, wie Sie REST-API-Aufrufe eines Data Lake Storage Gen1-Kontos ausführen.

## <a name="end-user-authentication"></a>Authentifizierung von Endbenutzern
Die Authentifizierung von Endbenutzern wird empfohlen, wenn sich ein Benutzer mithilfe von Azure AD bei Ihrer Anwendung anmelden soll. Die Anwendung kann mit der gleichen Zugriffsstufe wie der angemeldete Benutzer auf Azure-Ressourcen zugreifen. Der Benutzer muss seine Anmeldeinformationen in regelmäßigen Abständen eingeben, um weiter Zugriff zu haben.

Das Ergebnis der Endbenutzeranmeldung ist, dass Ihre Anwendung über ein Zugriffs- und ein Aktualisierungstoken verfügt. Das Zugriffstoken wird an jede an Data Lake Storage Gen1 oder Data Lake Analytics gestellte Anforderung angefügt und ist standardmäßig eine Stunde gültig. Mithilfe des Aktualisierungstokens kann ein neues Zugriffstoken abgerufen werden, das bei regelmäßiger Nutzung standardmäßig bis zu zwei Wochen gültig ist. Es gibt zwei Ansätze für die Anmeldung von Endbenutzern.

In diesem Szenario wird der Benutzer in der Anwendung zum Anmelden aufgefordert. Alle Vorgänge werden im Zusammenhang mit dem Benutzer durchgeführt. Führen Sie die folgenden Schritte aus:

1. Leiten Sie den Benutzer über die Anwendung an die folgende URL um:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<REDIRECT-URI&gt; muss für die Verwendung in einer URL codiert werden. Für https://localhost verwenden Sie also `https%3A%2F%2Flocalhost` ).

    In diesem Tutorial können Sie die Platzhalterwerte in der URL oben ersetzen und in der Adressleiste des Webbrowsers einfügen. Sie werden umgeleitet und authentifizieren sich mit Ihrer Azure-Anmeldung. Nachdem Sie sich erfolgreich angemeldet haben, wird die Antwort in der Adressleiste des Browsers angezeigt. Die Antwort wird im folgenden Format angezeigt:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Erfassen Sie den in der Antwort angegebenen Autorisierungscode. In diesem Tutorial können Sie den Autorisierungscode aus der Adressleiste des Webbrowsers kopieren und wie im folgenden Codeausschnitt gezeigt in der POST-Anforderung an den Tokenendpunkt übergeben:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>

   > [!NOTE]
   > In diesem Fall muss \<REDIRECT-URI> nicht codiert werden.
   > 
   > 

3. Die Antwort ist ein JSON-Objekt, das ein Zugriffstoken (z.B. `"access_token": "<ACCESS_TOKEN>"`) und ein Aktualisierungstoken (z.B. `"refresh_token": "<REFRESH_TOKEN>"`) enthält. Die Anwendung verwendet das Zugriffstoken beim Zugriff auf Azure Data Lake Storage Gen1 und das Aktualisierungstoken zum Abrufen eines anderen Zugriffstokens, wenn ein Zugriffstoken abläuft.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Wenn das Zugriffstoken abläuft, können Sie wie im folgenden Codeausschnitt gezeigt mithilfe des Aktualisierungstokens ein neues Zugriffstoken anfordern:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Weitere Informationen zur interaktiven Benutzerauthentifizierung finden Sie unter [Autorisieren des Zugriffs auf Webanwendungen mit OAuth 2.0 und Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645542.aspx).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierung zwischen Diensten verwenden, um sich mit der REST-API bei Azure Data Lake Storage Gen1 zu authentifizieren. In den folgenden Artikeln wird erörtert, wie Sie die REST-API mit Azure Data Lake Storage Gen1 verwenden.

* [Kontoverwaltungsvorgänge für Azure Data Lake Storage Gen1 mit der REST-API](data-lake-store-get-started-rest-api.md)
* [Dateisystemvorgänge in Data Lake Storage Gen1 mit der REST-API](data-lake-store-data-operations-rest-api.md)

