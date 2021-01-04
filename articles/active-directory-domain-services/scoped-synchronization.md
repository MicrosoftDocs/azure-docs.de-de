---
title: Bereichsbezogene Synchronisierung für Azure AD Domain Services | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die bereichsbezogene Synchronisierung von Azure AD für eine verwaltete Azure Active Directory Domain Services-Domäne im Azure-Portal konfigurieren.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e65b47b2a1fd71c69ecb350f60df1fedff66b74
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618908"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Konfigurieren der bereichsbezogenen Synchronisierung von Azure AD für Azure Active Directory Domain Services mithilfe des Azure-Portals

Zum Bereitstellen von Authentifizierungsdiensten synchronisiert Azure Active Directory Domain Services (Azure AD DS) Benutzer und Gruppen von Azure AD. In einer Hybridumgebung können Benutzer und Gruppen aus einer lokalen AD DS-Umgebung (Active Directory Domain Services) zuerst mithilfe von Azure AD Connect mit Azure AD und anschließend mit einer verwalteten Azure AD DS-Domäne synchronisiert werden.

Standardmäßig werden alle Benutzer und Gruppen aus einem Azure AD-Verzeichnis in einer verwalteten Domäne synchronisiert. Bei speziellen Anforderungen können Sie stattdessen festlegen, dass nur eine definierte Gruppe von Benutzern synchronisiert werden soll.

In diesem Artikel erfahren Sie, wie Sie eine bereichsbezogene Synchronisierung konfigurieren und anschließend die Gruppe der Benutzer für diesen Bereich über das Azure-Portal ändern oder deaktivieren. Sie können [diese Schritte auch mithilfe von PowerShell ausführen][scoped-sync-powershell].

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Führen Sie bei Bedarf das Tutorial zum [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne][tutorial-create-instance] aus.
* Sie benötigen die Berechtigungen eines *globalen Administrators* in Ihrem Azure AD-Mandanten, um den AD DS-Synchronisierungsbereich ändern zu können.

## <a name="scoped-synchronization-overview"></a>Übersicht über die bereichsbezogene Synchronisierung

Standardmäßig werden alle Benutzer und Gruppen aus einem Azure AD-Verzeichnis in einer verwalteten Domäne synchronisiert. Wenn nur wenige Benutzer auf die verwaltete Domäne zugreifen müssen, können Sie nur diese Benutzerkonten synchronisieren. Diese bereichsbezogene Synchronisierung ist gruppenbasiert. Wenn Sie eine gruppenbasierte bereichsbezogene Synchronisierung konfigurieren, werden nur die Benutzerkonten, die zu den angegebenen Gruppen gehören, mit der verwalteten Domäne synchronisiert. Geschachtelte Gruppen werden nicht synchronisiert, nur die von Ihnen ausgewählten.

Sie können den Synchronisierungsbereich ändern, wenn Sie die verwaltete Domäne erstellen, oder nach erfolgter Bereitstellung. Sie können nun auch den Synchronisierungsbereich für eine vorhandene verwaltete Domäne ändern, ohne dass Sie sie dafür neu erstellen müssen.

Weitere Informationen zum Synchronisierungsvorgang finden Sie unter [Grundlegendes zur Synchronisierung in Azure AD Domain Services.][concepts-sync]

> [!WARNING]
> Wenn Sie den Bereich der Synchronisierung ändern, werden alle Daten in der verwalteten Domäne neu synchronisiert. Es gelten die folgenden Bedingungen:
>
>  * Wenn Sie den Synchronisierungsbereich für eine verwaltete Domäne ändern, erfolgt eine vollständige Neusynchronisierung.
>  * Objekte, die in der verwalteten Domäne nicht mehr erforderlich sind, werden gelöscht. Neue Objekte werden in der verwalteten Domäne erstellt.

## <a name="enable-scoped-synchronization"></a>Aktivieren der bereichsbezogenen Synchronisierung

Führen Sie die folgenden Schritte aus, um die bereichsbezogene Synchronisierung im Azure-Portal zu aktivieren:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie im Menü auf der linken Seite **Synchronisierung** aus.
1. Klicken Sie für *Synchronisierungstyp* auf **Bereichsbezogen**.
1. Klicken Sie auf **Gruppen auswählen**, und suchen Sie nach den Gruppen, die Sie hinzufügen möchten, und wählen Sie sie aus.
1. Wenn alle Änderungen vorgenommen wurden, wählen Sie **Synchronisierungsbereich speichern** aus.

Wenn Sie den Bereich der Synchronisierung ändern, werden alle Daten in der verwalteten Domäne neu synchronisiert. Objekte, die in der verwalteten Domäne nicht mehr erforderlich sind, werden gelöscht, und die Neusynchronisierung kann einige Zeit in Anspruch nehmen.

## <a name="modify-scoped-synchronization"></a>Ändern der bereichsbezogenen Synchronisierung

Führen Sie die folgenden Schritte aus, um die Liste der Gruppen zu ändern, deren Benutzer mit der verwalteten Domäne synchronisiert werden sollen:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie im Menü auf der linken Seite **Synchronisierung** aus.
1. Um eine Gruppe hinzuzufügen, wählen Sie im oberen Bereich **+ Gruppen auswählen** aus, und wählen Sie dann die hinzuzufügenden Gruppen aus.
1. Um eine Gruppe aus dem Synchronisierungsbereich zu entfernen, wählen Sie diese in der Liste der derzeit synchronisierten Gruppen aus, und wählen Sie dann **Gruppen entfernen** aus.
1. Wenn alle Änderungen vorgenommen wurden, wählen Sie **Synchronisierungsbereich speichern** aus.

Wenn Sie den Bereich der Synchronisierung ändern, werden alle Daten in der verwalteten Domäne neu synchronisiert. Objekte, die in der verwalteten Domäne nicht mehr erforderlich sind, werden gelöscht, und die Neusynchronisierung kann einige Zeit in Anspruch nehmen.

## <a name="disable-scoped-synchronization"></a>Deaktivieren der bereichsbezogenen Synchronisierung

Führen Sie die folgenden Schritte aus, um die gruppenbasierte bereichsbezogene Synchronisierung für eine verwaltete Domäne zu deaktivieren:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie im Menü auf der linken Seite **Synchronisierung** aus.
1. Legen Sie den *Synchronisierungstyp* von **Bereichsbezogen** auf **Alle** fest, und klicken Sie dann auf **Synchronisierungsbereich speichern**.

Wenn Sie den Bereich der Synchronisierung ändern, werden alle Daten in der verwalteten Domäne neu synchronisiert. Objekte, die in der verwalteten Domäne nicht mehr erforderlich sind, werden gelöscht, und die Neusynchronisierung kann einige Zeit in Anspruch nehmen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Synchronisierungsvorgang finden Sie unter [Grundlegendes zur Synchronisierung in Azure AD Domain Services.][concepts-sync]

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
