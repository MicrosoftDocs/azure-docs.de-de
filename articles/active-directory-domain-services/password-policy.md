---
title: Erstellen und Verwenden von Kennwortrichtlinien in Azure AD Domain Services | Microsoft-Dokumentation
description: Hier erfahren Sie, wie und warum differenzierte Kennwortrichtlinien verwendet werden, um Kontokennwörter in einer verwalteten Azure AD DS-Domäne zu sichern und zu steuern.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: df132af1675b3f373fe1eab5685c5d2f07813445
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619231"
---
# <a name="password-and-account-lockout-policies-on-azure-active-directory-domain-services-managed-domains"></a>Kennwort- und Kontosperrungsrichtlinien für verwaltete Azure Active Directory Domain Services-Domänen

Um die Benutzersicherheit in Azure Active Directory Domain Services (Azure AD DS) zu verwalten, können Sie differenzierte Kennwortrichtlinien definieren, die die Einstellungen für die Kontosperre oder die minimale Kennwortlänge und -komplexität steuern. Eine differenzierte Standardkennwortrichtlinie wird erstellt und auf alle Benutzer in einer verwalteten Azure AD DS-Domäne angewendet. Um eine differenzierte Steuerung zu gewährleisten und bestimmte Geschäfts- oder Complianceanforderungen zu erfüllen, können zusätzliche Richtlinien erstellt und auf bestimmte Gruppen von Benutzern angewendet werden.

In diesem Artikel wird das Erstellen und Konfigurieren einer differenzierten Kennwortrichtlinie in Azure AD DS mithilfe des Active Directory-Verwaltungscenters erläutert.

> [!NOTE]
> Kennwortrichtlinien sind nur für verwaltete Domänen verfügbar, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden. Bei älteren verwalteten Domänen, die auf klassische Weise erstellt wurden, sollten Sie eine [Migration vom klassischen virtuellen Netzwerkmodell zu Resource Manager][migrate-from-classic] ausführen.

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
  * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
  * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
  * Führen Sie bei Bedarf das Tutorial zum [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance] aus.
  * Die verwaltete Domäne muss mit dem Azure Resource Manager-Bereitstellungsmodell erstellt worden sein. Bei Bedarf sollten Sie eine [Migration vom klassischen virtuellen Netzwerkmodell zu Resource Manager][migrate-from-classic] ausführen.
* Eine Windows Server-Verwaltungs-VM, die in die verwaltete Domäne eingebunden ist.
  * Führen Sie bei Bedarf das Tutorial zum [Erstellen eines virtuellen Verwaltungscomputers][tutorial-create-management-vm] aus.
* Ein Benutzerkonto, das Mitglied der *Administratorengruppe für Azure AD-Domänencontroller* (AAD-DC-Administratoren) in Ihrem Azure AD-Mandanten ist.

## <a name="default-password-policy-settings"></a>Standardeinstellungen von Kennwortrichtlinien

Mit differenzierten Kennwortrichtlinien (FGPP) können Sie bestimmte Einschränkungen für Kennwort- und Kontosperrungsrichtlinien auf verschiedene Benutzer in einer Domäne anwenden. Zum Sichern privilegierter Konten können Sie beispielsweise strengere Einstellungen für Kontosperren anwenden als für reguläre Konten, die nicht privilegiert sind. Sie können mehrere FGPPs innerhalb einer verwalteten Domäne erstellen und die Reihenfolge der Priorität festlegen, in der sie auf die Benutzer angewendet werden sollen.

Weitere Informationen zu Kennwortrichtlinien und zum Verwenden des Active Directory-Verwaltungscenters finden Sie in den folgenden Artikeln:

* [Informationen über differenzierte Kennwortrichtlinien](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurieren differenzierter Kennwortrichtlinien mit dem Active Directory-Verwaltungscenter](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

Richtlinien werden über die Gruppenzuordnung in einer verwalteten Domäne verteilt. Von Ihnen vorgenommene Änderungen werden bei der nächsten Benutzeranmeldung angewendet. Wenn Sie die Richtlinie ändern, wird ein bereits gesperrtes Benutzerkonto nicht entsperrt.

Kennwortrichtlinien verhalten sich je nachdem, wie das Benutzerkonto erstellt wurde, auf das sie angewendet werden, etwas anders. Es gibt zwei Möglichkeiten, wie ein Benutzerkonto in Azure AD DS erstellt werden kann:

* Das Benutzerkonto kann aus Azure AD synchronisiert werden. Dazu gehören reine Cloudbenutzerkonten, die direkt in Azure erstellt wurden, sowie hybride Benutzerkonten, die aus einer lokalen AD DS-Umgebung mit Azure AD Connect synchronisiert werden.
    * Der Großteil der Benutzerkonten in Azure AD DS wird durch den Synchronisierungsprozess aus Azure AD erstellt.
* Das Benutzerkonto kann in einer verwalteten Domäne manuell erstellt werden und ist dann in Azure AD nicht vorhanden.

Für alle Benutzer (unabhängig davon, wie sie erstellt werden) gelten die folgenden Kontosperrrichtlinien, die von der Standardkennwortrichtlinie in Azure AD DS angewendet werden:

* **Kontosperrungsdauer:** 30
* **Zulässige Anzahl fehlerhafter Anmeldeversuche:** 5
* **Anzahl fehlerhafter Anmeldeversuche zurücksetzen nach:** 30 Minuten
* **Maximales Kennwortalter (Gültigkeitsdauer):** 90 Tage

Bei diesen Standardeinstellungen werden Benutzerkonten für 30 Minuten gesperrt, wenn innerhalb von zwei Minuten fünf ungültige Kennwörter verwendet werden. Nach 30 Minuten werden die Konten automatisch wieder entsperrt.

Kontosperrungen erfolgen nur innerhalb der verwalteten Domäne. Benutzerkonten werden nur in Azure AD DS gesperrt, und zwar nur aufgrund fehlerhafter Anmeldeversuche bei der verwalteten Domäne. Benutzerkonten, die aus Azure AD oder lokal synchronisiert wurden, werden in ihren Quellverzeichnissen nicht gesperrt, sondern nur in Azure AD DS.

Wenn Sie eine Azure AD-Kennwortrichtlinie verwenden, die ein maximales Kennwortalter von mehr als 90 Tagen angibt, wird dieses Kennwortalter auf die Standardrichtlinie in Azure AD DS angewendet. Sie können eine benutzerdefinierte Kennwortrichtlinie konfigurieren, um ein anderes maximales Kennwortalter in Azure AD DS zu definieren. Seien Sie vorsichtig, wenn Sie ein kürzeres maximales Kennwortalter in einer Azure AD DS-Kennwortrichtlinie konfiguriert haben als in Azure AD oder einer lokalen AD DS-Umgebung. In diesem Szenario kann das Kennwort eines Benutzers in Azure AD DS ablaufen, bevor er in Azure AD oder einer lokalen AD DS-Umgebung aufgefordert wird, das Kennwort zu ändern.

Auf in einer verwalteten Domäne manuell erstellte Benutzerkonten werden auch die folgenden zusätzlichen Kennworteinstellungen aus der Standardrichtlinie angewendet. Diese Einstellungen gelten nicht für Benutzerkonten, die aus Azure AD synchronisiert werden, da ein Benutzer sein Kennwort in Azure AD DS nicht direkt aktualisieren kann.

* **Minimale Kennwortlänge (Zeichen):** 7
* **Kennwörter müssen den Anforderungen an die Komplexität entsprechen**

Sie können die Einstellungen für Kontosperrung oder Kennwort nicht in der Standardkennwortrichtlinie ändern. Mitglieder der Gruppe *AAD DC-Administratoren* können stattdessen benutzerdefinierte Kennwortrichtlinien erstellen und so konfigurieren, dass sie die integrierte Standardrichtlinie außer Kraft setzen, wie im nächsten Abschnitt gezeigt wird.

## <a name="create-a-custom-password-policy"></a>Erstellen einer benutzerdefinierten Kennwortrichtlinie

Wenn Sie Anwendungen in Azure erstellen und ausführen, sollten Sie eine benutzerdefinierte Kennwortrichtlinie konfigurieren. Beispielsweise können Sie eine Richtlinie erstellen, um unterschiedliche Einstellungen für eine Kontosperrungsrichtlinie festzulegen.

Benutzerdefinierte Kennwortrichtlinien werden auf Gruppen in einer verwalteten Domäne angewendet. Diese Konfiguration überschreibt effektiv die Standardrichtlinie.

Um eine benutzerdefinierte Kennwortrichtlinie zu erstellen, verwenden Sie die Active Directory-Verwaltungstools auf einer in die Domäne eingebundenen VM. Im Active Directory-Verwaltungscenter können Sie Ressourcen in einer verwalteten Domäne (einschließlich Organisationseinheiten) anzeigen, bearbeiten und erstellen.

> [!NOTE]
> Um eine benutzerdefinierte Kennwortrichtlinie in einer verwalteten Domäne erstellen zu können, müssen Sie bei einem Benutzerkonto angemeldet sein, das Mitglied der Gruppe *AAD DC-Administratoren* ist.

1. Klicken Sie auf dem Startbildschirm auf **Verwaltung**. Es wird eine Liste der verfügbaren Verwaltungstools angezeigt, die im Tutorial zum [Erstellen eines virtuellen Verwaltungscomputers][tutorial-create-management-vm] installiert wurden.
1. Wählen Sie zum Erstellen und Verwalten von Organisationseinheiten **Active Directory-Verwaltungscenter** aus der Liste der Verwaltungstools aus.
1. Wählen Sie im linken Bereich Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Öffnen Sie den Container **System** und dann den Container **Kennworteinstellungen**.

    Es wird eine integrierte Kennwortrichtlinie für die verwaltete Domäne angezeigt. Sie können diese integrierte Richtlinie nicht ändern. Erstellen Sie stattdessen eine benutzerdefinierte Kennwortrichtlinie, um die Standardrichtlinie außer Kraft zu setzen.

    ![Erstellen einer benutzerdefinierten Kennwortrichtlinie im Active Directory-Verwaltungscenter](./media/password-policy/create-password-policy-adac.png)

1. Klicken Sie im Bereich **Tasks** auf der rechten Seite auf **Neu > Kennworteinstellungen**.
1. Geben Sie im Dialogfeld **Kennworteinstellungen erstellen** einen Namen für die Richtlinie ein, z. B. *MyCustomFGPP*.
1. Wenn mehrere Kennwortrichtlinien vorhanden sind, wird die Richtlinie mit der höchsten Rangfolge oder Priorität auf einen Benutzer angewendet. Je niedriger die Zahl, desto höher die Priorität. Die Standardkennwortrichtlinie weist die Priorität *200* auf.

    Legen Sie die Rangfolge für Ihre benutzerdefinierte Kennwortrichtlinie so fest, dass die Standardeinstellung überschrieben wird, z.B. als *1*.

1. Bearbeiten Sie andere Einstellungen für Kennwortrichtlinien wie gewünscht. Beachten Sie die folgenden wichtigen Punkte:

    * Einstellungen wie Kennwortkomplexität, Alter oder Ablaufzeit gelten nur für Benutzer, die in einer verwalteten Domäne manuell erstellt werden.
    * Kontosperreinstellungen gelten für alle Benutzer, werden jedoch nur in der verwalteten Domäne und nicht in Azure AD wirksam.

    ![Erstellen einer benutzerdefinierten differenzierten Kennwortrichtlinie](./media/password-policy/custom-fgpp.png)

1. Deaktivieren Sie **Vor versehentlichem Löschen schützen**. Wenn diese Option aktiviert ist, kann die FGPP nicht gespeichert werden.
1. Wählen Sie im Abschnitt **Direkt anwendbar auf** die Schaltfläche **Hinzufügen** aus. Wählen Sie im Dialogfeld **Benutzer oder Gruppen auswählen** die Schaltfläche **Speicherorte** aus.

    ![Auswählen von Benutzern und Gruppen, auf die die Kennwortrichtlinie angewendet werden soll](./media/password-policy/fgpp-applies-to.png)

1. Kennwortrichtlinien können nur auf Gruppen angewendet werden. Erweitern Sie im Dialogfeld **Speicherorte** den Domänennamen (z. B. *aaddscontoso.com*) und wählen Sie dann eine Organisationseinheit (z. B. **AADDC-Benutzer**) aus. Wenn Sie über eine benutzerdefinierte Organisationseinheit verfügen, die eine Gruppe von Benutzern enthält, die Sie anwenden möchten, wählen Sie diese Organisationseinheit aus.

    ![Auswählen der Organisationseinheit, zu der die Gruppe gehört](./media/password-policy/fgpp-container.png)

1. Geben Sie den Namen der Gruppe ein, auf die die Richtlinie angewendet werden soll, und klicken Sie dann auf **Namen überprüfen**, um sicherzustellen, dass die Gruppe vorhanden ist.

    ![Suchen und Auswählen der Gruppe zum Anwenden der FGPP](./media/password-policy/fgpp-apply-group.png)

1. Der Name der ausgewählten Gruppe wird jetzt im Abschnitt **Direkt anwendbar auf** angezeigt. Wählen Sie **OK** aus, um die benutzerdefinierte Kennwortrichtlinie zu speichern.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Kennwortrichtlinien und zum Verwenden des Active Directory-Verwaltungscenters finden Sie in den folgenden Artikeln:

* [Informationen über differenzierte Kennwortrichtlinien](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurieren differenzierter Kennwortrichtlinien mit dem Active Directory-Verwaltungscenter](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
