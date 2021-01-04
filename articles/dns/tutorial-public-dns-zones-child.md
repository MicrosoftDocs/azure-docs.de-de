---
title: 'Tutorial: Erstellen einer untergeordneten Azure DNS-Zone'
titleSuffix: Azure DNS
description: Tutorial zum Erstellen untergeordneter DNS-Zonen im Azure-Portal.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: d805fb926131e047572887758b10506e4edd5944
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183378"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Tutorial: Erstellen einer neuen untergeordneten DNS-Zone

In diesem Tutorial lernen Sie Folgendes: 

> [!div class="checklist"]
> * Anmelden beim Azure-Portal
> * Erstellen einer untergeordneten DNS-Zone über eine neue DNS-Zone
> * Erstellen einer untergeordneten DNS-Zone über eine übergeordnete DNS-Zone
> * Überprüfen der Namenserverdelegierung für die neue untergeordnete DNS-Zone



## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement.  Wenn Sie noch kein Konto haben, können Sie [ein Konto kostenlos erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Vorhandene übergeordnete Azure DNS-Zone  

In diesem Tutorial verwenden wir contoso.com als übergeordnete Zone und subdomain.contoso.com als Namen der untergeordneten Domäne.  Ersetzen Sie *contoso.com* durch den Namen Ihrer übergeordneten Domäne und *subdomain* durch Ihre untergeordnete Domäne.  Wenn Sie Ihre übergeordnete DNS-Zone nicht erstellt haben, lesen Sie die Schritte zum [Erstellen einer DNS-Zone im Azure-Portal](./dns-getstarted-portal.md#create-a-dns-zone). 


## <a name="sign-in-to-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.
Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen.

Es gibt zwei Möglichkeiten, wie Sie Ihre untergeordnete DNS-Zone erstellen können:
1.  Über die Portalseite „DNS-Zone erstellen“
1.  Über die Konfigurationsseite der übergeordneten DNS-Zone


## <a name="create-child-dns-zone-via-create-dns-zone"></a>Erstellen einer untergeordneten DNS-Zone auf der Seite „DNS-Zone erstellen“

In diesem Schritt erstellen wir eine neue untergeordnete DNS-Zone mit dem Namen **subdomain.contoso.com** und delegieren sie an die vorhandene übergeordnete DNS-Zone **contoso.com**. Sie erstellen die DNS-Zone mithilfe der Registerkarten auf der Seite **DNS-Zone erstellen**.
1.  Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus. Das Fenster **Neu** wird angezeigt.
1.  Wählen Sie **Netzwerk**, dann **DNS-Zone** und anschließend die Schaltfläche **Hinzufügen** aus.

1.  Geben Sie auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:
    * **Abonnement**: Wählen Sie ein Abonnement aus, in dem Sie die Zone erstellen möchten.
    * **Ressourcengruppe**: Geben Sie Ihre vorhandene Ressourcengruppe ein. Sie können auch eine neue Ressourcengruppe erstellen, indem Sie **Neu erstellen** auswählen, *Meine Ressourcengruppe* eingeben und **OK** auswählen. Der Name der Ressourcengruppe muss innerhalb des Azure-Abonnements eindeutig sein.
    * Aktivieren Sie dieses Kontrollkästchen: **Diese Zone ist ein untergeordnetes Element einer vorhandenen Zone, die bereits in Azure DNS gehostet wird**
    * **Abonnement für übergeordnete Zone**: Suchen Sie in dieser Dropdownliste den Abonnementnamen, unter dem die übergeordnete DNS-Zone *contoso.com* erstellt wurde, und/oder wählen Sie ihn aus.
    * **Übergeordnete Zone**: Geben Sie in die Suchleiste *contoso.com* ein, um sie in die Dropdownliste zu laden. Wählen Sie nach dem Laden in der Dropdownliste *contoso.com* aus.
    * **Name:** Geben Sie für dieses Tutorialbeispiel *subdomain* ein. Beachten Sie, dass der Name Ihrer übergeordneten DNS-Zone *contoso.com* automatisch als Suffix zum Namen hinzugefügt wird, wenn wir die übergeordnete Zone im obigen Schritt auswählen.

1. Klicken Sie auf **Weiter: Überprüfen + erstellen**.
1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die Zusammenfassung, beheben Sie alle Validierungsfehler, und wählen Sie dann **Erstellen** aus.
Die Erstellung der Zone kann einige Minuten dauern.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Screenshot der Seite „DNS-Zone erstellen“." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Erstellen einer untergeordneten DNS-Zone auf der Seite „Übersicht“ der übergeordneten DNS-Zone
Sie können auch eine neue untergeordnete DNS-Zone erstellen und sie an die übergeordnete DNS-Zone delegieren, indem Sie auf der Seite „Übersicht“ der übergeordnete Zonen die Schaltfläche **Untergeordnete Zone** auswählen. Über diese Schaltfläche werden die Parameter der übergeordneten Zone für die untergeordnete Zone automatisch vorausgefüllt. 

1.  Öffnen Sie im Azure-Portal unter **Alle Ressourcen** in der Ressourcengruppe **MyResourceGroup** die DNS-Zone *contoso.com*. Sie können *contoso.com* im Feld **Nach Name filtern** eingeben, damit Sie sie leichter finden.
1.  Wählen Sie auf der Seite „Übersicht“ der DNS-Zone die Schaltfläche **+ Untergeordnete Zone** aus.

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Screenshot der Schaltfläche „Untergeordnete Zone“" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Daraufhin wird die Seite „DNS-Zone erstellen“ geöffnet. Die Option „Untergeordnete Zone“ ist bereits aktiviert. Das Abonnement für übergeordnete Zone und die übergeordnete Zone sind auf dieser Seite bereits für Sie ausgefüllt.
1.  Geben Sie für dieses Tutorialbeispiel *child* als Namen ein. Beachten Sie, dass der Name Ihrer übergeordneten DNS-Zone contoso.com dem Namen automatisch als Präfix hinzugefügt wird.
1.  Klicken Sie auf **Weiter: Tags** und dann auf **Weiter: Überprüfen + erstellen**.
1.  Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die Zusammenfassung, beheben Sie alle Validierungsfehler, und wählen Sie dann **Erstellen** aus.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Screenshot der ausgewählten untergeordneten Zone" border="true"  lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::
## <a name="verify-child-dns-zone"></a>Überprüfen der untergeordneten DNS-Zone
Sie haben die neue untergeordnete DNS-Zone *subdomain.contoso.com* eingerichtet. Um zu überprüfen, ob die Delegierung ordnungsgemäß erfolgt ist, sollten Sie die Namenservereinträge (NS) für Ihre untergeordnete Zone in der übergeordneten Zone überprüfen, wie unten beschrieben.  

**Abrufen der Namenserver der untergeordneten DNS-Zone:**

1.  Öffnen Sie im Azure-Portal unter **Alle Ressourcen** in der Ressourcengruppe **MyResourceGroup** die DNS-Zone *subdomain.contoso.com*. Sie können *subdomain.contoso.com* im Feld **Nach Name filtern** eingeben, damit Sie sie leichter finden.
1.  Rufen Sie die Namenserver über die Seite „Übersicht“ für die DNS-Zone ab. In diesem Beispiel wurden der Zone contoso.com die Namenserver *ns1-08.azure-dns.com, ns2-08.azure-dns.net, ns3-08.azure-dns.org* und *ns4-08.azure-dns.info* zugewiesen:

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Screenshot der Namenserver der untergeordneten Zone" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Überprüfen Sie den NS-Eintrag in der übergeordneten DNS-Zone:**

In diesem Schritt wechseln wir nun zur übergeordneten DNS-Zone *contoso.com* und überprüfen, ob ihr NS-Eintrag für die Namenserver der untergeordneten Zonen erstellt wurde.

1. Öffnen Sie im Azure-Portal unter **Alle Ressourcen** in der Ressourcengruppe **MyResourceGroup** die DNS-Zone contoso.com. Sie können contoso.com im Feld **Nach Namen filtern** eingeben, damit Sie sie leichter finden.
1.  Suchen Sie auf der Seite Übersicht für die zu *contoso.com* gehörigen DNS-Zonen nach den Einträgen.
1.  Sie werden feststellen, dass ein Eintrag mit dem Typ „NS“ und Namen „subdomain“ bereits in der übergeordneten DNS-Zone erstellt wurde. Überprüfen Sie die Werte für diesen dahingehend, ob sie der Namenserverliste ähnlich sind, die wir im obigen Schritt aus der untergeordneten DNS-Zone abgerufen haben.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Screenshot der Überprüfung der Namenserver der untergeordneten Zone" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die in diesem Tutorial erstellten Ressourcen nicht mehr benötigen, können Sie sie entfernen, indem Sie die Ressourcengruppe **MyResourceGroup** löschen. Öffnen Sie die Ressourcengruppe **MyResourceGroup**, und wählen Sie **Ressourcengruppe löschen** aus.



## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Azure DNS Private Zones-Szenarien](private-dns-scenarios.md)