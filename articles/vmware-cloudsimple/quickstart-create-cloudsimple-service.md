---
title: Azure-VMware-Lösung von CloudSimple – Schnellstart – Erstellen des Diensts
description: Erfahren Sie, wie der CloudSimple-Dienst erstellt wird und wie Knoten gekauft und reserviert werden.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b3b95db24f4b0f9a0cf8f5102dfeea5dc51e29f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64576888"
---
# <a name="quickstart---create-service"></a>Schnellstart: Erstellen des Diensts

Erstellen Sie zunächst die Azure-VMware-Lösung von CloudSimple im Azure-Portal ein.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware-Lösung von CloudSimple: Übersicht über den Dienst

Der CloudSimple-Dienst ermöglicht es Ihnen, die Azure-VMware-Lösung von CloudSimple zu nutzen.  Nach Erstellen des Diensts können Sie Knoten kaufen, Knoten reservieren und Sie private Clouds erstellen.  Sie fügen den CloudSimple-Dienst in jeder Azure-Region hinzu, in der der CloudSimple-Dienst verfügbar ist.  Der Dienst definiert das Umkreisnetzwerk von Azure-VMware-Lösung von CloudSimple.  Dieses Umkreisnetzwerk wird für Dienste verwendet, zu denen VPN, ExpressRoute und Internetkonnektivität mit Ihren privaten Clouds gehören.

Um den CloudSimple-Dienst hinzuzufügen, müssen Sie ein Gatewaysubnetz erstellen. Das Gatewaysubnetz wird verwendet, wenn das Edge-Netzwerk erstellt wird, und das Gatewaysubnetz erfordert einen /28-CIDR-Block. Der Gatewaysubnetz-Adressraum muss eindeutig sein. Er darf nicht mit einem Ihrer lokalen Netzwerkadressräume oder mit einem Adressraum des virtuellen Azure-Netzwerk überlappen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Aktivieren des Microsoft.VMwareCloudSimple-Ressourcenanbieters

Gehen Sie folgendermaßen vor, um den Ressourcenanbieter für den CloudSimple-Dienst zu aktivieren.

1. Wählen Sie **Alle Dienste** aus.
2. Suchen Sie nach **Abonnements**, und wählen Sie diese Option aus.

    ![Auswählen von Abonnements](media/cloudsimple-service-select-subscriptions.png)

3. Wählen Sie das Abonnement aus, für das Sie den CloudSimple-Dienst aktivieren möchten.
4. Klicken Sie auf **Ressourcenanbieter** für das Abonnement.
5. Verwenden Sie **Microsoft.VMwareCloudSimple**, um den Ressourcenanbieter zu filtern.
6. Wählen Sie den **Microsoft.VMwareCloudSimple**-Ressourcenanbieter aus, und klicken Sie auf **Registrieren**.

    ![Registrieren des Ressourcenanbieters](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Erstellen des Diensts

>[!NOTE]
> Der CloudSimple-Dienst muss für Ihr Abonnement aktiviert sein. Ist er nicht für Ihr Abonnement aktiviert, erhalten Sie einen Fehler, wenn Sie versuchen, den Dienst zu erstellen.  Führen Sie die Schritte aus dem Artikel [Enable the CloudSimple service](https://docs.azure.cloudsimple.com/enable-cloudsimple-service) (Aktivieren des CloudSimple-Diensts) aus, um den Dienst zu aktivieren.

1. Wählen Sie **Alle Dienste** aus.
2. Suchen Sie nach **CloudSimple Service**.

    ![Suchen nach dem CloudSimple-Dienst](media/create-cloudsimple-service-search.png)

3. Wählen Sie **CloudSimple Service** aus.
4. Klicken Sie auf **Hinzufügen**, um einen neuen Dienst zu erstellen.

    ![Hinzufügen des CloudSimple-Diensts](media/create-cloudsimple-service-add.png)

5. Wählen Sie das Abonnement aus, in dem Sie den CloudSimple-Dienst erstellen möchten.
6. Wählen Sie die Ressourcengruppe für den Dienst aus. Um eine neue Ressourcengruppe hinzuzufügen, klicken Sie auf **Neue erstellen**.
7. Geben Sie einen Namen ein, um den Dienst zu benennen.
8. Geben Sie das CIDR für das Dienstgateway ein. Geben Sie ein /28-Subnetz an, das mit keinem Ihrer lokalen Subnetze, Azure-Subnetze oder geplanten CloudSimple-Subnetze überlappt. Sie können das CIDR nicht mehr ändern, nachdem der Dienst erstellt wurde.

    ![Erstellen des CloudSimple-Diensts](media/create-cloudsimple-service.png)

9. Klicken Sie auf **OK**.

Der Dienst wird erstellt und zur Liste der Dienste hinzugefügt.

## <a name="purchase-nodes"></a>Kaufen von Knoten

Um für die Umgebung einer privaten CloudSimple-Cloud Kapazität mit nutzungsbasierter Bezahlung einzurichten, müssen Sie zuerst Knoten im Azure-Portal bereitstellen.

1. Wählen Sie **Alle Dienste** aus.
2. Suchen Sie nach **CloudSimple-Knoten**.

    ![Suchen nach CloudSimple-Knoten](media/create-cloudsimple-node-search.png)

3. Wählen Sie **CloudSimple-Knoten** aus.
4. Klicken Sie auf **Hinzufügen**, um Knoten zu erstellen.

    ![Hinzufügen von CloudSimple-Knoten](media/create-cloudsimple-node-add.png)

5. Wählen Sie das Abonnement aus, für das Sie CloudSimple-Knoten kaufen möchten.
6. Wählen Sie die Ressourcengruppe für die Knoten aus. Um eine neue Ressourcengruppe hinzuzufügen, klicken Sie auf **Neue erstellen**.
7. Geben Sie das Präfix ein, um die Knoten zu kennzeichnen.
8. Wählen Sie den Standort für die Knotenressourcen aus.
9. Wählen Sie den dedizierten Standort (Dedicated location) aus, in dem die Knotenressourcen gehostet werden sollen.
10. Wählen Sie den Knotentyp aus. Sie können zwischen der [CS28- und CS36-Option](cloudsimple-node.md) wählen. Die zweite Option umfasst die maximale Compute- und Arbeitsspeicherkapazität.
11. Wählen Sie die Anzahl der bereitzustellenden Knoten aus.
12. Klicken Sie auf **Überprüfen + erstellen**.
13. Überprüfen Sie die Einstellungen. Wenn Sie irgendwelche Einstellungen ändern möchten, klicken Sie auf **Zurück**.
14. Klicken Sie auf **Erstellen**.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer privaten Cloud und Konfigurieren der Umgebung](quickstart-create-private-cloud.md)
* Weitere Informationen über den [CloudSimple-Dienst](https://docs.azure.cloudsimple.com/cloudsimple-service)
