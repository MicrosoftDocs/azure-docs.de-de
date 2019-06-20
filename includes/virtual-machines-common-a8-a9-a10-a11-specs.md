---
title: include file
description: include file
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep;amverma
ms.custom: include file
ms.openlocfilehash: 5cbc19d5aade2bbcc8b8dca277352d1b17d1d35a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755179"
---
## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung
* **Azure-Abonnement:** Um eine größere Anzahl von rechenintensiven Instanzen bereitzustellen, sollten Sie ein Abonnement mit nutzungsbasierter Bezahlung oder andere Kaufoptionen in Erwägung ziehen. Bei Verwendung eines [kostenlosen Azure-Kontos](https://azure.microsoft.com/free/)können Sie nur eine begrenzte Anzahl von Azure-Compute-Kernen nutzen.

* **Preise und Verfügbarkeit:** Diese VM-Größen werden nur im Tarif „Standard“ angeboten. Informationen zur Verfügbarkeit in den Azure-Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) . 
* **Kontingent Speicherkerne:** Es kann sein, dass Sie das Kontingent für die Speicherkerne in Ihrem Azure-Abonnement anpassen müssen, indem Sie den Standardwert erhöhen. Möglicherweise ist bei Ihrem Abonnement auch die Anzahl von Kernen beschränkt, die in bestimmten VM-Größenkategorien bereitgestellt werden können. In diesem Fall können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](../articles/azure-supportability/how-to-create-azure-support-request.md) und eine Erhöhung des Kontingents anfordern. (Standardgrenzwerte variieren unter Umständen je nach Abonnementkategorie.)
  
  > [!NOTE]
  > Wenn Sie einen umfangreichen Kapazitätsbedarf haben, wenden Sie sich an den Azure-Support. Azure-Kontingente sind Angebotsbeschränkungen, keine Kapazitätsgarantien. Unabhängig von Ihrem Kontingent werden nur die tatsächlich verwendeten Kerne in Rechnung gestellt.
  > 
  > 
* **Virtuelles Netzwerk:** Ein [virtuelles Azure-Netzwerk](https://azure.microsoft.com/documentation/services/virtual-network/) ist nicht erforderlich, um die rechenintensiven Instanzen zu verwenden. Für viele Bereitstellungen benötigen Sie jedoch mindestens ein cloudbasiertes virtuelles Azure-Netzwerk oder auch eine Site-to-Site-Verbindung für den Zugriff auf lokale Ressourcen. Erstellen Sie ggf. ein neues virtuelles Netzwerk zum Bereitstellen der Instanzen. Das Hinzufügen rechenintensiver virtueller Computer zu einem virtuellen Netzwerk in einer Affinitätsgruppe wird nicht unterstützt.
* **Größenanpassung:** Aufgrund der speziellen Hardware können Sie die Größe rechenintensiver Instanzen nur innerhalb der gleichen Größenfamilie (H-Serie oder rechenintensive A-Serie) anpassen. So können Sie beispielsweise die Größe eines virtuellen Computers der H-Serie nur auf eine andere Größe der H-Serie festlegen. Wechsel von einer nicht rechenintensiven Größe zu einer rechenintensiven Größe werden nicht unterstützt.  

## <a name="rdma-capable-instances"></a>RDMA-fähige Instanzen
Eine Teilmenge der rechenintensiven Instanzen (A8, A9, H16r, H16mr, HB und HC) verfügt über eine Netzwerkschnittstelle für RDMA-Verbindungen (Remote Direct Memory Access). Ausgewählte N-Seriengrößen mit dem Zusatz „r“, z. B. die NC24rs-Konfigurationen (NC24rs_v2 und NC24rs_v3), sind ebenfalls RDMA-fähig. Diese Schnittstelle steht zusätzlich zur standardmäßigen Azure-Netzwerkschnittstelle anderer VM-Größen zur Verfügung. 
  
Mithilfe dieser Schnittstelle können die RDMA-fähigen Instanzen über ein InfiniBand-Netzwerk (IB) kommunizieren, das mit EDR-Raten für virtuelle Computer der Größen HB und HC sowie mit FDR-Raten für virtuelle Computer der Größen H16r und H16mr sowie für RDMA-fähige virtuelle Computer der N-Serie und mit QDR-Raten für virtuelle Computer der Größe A8 und A9 betrieben wird. Mit diesen RDMA-Funktionen können Skalierbarkeit und Leistung von bestimmten MPI-Anwendungen (Message Passing Interface) gesteigert werden. Weitere Informationen zur Geschwindigkeit finden Sie in den Details in den Tabellen auf dieser Seite.

> [!NOTE]
> In Azure wird „IP over IB“ nur auf SR-IOV-fähigen virtuellen Computern unterstützt (derzeit HB und HC). „RDMA over IB“ wird für alle RDMA-fähigen Instanzen unterstützt.
>

