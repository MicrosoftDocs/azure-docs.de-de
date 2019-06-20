---
title: Erstellen eines Azure AD-Mandanten für Azure Red Hat OpenShift | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie einen Azure AD-Mandaten erstellen, in dem Sie Ihren Microsoft Azure Red Hat OpenShift-Cluster hosten.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 560cdcf8a99a486c7f5177b675cff327c6fb6a41
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306458"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Erstellen eines Azure AD-Mandanten für Azure Red Hat OpenShift

Für Microsoft Azure Red Hat OpenShift ist ein [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)-Mandant (Azure AD-Mandant) erforderlich, in dem Sie Ihren Cluster erstellen. Ein *Mandant* ist eine dedizierte Instanz von Azure AD, die Unternehmen oder App-Entwicklern bereitgestellt wird, wenn diese sich für Azure, Microsoft Intune oder Microsoft 365 registrieren und damit eine Geschäftsbeziehung mit Microsoft eingehen. Jeder Azure AD-Mandant unterscheidet sich von anderen Azure AD-Mandanten und verfügt über eigene Identitäten für Geschäfts-, Schul- oder Unikonten sowie über eigene App-Registrierungen.

Wenn Sie noch keinen Azure AD-Mandanten besitzen, können Sie mit den folgenden Schritten einen erstellen.

## <a name="create-a-new-azure-ad-tenant"></a>Erstellen eines neuen Azure AD-Mandanten

So erstellen Sie einen Mandanten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit dem Konto an, das Sie Ihrem Azure Red Hat OpenShift-Cluster zuordnen möchten.
2. Öffnen Sie das Blatt [Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory), um einen neuen Mandanten zu erstellen. Dieser wird auch als neue *Azure Active Directory*-Instanz bezeichnet.
3. Geben Sie einen **Organisationsnamen** an.
4. Geben Sie den **Namen der Anfangsdomäne** an. Diesem wird *onmicrosoft.com* angefügt. Sie können den Wert für *Organisationsname* hier wiederverwenden.
5. Wählen Sie ein Land oder eine Region aus, in der der Mandant erstellt wird.
6. Klicken Sie auf **Create**.
7. Klicken Sie nach der Erstellung des Azure AD-Mandanten auf den Link **Click here to manage your new directory** (Hier klicken, um neues Verzeichnis zu verwalten). Der Name Ihres neuen Mandanten sollte oben rechts im Azure-Portal angezeigt werden:  

    ![Screenshot des Portals mit Mandantennamen oben rechts][tenantcallout]  

8. Notieren Sie sich die *Mandanten-ID*, damit Sie später angeben können, wo Ihr Azure Red Hat OpenShift-Cluster erstellt werden soll. Im Portal sollte jetzt das Azure Active Directory-Übersichtsblatt für Ihren neuen Mandanten angezeigt werden. Klicken Sie auf **Eigenschaften**, und kopieren Sie den Wert der **Verzeichnis-ID**. Im Tutorial [Erstellen eines Azure Red Hat OpenShift-Clusters](tutorial-create-cluster.md) bezeichnen wir diesen Wert als `TENANT`.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Ressourcen

Weitere Informationen zu [Azure AD-Mandanten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) finden Sie in der [Azure Active Directory-Dokumentation](https://docs.microsoft.com/azure/active-directory/).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie einen Dienstprinzipal erstellen, ein Clientgeheimnis und eine Rückruf-URL für die Authentifizierung generieren sowie einen neuen Active Directory-Benutzer zum Testen von Apps in Ihrem Azure Red Hat OpenShift-Cluster erstellen.

[Erstellen eines App-Objekts und eines Benutzers für Azure AD](howto-aad-app-configuration.md)