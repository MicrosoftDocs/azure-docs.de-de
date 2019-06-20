---
title: 'Azure Active Directory Domain Services: Leitfaden zur Verwaltung | Microsoft-Dokumentation'
description: Erstellen einer Organisationseinheit (OE) in durch Azure AD Domain Services verwalteten Domänen
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: e7ff91a87377deed06fa4214cc1e5764e8b2da92
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245347"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Erstellen einer Organisationseinheit (OE) in einer durch Azure AD Domain Services verwalteten Domäne
Zu den durch Azure AD Domain Services verwalteten Domänen gehören zwei integrierte Container mit den Bezeichnungen „AADDC Computers“ und „AADDC Users“. Der Container „AADDC Computers“ enthält Computerobjekte für alle Computer, die in die verwaltete Domäne eingebunden sind. Der Container „AADDC Users“ enthält die Benutzer und Gruppen im Azure AD-Mandanten. Gelegentlich kann es notwendig werden, Dienstkonten für die verwaltete Domäne zu erstellen, um Workloads bereitzustellen. Zu diesem Zweck können Sie eine benutzerdefinierte Organisationseinheit (OE) in der verwalteten Domäne erstellen und innerhalb dieser Organisationseinheit Dienstkonten erstellen. In diesem Artikel wird gezeigt, wie Sie in der verwalteten Domäne eine Organisationseinheit erstellen.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voraussetzungen
Um die in diesem Artikel beschriebenen Aufgaben auszuführen, benötigen Sie Folgendes:

1. Ein gültiges **Azure-Abonnement**.
2. Ein **Azure AD-Verzeichnis** – entweder synchronisiert mit einem lokalen Verzeichnis oder als reines Cloud-Verzeichnis
3. **Azure AD Domain Services** müssen für das Azure AD-Verzeichnis aktiviert sein. Wenn dies noch nicht der Fall ist, führen Sie alle Aufgaben im Leitfaden [Erste Schritte](create-instance.md)aus.
4. Einen in die Domäne eingebundenen virtuellen Computer, über den Sie die mit den Azure AD-Domänendiensten verwaltete Domäne verwalten. Wenn Sie nicht über einen virtuellen Computer dieser Art verfügen, führen Sie alle Schritte wie in Artikel [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-windows-vm.md) beschriebenen aus.
5. Sie benötigen die Anmeldeinformationen eines **Benutzerkontos, das in Ihrem Verzeichnis der Administratorengruppe für AAD-Domänencontroller angehört**, um innerhalb Ihrer verwalteten Domäne eine benutzerdefinierte Organisationseinheit (OE) erstellen zu können.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Installieren von AD-Verwaltungstools auf einem in die Domäne eingebundenen virtuellen Computer für die Remoteverwaltung
Durch die Azure AD Domain Services verwaltete Domänen können mithilfe bekannter Active Directory-Verwaltungstools verwaltet werden, beispielsweise über das Active Directory-Verwaltungscenter (Active Directory Administrative Center, ADAC) oder AD PowerShell. Mandantenadministratoren besitzen keine Berechtigungen, um über Remotedesktop eine Verbindung mit Domänencontrollern in der verwalteten Domäne herzustellen. Um die verwaltete Domäne zu verwalten, installieren Sie die AD-Verwaltungstools auf einem virtuellen Computer, der per Domänenbeitritt in die verwaltete Domäne eingebunden wurde. Anweisungen hierzu finden Sie im Artikel [Verwalten einer Azure AD Domain Services-Domäne](manage-domain.md).

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Erstellen einer Organisationseinheit in einer verwalteten Domäne
Nachdem Sie die AD-Verwaltungstools auf dem in die Domäne eingebundenen virtuellen Computer installiert haben, können Sie mithilfe dieser Verwaltungstools in der verwalteten Domäne eine Organisationseinheit erstellen. Führen Sie die folgenden Schritte aus:

> [!NOTE]
> Nur Mitglieder der Gruppe „AAD DC Administrators“ verfügen über die erforderlichen Berechtigungen zum Erstellen einer benutzerdefinierten Organisationseinheit. Stellen Sie darum sicher, dass Sie ein Benutzer dieser Gruppe sind, bevor Sie die folgenden Schritte ausführen.
>
>

1. Klicken Sie auf dem Startbildschirm auf **Verwaltung**. Daraufhin werden die auf dem virtuellen Computer installierten AD-Verwaltungstools angezeigt.

    ![Auf dem Server installierte Verwaltungstools](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Klicken Sie auf **Active Directory-Verwaltungscenter**.

    ![Active Directory-Verwaltungscenter](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Klicken Sie im linken Bereich auf den Domänennamen (z.B. „contoso100.com“), um die Domäne anzuzeigen.

    ![ADAC – Domäne anzeigen](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. Klicken Sie auf der rechten Seite im Bereich **Aufgaben** unter dem Knoten für den Domänennamen auf **Neu**. In diesem Beispiel klicken Sie rechts im Bereich **Aufgaben** unter dem Knoten „contoso100(local)“ auf **Neu**.

    ![ADAC – neue Organisationseinheit](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Die Option zum Erstellen einer Organisationseinheit wird angezeigt. Klicken Sie auf **Organisationseinheit**, um das Dialogfeld **Organisationseinheit erstellen** zu öffnen.
6. Geben Sie im Dialogfeld **Organisationseinheit erstellen** einen **Namen** für die neue Organisationseinheit an. Geben Sie eine kurze Beschreibung für die Organisationseinheit an. Sie können auch im Feld **Verwaltet von** für die Organisationseinheit Angaben hinterlegen. Klicken Sie auf **OK**, um die benutzerdefinierte Organisationseinheit zu erstellen.

    ![ADAC – Dialogfeld „Organisationseinheit erstellen“](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. Die neu erstellte Organisationseinheit wird jetzt im Active Directory-Verwaltungscenter (AD Administrative Center, ADAC) angezeigt.

    ![ADAC – Organisationseinheit erstellt](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Berechtigungen und Sicherheit für neu erstellte Organisationseinheiten
Standardmäßig erhält der Benutzer, der die benutzerdefinierte Organisationseinheit erstellt hat (und Mitglied der Gruppe „AAD DC Administrators“ ist), die Administratorrechte für die diese Organisationseinheit (Vollzugriff). Dieser Benutzer kann dann anderen Benutzern oder der Gruppe „AAD DC Administrators“ ganz nach Bedarf Berechtigungen erteilen. Wie im folgenden Screenshot ersichtlich hat der Benutzer bob@domainservicespreview.onmicrosoft.com, der die neue Organisationseinheit „MyCustomOU“ erstellt hat, vollständige Kontrolle darüber (Vollzugriff).

 ![ADAC – Sicherheit für neue Organisationseinheit](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Hinweise zum Verwalten von benutzerdefinierten Organisationseinheiten
Nach dem Erstellen einer benutzerdefinierten Organisationseinheit können Sie in dieser Organisationseinheit Benutzer, Gruppen, Computer und Dienstkonten erstellen. Sie können keine Benutzer oder Gruppen aus der Organisationseinheit „AADDC Users“ in benutzerdefinierte Organisationseinheiten verschieben.

> [!WARNING]
> Benutzerkonten, Gruppen, Dienstkonten und Computerobjekte, die Sie in benutzerdefinierten Organisationseinheiten erstellen, stehen in Ihrem Azure AD-Mandanten nicht zur Verfügung. Anders ausgedrückt: Diese Objekte können also mit der Azure AD Graph-API oder in der Azure AD-Benutzeroberfläche angezeigt werden. Sie stehen nur in Ihrer durch Azure AD Domain Services verwalteten Domäne zur Verfügung.
>
>

## <a name="related-content"></a>Verwandte Inhalte
* [Verwalten einer Azure AD Domain Services-Domäne](manage-domain.md)
* [Verwalten von Gruppenrichtlinien für Azure AD Domain Services](manage-group-policy.md)
* [Active Directory-Verwaltungscenter: Erste Schritte](https://technet.microsoft.com/library/dd560651.aspx)
* [Schrittweise Anleitung zu Dienstkonten](https://technet.microsoft.com/library/dd548356.aspx)
