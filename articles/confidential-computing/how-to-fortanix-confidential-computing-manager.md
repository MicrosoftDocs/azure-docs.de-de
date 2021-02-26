---
title: Fortanix Confidential Computing Manager in einer verwalteten Azure-Anwendung
description: Erfahren Sie, wie Sie Fortanix Confidential Computing Manager (CCM) über das Azure-Portal in einer verwalteten Anwendung bereitstellen.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 6132b864e94c5c2af691183e198ca882c361d5ec
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551122"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix Confidential Computing Manager in einer verwalteten Azure-Anwendung

In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal eine Anwendung bereitstellen, die mit Fortanix Confidential Computing Manager (CCM) verwaltet wird.

Fortanix ist ein Drittanbieter von Software mit Produkten und Diensten, die auf Azure-Infrastruktur aufsetzen. Andere Drittanbieter bieten ähnliche Confidential Computing-Dienste in Azure an.

> [!NOTE]
>Die Produkte, auf die in diesem Dokument verwiesen wird, unterliegen nicht der Kontrolle von Microsoft. Microsoft stellt Ihnen diese Informationen nur aus praktischen Gründen zur Verfügung, und der Verweis auf diese Nicht-Microsoft-Produkte bedeutet nicht, dass Microsoft sie empfiehlt.

## <a name="prerequisites"></a>Voraussetzungen

- Eine private Docker-Registrierung für die Pushübertragung konvertierter Anwendungsimages.
- Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), bevor Sie beginnen.

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Bereitstellen eines Confidential Computing-Managers über eine verwaltete Azure-Anwendung

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure-Portal":::

2. Suchen Sie auf der Suchleiste nach „Fortanix Confidential Computing Manager“, um die Marketplace-Auflistung für Fortanix CCM anzuzeigen. Wählen Sie **Fortanix Confidential Computing Manager in Azure** aus.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Marketplace-Auflistung":::

3. Die Seite, auf der Sie die von CCM verwaltete Anwendung erstellen, wird geöffnet. Klicken Sie auf **Erstellen**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Erstellen der Anwendung":::

4. Füllen Sie alle Pflichtfelder aus.
   1. Im Abschnitt „Details zur verwalteten Anwendung“ enthält das Feld **Verwaltete Ressourcengruppe** einen Standardwert, der vom Benutzer bei Bedarf geändert werden kann.
   2. Wählen Sie im Feld **Region** eine der folgenden Regionen aus: **Australien, Osten**, **Australien, Südosten**, **USA, Osten**, **USA, Westen 2**, **Europa, Westen**, **Europa, Norden**, **Kanada, Mitte**, **Kanada, Osten** oder **USA, Osten 2 EUAP**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Pflichtfelder":::

   Wählen Sie **Überprüfen und erstellen** aus, um die von Fortanix CCM verwaltete Anwendung zu erstellen.

5. Überprüfen Sie die Details, und aktivieren Sie anschließend das Kontrollkästchen **Ich stimme den oben genannten Geschäftsbedingungen zu**. Wählen Sie dann **Erstellen** aus, um die verwaltete Anwendung zu erstellen.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Überprüfen der Details":::

6. Die Bereitstellung von Fortanix CCM wird gestartet, und Sie werden über die Ausführung der Bereitstellung informiert.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Bereitstellungsstatus":::

7. Wenn die Bereitstellung fertiggestellt ist, wählen Sie die Schaltfläche **Zu Ressourcen wechseln** aus, um zur Seite „Übersicht“ der bereitgestellten von CCM verwalteten Anwendung zu wechseln und den Serverknoten zu registrieren.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Screenshot der erfolgreichen Bereitstellung im Azure-Portal":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Screenshot der Übersicht über die Confidential Computing-Ressource im Azure-Portal":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Registrieren des Serverknotens in Fortanix CCM

1. Wählen Sie im linken Navigationsmenü **Confidential Computing Manager** aus. Melden Sie sich bei Fortanix CCM an, und erstellen Sie ein Konto, wie in **Abbildung 9** gezeigt.

    Weitere Informationen zur Registrierung und Anmeldung sowie zur Erstellung eines Kontos in CCM finden Sie unter [CCM Getting Started](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in) (Erste Schritte mit CCM).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Screenshot der Anmeldung beim Fortanix Confidential Computing Manager":::
    
2. Um das Token für den Beitritt über die CCM-Verwaltungskonsole abzurufen, wählen Sie zunächst die Schaltfläche **ENROLL NODE** (Knoten registrieren) aus. Wählen Sie dann im Fenster „ENROLL NODE“ (Knoten registrieren) die Schaltfläche **COPY** (Kopieren) aus, um das Token für den Beitritt zu kopieren.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Screenshot zum Abrufen des Tokens für den Beitritt":::

3. Um als Nächstes einen Knoten-Agent zu registrieren, wählen Sie die Registerkarte **Confidential Computing Node Agent** (Confidential Computing-Knoten-Agent) und dann **Hinzufügen** aus, um einen CCM-Knoten-Agent hinzuzufügen.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Screenshot des Hinzufügens des Knoten-Agents":::

4.  Füllen Sie im Formular für den CCM-Knoten-Agent alle Pflichtfelder aus. Fügen Sie das in Schritt 2 kopierte Token für den Beitritt in **Token für Beitritt** ein. Wählen Sie zur Bestätigung **Überprüfen und einreichen** aus.

    Weitere Informationen zum Registrieren eines CCM-Serverknotens finden Sie unter [Enroll Compute Node](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes) (Registrieren eines Serverknotens).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Screenshot der Registrierung des Serverknotens":::
    
5. Wählen Sie nach Abschluss der Überprüfung **Übermitteln** aus, um die Erstellung des Knoten-Agents abzuschließen.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Screenshot des erstellten Knoten-Agents":::

6. Um den Bereitstellungsstatus zu überprüfen, wechseln Sie zur Registerkarte **Übersicht**, und wählen Sie den Link **Verwaltete Ressourcengruppe** aus.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Screenshot des registrierten Knotens":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Screenshot der Überprüfung des Bereitstellungsstatus":::

7. Wie Sie nun feststellen können, wird die Bereitstellung noch ausgeführt. Die erfolgreiche Registrierung des Knoten-Agents kann einige Minuten dauern.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Screenshot der ausgeführten Bereitstellung":::

8. Wenn die Registrierung des Knoten-Agents erfolgreich abgeschlossen wurde, ändert sich der Status in „Erfolgreich“.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Screenshot der erfolgreich abgeschlossenen Bereitstellung":::

9. Wechseln Sie nun in der von CCM verwalteten Anwendung zu den Seiten mit den Serverknoten. Sie werden feststellen, dass der Status des Knotens **Aktiv** lautet und die Registrierung damit erfolgreich abgeschlossen wurde.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Screenshot des erfolgreich registrierten Knotens":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Benutzer können auf der Seite des Confidential Computing-Knoten-Agents auch einen CCM-Knoten-Agent löschen. Wählen Sie zum Löschen eines Knoten-Agents den gewünschten Knoten-Agent und dann auf der oberen Leiste die Schaltfläche **Löschen** aus.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Screenshot des Löschens des Knoten-Agents":::

## <a name="next-steps"></a>Nächste Schritte

In diesen Schnellstart haben Sie mithilfe einer verwalteten Azure-App einen Knoten für den Confidential Computing Manager von Fortanix registriert. Durch die Knotenregistrierung können Sie Ihr Anwendungsimage so konvertieren, dass es auf einer Confidential Computing-VM ausgeführt werden kann. Weitere Informationen zu virtuellen Confidential Computing-Computern in Azure finden Sie unter [Lösungen für virtuelle Computer](virtual-machine-solutions.md).

Weitere Informationen zu den Confidential Computing-Angeboten von Azure finden Sie unter [Confidential Computing unter Azure](overview.md).

Erfahren Sie, wie Sie ähnliche Aufgaben mit anderen Drittanbieterangeboten für Azure wie [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) und [Scone](https://sconedocs.github.io) erledigen können.

