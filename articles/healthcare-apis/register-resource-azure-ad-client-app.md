---
title: Registrieren einer Ressourcenanwendung in Azure Active Directory – Azure API for FHIR
description: In diesem Artikel wird erläutert, wie Sie eine Ressourcenanwendung in Azure Active Directory registrieren.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e8305c5a69fa3fda29f4f1292b7faa59f8ec3608
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60709607"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrieren einer Ressourcenanwendung in Azure Active Directory

In diesem Artikel erfahren Sie, wie Sie eine Ressourcenanwendung (oder API-Anwendung) in Azure Active Directory registrieren. Eine Ressourcenanwendung ist eine Azure Active Directory-Darstellung der FHIR-Server-API selbst, und Clientanwendungen können bei der Authentifizierung den Zugriff auf die Ressource anfordern. Die Ressourcenanwendung wird im OAuth-Kontext auch als *Zielgruppe* bezeichnet.

## <a name="app-registrations-in-azure-portal"></a>App-Registrierungen im Azure-Portal

1. Klicken Sie im linken Navigationsbereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory**.

2. Klicken Sie auf dem Blatt **Azure Active Directory** auf **App-Registrierungen (Vorschauversion)** :

    ![Azure-Portal. Neue App-Registrierung.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicken Sie auf **Neue Registrierung**.

## <a name="add-a-new-application-registration"></a>Hinzufügen einer neuen Anwendungsregistrierung

Füllen Sie die Details für die neue Anwendung aus. Es gibt keine bestimmten Anforderungen an den Anzeigenamen, aber wenn er auf den URI des FHIR-Servers festgelegt wird, ist er leicht zu finden:

![Registrierung einer neuen Anwendung](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

## <a name="set-identifier-uri-and-define-scopes"></a>Festlegen des Bezeichner-URIs und Definieren von Bereichen

Eine Ressourcenanwendung verfügt über einen Bezeichner-URI (Anwendungs-ID-URI), mit dem Clients den Zugriff auf die Ressource anfordern können. Dieser Wert füllt den `aud`-Anspruch des Zugriffstokens. Es wird empfohlen, dass Sie diesen URI so festlegen, dass er dem URI Ihres FHIR-Servers entspricht. Bei SMART für FHIR-Apps wird davon ausgegangen, dass die *Zielgruppe* der URI des FHIR-Servers ist.

1. Klicken Sie auf **Eine API verfügbar machen**.

2. Klicken Sie neben *Anwendungs-ID-URI* auf **Festlegen**.

3. Geben Sie den Bezeichner-URI ein, und klicken Sie auf **Speichern**. Ein geeigneter Bezeichner-URI wäre der URI Ihres FHIR-Servers.

4. Klicken Sie auf **Bereich hinzufügen**, und fügen Sie alle Bereiche hinzu, die Sie für Ihre API definieren möchten. Azure AD gestattet derzeit keine Schrägstriche (`/`) in Bereichsnamen. Stattdessen wird die Verwendung von `$` empfohlen. Ein Bereich wie `patient/*.read` wäre `patient$*.read`.

    ![Zielgruppe und Bereich](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

## <a name="define-application-roles"></a>Definieren der Anwendungsrollen

Azure API for FHIR und der OSS FHIR-Server für Azure verwenden [Azure Active Directory-Anwendungsrollen](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) für die rollenbasierte Zugriffssteuerung. Um festzulegen, welche Rollen für Ihre FHIR-Server-API verfügbar sein sollen, öffnen Sie das [Manifest](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/) der Ressourcenanwendung:

1. Klicken Sie auf **Manifest**:

    ![Anwendungsrollen](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. Fügen Sie in der `appRoles`-Eigenschaft die Rollen hinzu, die Benutzer oder Anwendungen erhalten sollen:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Ressourcenanwendung in Azure Active Directory registrieren. Als Nächstes stellen Sie eine FHIR-API in Azure bereit.
 
>[!div class="nextstepaction"]
>[Bereitstellen von Open Source-FHIR-Servern](fhir-oss-powershell-quickstart.md)