---
title: Aktivieren des SharePoint-Benutzerprofildiensts mit Azure AD DS | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine durch Azure Active Directory Domain Services verwaltete Domäne für die Unterstützung der Profilsynchronisierung für SharePoint Server konfigurieren.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8fbde10ccf5a7f083f5fbddaadd6668d880a1fac
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619826"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Konfigurieren von Azure Active Directory Domain Services für die Unterstützung der Benutzerprofilsynchronisierung für SharePoint Server

SharePoint Server umfasst einen Dienst zum Synchronisieren von Benutzerprofilen. Mit diesem Feature können Benutzerprofile an einem zentralen Ort gespeichert werden, und es kann über mehrere SharePoint-Websites und -Farmen darauf zugegriffen werden. Zum Konfigurieren des SharePoint Server-Benutzerprofildiensts müssen die entsprechenden Berechtigungen in einer durch Azure Active Directory Domain Services (Azure AD DS) verwalteten Domäne erteilt werden. Weitere Informationen finden Sie unter der [Benutzerprofilsynchronisierung in SharePoint Server](/SharePoint/administration/user-profile-service-administration).

In diesem Artikel wird gezeigt, wie Sie Azure AD DS so konfigurieren, dass der SharePoint Server-Synchronisierungsdienst für Benutzerprofile zulässig ist.

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Führen Sie bei Bedarf das Tutorial zum [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance] aus.
* Eine Windows Server-Verwaltungs-VM, die in die verwaltete Azure AD DS-Domäne eingebunden ist.
    * Führen Sie bei Bedarf das Tutorial zum [Erstellen eines virtuellen Verwaltungscomputers][tutorial-create-management-vm] aus.
* Ein Benutzerkonto, das Mitglied der *Administratorengruppe für Azure AD-Domänencontroller* (AAD-DC-Administratoren) in Ihrem Azure AD-Mandanten ist.
* Ein SharePoint-Dienstkonto für den Benutzerprofil-Synchronisierungsdienst.
    * Bei Bedarf finden Sie weitere Informationen unter [Planen von Administrator- und Dienstkonten in SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Übersicht über Dienstkonten

In einer verwalteten Domäne ist eine Sicherheitsgruppe mit dem Namen *AAD DC-Dienstkonten* als Teil der Organisationseinheit *Benutzer* vorhanden. An Mitglieder dieser Sicherheitsgruppe werden die folgenden Berechtigungen delegiert:

- Berechtigung **Verzeichnisänderungen replizieren** für den Stamm-DSE
- Berechtigung **Verzeichnisänderungen replizieren** im Namenskontext der *Konfiguration* (`cn=configuration`-Container)

Die Sicherheitsgruppe *AAD DC-Dienstkonten* ist außerdem ein Mitglied der integrierten Gruppe *Prä-Windows 2000 kompatibler Zugriff*.

Beim Hinzufügen zu dieser Sicherheitsgruppe werden dem Dienstkonto für den SharePoint Server-Synchronisierungsdienst für Benutzerprofile die erforderlichen Berechtigungen erteilt, um ordnungsgemäß zu funktionieren.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Aktivieren der Unterstützung für die SharePoint Server-Benutzerprofilsynchronisierung

Das Dienstkonto für SharePoint Server benötigt geeignete Berechtigungen zum Replizieren von Änderungen am Verzeichnis und ordnungsgemäßem Funktionieren der SharePoint Server-Benutzerprofilsynchronisierung. Zum Bereitstellen dieser Berechtigungen fügen Sie das Dienstkonto, das für die SharePoint-Benutzerprofilsynchronisierung verwendet wird, der Gruppe *AAD DC-Dienstkonten* hinzu.

Führen Sie auf der VM für die Azure AD DS-Verwaltung die folgenden Schritte aus:

> [!NOTE]
> Zum Bearbeiten der Gruppenmitgliedschaft in einer verwalteten Domäne müssen Sie bei einem Benutzerkonto angemeldet sein, das Mitglied der Gruppe *AAD DC-Administratoren* ist.

1. Klicken Sie auf dem Startbildschirm auf **Verwaltung**. Es wird eine Liste der verfügbaren Verwaltungstools angezeigt, die im Tutorial zum [Erstellen eines virtuellen Verwaltungscomputers][tutorial-create-management-vm] installiert wurden.
1. Zum Verwalten der Gruppenmitgliedschaft wählen Sie **Active Directory-Verwaltungscenter** aus der Liste der Verwaltungstools aus.
1. Wählen Sie im linken Bereich Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus. Eine Liste der vorhandenen Organisationseinheiten und Ressourcen wird angezeigt.
1. Wählen Sie die Organisationseinheit **Benutzer** und dann die Sicherheitsgruppe *AAD DC-Dienstkonten* aus.
1. Wählen Sie **Mitglieder** und dann **Hinzufügen...** aus.
1. Geben Sie den Namen des SharePoint-Dienstkontos ein, und klicken Sie dann auf **OK**. Im folgenden Beispiel hat das SharePoint-Dienstkonto den Namen *spadmin*:

    ![Hinzufügen des SharePoint-Dienstkontos zur Sicherheitsgruppe „AAD DC-Dienstkonten“](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Verwalten der Benutzerprofilsynchronisierung in SharePoint Server](/SharePoint/administration/manage-profile-synchronization).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts