---
title: Partner Center-Übermittlungs-API zum Integrieren von Azure-Apps im kommerziellen Microsoft-Marketplace
description: Informieren Sie sich über die Voraussetzungen für die Verwendung der Partner Center-Übermittlungs-API für Azure-Apps im kommerziellen Marketplace im Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9863ed24da9e427f885a4794bda7e103b0c1cc8e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455444"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>Partner Center-Übermittlungs-API zum Integrieren von Azure-Apps im Partner Center

Verwenden Sie die *Partner Center-Übermittlungs-API*, um Azure-Angebote programmgesteuert abzufragen, zu erstellen und zu veröffentlichen.  Diese API ist nützlich, wenn Ihr Konto viele Angebote verwaltet und Sie den Übermittlungsvorgang für diese Angebote automatisieren und optimieren möchten.

## <a name="api-prerequisites"></a>API-Voraussetzungen

Es gibt einige programmgesteuerte Ressourcen, die Sie benötigen, um die Partner Center-API für Azure-Produkte zu verwenden: 

- Eine Azure Active Directory-Anwendung
- Ein Azure AD-Zugriffstoken (Azure Active Directory)

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Schritt 1: Vollständige Voraussetzungen für die Verwendung der Partner Center-Übermittlungs-API

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie mit dem Schreiben von Code zum Aufrufen der Partner Center-Übermittlungs-API beginnen.

- Sie (oder Ihre Organisation) müssen über ein Azure AD-Verzeichnis verfügen, und Ihnen müssen die Berechtigungen [globaler Administrator](../../active-directory/roles/permissions-reference.md) für das Verzeichnis gewährt worden sein. Wenn Sie Microsoft 365 oder andere Unternehmensdienste von Microsoft verwenden, verfügen Sie bereits über ein Azure AD-Verzeichnis. Andernfalls können Sie ohne zusätzliche Kosten [eine neue Azure AD-Instanz in Partner Center erstellen](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account).

- Sie müssen [Ihrem Partner Center-Konto eine Azure AD-Anwendung zuordnen](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) und Ihre Mandanten-ID, die Client-ID und den Schlüssel abrufen. Sie benötigen diese Werte, um ein Azure AD-Zugriffstoken zu erhalten, das Sie in Aufrufen der Microsoft Store-Übermittlungs-API verwenden.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Zuordnen einer anderen Azure AD-Anwendung zu Ihrem Partner Center-Konto

Um die Microsoft Store-Übermittlungs-API zu verwenden, müssen Sie Ihrem Partner Center-Konto eine Azure AD-Anwendung zuordnen, die Mandanten-ID und die Client-ID für die Anwendung abrufen und einen Schlüssel generieren. Die Azure AD-Anwendung stellt die App oder den Dienst dar, von der bzw. dem aus Sie die Partner Center-Übermittlungs-API aufrufen möchten. Sie benötigen die Mandanten-ID, die Client-ID und den Schlüssel, um ein Azure AD-Zugriffstoken zu erhalten, das Sie an die API übergeben.

>[!Note]
>Sie müssen diese Aufgabe nur einmal ausführen. Nachdem Sie über die Mandanten-ID, die Client-ID und den Schlüssel verfügen, können Sie diese jederzeit wiederverwenden, wenn Sie ein neues Azure AD-Zugriffstoken erstellen müssen.

1. Verknüpfen Sie in Partner Center [das Partner Center-Konto Ihres Unternehmens mit dem Azure AD-Verzeichnis](/windows/uwp/publish/associate-azure-ad-with-partner-center) Ihrer Organisation.
1. Fügen Sie als Nächstes auf der Seite **Benutzer** im Abschnitt **Kontoeinstellungen** von Partner Center die [Azure AD-Anwendung](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) hinzu, die die App oder den Dienst darstellt, mit der bzw. dem Sie auf die Übermittlungen Ihres Partner Center-Kontos zugreifen. Stellen Sie sicher, dass Sie dieser Anwendung die Rolle **Manager** zuweisen. Wenn die Anwendung noch nicht in Ihrem Azure AD-Verzeichnis vorhanden ist, können Sie [in Partner Center eine neue Azure AD-Anwendung erstellen](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Kehren Sie zur Seite **Benutzer** zurück, klicken Sie auf den Namen Ihrer Azure AD-Anwendung, um die Anwendungseinstellungen zu öffnen, und schreiben Sie die Werte **Mandanten-ID** und **Client-ID** auf.
1. Klicken Sie auf **Neuen Schlüssel hinzufügen**. Notieren Sie auf dem folgenden Bildschirm den Wert von **Schlüssel**. Nachdem Sie diese Seite verlassen haben, können Sie nicht mehr auf diese Informationen zugreifen. Weitere Informationen finden Sie unter [Verwalten von Schlüsseln für eine Azure AD-Anwendung](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Schritt 2: Abrufen eines Azure AD-Zugriffstokens

Bevor Sie eine der Methoden in der Partner Center-Übermittlungs-API aufrufen, müssen Sie zunächst ein Azure AD-Zugriffstoken abrufen, das Sie an den **Authorization**-Header jeder Methode in der API übergeben. Nachdem Sie ein Zugriffstoken erhalten haben, haben Sie 60 Minuten Zeit, es zu verwenden, bevor es abläuft. Nach dem Ablauf können Sie das Token aktualisieren, damit Sie es in zukünftigen Aufrufen der API weiterhin verwenden können.

Um das Zugriffstoken zu erhalten, befolgen Sie die Anweisungen in [Verwenden von Clientanmeldeinformationen für Dienst-zu-Dienst-Aufrufe](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md), um eine `HTTP POST`-Anweisung an den Endpunkt `https://login.microsoftonline.com/<tenant_id>/oauth2/token` zu senden. Hier ist eine Beispielanforderung:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Geben Sie die Mandanten-ID, die Client-ID und den Schlüssel für die Anwendung, die Sie im vorherigen Abschnitt aus Partner Center abgerufen haben, für den Wert *tenant_id* in `POST URI` und die Parameter *client_id* und *client_secret* an. Für den Parameter *resource* müssen Sie `https://api.partner.microsoft.com` angeben.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Schritt 3: Verwenden der Microsoft Store-Übermittlungs-API

Nachdem Sie über ein Azure AD-Zugriffstoken verfügen, können Sie Methoden in der Partner Center-Übermittlungs-API aufrufen. Zum Erstellen oder Aktualisieren von Übermittlungen werden in der Partner Center-Übermittlungs-API in der Regel mehrere Methoden in einer bestimmten Reihenfolge aufgerufen. Informationen zu den einzelnen Szenarien und der Syntax der einzelnen Methoden finden Sie bei der Erfassungs-API von Swagger.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Erstellen einer technischen Azure-VM-Ressource](../create-azure-container-technical-assets.md).
* Lesen Sie mehr über das [Erstellen eines Azure-Containerangebots](../create-azure-container-offer.md).