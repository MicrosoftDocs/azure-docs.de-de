---
title: Aktivieren einer Azure VMware Solution-Ressource
description: Erfahren Sie, wie Sie eine Supportanfrage einreichen, um Ihre Azure VMware Solution-Ressource zu aktivieren. Sie können auch weitere Hosts in Ihrer vorhandenen privaten Azure VMware Solution-Cloud anfordern.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 526d6b38f4b4e3f6c4806b71b4728dee90cf558a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325076"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Aktivieren einer Azure VMware Solution-Ressource
Hier erfahren Sie, wie Sie eine Supportanfrage einreichen, um Ihre Ressource vom Typ [Azure VMware Solution](introduction.md) zu aktivieren. Sie können auch weitere Hosts in Ihrer vorhandenen privaten Azure VMware Solution-Cloud anfordern.

## <a name="eligibility-criteria"></a>Wer kann Azure Government erwerben?

Sie benötigen ein Azure-Konto in einem Azure-Abonnement. Das Azure-Abonnement muss eines der folgenden Kriterien erfüllen:

* Abonnement unter einem [Azure Konzernvertrag (Enterprise Agreement, EA)](../cost-management-billing/manage/ea-portal-agreements.md) mit Microsoft
* Von einem Cloudlösungsanbieter (Cloud Solution Provider, CSP) verwaltetes Abonnement unter einem Azure-Plan


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Aktivieren einer Azure VMware Solution-Ressource für EA-Kunden
Bevor Sie Ihre Azure VMware Solution-Ressource erstellen, müssen Sie ein Supportticket übermitteln, damit Ihre Hosts zugewiesen werden. Nachdem das Supportteam Ihre Anforderung erhalten hat, dauert es bis zu fünf Werktage, bis die Anforderung bestätigt wurde und Ihre Hosts zugewiesen wurden. Wenn Sie über eine vorhandene private Azure VMware Solution-Cloud verfügen und weitere Hosts zugeordnet werden sollen, müssen Sie den gleichen Prozess durchlaufen.


1. Erstellen Sie im Azure-Portal unter **Hilfe und Support** eine **[Neue Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support)** , und geben Sie die folgenden Informationen für das Ticket an:
   - **Problemtyp:** Technisch
   - **Abonnement:** Wählen Sie Ihr Abonnement aus.
   - **Dienst:** Alle Dienste > Azure VMware Solution
   - **Ressource:** Allgemeine Frage 
   - **Zusammenfassung:** Kapazität erforderlich
   - **Problemtyp:** Capacity Management Issues (Kapazitätsverwaltungsprobleme)
   - **Problemuntertyp:** Customer Request for Additional Host Quota/Capacity (Kundenanfrage für zusätzliches Hostkontingent/zusätzliche Hostkapazität)

1. Geben Sie in der **Beschreibung** des Supporttickets auf der Registerkarte **Details** Folgendes an:

   - Proof of Concept oder Produktion 
   - Name der Region
   - Anzahl von Hosts
   - Weitere Details

   >[!NOTE]
   >Für Azure VMware Solution werden mindestens drei Hosts empfohlen, um Ihre private Cloud aufzubauen. Für Redundanzzwecke werden N+1 Hosts empfohlen. 

1. Wählen Sie **Überprüfen + erstellen** aus, um die Anforderung zu übermitteln.

   Es dauert bis zu fünf Werktage, bis ein Supportmitarbeiter Ihre Anforderung bestätigt.

   >[!IMPORTANT] 
   >Wenn Sie bereits über eine Azure VMware Solution-Instanz verfügen und zusätzliche Hosts anfordern, beachten Sie bitte, dass wir fünf Werktage für die Zuordnung der Hosts benötigen. 

1. Bevor Sie Ihre Hosts bereitstellen, vergewissern Sie sich, dass Sie den Ressourcenanbieter **Microsoft.AVS** im Azure-Portal registriert haben.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Informationen zu weiteren Möglichkeiten für die Ressourcenanbieterregistrierung finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Aktivieren einer Azure VMware Solution-Ressource für CSP-Kunden 

CSPs müssen [Microsoft Partner Center](https://partner.microsoft.com) verwenden, um Azure VMware Solution für ihre Kunden zu aktivieren. 

   >[!IMPORTANT] 
   >Der Azure VMware Solution-Dienst bietet keine Mehrinstanzenfähigkeit. Hostingpartner, die dies erfordern, werden nicht unterstützt. 

1. Wählen Sie in **Partner Center** die Option **CSP** aus, um auf den Bereich **Kunden** zuzugreifen.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Kundenbereich in Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Wählen Sie Ihren Kunden und anschließend **Produkte hinzufügen** aus.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Wählen Sie **Azure-Plan** und anschließend **Zu Warenkorb hinzufügen** aus. 

1. Überprüfen Sie die allgemeine Einrichtung des Azure-Planabonnements für Ihren Kunden, und schließen Sie sie ab. Weitere Informationen finden Sie in der [Dokumentation zu Microsoft Partner Center](/partner-center/azure-plan-manage).

Nachdem Sie den Azure-Plan konfiguriert und die erforderlichen vSphere-RBAC-Berechtigungen als CSP eingerichtet haben, wenden Sie sich an Microsoft, um das Kontingent für ein Azure-Planabonnement zu aktivieren. Greifen Sie unter Verwendung der Prozedur **Administrator im Namen von** (Admin on Behalf Of, AOBO) von Partner Center aus auf das Azure-Portal zu.

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com) an.

1. Wählen Sie **CSP** aus, um auf den **Kundenbereich** zuzugreifen.

1. Erweitern Sie die Kundendetails, und wählen Sie **Microsoft Azure-Verwaltungsportal** aus.

1. Erstellen Sie im Azure-Portal unter **Hilfe und Support** eine **[Neue Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support)** , und geben Sie die folgenden Informationen für das Ticket an:
   - **Problemtyp:** Technisch
   - **Abonnement:** Wählen Sie Ihr Abonnement aus.
   - **Dienst:** Alle Dienste > Azure VMware Solution
   - **Ressource:** Allgemeine Frage 
   - **Zusammenfassung:** Kapazität erforderlich
   - **Problemtyp:** Capacity Management Issues (Kapazitätsverwaltungsprobleme)
   - **Problemuntertyp:** Customer Request for Additional Host Quota/Capacity (Kundenanfrage für zusätzliches Hostkontingent/zusätzliche Hostkapazität)

1. Geben Sie in der **Beschreibung** des Supporttickets auf der Registerkarte **Details** Folgendes an:

   - Proof of Concept oder Produktion 
   - Name der Region
   - Anzahl von Hosts
   - Weitere Details
   - Sollen mehrere Kunden gehostet werden?

   >[!NOTE]
   >Für Azure VMware Solution werden mindestens drei Hosts empfohlen, um Ihre private Cloud aufzubauen. Für Redundanzzwecke werden N+1 Hosts empfohlen. 

1. Wählen Sie **Überprüfen + erstellen** aus, um die Anforderung zu übermitteln.

   Es dauert bis zu fünf Werktage, bis ein Supportmitarbeiter Ihre Anforderung bestätigt.

   >[!IMPORTANT] 
   >Wenn Sie bereits über eine Azure VMware Solution-Instanz verfügen und zusätzliche Hosts anfordern, beachten Sie bitte, dass wir fünf Werktage für die Zuordnung der Hosts benötigen. 

1. Wenn das Abonnement vom Dienstanbieter verwaltet wird, muss das Administrationsteam des Anbieters wiederum über den **Admin On Behalf Of**-Vorgang (AOBO) aus Partner Center auf das Azure-Portal zugreifen. Im Azure-Portal starten Sie eine [Cloud Shell](../cloud-shell/overview.md)-Instanz und registrieren den Ressourcenanbieter **Microsoft.AVS**. Danach fahren Sie mit der Bereitstellung der privaten Azure VMware Solution-Cloud fort.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Informationen zu weiteren Möglichkeiten für die Ressourcenanbieterregistrierung finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).

1. Wenn das Abonnement direkt vom Kunden verwaltet wird, muss die Registrierung des Ressourcenanbieters **Microsoft.AVS** durch einen Benutzer erfolgen, der über ausreichende Berechtigungen im Abonnement verfügt. Weitere Informationen dazu und zur Registrierung des Ressourcenanbieters finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md). 


## <a name="next-steps"></a>Nächste Schritte

Nach der Aktivierung Ihrer Azure VMware Solution-Ressource und der ordnungsgemäßen Einrichtung des Netzwerks können Sie [eine private Cloud erstellen](tutorial-create-private-cloud.md).