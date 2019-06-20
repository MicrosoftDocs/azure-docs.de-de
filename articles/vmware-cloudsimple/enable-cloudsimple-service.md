---
title: Aktivieren der Azure-VMware-Lösung von CloudSimple-Dienst
description: Beschreibt, wie Sie den CloudSimple-Dienst in einem Azure-Abonnement aktivieren und anschließend den CloudSimple-Ressourcenanbieter registrieren
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576672"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registrieren des Microsoft.VMwareCloudSimple-Ressourcenanbieters in Ihrem Azure-Abonnement

Der CloudSimple-Dienst ermöglicht es Ihnen, die Azure-VMware-Lösung von CloudSimple zu nutzen. Um den CloudSimple-Dienst nutzen zu können, muss er zuerst in Ihrem Azure-Abonnement aktiviert werden. Anschließend können Sie den Microsoft.VMwareCloudSimple-Dienst als Ihren Ressourcenanbieter registrieren.

## <a name="enable-the-cloudsimple-service"></a>Aktivieren des CloudSimple-Diensts

Um den CloudSimple-Dienst in Ihrem Azure-Abonnement zu aktivieren, öffnen Sie eine Supportanfrage bei [Microsoft-Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Wählen Sie beim Senden der Anfrage die folgenden Optionen aus.

* Problemtyp: **Technisch**
* Abonnement: **Ihre Abonnement-ID**
* Diensttyp: **VMware-Lösung von CloudSimple**
* Problemtyp: **Kontingent dedizierter Knoten**
* Problemuntertyp: **Erhöhen des Kontingents der dedizierten Knoten**
* Antragsteller: **Aktivieren des CloudSimple-Diensts**

Sie können sich auch unter [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) an Ihren Microsoft-Kontobeauftragten wenden. Geben Sie in der E-Mail Ihre Azure-Abonnement-ID an.  

Nachdem der CloudSimple-Dienst für Ihr Abonnement aktiviert wurde, können Sie den Ressourcenanbieter für das Abonnement aktivieren.

## <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Alle Dienste** aus.

3. Suchen Sie nach **Abonnements**, und wählen Sie diese Option aus.

    ![Auswählen von Abonnements](media/cloudsimple-service-select-subscriptions.png)

4. Wählen Sie das Abonnement aus, für das Sie den CloudSimple-Dienst aktivieren möchten.

5. Klicken Sie auf **Ressourcenanbieter** für das Abonnement.

6. Verwenden Sie **Microsoft.VMwareCloudSimple**, um den Ressourcenanbieter zu filtern.

7. Wählen Sie den Ressourcenanbieter aus, und klicken Sie auf **Registrieren**.

    ![Registrieren des Ressourcenanbieters](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Erstellen eines CloudSimple-Diensts](create-cloudsimple-service.md).
* Erfahren Sie mehr über [Konfigurieren einer Private Cloud-Umgebung](quickstart-create-private-cloud.md).