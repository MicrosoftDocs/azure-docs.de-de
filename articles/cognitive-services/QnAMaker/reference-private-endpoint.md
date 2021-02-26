---
title: Einrichten eines privaten Endpunkts – QnA Maker
description: Hier erfahren Sie mehr über die in „QnA Maker managed“ verfügbare Option zum Erstellen von privaten Endpunkten.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 5a7ddcf25993ab4ce87bdc47c5e0ae32fa24b07f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987183"
---
# <a name="private-endpoints"></a>Private Endpunkte

Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der QnA Maker bietet jetzt Unterstützung für das Erstellen privater Endpunkte im Azure Search-Dienst. Diese Funktionalität ist in „QnA Maker managed“ verfügbar. 

Private Endpunkte werden durch [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview) als separater Dienst bereitgestellt. Weitere Informationen zu den Kosten finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/private-link/). 

## <a name="prerequisites"></a>Voraussetzungen
> [!div class="checklist"]
> * Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.
> * eine über das Azure-Portal erstellte [„QnA Maker managed“-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker). Merken Sie sich die Azure Active Directory-ID, das Abonnement und den QnA-Ressourcennamen, die Sie beim Erstellen der Ressource ausgewählt haben.

## <a name="steps-to-enable-private-endpoint"></a>Schritte zum Aktivieren des privaten Endpunkts
1. Weisen Sie dem Dienst „QnA Maker managed“ in der Azure Search-Dienstinstanz die Rolle *Contributer* (Mitwirkender) zu. Dieser Vorgang erfordert die Zugriffsberechtigungen der Rolle *Besitzer* für das Abonnement. Wechseln Sie zur Registerkarte „Identität“ in der Dienstressource, um die Identität abzurufen.
![Verwaltete Dienstidentität](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Fügen Sie die oben genannte Identität als *Contributer* (Mitwirkender) hinzu, indem Sie zur Registerkarte „Zugriffssteuerung (IAM)“ des Azure Search-Diensts navigieren. ![Registerkarte „Zugriffssteuerung (IAM)“ für verwalteten Dienst](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Klicken Sie auf *Add role assignments* (Rollenzuweisungen hinzufügen), fügen Sie die Identität hinzu, und klicken Sie auf *Speichern*.
![Rollenzuweisung für verwalteten Dienst](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Navigieren Sie nun in der Azure Search-Dienstinstanz zur Registerkarte *Netzwerk*, und ändern Sie die Einstellung für „Endpunktkonnektivität (Daten)“ von *Öffentlich* in *Privat*. Dieser Vorgang ist ein zeitintensiver Prozess und kann bis zu 30 Minuten in Anspruch nehmen. 
![Registerkarte „Netzwerk“ für verwalteten Azure Search-Dienst](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Wechseln Sie zur Registerkarte *Netzwerk* des Diensts „QnA Maker managed“, wählen Sie unter *Zugriff erlauben von* die Option *Ausgewählte Netzwerke und private Endpunkte* aus, und klicken Sie auf *Speichern*. 
![Registerkarte „Netzwerk“ für „QnA Maker managed“](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Dadurch wird eine private Endpunktverbindung zwischen dem QnA Maker-Dienst und der Azure Cognitive Search-Dienstinstanz hergestellt. Sie können die private Endpunktverbindung auf der Registerkarte *Netzwerk* der Azure Search-Dienstinstanz überprüfen. Nachdem der gesamte Vorgang abgeschlossen ist, können Sie Ihren QnA Maker-Dienst verwenden. 
![Registerkarte „Netzwerk“ für verwalteten Dienst](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Details zur Unterstützung
 * Das Ändern des Azure Search-Diensts wird nicht mehr unterstützt, wenn Sie den privaten Zugriff auf Ihren QnA Maker-Dienst aktivieren. Wenn Sie den Azure Search-Dienst über die Registerkarte „Konfiguration“ ändern, nachdem Sie den privaten Zugriff aktiviert haben, wird der QnA Maker-Dienst unbrauchbar.
 * Wenn Sie nach dem Einrichten der privaten Endpunktverbindung die Einstellung für das Netzwerk des Azure Search-Diensts in „Öffentlich“ ändern, können Sie den QnA Maker-Dienst nicht verwenden. Sie müssen für das Netzwerk des Azure Search-Diensts die Einstellung „Privat“ verwenden, damit die private Endpunktverbindung funktioniert.
