---
title: 'Schnellstart: Bereitstellen und Verwalten von NSG-Datenflussprotokollen mit Azure Policy'
titleSuffix: Azure Network Watcher
description: In diesem Artikel wird erläutert, wie die integrierten Richtlinien zum Verwalten der Bereitstellung von NSG-Datenflussprotokollen verwendet werden.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: damendo
ms.openlocfilehash: 54b87da73d4427234e65e406d183525d55c6c00d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948543"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Schnellstart: Bereitstellen und Verwalten von NSG-Datenflussprotokollen mit Azure Policy 

## <a name="overview"></a>Übersicht
Azure Policy hilft bei der Durchsetzung von Organisationsstandards und bei der Bewertung der Compliance nach Bedarf. Häufige Anwendungsfälle für Azure Policy sind die Implementierung von Governance für Ressourcenkonsistenz, Einhaltung gesetzlicher Bestimmungen, Sicherheit, Kosten und Verwaltung. In diesem Artikel werden zwei integrierte Richtlinien verwendet, die NSG-Datenflussprotokollen zur Verwaltung Ihrer Datenflussprotokoll-Einrichtung zur Verfügung stehen. Die erste Richtlinie kennzeichnet alle NSGs, für die keine Datenflussprotokolle aktiviert sind. Die zweite Richtlinie stellt automatisch Datenflussprotokolle für NSGs ohne aktivierte Datenflussprotokolle bereit. 

Wenn Sie zum ersten Mal eine Azure-Richtlinie erstellen, können Sie Folgendes lesen: 
- [Azure Policy – Übersicht](../governance/policy/overview.md) 
- [Tutorial zum Erstellen einer Richtlinie](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Suchen der Richtlinien
1. Wechseln Sie zum Azure-Portal: [portal.azure.com](https://portal.azure.com) 

Navigieren Sie zur Seite „Azure Policy“, indem Sie in der oberen Suchleiste nach „Policy“ suchen. ![Policy-Startseite](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Navigieren Sie im linken Bereich zur Registerkarte **Zuweisungen**.

![Registerkarte „Zuweisungen“](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Klicken Sie auf die Schaltfläche **Richtlinie zuweisen**. 

![Schaltfläche „Richtlinie zuweisen“](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Klicken Sie unter „Richtliniendefinitionen“ auf das Drei- Punkte-Menü, um die verfügbaren Richtlinien anzuzeigen.

5. Verwenden Sie den Filter „Typ“, und wählen Sie „Integriert“ aus. Suchen Sie dann nach „Datenflussprotokoll“.

Es sollten die beiden integrierten Richtlinien für die Datenflussprotokolle angezeigt werden. ![Richtlinienliste ](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Wählen Sie die Richtlinie aus, die Sie zuweisen möchten.

- *„Für jede Netzwerksicherheitsgruppe muss ein Datenflussprotokoll konfiguriert sein“* ist die Überwachungsrichtlinie, die nicht kompatible NSGs kennzeichnet, d. h. NSGs ohne aktivierte Datenflussprotokollierung.
- *„Ressource für Datenflussprotokolle mit Netzwerksicherheitsgruppe für Ziel bereitstellen“* ist die Richtlinie mit einer Bereitstellungsaktion. Sie ermöglicht Datenflussprotokolle in allen NSGs ohne Datenflussprotokolle.

Für jede Richtlinie sind unten separate Anweisungen aufgeführt.  

## <a name="audit-policy"></a>Überwachungsrichtlinie 

### <a name="how-the-policy-works"></a>Funktionsweise der Richtlinie

Die Richtlinie überprüft alle vorhandenen ARM-Objekte vom Typ „Microsoft.Network/networkSecurityGroups“, d. h., sie prüft alle NSGs in einem bestimmten Bereich und überprüft über die Eigenschaft „Datenflussprotokolle“ der NSG, ob verknüpfte Datenflussprotokolle vorhanden sind. Wenn die Eigenschaft nicht vorhanden ist, wird die NSG gekennzeichnet.

Wenn Sie die vollständige Definition der Richtlinie anzeigen möchten, rufen Sie die Registerkarte [Definitionen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) auf, und suchen Sie nach „Datenflussprotokolle“, um die Richtlinie zu finden.

### <a name="assignment"></a>Zuordnung

1. Geben Sie Ihre Richtliniendetails ein.

- Umfang: Ein Abonnement ist die übliche Wahl. Sie können auch eine Verwaltungsgruppe oder eine Ressourcengruppe auswählen, die für Sie relevant ist.  
- Richtliniendefinition: Sollte wie im Abschnitt „Suchen der Richtlinien" ausgewählt werden.
- Zuweisungsname: Wählen Sie einen beschreibenden Namen aus. 

2. Klicken Sie auf „Überprüfen + erstellen“, um Ihre Zuweisung zu überprüfen.

Die Richtlinie erfordert keine Parameter. Wenn Sie eine Überwachungsrichtlinie zuweisen, müssen Sie die Registerkarte „Wartung“ nicht ausfüllen.  

![Überprüfen der Überwachungsrichtlinie](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Ergebnisse

Um die Ergebnisse zu überprüfen, öffnen Sie die Registerkarte „Konformität“, und suchen Sie nach dem Namen Ihrer Zuweisung.
Sobald Ihre Richtlinie ausgeführt wird, sollte die Anzeige dem folgenden Screenshot ähneln. Wenn Ihre Richtlinie nicht ausgeführt wurde, warten Sie noch etwas ab. 

![Ergebnisse der Überwachungsrichtlinie](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Richtlinie für Bereitstellung, falls nicht vorhanden 

### <a name="policy-structure"></a>Richtlinienstruktur

Die Richtlinie überprüft alle vorhandenen ARM-Objekte vom Typ „Microsoft.Network/networkSecurityGroups“, d. h., sie prüft alle NSGs in einem bestimmten Bereich und überprüft über die Eigenschaft „Datenflussprotokolle“ der NSG, ob verknüpfte Datenflussprotokolle vorhanden sind. Wenn die Eigenschaft nicht vorhanden ist, stellt die Richtlinie ein Datenflussprotokoll bereit. 

Wenn Sie die vollständige Definition der Richtlinie anzeigen möchten, rufen Sie die Registerkarte [Definitionen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) auf, und suchen Sie nach „Datenflussprotokolle“, um die Richtlinie zu finden. 

### <a name="assignment"></a>Zuordnung

1. Geben Sie Ihre Richtliniendetails ein.

- Umfang: Ein Abonnement ist die übliche Wahl. Sie können auch eine Verwaltungsgruppe oder eine Ressourcengruppe auswählen, die für Sie relevant ist.  
- Richtliniendefinition: Sollte wie im Abschnitt „Suchen der Richtlinien" ausgewählt werden.
- Zuweisungsname: Wählen Sie einen beschreibenden Namen aus. 

2. Hinzufügen von Richtlinienparametern 

Der Network Watcher-Dienst ist ein regionaler Dienst. Mit diesen Parametern kann die Richtlinienaktion zur Bereitstellung von Datenflussprotokollen ausgeführt werden. 
- NSG-Region: Azure-Regionen, auf die die Richtlinie ausgerichtet ist.
- Speicher-ID: Hierbei handelt es sich um die vollständige Ressourcen-ID des Speicherkontos. Hinweis: Das Speicherkonto sollte sich in derselben Region wie die NSG befinden. 
- Network Watcher-Ressourcengruppe: Der Name der Ressourcengruppe, die Ihre Network Watcher-Ressource enthält. Wenn Sie diese nicht umbenannt haben, können Sie die Standardeinstellung „NetworkWatcherRG“ eingeben.
- Network Watcher-Name: Der Name des regionalen Network Watcher-Diensts. Format: NetworkWatcher_RegionName. Beispiel: NetworkWatcher_centralus. Die vollständige Liste finden Sie hier.

![DINE-Richtlinie: Parameter](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Hinzufügen von Wartungsdetails

- Aktivieren Sie das Kontrollkästchen „Wartungstask erstellen“, wenn sich die Richtlinie auf vorhandene Ressourcen auswirken soll. 
- „Verwaltete Identität erstellen“ sollte bereits aktiviert sein.
- Wählen Sie den gleichen Speicherort wie zuvor für Ihre verwaltete Identität aus. 
- Sie benötigen Berechtigungen als Mitwirkender oder Besitzer, um diese Richtlinie zu verwenden. Wenn Sie über diese Berechtigungen verfügen, sollten keine Fehler angezeigt werden.

![DINE-Richtlinie: Wartung](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. Klicken Sie auf „Überprüfen + erstellen“, um die Zuweisung zu überprüfen. Die Anzeige sollte dem folgenden Screenshot ähneln.

![DINE-Richtlinie: Überprüfung](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Ergebnisse

Um die Ergebnisse zu überprüfen, öffnen Sie die Registerkarte „Konformität“, und suchen Sie nach dem Namen Ihrer Zuweisung.
Wenn Ihre Richtlinie ausgeführt wurde, sollte ein Screenshot angezeigt werden, der dem folgenden ähnelt. Wenn Ihre Richtlinie nicht ausgeführt wurde, warten Sie noch etwas ab.

![DINE-Richtlinie: Ergebnisse](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Nächste Schritte 

-   Erfahren Sie in diesem [Tutorial](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md), wie Sie mithilfe von ARM-Vorlagen Datenflussprotokolle und Traffic Analytics bereitstellen.
-   Erfahren Sie mehr über [Network Watcher](./index.yml).