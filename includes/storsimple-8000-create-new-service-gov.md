---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c3f913aa3de1b723cd4eae70ff8e578a8abbec12
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557508"
---
#### <a name="to-create-a-new-service"></a>So erstellen Sie einen neuen Dienst
1. Melden Sie sich mithilfe Ihrer Microsoft-Kontoanmeldeinformationen beim [Microsoft Azure Government-Portal](https://portal.azure.us/) an.
2. Klicken Sie im Behördenportal auf **+** und dann im Marketplace auf **Alle anzeigen**. Suchen Sie nach _physisches Storsimple_. Wählen Sie **Physische StorSimple-Geräteserie** aus, klicken Sie darauf und dann auf **Erstellen**. Alternativ können Sie im Behördenportal auf **+** und dann unter **Speicher** auf **Physische StorSimple-Geräteserie** klicken.
3. Führen Sie auf dem Blatt **StorSimple-Geräte-Manager** die folgenden Schritte aus:
   
   1. Geben Sie einen eindeutigen **Ressourcennamen** für Ihren Dienst an. Dies ist ein Anzeigename, der zum Identifizieren des Diensts verwendet werden kann. Der Name kann zwischen 2 und 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.
   2. Wählen Sie ein **Abonnement** aus der Dropdownliste aus. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. Dieses Feld wird nicht angezeigt, wenn Sie nur ein Abonnement besitzen.
   3. Wählen Sie für **Ressourcengruppe** die Option **Vorhandene verwenden** oder **Neu erstellen** aus. Weitere Informationen finden Sie unter [Azure resource groups (Azure-Ressourcengruppen)](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).
   4. Geben Sie einen **Standort** für Ihren Dienst an. Der Standort bezieht sich auf die geografische Region, in der Ihr Gerät bereitgestellt werden soll. Wählen Sie **USGov Iowa** oder **USGov Virginia** aus.
   5. Aktivieren Sie **Neues Speicherkonto erstellen** , um automatisch ein Speicherkonto mit dem Dienst zu erstellen. Geben Sie einen Namen für dieses Speicherkonto an. Wenn Ihre Daten an einem anderen Speicherort gespeichert werden sollen, deaktivieren Sie dieses Kontrollkästchen.
   6. Überprüfen Sie **An Dashboard anheften**, wenn Sie einen Quicklink zu diesem Dienst auf Ihrem Dashboard erstellen möchten.
   7. Klicken Sie auf **Erstellen**, um den StorSimple-Geräte-Manager zu erstellen. Die Diensterstellung dauert einige Minuten. Nachdem der Dienst erfolgreich erstellt wurde, wird eine Benachrichtigung angezeigt, und das Blatt des neuen Dienstes wird geöffnet.