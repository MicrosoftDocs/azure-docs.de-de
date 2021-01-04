---
title: 'Tutorial: Erstellen einer Replikatgruppe in Azure AD Domain Services | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie im Azure-Portal Replikatgruppen für die Dienstresilienz mit Azure AD Domain Services erstellen und verwenden.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: justinha
ms.openlocfilehash: 059f711269fd5f1eea9675f238a6003eaf1a0534
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618109"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services-preview"></a>Tutorial: Erstellen und Verwenden von Replikatgruppen für Resilienz oder Geolocation in Azure Active Directory Domain Services (Vorschau)

Sie können *Replikatgruppen* verwenden, um die Resilienz von Azure Active Directory Domain Services (Azure AD DS) zu verbessern oder Bereitstellungen an zusätzlichen geografischen Standorten in der Nähe Ihrer Anwendungen durchzuführen. Jeder Namespace einer verwalteten Azure AD DS-Domäne (beispielsweise *aaddscontoso.com*) enthält eine anfängliche Replikatgruppe. Durch die Erstellung zusätzlicher Replikatgruppen in anderen Azure-Regionen lässt sich geografische Resilienz für eine verwaltete Domäne erzielen.

Eine Replikatgruppe kann einem beliebigen, mittels Peering verknüpften virtuellen Netzwerk in einer beliebigen Azure-Region mit Azure AD DS-Unterstützung hinzugefügt werden.

Replikatgruppen sind eine öffentliche Previewfunktion in Azure AD Domain Services. Berücksichtigen Sie die für Previewfunktionen geltenden Supportunterschiede. Weitere Informationen zu Vorschauversionen Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Ermitteln der Anforderungen für virtuelle Netzwerke
> * Erstellen einer Replikatgruppe
> * Löschen einer Replikatgruppe

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurde und in Ihrem Azure AD-Mandanten konfiguriert ist.
    * [Erstellen und konfigurieren Sie eine verwaltete Azure Active Directory Domain Services-Domäne][tutorial-create-instance], sofern erforderlich.

    > [!IMPORTANT]
    > Verwaltete Domänen, die mit dem klassischen Bereitstellungsmodell erstellt wurden, können keine Replikatgruppen verwenden. Sie müssen außerdem mindestens ein *Enterprise*-SKU für Ihre verwaltete Domäne verwenden. [Ändern Sie die SKU ggf. in eine verwaltete Domäne.][howto-change-sku]

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

In diesem Tutorial werden Replikatgruppen über das Azure-Portal erstellt und verwaltet. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com) an.

## <a name="networking-considerations"></a>Überlegungen zum Netzwerkbetrieb

Die virtuellen Netzwerke, die als Host für Replikatgruppen fungieren, müssen miteinander kommunizieren können. Darüber hinaus müssen von Azure AD DS abhängige Anwendungen und Dienste über Netzwerkkonnektivität mit den virtuellen Netzwerken verfügen, in denen die Replikatgruppen gehostet werden. Zwischen allen virtuellen Netzwerken muss das Peering virtueller Azure-Netzwerke konfiguriert werden, um ein voll vermaschtes Netzwerk zu erstellen. Dieses Peering ermöglicht eine effektive standortinterne Replikation zwischen Replikatgruppen.

Machen Sie sich vor der Verwendung von Replikatgruppen in Azure AD DS zunächst mit den folgenden Anforderungen für virtuelle Azure-Netzwerke vertraut:

* Vermeiden Sie Überschneidungen zwischen IP-Adressräumen, um VNET-Peering und -Routing zu ermöglichen.
* Es wurden Subnetze mit genügend IP-Adressen erstellt, um Ihr Szenario zu unterstützen.
* Stellen Sie sicher, dass Azure AD DS über ein eigenes Subnetz verfügt. Dieses VNET-Subnetz darf nicht von virtuellen Anwendungscomputern und Diensten verwendet werden.
* Über Peering verbundene virtuelle Netzwerke sind nicht transitiv.

> [!TIP]
> Wenn Sie im Azure-Portal eine Replikatgruppe erstellen, werden die Netzwerkpeerings zwischen virtuellen Netzwerken für Sie erstellt.
>
> Bei Bedarf können Sie ein virtuelles Netzwerk und ein Subnetz erstellen, wenn Sie im Azure-Portal eine Replikatgruppe hinzufügen. Alternativ können Sie vorhandene VNET-Ressourcen in der Zielregion für eine Replikatgruppe auswählen und die Peerings automatisch erstellen lassen, sofern sie noch nicht vorhanden sind.

## <a name="create-a-replica-set"></a>Erstellen einer Replikatgruppe

Wenn Sie eine verwaltete Domäne erstellen (beispielsweise *aaddscontoso.com*), wird eine anfängliche Replikatgruppe erstellt. Von weiteren Replikatgruppen werden der gleiche Namespace und die gleiche Konfiguration verwendet. Änderungen an Azure AD DS (einschließlich Konfiguration, Benutzeridentität und Anmeldeinformationen, Gruppen, Gruppenrichtlinienobjekte, Computerobjekte und andere Änderungen) werden mittels AD DS-Replikation auf alle Replikatgruppen in der verwalteten Domäne angewendet.

In diesem Tutorial wird eine zusätzliche Replikatgruppe in einer Azure-Region erstellt, die sich von der Region der anfänglichen Azure AD DS-Replikatgruppe unterscheidet.

Führen Sie die folgenden Schritte aus, um eine zusätzliche Replikatgruppe zu erstellen:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus.
1. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie auf der linken Seite **Replica sets (preview)** (Replikatgruppen (Vorschauversion)) aus. Jede verwaltete Domäne enthält eine anfängliche Replikatgruppe in der ausgewählten Region, wie im folgenden Beispielscreenshot zu sehen:

    ![Beispielscreenshot: Anzeigen und Hinzufügen einer Replikatgruppe im Azure-Portal](./media/tutorial-create-replica-set/replica-set-list.png)

    Wählen Sie zum Erstellen einer zusätzlichen Replikatgruppe die Option **+ Hinzufügen** aus.

1. Wählen Sie im Fenster *Add a replica set* (Replikatgruppe hinzufügen) die Zielregion aus (beispielsweise *USA, Osten*).

    Wählen Sie ein virtuelles Netzwerk in der Zielregion (beispielsweise *vnet-eastus*) und anschließend ein Subnetz (beispielsweise *aadds-subnet*) aus. Wählen Sie bei Bedarf **Neu erstellen** aus, um ein virtuelles Netzwerk in der Zielregion hinzuzufügen, und wählen Sie anschließend **Verwalten** aus, um ein Subnetz für Azure AD DS zu erstellen.

    Die Azure-VNET-Peerings zwischen dem virtuellen Netzwerk Ihrer vorhandenen verwalteten Domäne und dem virtuellen Zielnetzwerk werden automatisch erstellt, sofern sie noch nicht vorhanden sind.

    Der folgende Screenshot zeigt die Erstellung einer neuen Replikatgruppe in *USA, Osten*:

    ![Beispielscreenshot: Erstellen einer Replikatgruppe im Azure-Portal](./media/tutorial-create-replica-set/create-replica-set.png)

1. Wählen Sie **Speichern** aus, wenn Sie so weit sind.

Die Erstellung der Replikatgruppe bzw. die Erstellung der Ressourcen in der Zielregion dauert etwas. Die verwaltete Domäne selbst wird dann per AD DS-Replikation repliziert.

Während des Bereitstellungsvorgangs hat die Replikatgruppe den Status *Bereitstellung*, wie im folgenden Screenshot zu sehen. Nach Abschluss des Vorgangs wird für die Replikatgruppe der Status *Ausgeführt* angezeigt.

![Beispielscreenshot: Bereitstellungsstatus der Replikatgruppe im Azure-Portal](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Löschen einer Replikatgruppe

Eine verwaltete Domäne ist gegenwärtig auf vier Replikate beschränkt: die anfängliche Replikatgruppe und drei zusätzliche Replikatgruppen. Wenn Sie eine Replikatgruppe nicht mehr benötigen oder eine Replikatgruppe in einer anderen Region erstellen möchten, können Sie nicht benötigte Replikatgruppen löschen.

> [!IMPORTANT]
> Die letzte Replikatgruppe in einer verwalteten Domäne kann nicht gelöscht werden.

Führen Sie die folgenden Schritte aus, um eine Replikatgruppe zu löschen:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus.
1. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie auf der linken Seite **Replica sets (preview)** (Replikatgruppen (Vorschauversion)) aus. Wählen Sie in der Liste mit den Replikatgruppen das Kontextmenü ( **...** ) neben der Replikatgruppe aus, die Sie löschen möchten.
1. Wählen Sie im Kontextmenü die Option **Löschen** aus, und bestätigen Sie, dass Sie die Replikatgruppe löschen möchten.

> [!NOTE]
> Das Löschen einer Replikatgruppe kann zeitaufwendig sein.

Wenn Sie das virtuelle Netzwerk oder das Peering, das von der Replikatgruppe verwendet wird, nicht mehr benötigen, können Sie auch diese Ressourcen löschen. Vergewissern Sie sich vor dem Löschen, dass die Netzwerkverbindungen von keinen anderen Anwendungsressourcen in der anderen Region benötigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren des VNET-Peerings
> * Erstellen einer Replikatgruppe in einer anderen geografischen Region
> * Löschen einer Replikatgruppe

Machen Sie sich mit der Funktionsweise von Replikatgruppen in Azure AD DS vertraut, um weitere Konzeptinformationen zu erhalten.

> [!div class="nextstepaction"]
> [Replikatgruppen: Konzepte und Features für Azure Active Directory Domain Services (Vorschau)][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
