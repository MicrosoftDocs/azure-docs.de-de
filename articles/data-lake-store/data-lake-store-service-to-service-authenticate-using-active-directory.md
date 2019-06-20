---
title: 'Dienst-zu-Dienst-Authentifizierung: Azure Data Lake Storage Gen1 mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie mithilfe von Azure Active Directory die Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen1 implementieren.
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
ms.openlocfilehash: 3fbf2f2540e8f1ca84aad2759b9a1fc790e4065d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241373"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Dienst-zu-Dienst-Authentifizierung mit Azure Data Lake Storage Gen1 unter Verwendung von Azure Active Directory
> [!div class="op_single_selector"]
> * [Authentifizierung von Endbenutzern](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Dienst-zu-Dienst-Authentifizierung](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 verwendet Azure Active Directory für die Authentifizierung. Vor dem Erstellen einer Anwendung, die mit Data Lake Storage Gen1 arbeitet, müssen Sie entscheiden, wie Sie Ihre Anwendung bei Azure Active Directory (Azure AD) authentifizieren möchten. Sie haben zwei Möglichkeiten:

* Authentifizierung von Endbenutzern 
* Dienst-zu-Dienst-Authentifizierung (dieser Artikel) 

Bei beiden Optionen erhält Ihre Anwendung ein OAuth 2.0-Token, das an jede an Data Lake Storage Gen1 gestellte Anforderung angefügt wird.

In diesem Artikel wird erläutert, wie Sie eine **Azure AD-Webanwendung für die Dienst-zu-Dienst-Authentifizierung** erstellen. Anweisungen zum Konfigurieren von Azure AD-Anwendungen für die Authentifizierung von Endbenutzern finden Sie unter [Authentifizierung von Endbenutzern bei Data Lake Storage Gen1 mithilfe von Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Voraussetzungen
* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Schritt 1: Erstellen einer Active Directory-Webanwendung

Erstellen und Konfigurieren Sie eine Azure AD-Webanwendung für die Dienst-zu-Dienst-Authentifizierung mit Azure Data Lake Storage Gen1 unter Verwendung von Azure Active Directory. Anweisungen finden Sie unter [Erstellen einer Azure AD-Anwendung](../active-directory/develop/howto-create-service-principal-portal.md).

Wenn Sie die Anweisungen unter dem obigen Link befolgen, stellen Sie sicher, dass Sie beim Typ der Anwendung **Web-App/API** auswählen, wie im folgenden Screenshot gezeigt:

![Erstellen einer Web-App](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Erstellen einer Web-App")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Schritt 2: Abrufen von Anwendungs-ID, Authentifizierungsschlüssel und Mandanten-ID
Beim programmgesteuerten Anmelden benötigen Sie die ID für Ihre Anwendung. Wenn die Anwendung mit ihren eigenen Anmeldeinformationen ausgeführt wird, benötigen Sie außerdem einen Authentifizierungsschlüssel.

* Eine Anleitung zum Abrufen der Anwendungs-ID und des Authentifizierungsschlüssels (auch „Clientgeheimnis“ genannt) finden Sie unter [Abrufen der Anwendungs-ID und des Authentifizierungsschlüssels](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

* Informationen zum Abrufen der Mandanten-ID finden Sie unter [Abrufen der Mandanten-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Schritt 3: Zuweisen der Azure AD-Anwendung zur Datei oder zum Ordner des Azure Data Lake Storage Gen1-Kontos


1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an. Öffnen Sie das Data Lake Storage Gen1-Konto, das Sie der zuvor erstellten Azure Active Directory-Anwendung zuordnen möchten.
2. Klicken Sie auf dem Blatt für Ihr Data Lake Storage Gen1-Konto auf **Daten-Explorer**.
   
    ![Erstellen von Verzeichnissen im Data Lake Storage Gen1-Konto](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Erstellen von Verzeichnissen im Data Lake-Konto")
3. Klicken Sie auf dem Blatt **Daten-Explorer** auf die Datei oder den Ordner, für die bzw. den Sie den Zugriff auf die Azure AD-Anwendung festlegen möchten, und klicken Sie dann auf **Zugriff**. Um den Zugriff auf eine Datei zu konfigurieren, müssen Sie auf dem Blatt **Dateivorschau** auf **Zugriff** klicken.
   
    ![Festlegen von Zugriffssteuerungslisten für das Data Lake-Dateisystem](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Festlegen von Zugriffssteuerungslisten für das Data Lake-Dateisystem")
4. Auf dem Blatt **Zugriff** sind der Standardzugriff und der benutzerdefinierte Zugriff aufgeführt, die dem Stamm bereits zugewiesen wurden. Klicken Sie auf das Symbol **Hinzufügen** , um Zugriffssteuerungslisten auf benutzerdefinierter Ebene hinzuzufügen.
   
    ![Auflisten von Standardzugriff und benutzerdefiniertem Zugriff](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Auflisten von Standardzugriff und benutzerdefiniertem Zugriff")
5. Klicken Sie auf das Symbol **Hinzufügen**, um das Blatt **Benutzerdefinierten Zugriff hinzufügen** zu öffnen. Klicken Sie auf diesem Blatt auf **Benutzer oder Gruppe auswählen**, und suchen Sie dann auf dem Blatt **Benutzer oder Gruppe auswählen** nach der Azure Active Directory-Anwendung, die Sie zuvor erstellt haben. Wenn Sie über viele Gruppen verfügen, in denen Sie suchen können, können Sie das Textfeld oben zum Filtern nach dem Gruppennamen verwenden. Klicken Sie auf die Gruppe, die Sie hinzufügen möchten, und klicken Sie dann auf **Auswählen**.
   
    ![Hinzufügen einer Gruppe](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Hinzufügen einer Gruppe")
6. Klicken Sie auf **Berechtigungen auswählen**, wählen Sie die Berechtigungen aus, und legen Sie fest, ob die Berechtigungen als Standard-ACL und/oder Zugriffs-ACL zugewiesen werden sollen. Klicken Sie auf **OK**.
   
    ![Zuweisen von Berechtigungen zu einer Gruppe](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Zuweisen von Berechtigungen zu einer Gruppe")
   
    Weitere Informationen zu Berechtigungen in Data Lake Storage Gen1 und zu Standard- und Zugriffs-ACLs finden Sie unter [Zugriffssteuerung in Data Lake Storage Gen1](data-lake-store-access-control.md).
7. Klicken Sie auf dem Blatt **Benutzerdefinierten Zugriff hinzufügen** auf **OK**. Die neu hinzugefügte Gruppe mit den zugeordneten Berechtigungen wird auf dem Blatt **Zugriff** aufgelistet.
   
    ![Zuweisen von Berechtigungen zu einer Gruppe](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Zuweisen von Berechtigungen zu einer Gruppe")

> [!NOTE]
> Wenn Sie Ihre Azure Active Directory-Anwendung auf einen bestimmten Ordner beschränken möchten, müssen Sie außerdem derselben Azure Active Directory-Anwendung die **Execute**-Berechtigung für das Stammverzeichnis gewähren, um den Dateierstellungszugriff über das .NET SDK zu aktivieren.

> [!NOTE]
> Wenn Sie die SDKs zum Erstellen eines Data Lake Storage Gen1-Kontos verwenden möchten, müssen Sie die Azure AD-Webanwendung als Rolle der Ressourcengruppe zuweisen, in der Sie das Data Lake Storage Gen1-Konto erstellen.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Schritt 4: Abrufen des OAuth 2.0-Token-Endpunkts (nur für Java-basierte Anwendungen)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie im linken Bereich auf „Active Directory“.

2. Klicken Sie im linken Bereich auf **App-Registrierungen**.

3. Klicken Sie oben im Blatt „App-Registrierungen“ auf **Endpunkte**.

    ![OAuth-Token-Endpunkt](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth-Token-Endpunkt")

4. Kopieren Sie aus der Liste der Endpunkte den OAuth 2.0-Token-Endpunkt.

    ![OAuth-Token-Endpunkt](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth-Token-Endpunkt")   

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie eine Azure AD-Webanwendung erstellt und die Informationen gesammelt, die für Ihre Clientanwendungen nötig sind, die Sie mit dem .NET SDK, mit Java, Python, der REST-API usw. erstellen. Sie können nun mit den nachfolgend aufgeführten Artikeln fortfahren, in denen erläutert wird, wie Sie die native Azure AD-Anwendung verwenden, um sich zum ersten Mal mit Data Lake Storage Gen1 zu authentifizieren und anschließend andere Vorgänge für den Speicher durchzuführen.

* [Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe von Java](data-lake-store-service-to-service-authenticate-java.md)
* [Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe des .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe von Python](data-lake-store-service-to-service-authenticate-python.md)
* [Dienst-zu-Dienst-Authentifizierung bei Data Lake Storage Gen1 mithilfe der REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)


