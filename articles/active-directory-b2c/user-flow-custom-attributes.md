---
title: Definieren benutzerdefinierter Attribute in Azure Active Directory B2C | Microsoft-Dokumentation
description: Definieren benutzerdefinierter Attribute für Ihre Anwendung in Azure Active Directory B2C zum Erfassen von Informationen zu Ihren Kunden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2233dbd7b0e669c23397b4bc6a84f2bfdc208391
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952810"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definieren benutzerdefinierter Attribute in Azure Active Directory B2C

 Für jede kundenorientierte Anwendung gelten spezifische Anforderungen im Hinblick auf die Informationen, die erfasst werden sollen. Ihr Azure Active Directory B2C-Mandant (Azure AD B2C-Mandant) umfasst einen integrierten Satz von in Attributen gespeicherten Informationen, z. B. Vorname, Nachname, Ort und Postleitzahl. Mit Azure AD B2C haben Sie die Möglichkeit, den für die einzelnen Kundenkonten gespeicherten Satz von Attributen zu erweitern.

 Im [Azure-Portal](https://portal.azure.com/) können Sie benutzerdefinierte Attribute erstellen und sie in den Benutzerflows für die Registrierung, den Benutzerflows für Registrierung und Anmeldung und den Benutzerflows für die Profilbearbeitung verwenden. Außerdem können Sie diese Attribute mit der [Microsoft Graph-API](manage-user-accounts-graph-api.md) lesen und schreiben.

## <a name="create-a-custom-attribute"></a>Erstellen eines benutzerdefinierten Attributs

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
2. Vergewissern Sie sich, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie oben rechts im Azure-Portal zu diesem Verzeichnis wechseln. Wählen Sie die Abonnementinformationen aus, und klicken Sie dann auf **Verzeichnis wechseln**.

    ![Wechseln zu Ihrem Azure AD B2C-Mandanten](./media/user-flow-custom-attributes/switch-directories.png)

    Wählen Sie das Verzeichnis aus, das den Mandanten enthält.

    ![Filter „Verzeichnis und Abonnement“ mit hervorgehobenem B2C-Mandanten](./media/user-flow-custom-attributes/select-directory.PNG)

3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
4. Wählen Sie **Benutzerattribute** und dann **Hinzufügen** aus.
5. Geben Sie einen **Namen** für das benutzerdefinierte Attribut an (z.B. „ShoeSize“).
6. Wählen Sie einen **Datentyp** aus. Es stehen nur **Zeichenfolge**, **Boolescher Wert** und **Int** zur Verfügung.
7. Geben Sie optional eine **Beschreibung** zu Informationszwecken ein.
8. Klicken Sie auf **Erstellen**.

Das benutzerdefinierte Attribut steht jetzt in der Liste der **Benutzerattribute** zur Verfügung und kann in den Benutzerflows verwendet werden. Ein benutzerdefiniertes Attribut wird nur erstellt, wenn es zum ersten Mal in einem Benutzerflow verwendet wird, und nicht, wenn Sie es der Liste der **Benutzerattribute** hinzufügen.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Verwenden eines benutzerdefinierten Attributs in einem Benutzerflow

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Wählen Sie eine Richtlinie aus (z.B. „B2C_1_SignupSignin“), um sie zu öffnen.
1. Wählen Sie **Benutzerattribute** und dann das benutzerdefinierte Attribut aus (z.B. „ShoeSize“). Klicken Sie auf **Speichern**.
1. Wählen Sie **Anwendungsansprüche** und dann das benutzerdefinierte Attribut aus.
1. Klicken Sie auf **Speichern**.

Nach dem Erstellen eines neuen Benutzers über einen Benutzerflow, der das neu erstellte benutzerdefinierte Attribut verwendet, kann das Objekt im [Microsoft Graph-Explorer](https://developer.microsoft.com/graph/graph-explorer) abgefragt werden. Alternativ können Sie mit der Funktion [Benutzerflow ausführen](./tutorial-create-user-flows.md) für den Benutzerflow die Benutzerfreundlichkeit überprüfen. **ShoeSize** sollte jetzt in der Liste der Attribute, die während der Registrierung erfasst werden, sowie in dem Token angezeigt werden, das zurück an die Anwendung gesendet wird.