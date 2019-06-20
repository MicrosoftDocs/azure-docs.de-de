---
title: 'Erstellen einer Azure-VMware-Lösung von CloudSimple: – Dienst'
description: Beschreibt, wie der CloudSimple-Dienst im Azure-Portal erstellt wird
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a0ccce6f298270b2751307868fdf85697cb7e8ee
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154958"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Erstellen einer Azure-VMware-Lösung von CloudSimple – Dienst

Als ersten Schritt für die Azure-VMware-Lösung von CloudSimple erstellen Sie den Dienst „Azure-VMware-Lösung von CloudSimple“ im Azure-Portal.

> [!NOTE]
> Vor dem Erstellen des CloudSimple-Diensts müssen Sie den Microsoft.VMwareCloudSimple-Ressourcenanbieter in Ihrem Azure-Abonnement registrieren. Führen Sie dazu die Schritte in [Aktivieren des Microsoft.VMwareCloudSimple-Ressourcenanbieters in Ihrem Azure-Abonnement](enable-cloudsimple-service.md) aus.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-the-service"></a>Erstellen des Diensts

1. Wählen Sie **Alle Dienste** aus.

2. Suchen Sie nach **CloudSimple Services**.

    ![Suchen nach dem CloudSimple-Dienst](media/create-cloudsimple-service-search.png)

3. Wählen Sie **CloudSimple Service** aus.

4. Klicken Sie auf **Hinzufügen**, um einen neuen Dienst zu erstellen.

    ![Hinzufügen des CloudSimple-Diensts](media/create-cloudsimple-service-add.png)

5. Wählen Sie das Abonnement aus, in dem Sie den CloudSimple-Dienst erstellen möchten.

6. Wählen Sie die Ressourcengruppe für den Dienst aus. Um eine neue Ressourcengruppe hinzuzufügen, klicken Sie auf **Neue erstellen**.

7. Geben Sie einen Namen ein, um den Dienst zu benennen.

8. Geben Sie das CIDR für das Dienstgateway ein. Geben Sie ein /28-Subnetz an, das sich mit keinem Ihrer vorhandenen Subnetze überschneidet.  Dazu gehören lokale Subnetze, Azure-Subnetze oder beliebige geplante CloudSimple-Subnetze. Sie können das CIDR nicht mehr ändern, nachdem der Dienst erstellt wurde.

    ![Erstellen des CloudSimple-Diensts](media/create-cloudsimple-service.png)

9. Klicken Sie auf **OK**.

Der Dienst wird erstellt und zur Liste der Dienste hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Erstellen einer privaten Cloud](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Erfahren Sie mehr über [Konfigurieren einer Private Cloud-Umgebung](quickstart-create-private-cloud.md).
