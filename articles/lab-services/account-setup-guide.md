---
title: Leitfaden für beschleunigte Lab-Konto-Einrichtung für Azure Lab Services
description: Dieser Leitfaden unterstützt Administratoren beim schnellen Einrichten eines Lab-Kontos für die Verwendung innerhalb ihrer Bildungseinrichtung.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659861"
---
# <a name="lab-account-setup-guide"></a>Einrichtungsleitfaden für Lab-Konten
Zum Einrichten der Azure Lab Services-Umgebung müssen Administratoren zunächst ein **Lab-Konto** innerhalb des Azure-Abonnements einrichten. Ein Lab-Konto ist ein Container für Ihre Labs, dessen Einrichtung nur wenige Minuten in Anspruch nimmt.

Dieser Leitfaden umfasst drei Abschnitte:
-  Im ersten Abschnitt liegt der Fokus auf den Voraussetzungen zum Einrichten eines Lab-Kontos, um die Sie sich *vorab* kümmern müssen.
-  Der zweite Abschnitt enthält eine Anleitung zum Planen der Einstellungen Ihres Lab-Kontos.
-  Im dritten Abschnitt wird ausführlich beschrieben, wie Sie ein Lab-Konto einrichten.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>Voraussetzungen zum Einrichten eines Lab-Kontos
In diesem Abschnitt werden die Voraussetzungen beschrieben, die Sie vor dem Einrichten eines Lab-Kontos erfüllen müssen.

### <a name="obtain-an-azure-subscription"></a>Abschließen eines Azure-Abonnements
Zum Erstellen eines Lab-Kontos benötigen Sie Zugriff auf ein Azure-Abonnement, das für Ihre Schule eingerichtet ist. Ihre Schule kann über ein oder mehrere Abonnements verfügen. Mit einem Abonnement verwalten Sie Abrechnung und Sicherheit für Ihre sämtlichen Azure-Ressourcen und -Dienste. Hierzu zählen auch Lab-Konten.  Azure-Abonnements werden in der Regel von Ihrer IT-Abteilung verwaltet.  Weitere Informationen finden Sie im folgenden Artikel:
 - [Administratorleitfaden: Abonnement](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>Schätzen der Anzahl der benötigten VMs und der benötigten VM-Größen
Sie müssen die Anzahl der virtuellen Computer (VMs) und die [VM-Größen](./administrator-guide.md#vm-sizing) schätzen, die von Ihrer Schule benötigt werden.  Im folgenden Blogbeitrag finden Sie eine Anleitung zum Strukturieren Ihrer Labs/Images.  In diesem Blogbeitrag erfahren Sie außerdem, wie Sie die erforderliche Anzahl von VMs und die erforderlichen VM-Größen festlegen:
- [Moving from a Physical Lab to Azure Lab Services (Von einem physischen Lab zu Azure Lab Services)](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Weitere Anleitungen zum Strukturieren von Labs finden Sie auch im folgenden Artikel:
- [Administratorleitfaden: Lab](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Grundlegendes zu VM-Einschränkungen und zur regionalen VM-Kapazität
Sobald Sie die Anzahl der benötigten VMs sowie die erforderlichen VM-Größen für Ihre Labs geschätzt haben, müssen Sie folgendermaßen fortfahren:

- Stellen Sie sicher, dass die Kapazitätseinschränkung Ihres Azure-Abonnements die von Ihnen für die Verwendung in Ihren Labs geplante Anzahl von VMs und VM-Größen zulässt.

- Erstellen Sie ein Lab-Konto innerhalb einer Region, in der ausreichend VM-Kapazität verfügbar ist.

Weitere Informationen finden Sie im folgenden Blogbeitrag: [VM Subscription Limits and Regional Capacity (VM-Abonnementeinschränkungen und regionale Kapazität)](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)

### <a name="decide-how-many-lab-accounts-to-create"></a>Festlegen der Anzahl zu erstellender Lab-Konten

Erstellen Sie ein einzelnes Lab-Konto innerhalb seiner eigenen Ressourcengruppe, um schnell loslegen zu können.  Später können Sie nach Bedarf weitere Lab-Konten (und Ressourcengruppen) erstellen. Beispielsweise bietet es sich möglicherweise an, ein Lab-Konto und eine Ressourcengruppe pro Abteilung zu erstellen, um so die Kosten klar zu trennen.  In den folgenden Artikeln finden Sie weitere Informationen zu Lab-Konten, Ressourcengruppen und zum Trennen von Kosten:
- [Administratorleitfaden: Ressourcengruppe](./administrator-guide.md#resource-group)
- [Administratorleitfaden: Lab-Konto](./administrator-guide.md#lab-account) 
- [Kostenverwaltung für Azure Lab Services](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>Planen der Einstellungen des Lab-Kontos

Sie sollten sich bei der Planung der Einstellungen des Lab-Kontos die folgenden Fragen stellen.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Wer sollte Besitzer und Mitwirkender für das Lab-Konto sein?

   Die IT-Administratoren Ihrer Schule sind in der Regel Besitzer und Mitwirkende eines Lab-Kontos. Sie sind für die Verwaltung der Richtlinien verantwortlich, die für alle Labs im Lab-Konto gelten. Die Person, die das Lab-Konto erstellt, ist automatisch Besitzer des Kontos. Sie können weitere Besitzer und Mitwirkende aus dem mit Ihrem Abonnement verknüpften AD-Mandanten (Azure Active Directory) hinzufügen. Weitere Informationen zu den Rollen Besitzer und Mitwirkender von Lab-Konten finden Sie unter folgendem Artikel:
   -  [Administratorleitfaden: Verwalten der Identität](./administrator-guide.md#manage-identity)

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   Lab-Benutzern wird innerhalb von Azure Lab Services nur eine einzelne Liste der virtuellen Computer angezeigt, auf die sie mandantenübergreifend Zugriff haben.

### <a name="who-will-be-allowed-to-create-labs"></a>Wer ist berechtigt, Labs zu erstellen?

   Sie können festlegen, dass Ihre IT-Mitarbeiter und Lehrkräfte Labs erstellen können. Wenn ein Benutzer ein Lab erstellt, wird ihm automatisch die Rolle des Lab-Besitzers zugewiesen.  Benutzer (in der Regel aus dem mit Ihrem Abonnement verknüpften Azure AD-Mandanten) müssen der Rolle „Ersteller des Labs“ im Lab-Konto zugewiesen werden, um Labs erstellen zu können.  Weitere Informationen zur Rolle „Ersteller des Labs“ finden Sie unter folgendem Artikel:
   -  [Administratorleitfaden: Verwalten der Identität](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Wer ist berechtigt, Labs zu besitzen und zu verwalten?

   Sie können auch festlegen, dass IT-Mitarbeiter und Lehrkräfte Labs besitzen bzw. verwalten können, *ohne* dass sie zum Erstellen von Labs berechtigt sind.  In diesem Fall wird Benutzern aus dem Azure AD-Mandanten Ihres Abonnements für vorhandene Labs entweder die Rolle Besitzer oder die Rolle Mitwirkender zugewiesen.  Weitere Informationen zu den Rollen Besitzer und Mitwirkender eines Lab-Kontos finden Sie unter folgendem Artikel:
   - [Administratorleitfaden: Verwalten der Identität](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>Möchten Sie Images speichern, die in mehreren Labs gemeinsam genutzt werden können?
Eine Shared Image Gallery ist ein Repository, das Sie zum Speichern und Freigeben von Images verwenden können. Wenn Klassen dasselbe Image verwenden müssen, können Lab-Ersteller das Image erstellen und dann in Shared Image Gallery exportieren.  Sobald ein Image in Shared Image Gallery exportiert wurde, kann es zum Erstellen neuer Labs verwendet werden.

Außerdem sollten Sie die Images in Ihrer physischen Umgebung erstellen und dann in Shared Image Gallery importieren.  Weitere Informationen zu diesem Prozess finden Sie im folgenden Blogbeitrag: 
- [Import Custom Image to Shared Image Gallery (Importieren eines benutzerdefinierten Images in Shared Image Gallery)](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Wenn Sie Shared Image Gallery verwenden möchten, müssen Sie eine Shared Image Gallery-Instanz erstellen oder eine vorhandene an Ihr Lab-Konto anfügen. Sie können diese Entscheidung auch erst zu einem späteren Zeitpunkt treffen, da Shared Image Gallery jederzeit an ein Lab-Konto angefügt werden kann.  Weitere Informationen zu Shared Image Gallery finden Sie in den folgenden Artikeln:
- [Administratorleitfaden: Shared Image Gallery](./administrator-guide.md#shared-image-gallery)
- [Administratorleitfaden: Preise für Shared Image Gallery](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Welche Images in Azure Marketplace werden von Ihren Labs verwendet?
Azure Marketplace bietet Hunderte von Images, die Sie aktivieren können, damit Lab-Ersteller das Image zum Erstellen des Labs verwenden können. Einige Images enthalten möglicherweise alles, was für ein Lab benötigt wird. In anderen Fällen können Sie ein Image als Ausgangspunkt verwenden, das der Ersteller des Labs durch die Installation zusätzlicher Anwendungen oder Tools anpassen kann.

Wenn Sie nicht wissen, welche Images Sie benötigen, können Sie diese auch später noch aktivieren. Sie können am besten feststellen, welche Images verfügbar sind, indem Sie zuerst ein Lab-Konto erstellen. Dadurch erhalten Sie Zugriff auf die Liste der verfügbaren Images und deren Inhalte und können diese prüfen.  Weitere Informationen zu Marketplace-Images finden Sie im folgenden Artikel:
- [Angeben von für Lab-Ersteller verfügbare Marketplace-Images](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Benötigen die virtuellen Computer des Labs Zugriff auf andere Azure- oder lokale Ressourcen?
Wenn Sie ein Lab-Konto einrichten, können Sie auch ein Peering mit einem virtuellen Netzwerk (VNET) durchführen.  Beachten Sie, dass sich das VNET und das Lab-Konto in derselben Region befinden müssen.  Berücksichtigen Sie beim Entscheiden, ob ein Peering mit einem VNET erforderlich ist, die folgenden Szenarios:

- **Zugriff auf einen Lizenzierungsserver**
  
   Wenn Sie Azure Marketplace-Images verwenden, werden die Kosten für die Betriebssystemlizenz in den Preisen für Lab-Dienste gebündelt. Jedoch müssen Sie keine Lizenzen für das Betriebssystem selbst bereitstellen. Bei Installation zusätzlicher Software und Anwendungen müssen Sie jedoch ggf. eine entsprechende Lizenz bereitstellen.  So greifen Sie auf einen Lizenzierungsserver zu:
   - Sie können sich dazu entscheiden, eine Verbindung mit einem lokalen Lizenzierungsserver herzustellen.  Zum Herstellen einer Verbindung mit einem lokalen Lizenzierungsserver ist eine zusätzliche Einrichtung erforderlich.
   - Eine weitere Option, die schneller eingerichtet werden kann, ist das Erstellen eines Lizenzierungsservers, den Sie auf einer Azure-VM hosten.  Die Azure-VM befindet sich in einem virtuellen Netzwerk, das Sie mittels Peering mit Ihrem Lab-Konto verknüpfen.

- **Zugriff auf andere lokale Ressourcen wie eine Dateifreigabe oder eine Datenbank**

   Sie erstellen ein VNET, um den Zugriff auf lokale Ressourcen zu ermöglichen. In der Regel verwenden Sie hierfür ein virtuelles Site-to-Site-Netzwerkgateway. Das Einrichten dieser Art von Umgebung nimmt zusätzliche Zeit in Anspruch.

- **Zugriff auf andere Azure-Ressourcen außerhalb eines VNET**

   Wenn Sie Zugriff auf Azure-Ressourcen benötigen, die *nicht* innerhalb eines VNET gesichert sind, können Sie über das öffentliche Internet ohne Peering auf diese Ressourcen zugreifen.

Weitere Informationen zu virtuellen Netzwerken finden Sie in den folgenden Artikeln:
- [Grundlegendes zur Architektur: Virtuelles Netzwerk](./classroom-labs-fundamentals.md#virtual-network)
- [Herstellen einer Verbindung mit einem virtuellen Netzwerk](./how-to-connect-peer-virtual-network.md)
- [Erstellen eines Labs mit einer freigegebenen Ressource in Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Einrichten Ihres Lab-Kontos

Nachdem Sie die Planung abgeschlossen haben, können Sie Ihr Lab-Konto einrichten.  Die gleichen Schritte müssen auch beim Einrichten eines Labs durchgeführt werden: [Azure Lab Services mit Teams](./lab-services-within-teams-overview.md).

1. **Erstellen Ihres Lab-Kontos** Im Tutorial zum [Erstellen eines Lab-Kontos](./tutorial-setup-lab-account.md#create-a-lab-account) finden Sie weitere Anleitungen.
   
    Weitere Anleitungen zu Benennungen finden Sie im folgenden Artikel:

   - [Benennungsrichtlinien für Ressourcen](./administrator-guide.md#naming)

2. **Hinzufügen von Benutzern zur Rolle „Lab-Ersteller“** . Anweisungen hierzu finden Sie unter [Hinzufügen von Benutzern zur Rolle „Ersteller des Labs“](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


3. **Herstellen einer Verbindung mit einem virtuellen Peernetzwerk** Eine Anleitung finden Sie unter [Verbinden des Netzwerks Ihres Labs mit einem virtuellen Peernetzwerk](./how-to-connect-peer-virtual-network.md).

   Möglicherweise benötigen Sie auch die Anweisungen unter [Konfigurieren des Adressbereichs der Lab-VMs](./how-to-configure-lab-accounts.md).

4. **Aktivieren und Überprüfen von Images**. Eine Anleitung finden Sie unter [Aktivieren von Azure Marketplace-Images für Lab-Ersteller](./specify-marketplace-images.md).

   Um die Inhalte der einzelnen Azure Marketplace-Images zu überprüfen, wählen Sie den Imagenamen aus. Im folgenden Screenshot werden beispielsweise die Details für das Image „Ubuntu Data Science VM“ gezeigt:

   ![Screenshot zum Überprüfen von Azure Marketplace-Images](./media/setup-guide/review-marketplace-images.png)

   Wenn Sie eine Shared Image Gallery-Instanz an Ihr Lab-Konto angefügt haben und benutzerdefinierte Images für Lab-Ersteller freigeben möchten, müssen Sie ähnliche Schritte wie im folgenden Screenshot gezeigt durchführen:

   ![Screenshot zum Aktivieren benutzerdefinierter Images in einer Shared Image Gallery](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Nächste Schritte

Nächste allgemeine Schritte zum Einrichten einer Lab-Umgebung:

- [Verwalten von Lab-Konten](how-to-manage-lab-accounts.md)
- [Leitfaden für die Lab-Einrichtung](setup-guide.md)