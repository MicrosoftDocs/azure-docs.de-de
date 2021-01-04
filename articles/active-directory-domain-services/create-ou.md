---
title: Erstellen einer Organisationseinheit (OE) in Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine benutzerdefinierte Organisationseinheit (OU) in einer von Azure AD Domain Services verwalteten Domäne erstellen und verwalten.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fbdfcc23553a27aaa4d7bbd4bff9c4f33cb8fd19
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620017"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Erstellen einer Organisationseinheit (OE) in einer verwalteten Azure Active Directory Domain Services-Domäne

Mit Organisationseinheiten in einer von Active Directory Domain Services (AD DS) verwalteten Domäne können Sie Objekte wie Benutzerkonten, Dienstkonten oder Computerkonten logisch gruppieren. Sie können dann Administratoren bestimmten Organisationseinheiten zuordnen und Gruppenrichtlinien anwenden, um gezielte Konfigurationseinstellungen zu erzwingen.

Von Azure AD DS verwaltete Domänen umfassen die folgenden beiden integrierten Organisationseinheiten:

* *AADDC-Computer:* enthält Computerobjekte für alle Computer, die in die verwaltete Domäne eingebunden sind.
* *AADDC-Benutzer:* enthält Benutzer und Gruppen, die über den Azure AD-Mandanten synchronisiert wurden.

Wenn Sie Workloads erstellen und ausführen, die Azure AD DS verwenden, müssen Sie möglicherweise Dienstkonten für Anwendungen erstellen, damit diese sich selbst authentifizieren können. Um diese Dienstkonten zu organisieren, erstellen Sie häufig eine benutzerdefinierte Organisationseinheit in der verwalteten Domäne und erstellen dann Dienstkonten innerhalb dieser Organisationseinheit.

In einer Hybridumgebung werden in einer lokalen AD DS-Umgebung erstellte Organisationseinheiten nicht mit der verwalteten Domäne synchronisiert. Verwaltete Domänen weisen eine flache OE-Struktur auf. Alle Benutzerkonten und -gruppen werden ungeachtet der Synchronisierung aus verschiedenen lokalen Domänen oder Gesamtstrukturen im Container *AADDC Users* gespeichert, auch wenn Sie eine hierarchische OE-Struktur konfiguriert haben.

In diesem Artikel wird gezeigt, wie Sie in der verwalteten Domäne eine Organisationseinheit erstellen.

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

## <a name="custom-ou-considerations-and-limitations"></a>Überlegungen und Einschränkungen zu benutzerdefinierten Organisationseinheiten

Wenn Sie benutzerdefinierte Organisationseinheiten in einer verwalteten Domäne erstellen, erhalten Sie zusätzliche Flexibilität bei der Benutzerverwaltung und der Anwendung von Gruppenrichtlinien. Im Vergleich zu einer lokalen AD DS-Umgebung gibt es einige Einschränkungen und Aspekte, die beim Erstellen und Verwalten einer benutzerdefinierten OE-Struktur in einer verwalteten Domäne zu berücksichtigen sind:

* Um benutzerdefinierte Organisationseinheiten erstellen zu können, müssen Benutzer Mitglied der Gruppe *AAD DC Administrators* sein.
* Ein Benutzer, der eine benutzerdefinierte Organisationseinheit erstellt, erhält Administratorrechte (Vollzugriff) über diese Organisationseinheit und ist der Ressourcenbesitzer.
    * Standardmäßig hat die Gruppe *AAD DC Administrators* auch Vollzugriff auf die benutzerdefinierte Organisationseinheit.
* Es wird eine Standardorganisationseinheit für *AADDC-Benutzer* erstellt, die alle synchronisierten Benutzerkonten Ihres Azure AD-Mandanten enthält.
    * Sie können keine Benutzer oder Gruppen der Organisationseinheit *AADDC-Benutzer* in benutzerdefinierte Organisationseinheiten verschieben, die Sie erstellen. Es können nur in der verwalteten Domäne erstellte Benutzerkonten oder Ressourcen in benutzerdefinierte Organisationseinheiten verschoben werden.
* Benutzerkonten, Gruppen, Dienstkonten und Computerobjekte, die Sie in benutzerdefinierten Organisationseinheiten erstellen, stehen in Ihrem Azure AD-Mandanten nicht zur Verfügung.
    * Diese Objekte werden nicht über die Microsoft Graph-API oder auf der Azure AD-Benutzeroberfläche angezeigt. Sie sind nur in Ihrer verwalteten Domäne verfügbar.

## <a name="create-a-custom-ou"></a>Erstellen einer benutzerdefinierten Organisationseinheit

Um eine benutzerdefinierte Organisationseinheit zu erstellen, verwenden Sie die Active Directory-Verwaltungstools auf einer in die Domäne eingebundenen VM. Im Active Directory-Verwaltungscenter können Sie Ressourcen in einer verwalteten Domäne (einschließlich Organisationseinheiten) anzeigen, bearbeiten und erstellen.

> [!NOTE]
> Um eine benutzerdefinierte Organisationseinheit in einer verwalteten Domäne erstellen zu können, müssen Sie bei einem Benutzerkonto angemeldet sein, das Mitglied der Gruppe *AAD DC-Administratoren* ist.

1. Melden Sie sich bei Ihrer Verwaltungs-VM an. Weitere Informationen zu den Schritten zum Herstellen einer Verbindung mithilfe des Azure-Portals finden Sie unter [Herstellen einer Verbindung mit einer Windows Server-VM][connect-windows-server-vm].
1. Klicken Sie auf dem Startbildschirm auf **Verwaltung**. Es wird eine Liste der verfügbaren Verwaltungstools angezeigt, die im Tutorial zum [Erstellen eines virtuellen Verwaltungscomputers][tutorial-create-management-vm] installiert wurden.
1. Wählen Sie zum Erstellen und Verwalten von Organisationseinheiten **Active Directory-Verwaltungscenter** aus der Liste der Verwaltungstools aus.
1. Wählen Sie im linken Bereich Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus. Eine Liste der vorhandenen Organisationseinheiten und Ressourcen wird angezeigt:

    ![Auswählen Ihrer verwalteten Domäne im Active Directory-Verwaltungscenter](./media/create-ou/create-ou-adac-overview.png)

1. Der Bereich **Aufgaben** wird auf der rechten Seite des Active Directory-Verwaltungscenters angezeigt. Wählen Sie unter der Domäne (z. B. *aaddscontoso.com*) die Option **Neu > Organisationseinheit** aus.

    ![Wählen Sie die Option aus, um eine neue Organisationseinheit im Active Directory-Verwaltungscenter zu erstellen.](./media/create-ou/create-ou-adac-new-ou.png)

1. Geben Sie im Dialogfeld **Organisationseinheit erstellen** einen **Namen** für die neue Organisationseinheit an, z. B. *MyCustomOu*. Geben Sie eine kurze Beschreibung für die Organisationseinheit an, z. B. *Benutzerdefinierte Organisationseinheit für Dienstkonten*. Bei Bedarf können Sie auch das Feld **Verwaltet von** für die Organisationseinheit festlegen. Wählen Sie **OK** aus, um die benutzerdefinierte Organisationseinheit zu erstellen.

    ![Erstellen einer benutzerdefinierten Organisationseinheit im Active Directory-Verwaltungscenter](./media/create-ou/create-ou-dialog.png)

1. Im Active Directory-Verwaltungscenter ist jetzt die benutzerdefinierte Organisationseinheit aufgeführt und kann verwendet werden:

    ![Zur Verwendung verfügbare benutzerdefinierte Organisationseinheit im Active Directory-Verwaltungscenter](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung der Verwaltungstools oder zum Erstellen und Verwenden von Dienstkonten finden Sie in den folgenden Artikeln:

* [Active Directory-Verwaltungscenter: Erste Schritte](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [Schrittweise Anleitung zu Dienstkonten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm