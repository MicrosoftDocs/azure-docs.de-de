---
title: Azure Lab Services – Administratorhandbuch | Microsoft-Dokumentation
description: Dieses Handbuch hilft Administratoren, die Lab-Konten mit Azure Lab Services erstellen und verwalten.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 3ad3ee38a6c08a6af85822d76012cc6dfc34ff4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462471"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – Administratorhandbuch
IT-Administratoren, die die Cloudressourcen einer Universität verwalten, sind in der Regel auch dafür verantwortlich, das Lab-Konto für diese Universität einzurichten. Nachdem sie ein Lab-Konto eingerichtet haben, erstellen Administratoren oder Dozenten Labs, die im Lab-Konto enthalten sind. Dieser Artikel bietet eine allgemeine Übersicht über die beteiligten Azure-Ressourcen und die Anleitungen zu deren Erstellung.

![Diagramm einer allgemeinen Ansicht über Azure-Ressourcen in einem Lab-Konto.](./media/administrator-guide/high-level-view.png)

- Labs werden innerhalb eines Azure-Abonnements gehostet, das sich im Besitz von Azure Lab Services befindet.
- Lab-Konten, eine Shared Image Gallery und Imageversionen werden innerhalb Ihres Abonnements gehostet.
- Ihr Lab-Konto und die Shared Image Gallery können in derselben Ressourcengruppe sein. In diesem Diagramm befinden Sie sich in verschiedenen Ressourcengruppen.

Weitere Informationen zur Architektur finden Sie unter [Grundlegendes zur Architektur in Azure Lab Services](./classroom-labs-fundamentals.md).

## <a name="subscription"></a>Subscription
Ihre Universität besitzt vielleicht mindestens ein Azure-Abonnement. Sie verwenden Abonnements, um Abrechnung und Sicherheit für alle darin verwendeten Azure-Ressourcen und -Dienste, einschließlich Lab-Konten, zu verwalten.

Die Beziehung zwischen einem Lab-Konto und seinem Abonnement ist aus folgenden Gründen wichtig:

- Die Abrechnung wird über das Abonnement gemeldet, das das Lab-Konto enthält.
- Sie können Benutzern im Azure AD-Mandanten (Azure Active Directory) des Abonnements Zugriff auf Azure Lab Services gewähren. Sie können einen Benutzer als Besitzer oder Mitwirkenden eines Lab-Kontos, als Lab-Ersteller oder Lab-Besitzer hinzufügen.

Labs und ihre VMs werden für Sie in einem Abonnement gehostet und verwaltet, das sich im Besitz von Azure Lab Services befindet.

## <a name="resource-group"></a>Resource group
Ein Abonnement enthält mindestens eine Ressourcengruppe. Ressourcengruppen werden verwendet, um logische Gruppierungen von Azure-Ressourcen zu erstellen, die innerhalb der selben Lösung verwendet werden.  

Wenn Sie ein Lab-Konto erstellen, müssen Sie die Ressourcengruppe konfigurieren, die das Lab-Konto enthält. 

Eine Ressourcengruppe ist auch erforderlich, wenn Sie eine [Shared Image Gallery](#shared-image-gallery) erstellen. Sie können Ihr Lab-Konto und Ihre Shared Image Gallery in derselben Ressourcengruppe oder in zwei separaten Ressourcengruppen platzieren. Sie sollten diesen zweiten Ansatz verwenden, wenn Sie planen, die Image Gallery auf verschiedene Lösungen zu verteilen.

Wenn Sie ein Lab-Konto erstellen, können Sie automatisch gleichzeitig eine Shared Image Gallery erstellen und anfügen.  Diese Option führt dazu, dass das Lab-Konto und die freigegebene Shared Image Gallery in separaten Ressourcengruppen erstellt werden. Dieses Verhalten tritt auf, wenn Sie die Schritte befolgen, die im Tutorial [Konfigurieren eines Katalogs mit freigegebenen Images zum Zeitpunkt der Laborkontoerstellung](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) beschrieben werden. Das Image zu Beginn dieses Artikels verwendet diese Konfiguration. 

Sie sollten im Voraus Zeit in die Planung der Struktur Ihrer Ressourcengruppen zu investieren, da es nach dem Erstellen einer Ressourcengruppe *nicht* mehr möglich ist, die Ressourcengruppe eines Lab-Kontos oder der Shared Image Gallery zu ändern. Wenn Sie die Ressourcengruppe für diese Ressourcen ändern müssen, müssen Sie Ihr Lab-Konto oder die Shared Image Gallery löschen und neu erstellen.

## <a name="lab-account"></a>Lab-Konto

Ein Lab-Konto dient als Container für mindestens ein Lab. Beim Einstieg in Azure Lab Services ist es üblich, nur ein einzelnes Lab-Konto zu haben. Wenn sich Ihre Lab-Nutzung steigert, können Sie später immer noch weitere Lab-Konten erstellen.

In der folgenden Liste werden Szenarien hervorgehoben, in denen mehrere Lab-Konten von Vorteil sein könnten:

- **Verwalten verschiedener Richtlinienanforderungen über Labs hinweg**

    Wenn Sie ein Lab-Konto einrichten, legen Sie Richtlinien fest, die für *alle* Labs unter dem Lab-Konto gelten, etwa:
    - Das virtuelle Azure-Netzwerk mit freigegebenen Ressourcen, auf die das Lab zugreifen kann. Beispielsweise könnten Sie über eine Reihe von Labs verfügen, die Zugriff auf ein freigegebenes Dataset in einem virtuellen Netzwerk benötigen.
    - Die VM-Images, die von den Labs zum Erstellen virtueller Computer verwendet werden können. Beispielsweise könnten Sie über eine Reihe von Labs verfügen, die Zugriff auf das Azure Marketplace-Image der [Data Science VM für Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) benötigen.

    Wenn für jedes Ihrer Labs besondere Richtlinienanforderungen gelten, könnte es vorteilhaft sein, separate Lab-Konten für die separate Verwaltung der einzelnen Labs zu erstellen.

- **Zuweisen eines separaten Budgets zu jedem Lab-Konto**
  
    Anstatt alle Kosten für ein einzelnes Lab-Konto melden zu lassen, benötigen Sie möglicherweise ein eindeutiger aufgeteiltes Budget. Sie können z. B. separate Lab-Konten für den mathematischen Fachbereich, den Informatikfachbereich usw. Ihrer Universität erstellen, um das Budget Fachbereiche übergreifend aufzuteilen.  Mithilfe von [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) können Sie dann die Kosten für jedes einzelne Lab-Konto anzeigen.

- **Isolieren von Pilot-Labs von aktiven oder Produktions-Labs**
  
    Möglicherweise gibt es Fälle, in denen Sie Richtlinienänderungen als Pilot in ein Lab-Konto übertragen möchten, ohne dass sich diese potenziell auf aktive oder Produktions-Labs auswirken. In dieser Art von Szenario ermöglicht Ihnen das Erstellen eines separaten Lab-Kontos für Pilotzwecke, Änderungen zu isolieren. 

## <a name="lab"></a>Labor

Ein Lab enthält VMs, die jeweils einem einzelnen Kursteilnehmer zugewiesen sind.  Generell können Sie von Folgendem ausgehen:

- Vorhandensein eines Labs für jeden Kurs.
- Erstellen Sie einen neuen Satz von Labs für jedes Semester, Quartal oder akademische System, das Sie verwenden. Für Kurse, die dasselbe Image verwenden müssen, sollten Sie eine [Shared Image Gallery](#shared-image-gallery) verwenden. Auf diese Weise können Sie Images Labs und akademische Zeiträume übergreifend wiederverwenden.

Wenn Sie ermitteln, wie Sie Ihre Labs strukturieren möchten, beachten Sie die folgenden Punkte:

- **Alle VMs innerhalb eines Labs werden mit demselben veröffentlichten Image bereitgestellt**.

    Daraus folgt, dass Sie für jedes Image ein separates Lab erstellen müssen, wenn Sie einen Kurs anbieten, für den verschiedene Lab-Images gleichzeitig veröffentlicht werden müssen.
  
- **Das Nutzungskontingent wird auf Lab-Ebene festgelegt und gilt für alle Benutzer innerhalb des Labs**

    Um unterschiedliche Kontingente für Benutzer festzulegen, müssen Sie gesonderte Labs erstellen. Es ist jedoch möglich, auch nach dem Festlegen des Kontingents bestimmten Benutzern noch weitere Stunden hinzuzufügen.
  
- **Der Zeitplan für das Starten oder Herunterfahren wird auf Lab-Ebene festgelegt und gilt für alle virtuellen Computer innerhalb des Labs**.

    Ähnlich wie bei der Kontingenteinstellung müssen Sie, wenn Sie unterschiedliche Zeitpläne für Benutzer festlegen müssen, für jeden Zeitplan gesonderte Labs erstellen.

Standardmäßig verfügt jedes Lab über ein eigenes virtuelles Netzwerk.  Wenn Peering virtueller Netzwerke aktiviert ist, verfügt jedes Lab über ein eigenes Subnetz, das mit dem angegebenen virtuellen Netzwerk über Peering verbunden ist.

## <a name="shared-image-gallery"></a>Shared Image Gallery

Eine Shared Image Gallery einem Lab-Konto angefügt und dient als zentrales Repository zum Speichern von Images. Ein Image wird im Katalog gespeichert, wenn sich ein Dozent entschließt, es von der Vorlagen-VM eines Labs aus zu exportieren. Jedes Mal, wenn der Dozent Änderungen an der Vorlagen-VM vornimmt und sie exportiert, werden neue Versionen des Images gespeichert, während die vorherigen Versionen erhalten bleiben.

Kursleiter können eine Imageversion aus der Shared Image Gallery veröffentlichen, wenn sie ein neues Lab erstellen. Zwar speichert der Katalog mehrere Versionen eines Images, Dozenten können während der Erstellung eines Labs jedoch nur die letzte Version auswählen.

Die Shared Image Gallery ist eine optionale Ressource, die Sie möglicherweise nicht sofort benötigen, wenn Sie mit nur wenigen Labs beginnen. Die Verwendung der Shared Image Gallery bietet jedoch viele Vorteile, die hilfreich sind, wenn Sie auf zusätzliche Labs erweitern:

- **Sie können Versionen eines Vorlagen-VM-Images speichern und verwalten**

    Dies ist hilfreich, um ein benutzerdefiniertes Image zu erstellen oder Änderungen (Software, Konfiguration usw.) an einem Image aus dem öffentlichen Azure Marketplace-Katalog vorzunehmen.  Beispielsweise ist es für Dozenten üblich, dass für sie unterschiedliche Software oder Tools installiert werden müssen. Statt von Studenten zu fordern, dass sie diese Voraussetzungen selbst installieren, können verschiedene Versionen des Vorlagen-VM-Images in eine Shared Image Gallery exportiert werden. Diese Imageversionen können Sie dann verwenden, wenn Sie neue Labs erstellen.

- **Sie können Vorlagen-VM-Images Labs übergreifend freigeben und wiederverwenden**

    Sie können ein Image speichern und wiederverwenden, sodass Sie es nicht jedes Mal neu konfigurieren müssen, wenn Sie ein neues Lab erstellen. Wenn z. B. für mehrere Kurse dasselbe Image verwendet werden muss, können Sie dieses Image ein Mal erstellen und in die Shared Image Gallery exportieren, damit es von Labs gemeinsam verwendet werden kann.

- **Die Bildverfügbarkeit wird durch die automatische Replikation sichergestellt**

    Wenn Sie ein Image aus einem Lab in der Shared Image Gallery speichern, wird es automatisch in andere [Regionen innerhalb desselben geografischen Raums](https://azure.microsoft.com/global-infrastructure/regions/) repliziert. Wenn es in einer Region zu einem Ausfall kommt, ist das Veröffentlichen des Image in Ihrem Lab davon nicht betroffen, weil ein Imagereplikat aus einer anderen Region verwendet werden kann.  Das Veröffentlichen von VMs aus mehreren Replikaten kann auch die Leistung verbessern.

Um freigegebene Images logisch zu gruppieren, können Sie eine der folgenden Aktionen ausführen:

- Erstellen mehrerer Shared Image Gallerys. Jedes Lab-Konto kann nur mit einer Shared Image Gallery eine Verbindung herstellen, sodass Sie für diese Option auch mehrere Lab-Konten erstellen müssen.
- Verwenden Sie eine einzelne Shared Image Gallery, die von mehreren Lab-Konten gemeinsam genutzt wird. In diesem Fall kann jedes Lab-Konto nur Images aktivieren, die für die darin enthaltenen Labs anwendbar sind.

## <a name="naming"></a>Benennung

Beim Einstieg in Azure Lab Services wird empfohlen, dass Sie Benennungskonventionen für Ressourcengruppen, Lab-Konten, Labs und die Shared Image Gallery einrichten. Die Benennungskonventionen, die Sie einrichten, gelten ausschließlich für die Anforderungen Ihrer Organisation, doch die folgende Tabelle enthält einige allgemeine Richtlinien:

| Ressourcentyp | Role | Vorgeschlagenes Muster | Beispiele |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Enthält mindestens ein Lab-Konto und mindestens eine Shared Image Gallery. | \<organization short name\>-\<environment\>-rg<ul><li>**Kurzname der Organisation** identifiziert den Namen der Organisation, die von der Ressourcengruppe unterstützt wird.</li><li>**Umgebung** identifiziert die Umgebung für die Ressource, z. B. *Pilot* oder *Produktion*.</li><li>**rg** steht für den Ressourcentyp *Ressourcengruppe*.</li></ul> | contosouniversitätlabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitätlabs-prod-rg |
| Lab-Konto | Enthält mindestens ein Lab. | \<organization short name\>-\<environment\>-la<ul><li>**Kurzname der Organisation** identifiziert den Namen der Organisation, die von der Ressourcengruppe unterstützt wird.</li><li>**Umgebung** identifiziert die Umgebung für die Ressource, z. B. *Pilot* oder *Produktion*.</li><li>**la** steht für den Ressourcentyp *Lab-Konto*.</li></ul> | contosouniversitätlabs-lk<br/>matheabtlabs-lk<br/>sciencedeptlabs-pilot-la<br/>naturwissenschaftabtlabs-prod-lk |
| Labor | Enthält mindestens eine VM. |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Kursname** identifiziert den Namen des Kurses, der von dem Lab unterstützt wird.</li><li>**Zeitrahmen** identifiziert den Zeitrahmen, in dem der Kurs angeboten wird.</li>**Dozentenbezeichner** identifiziert den Dozenten, der Besitzer des Labs ist.</li></ul> | CS1234-Herbst2019-KatrinKöhler<br/>CS1234-Frühling2019-KatrinKöhler |
| Shared Image Gallery | Enthält mindestens eine VM-Imageversion. | \<organization short name\>Katalog | contosouniversitätlabskatalog |

Weitere Informationen zur Benennung anderer Azure-Ressourcen finden Sie unter [Namenskonventionen für Azure-Ressourcen](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regionen/Standorte

Beim Einrichten Ihrer Azure Lab Services-Ressourcen müssen Sie eine Region oder einen Standort des Rechenzentrums angeben, in dem die Ressourcen gehostet werden sollen. In den nächsten Abschnitten wird beschrieben, wie sich eine Region oder ein Standort auf die einzelnen Ressourcen auswirken kann, die mit dem Einrichten eines Labs verbunden sind.

### <a name="resource-group"></a>Resource group

Die Region gibt das Rechenzentrum an, in dem Informationen über die Ressourcengruppe gespeichert werden. In der Ressourcengruppe enthaltene Azure-Ressourcen können sich in anderen Regionen befinden als ihre übergeordneten Ressourcen.

### <a name="lab-account"></a>Lab-Konto

Der Speicherort des Lab-Kontos zeigt die Region an, in der die Ressource vorhanden ist.  

### <a name="lab"></a>Labor

Der Speicherort, an dem ein Lab vorhanden ist, hängt von folgenden Faktoren ab:

  - **Peering des Lab-Kontos mit einem virtuellen Netzwerk**
  
    Sie können ein [Peering des Lab-Kontos mit einem virtuellen Netzwerk](./how-to-connect-peer-virtual-network.md) durchführen, wenn sie sich in derselben Region befinden.  Wenn für ein Lab-Konto Peering mit einem virtuellen Netzwerk erfolgt, werden Labs automatisch in der gleichen Region erstellt, in der sich das Lab-Konto und das virtuelle Netzwerk befinden.

    > [!NOTE]
    > Wenn für ein Lab-Konto Peering mit einem virtuellen Netzwerk erfolgt, ist die Einstellung **Auswahl des Lab-Speicherorts durch Lab-Ersteller zulassen** deaktiviert. Weitere Informationen finden Sie unter [Zulassen der Auswahl des Labstandorts durch den Lab-Ersteller](./allow-lab-creator-pick-lab-location.md).
    
  - **Kein Peering mit virtuellem Netzwerk *und* Lab-Ersteller dürfen den Lab-Speicherort nicht auswählen**
  
    Wenn *kein* Peering eines virtuellen Netzwerks mit dem Lab-Konto erfolgt und [Lab-Ersteller *nicht* den Lab-Speicherort auswählen dürfen](./allow-lab-creator-pick-lab-location.md), werden Labs automatisch in einer Region erstellt, die über verfügbare VM-Kapazität verfügt.  Azure Lab Services sucht insbesondere in [Regionen, die sich im gleichen geografischen Raum wie das Lab-Konto befinden](https://azure.microsoft.com/global-infrastructure/regions), nach Verfügbarkeit.

  - **Kein Peering mit virtuellem Netzwerk *und* Lab-Ersteller dürfen den Lab-Speicherort auswählen**
       
    Wenn *kein* Peering mit dem VNET erfolgt und [Lab-Ersteller den Lab-Speicherort auswählen *dürfen*](./allow-lab-creator-pick-lab-location.md), hängen die Speicherorte, die vom Lab-Ersteller ausgewählt werden können, von der verfügbaren Kapazität ab.

> [!NOTE]
> Um sicherzustellen, dass eine Region ausreichend VM-Kapazität aufweist, müssen Sie zunächst die Kapazität über das Lab-Konto anfordern, wenn Sie das Lab erstellen.

Eine allgemeine Regel ist, für eine Ressource eine Region festzulegen, die ihren Benutzern am nächsten liegt. Für Labs bedeutet dies, das Lab in maximaler Nähe zu Ihren Kursteilnehmern zu erstellen. Für Onlinekurse, bei denen die Kursteilnehmer weltweit verteilt sind, erstellen Sie nach Ihrem Ermessen ein Lab, das zentral angesiedelt ist. Alternativ können Sie einen Kurs basierend auf der jeweiligen Region Ihrer Kursteilnehmer auch in mehrere Labs aufteilen.

## <a name="vm-sizing"></a>Festlegen der VM-Größe

Wenn Administratoren oder Ersteller von Labs ein Lab erstellen, können sie basierend auf den Anforderungen für Ihren Kurs unter verschiedenen VM-Größen auswählen. Denken Sie daran, dass die Computegrößen, die verfügbar sind, von der Region abhängen, in der sich Ihr Lab-Konto befindet.

| Size | Spezifikationen | Reihen | Vorgeschlagene Verwendung |
| ---- | ----- | ------ | ------------- |
| Klein| <ul><li>2&nbsp;Kerne</li><li>3,5 Gigabyte (GB) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Am besten geeignet für die Befehlszeile, das Öffnen von Webbrowsern, Webserver mit geringem Datenverkehr und kleine bis mittelgroße Datenbanken. |
| Medium | <ul><li>4&nbsp;Kerne</li><li>7&nbsp;GB&nbsp;RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Am besten geeignet für relationale Datenbanken, speicherinternes Zwischenspeichern und Analysen. |
| Mittel (geschachtelte Virtualisierung) | <ul><li>4&nbsp;Kerne</li><li>16&nbsp;GB&nbsp;RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Am besten geeignet für relationale Datenbanken, speicherinternes Zwischenspeichern und Analysen.
| Groß | <ul><li>8&nbsp;Kerne</li><li>16&nbsp;GB&nbsp;RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Eignet sich am besten für Anwendungen, die schnellere CPUs, eine höhere lokale Datenträgerleistung, große Datenbanken und Caches erfordern.  Sie unterstützt auch die geschachtelte Virtualisierung. |
| Groß (geschachtelte Virtualisierung) | <ul><li>8&nbsp;Kerne</li><li>32&nbsp;GB&nbsp;RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Eignet sich am besten für Anwendungen, die schnellere CPUs, eine höhere lokale Datenträgerleistung, große Datenbanken und Caches erfordern. |
| Kleine GPU (Visualisierung) | <ul><li>6&nbsp;Kerne</li><li>56&nbsp;GB&nbsp;RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | Eignet sich am besten für Remotevisualisierung, Streaming, Gaming und die Codierung mit Frameworks wie beispielsweise OpenGL und DirectX. |
| Kleine GPU (Compute) | <ul><li>6&nbsp;Kerne</li><li>56&nbsp;GB&nbsp;RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |Eignet sich am besten für rechenintensive Anwendungen wie KI und Deep Learning. |
| Mittlere GPU (Visualisierung) | <ul><li>12&nbsp;Kerne</li><li>112&nbsp;GB&nbsp;RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Eignet sich am besten für Remotevisualisierung, Streaming, Gaming und die Codierung mit Frameworks wie beispielsweise OpenGL und DirectX. |

## <a name="manage-identity"></a>Verwalten der Identität

Mithilfe der [rollenbasierten Zugriffssteuerung von Azure (Azure RBAC)](../role-based-access-control/overview.md) können die folgenden Rollen zugewiesen werden, um Zugriff auf Lab-Konten und Labs zu gestatten:

- **Besitzer** des Lab-Kontos

    Einem Administrator, der ein Lab-Konto erstellt, wird automatisch die Rolle „Besitzer“ des Lab-Kontos zugewiesen. Die Rolle „Besitzer“ kann:
     - Die Einstellungen des Lab-Kontos ändern.
     - Anderen Administratoren als Besitzer oder Mitwirkende Zugriff auf das Lab-Konto gestatten.
     - Dozenten als Ersteller, Besitzer oder Mitwirkende Zugriff auf Labs gestatten.
     - Alle Labs im Lab-Konto erstellen und verwalten.

- **Mitwirkender** des Lab-Kontos

    Ein Administrator, dem die Rolle „Mitwirkender“ zugewiesen wurde, kann folgende Aktionen ausführen:
    - Die Einstellungen des Lab-Kontos ändern.
    - Alle Labs im Lab-Konto erstellen und verwalten.

    Der Mitwirkende kann anderen Benutzern jedoch *nicht* Zugriff auf Lab-Konten oder Labs gestatten.

- **Lab-Ersteller**

    Zum Erstellen von Labs in einem Lab-Konto muss eine Lehrkraft Mitglied der Rolle Lab-Ersteller sein.  Ein Dozent, der ein Lab erstellt, wird automatisch als Lab-Besitzer hinzugefügt. Weitere Informationen finden Sie unter [Hinzufügen eines Benutzers zur Rolle „Lab-Ersteller“](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- **Besitzer** oder **Mitwirkender** des Labs
  
    Ein Dozent in der Rolle „Besitzer“ oder „Mitwirkender“ des Labs kann die Einstellungen eines Labs anzeigen und ändern. Die Person muss auch Mitglied der Rolle „Leser“ des Lab-Kontos sein.

    Ein wichtiger Unterschied zwischen den Rollen „Besitzer“ und „Mitwirkender“ des Labs besteht darin, dass nur ein Besitzer anderen Benutzern Zugriff auf die Verwaltung eines Labs gewähren kann. Ein Mitwirkender kann anderen Benutzern *keinen* Zugriff auf die Verwaltung eines Labs gewähren.

- **Shared Image Gallery**

    Wenn Sie eine Shared Image Gallery einem Lab-Konto anfügen, erhalten Besitzer und Mitwirkende des Lab-Kontos sowie Lab-Ersteller, -Besitzer und -Mitwirkende automatisch Zugriff, sodass sie Images in der Gallery anzeigen und speichern können.

Wenn Sie Rollen zuweisen, sollten Sie diese Tipps befolgen:

   - Normalerweise sollten nur Administratoren Mitglieder der Rollen „Besitzer“ oder „Mitwirkender“ des Lab-Kontos sein. Das Lab-Konto verfügt möglicherweise über mehrere Besitzer oder Mitwirkende.
   - Um Dozenten die Möglichkeit zum Erstellen neuer Labs und Verwalten der Labs zu bieten, die sie erstellen, müssen Sie ihnen nur Zugriff auf die Rolle „Lab-Ersteller“ zuweisen.
   - Um Dozenten die Möglichkeit zu geben, bestimmte Labs zu verwalten, aber *nicht* die Möglichkeit, neue Labs zu erstellen, weisen Sie ihnen für jedes Lab, das sie verwalten, die Rolle „Besitzer“ oder „Mitwirkender“ zu. Sie könnten beispielsweise einem Professor und seinem Assistenten den Mitbesitz eines Labs erlauben. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Benutzer zu einem vorhandenen Lab in Azure Lab Services](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Preise

### <a name="azure-lab-services"></a>Azure Lab Services

Weitere Informationen zu den aktuellen Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).


### <a name="shared-image-gallery"></a>Gemeinsamer Image-Katalog

Sie müssen außerdem die Preise für die Shared Image Gallery berücksichtigen, wenn Sie planen, Shared Image Gallerys zum Speichern und Verwalten von Imageversionen zu verwenden. 

Das Erstellen einer Shared Image Gallery und das Anfügen des Katalogs an Ihr Lab sind kostenlos. Es fallen erst Kosten an, wenn Sie eine Imageversion in der Gallery speichern. Die Preise für die Verwendung einer Shared Image Gallery sind normalerweise praktisch vernachlässigbar, doch es ist wichtig zu verstehen, wie die Berechnung erfolgt, da die Gebühren nicht in den Preisen für Azure Lab Services enthalten sind.  

#### <a name="storage-charges"></a>Speichergebühren

Um Imageversionen zu speichern, verwendet eine Shared Image Gallery standardmäßig verwaltete HDD-Datenträger (Festplattenlaufwerke). Die Größe des verwendeten, verwalteten HDD-Datenträgers hängt von der Größe der Imageversion ab, die gespeichert wird. Informationen zu den Preisen finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Kosten für Replikation und ausgehende Netzwerkdaten

Wenn Sie eine Imageversion mittels einer Vorlagen-VM eines Labs speichern, speichert Azure Lab Services diese zuerst in einer Quellregion und repliziert die Quellimageversion dann automatisch in mindestens eine Zielregion. 

Es ist wichtig zu beachten, dass Azure Lab Services die Quellimageversion automatisch in alle [Zielregionen innerhalb des geografischen Raums](https://azure.microsoft.com/global-infrastructure/regions/) repliziert, in dem sich das Lab befindet. Wenn sich Ihr Lab beispielsweise im geografischen Raum der USA befindet, wird eine Imageversion in jede der acht Regionen repliziert, die innerhalb der USA vorhanden sind.

Eine Gebühr für ausgehenden Netzwerkdatenverkehr fällt an, wenn eine Imageversion aus der Quellregion in zusätzliche Zielregionen repliziert wird. Die Höhe der berechneten Gebühr basiert auf der Größe der Imageversion, wenn die Daten des Images anfänglich ausgehend aus der Quellregion übertragen werden.  Ausführliche Informationen zu Preisen finden Sie unter [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/).

Kunden, für die [Lizenzoptionen für spezielle Organisationen](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) gelten, könnten von Gebühren für ausgehenden Datenverkehr befreit werden. Um mehr zu erfahren, wenden Sie sich an Ihren Konto-Manager. 

Weitere Informationen finden Sie unter „Welche Programme für die Datenübertragung gibt es für akademische Kunden, und wie kann ich mich dafür registrieren?“ im FAQ-Abschnitt der Seite [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/).

#### <a name="pricing-example"></a>Preisbeispiel

Sehen wir uns ein Beispiel für die Kosten für das Speichern eines Vorlagen-VM-Images in einer Shared Image Gallery an. Vorausgesetzt werden die folgenden Szenarien:

- Sie verfügen über ein benutzerdefiniertes VM-Image.
- Sie speichern zwei Versionen des Images.
- Ihr Lab befindet sich in den USA, die aus insgesamt acht Regionen bestehen.
- Jede Imageversion ist 32 GB groß. Hieraus resultiert, dass der Preis für den verwalteten HDD-Datenträger $ 1,54 pro Monat beträgt.

Die monatlichen Gesamtkosten werden wie folgt geschätzt:

* *Anzahl der Images &times; Anzahl der Versionen &times; Anzahl der Replikate &times; Preis für verwalteten Datenträger = monatliche Gesamtkosten*

In diesem Beispiel betragen die Kosten:

* 1 benutzerdefiniertes Image (32 GB) &times; 2 Versionen &times; 8 US-Regionen &times; $ 1,54 = $ 24,64 pro Monat

> [!NOTE]
> Die vorhergehende Berechnung dient nur als Beispiel. Sie deckt die Speicherkosten für die Verwendung der Shared Image Gallery ab und enthält *keine* Kosten für ausgehenden Datenverkehr. Die tatsächlichen Preise für Speicher finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/en-us/pricing/details/managed-disks/).

#### <a name="cost-management"></a>Kostenverwaltung

Es ist wichtig, dass Lab-Kontoadministratoren die Kosten verwalten, indem sie routinemäßig nicht mehr benötigte Imageversionen aus dem Katalog löschen. 

Löschen Sie nicht die Replikation in bestimmten Regionen, um auf diese Weise die Kosten zu senken, obwohl diese Option in der Shared Image Gallery vorhanden ist. Replikationsänderungen könnten nachteilige Effekte auf die Fähigkeit von Azure Lab Service haben, VMs aus Images zu veröffentlichen, die in einer Shared Image Gallery gespeichert sind.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Einrichten und Verwalten von Labs finden Sie unter:

- [Einrichtungsleitfaden für Lab-Konten](account-setup-guide.md)  
- [Leitfaden für die Lab-Einrichtung](setup-guide.md)  
- [Kostenverwaltung für Labs](cost-management-guide.md)  
- [Azure Lab Services in Microsoft Teams](lab-services-within-teams-overview.md)
