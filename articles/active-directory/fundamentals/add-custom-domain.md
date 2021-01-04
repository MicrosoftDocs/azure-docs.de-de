---
title: Hinzufügen Ihrer benutzerdefinierten Domäne – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure Active Directory eine benutzerdefinierte Domäne hinzufügen.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28847ece3c08e93d14d381b35fc93fb16dad95a1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973897"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Hinzufügen Ihres benutzerdefinierten Domänennamens über das Azure Active Directory-Portal

Jeder neue Azure AD-Mandant verfügt über einen anfänglichen Domänennamen *\<domainname>.onmicrosoft.com*. Der anfängliche Domänenname kann nicht geändert oder gelöscht werden, Sie können jedoch die Namen Ihrer Organisation hinzufügen. Durch Hinzufügen benutzerdefinierter Domänennamen können Sie Benutzernamen erstellen, mit denen Ihre Benutzer vertraut sind (beispielsweise *alain\@contoso.com*).

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie einen benutzerdefinierten Domänennamen hinzufügen können, müssen Sie Ihren Domänennamen bei einer Domänenregistrierungsstelle erstellen. Eine Liste von anerkannten Domänenregistrierungsstellen finden Sie unter [ICANN-Accredited Registrars](https://www.icann.org/registrar-reports/accredited-list.html) (Von der ICANN anerkannte Registrierungsstellen).

## <a name="create-your-directory-in-azure-ad"></a>Erstellen Ihres Verzeichnisses in Azure AD

Nachdem Sie Ihren Domänennamen erhalten haben, können Sie Ihr erstes Azure AD-Verzeichnis erstellen. Melden Sie sich mit einem Konto mit der Rolle **Besitzer** für das Abonnement beim Azure-Portal für Ihr Verzeichnis an.

Erstellen Sie Ihr neues Verzeichnis, indem Sie den Schritten unter [Create a new tenant for your organization](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization) (Erstellen eines neuen Mandanten für Ihre Organisation) folgen.

>[!IMPORTANT]
>Die Person, die den Mandanten erstellt, wird automatisch zum globalen Administrator für diesen Mandanten. Der globale Administrator kann dem Mandanten weitere Administratoren hinzufügen.

Weitere Informationen zu Abonnementrollen finden Sie unter [Azure-Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

>[!TIP]
> Wenn Sie planen, Ihre lokale Windows Server AD mit Azure AD in einen Verbund aufzunehmen, müssen Sie **Ich möchte diese Domäne für einmaliges Anmelden bei meinem lokalen Active Directory konfigurieren** aktivieren, wenn Sie zum Synchronisieren Ihrer Verzeichnisse das Tool Azure AD Connect ausführen.
>
> Sie müssen den Domänennamen, den Sie für den Verbund mit Ihrem lokalen Verzeichnis im Schritt **Azure AD-Domäne** des Assistenten auswählen, außerdem registrieren. Um zu sehen, wie das Setup aussieht, lesen Sie [Überprüfen der für den Verbund ausgewählten Azure AD-Domäne](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Falls Sie nicht über das Azure AD Connect-Tool verfügen, können Sie es [hier herunterladen](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Hinzufügen des benutzerdefinierten Domänennamens zu Azure AD

Nachdem Sie Ihr Verzeichnis erstellt haben, können Sie Ihren benutzerdefinierten Domänennamen hinzufügen.

1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com/) an.

1. Suchen Sie auf einer beliebigen Seite nach *Azure Active Directory*, und wählen Sie es aus. Wählen Sie dann **Benutzerdefinierte Domänennamen** > **Benutzerdefinierte Domäne hinzufügen** aus.

    ![Seite „Benutzerdefinierte Domänennamen“ mit Option „Benutzerdefinierte Domäne hinzufügen“](media/add-custom-domain/add-custom-domain.png)

1. Geben Sie unter **Benutzerdefinierter Domänenname** den neuen Namen Ihrer Organisation ein, in diesem Beispiel *contoso.com*. Wählen Sie **Add domain** (Domäne hinzufügen) aus.

    ![Seite „Benutzerdefinierte Domänennamen“ mit Seite „Benutzerdefinierte Domäne hinzufügen“](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Der Domänenname muss *.com*, *.net* oder eine andere Erweiterung der obersten Ebene enthalten, damit dies korrekt funktioniert.

    Die überprüfte Domäne wird hinzugefügt. Es wird die Seite **contoso.com** mit Ihren DNS-Informationen angezeigt. Speichern Sie diese Informationen. Sie benötigen sie später zum Erstellen eines TXT-Eintrag zum Konfigurieren von DNS.

    ![Seite für Contoso mit DNS-Eintragsinformationen](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Hinzufügen Ihrer DNS-Informationen in der Domänenregistrierungsstelle

Nachdem Sie Azure AD Ihren benutzerdefinierten Domänennamen hinzugefügt haben, müssen Sie zur Domänenregistrierungsstelle zurückkehren und die DNS-Informationen von Azure AD aus der kopierten TXT-Datei hinzufügen. Durch die Erstellung dieses TXT-Eintrags wird der Besitz des Domänennamens überprüft.

Navigieren Sie zurück zu Ihrer Domänenregistrierungsstelle, und erstellen Sie basierend auf den kopierten DNS-Informationen einen neuen TXT-Eintrag für Ihre Domäne. Legen Sie die Gültigkeitsdauer (Time to Live, TTL) auf 3.600 Sekunden (60 Minuten) fest, und speichern Sie den Datensatz.

>[!IMPORTANT]
>Sie können beliebig viele Domänennamen registrieren. Jeder Domäne wird jedoch ein eigener TXT-Eintrag von Azure AD zugewiesen. Achten Sie darauf, dass Sie die Informationen aus der TXT-Datei in der Domänenregistrierungsstelle korrekt eingeben. Wenn Sie versehentlich falsche oder doppelte Informationen eingeben, müssen Sie warten, bis die Gültigkeitsdauer (60 Minuten) abgelaufen ist, bevor Sie es erneut versuchen können.

## <a name="verify-your-custom-domain-name"></a>Überprüfen des benutzerdefinierten Domänennamens

Nachdem Sie Ihren benutzerdefinierten Domänennamen registriert haben, müssen Sie sicherstellen, dass er in Azure AD gültig ist. Die Weitergabe der Informationen von der Domänenregistrierungsstelle an Azure AD kann je nach Registrierungsstelle sofort erfolgen oder einige Tage in Anspruch nehmen.

So überprüfen Sie Ihren benutzerdefinierten Domänennamen:

1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com/) an.

1. Suchen Sie auf einer beliebigen Seite nach *Azure Active Directory*, wählen Sie es aus, und klicken Sie dann auf **Benutzerdefinierte Domänennamen**.

1. Wählen Sie unter **Benutzerdefinierte Domänennamen** den benutzerdefinierten Domänennamen aus. Wählen Sie in diesem Beispiel die Option **contoso.com**.

    ![Seite „Fabrikam – Benutzerdefinierte Domänennamen“ mit hervorgehobener Domäne „contoso.com“](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Klicken Sie auf der Seite für **contoso.com** auf **Überprüfen**, um sicherzustellen, dass Ihre benutzerdefinierte Domäne korrekt registriert und für Azure AD gültig ist.

    ![Seite für Contoso mit DNS-Eintragsinformationen und der Schaltfläche „Überprüfen“](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Nachdem Sie Ihren benutzerdefinierten Domänennamen überprüft haben, können Sie Ihre für die Überprüfung verwendete TXT- oder MX-Datei löschen.

## <a name="common-verification-issues"></a>Häufige Probleme bei der Überprüfung

Versuchen Sie Folgendes, wenn Azure AD einen benutzerdefinierten Domänennamen nicht überprüfen kann:

- **Warten Sie mindestens eine Stunde, und versuchen Sie es dann noch einmal.** DNS-Einträge müssen weitergegeben (propagiert) werden, bevor Azure AD die Domäne überprüfen kann. Dies kann eine Stunde oder auch länger dauern.

- **Stellen Sie sicher, dass der DNS-Eintrag korrekt ist.** Kehren Sie zur Website der Domänenregistrierungsstelle zurück. Stellen Sie sicher, dass der Eintrag vorhanden ist und mit den von Azure AD bereitgestellten DNS-Eintragsinformationen übereinstimmt.

  Falls Sie den Eintrag auf der Website der Registrierungsstelle nicht aktualisieren können, geben Sie ihn für eine Person mit den erforderlichen Berechtigungen frei, um den Eintrag hinzufügen und seine Richtigkeit überprüfen zu lassen.

- **Stellen Sie sicher, dass der Domänenname nicht bereits in einem anderen Verzeichnis verwendet wird.** Ein Domänenname kann nur in einem Verzeichnis überprüft werden. Falls Ihr Domänenname gerade in einem anderen Verzeichnis überprüft wird, kann er daher nicht auch im neuen Verzeichnis überprüft werden. Um dieses Problem zu beheben, müssen Sie den Domänennamen aus dem alten Verzeichnis löschen. Weitere Informationen zum Löschen von Domänennamen finden Sie unter [Verwalten von benutzerdefinierten Domänennamen](../enterprise-users/domains-manage.md).

- **Stellen Sie sicher, dass keine nicht verwalteten Power BI-Mandanten vorhanden sind.** Wenn Ihre Benutzer Power BI über die Self-Service-Registrierung aktiviert und einen nicht verwalteten Mandanten für Ihre Organisation erstellt haben, müssen Sie die Verwaltung als interner oder externer Administrator mithilfe von PowerShell übernehmen. Weitere Informationen finden Sie unter [Übernehmen eines nicht verwalteten Verzeichnisses als Administrator in Azure Active Directory](../enterprise-users/domains-admin-takeover.md).

## <a name="next-steps"></a>Nächste Schritte

- Fügen Sie Ihrem Verzeichnis einen weiteren globalen Administrator hinzu. Weitere Informationen finden Sie unter [Zuweisen von Rollen und Administratoren](active-directory-users-assign-role-azure-portal.md).

- Fügen Sie Benutzer zu Ihrer Domäne hinzu. Weitere Informationen finden Sie unter [Hinzufügen oder Löschen von Benutzern](add-users-azure-active-directory.md).

- Verwalten Sie die Informationen zu Ihrem Domänennamen in Azure AD. Weitere Informationen finden Sie unter [Verwalten von benutzerdefinierten Domänennamen](../enterprise-users/domains-manage.md).

- Falls Sie lokale Windows Server-Versionen haben, die Sie zusammen mit Azure Active Directory verwenden möchten, gehen Sie wie unter [Integrate your on-premises directories with Azure Active Directory](../hybrid/whatis-hybrid-identity.md) (Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory) beschrieben vor.