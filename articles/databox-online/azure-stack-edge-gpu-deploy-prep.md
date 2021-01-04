---
title: Tutorial zum Vorbereiten des Azure-Portals und der Rechenzentrumsumgebung für das Bereitstellen von Azure Stack Edge Pro-Geräten mit GPU | Microsoft-Dokumentation
description: Im ersten Tutorial zum Bereitstellen von Azure Stack Edge Pro-Geräten mit GPU geht es um die Vorbereitung des Azure-Portals.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: cdfd012d5015e156439a1afa89e818bf82b64dc6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449340"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>Tutorial: Vorbereiten der Bereitstellung von Azure Stack Edge Pro-Geräten mit GPU 

Dies ist das erste Tutorial einer Reihe von Bereitstellungstutorials, die zum vollständigen Bereitstellen von Azure Stack Edge Pro-Geräten mit GPU erforderlich sind. In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal für die Bereitstellung einer Azure Stack Edge-Ressource vorbereiten.

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Die Vorbereitung des Portals erfordert weniger als 10 Minuten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

### <a name="get-started"></a>Erste Schritte

Für die Azure Stack Edge Pro-Bereitstellung müssen Sie zunächst Ihre Umgebung vorbereiten. Sobald die Umgebung bereit ist, führen Sie die erforderlichen Schritte und bei Bedarf auch optionale Schritte und Verfahren durch, um das Gerät vollständig bereitzustellen. Die detaillierten Bereitstellungsanleitungen geben auch an, wann Sie die folgenden erforderlichen und optionalen Schritte ausführen sollten.

| Schritt | BESCHREIBUNG |
| --- | --- |
| **Vorbereitung** |Diese Schritte müssen zur Vorbereitung der anstehenden Bereitstellung ausgeführt werden. |
| **[Konfigurationsprüfliste für die Bereitstellung](#deployment-configuration-checklist)** |Verwenden Sie diese Prüfliste, um vor und während der Bereitstellung Informationen zu erfassen und aufzuzeichnen. |
| **[Voraussetzungen für die Bereitstellung](#prerequisites)** |Mit diesen Schritten überprüfen Sie, ob die Umgebung für die Bereitstellung vorbereitet ist. |
|  | |
|**Tutorials für die Bereitstellung** |Diese Tutorials sind erforderlich, um Ihr Azure Stack Edge Pro-Gerät in der Produktionsumgebung bereitzustellen. |
|**[1. Vorbereiten des Azure-Portals für Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)** |Erstellen und konfigurieren Sie Ihre Azure Stack Edge-Ressource, bevor Sie ein physisches Azure Stack Edge-Gerät installieren. |
|**[2. Installieren von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)**|Packen Sie das physische Azure Stack Edge Pro-Gerät aus, montieren Sie es in einem Rack, und verkabeln Sie es.  |
|**[3. Herstellen einer Verbindung mit Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md)** |Sobald das Gerät installiert ist, stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts her.  |
|**[4. Konfigurieren von Netzwerkeinstellungen für Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Konfigurieren Sie das Netzwerk einschließlich Computenetzwerk und Webproxyeinstellungen für Ihr Gerät.   |
|**[5. Konfigurieren von Geräteeinstellungen für Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Weisen Sie einen Gerätenamen und eine DNS-Domäne zu, und konfigurieren Sie den Updateserver und die Gerätezeit. |
|**[6. Konfigurieren von Sicherheitseinstellungen für Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Konfigurieren Sie Zertifikate für Ihr Gerät. Verwenden Sie von Geräten generierte Zertifikate, oder stellen Sie Ihre eigenen Zertifikate bereit.   |
|**[7. Aktivieren von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md)** |Verwenden Sie den Aktivierungsschlüssel des Diensts, um das Gerät zu aktivieren. Das Gerät ist bereit, SMB- oder NFS-Freigaben einzurichten oder eine Verbindung über REST herzustellen. |
|**[8. Konfigurieren von Compute](azure-stack-edge-gpu-deploy-configure-compute.md)** |Konfigurieren Sie die Computerolle auf Ihrem Gerät. Dabei wird auch ein Kubernetes-Cluster erstellt. |
|**[9A. Übertragen von Daten mit Edge-Freigaben](azure-stack-edge-j-series-deploy-add-shares.md)** |Fügen Sie Freigaben hinzu, und stellen Sie über SMB oder NFS eine Verbindung mit Freigaben her. |
|**[9B. Übertragen von Daten mit Edge-Speicherkonten](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |Fügen Sie Speicherkonten hinzu, und stellen Sie über REST-APIs eine Verbindung mit Blob Storage her. |


Sie können nun damit beginnen, Informationen über die Softwarekonfiguration Ihres Azure Stack Edge Pro-Geräts zu erfassen.

## <a name="deployment-configuration-checklist"></a>Konfigurationsprüfliste für die Bereitstellung

Bevor Sie Ihr Gerät bereitstellen, müssen Sie Informationen zum Konfigurieren der Software auf Ihrem Azure Stack Edge Pro-Gerät sammeln. Wenn Sie einige dieser Informationen im Voraus sammeln, können Sie das Gerät in Ihrer Umgebung schneller bereitstellen. Verwenden Sie die [Konfigurationsprüfliste für die Bereitstellung von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-checklist.md), um die Konfigurationsdetails während der Bereitstellung Ihres Geräts zu notieren.


## <a name="prerequisites"></a>Voraussetzungen

Im Folgenden finden Sie die Konfigurationsvoraussetzungen für die Azure Stack Edge-Ressource, das Azure Stack Edge Pro-Gerät und das Rechenzentrumsnetzwerk.

### <a name="for-the-azure-stack-edge-resource"></a>Für die Azure Stack Edge-Ressource

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Ihr Microsoft Azure-Abonnement ist für eine Azure Stack Edge-Ressource aktiviert. Stellen Sie sicher, dass Sie ein unterstütztes Abonnement verwendet haben, z. B. [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) oder [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Abonnements mit nutzungsbasierter Bezahlung werden nicht unterstützt. Informationen zum Identifizieren des Azure-Abonnementtyps finden Sie unter [Was ist ein Azure-Angebot?](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer).
- Sie verfügen für die Azure Stack Edge Pro-/Data Box Gateway-, IoT Hub- und Azure Storage-Ressourcen über Zugriff als Besitzer oder Mitwirkender auf Ressourcengruppenebene.

    - Für die Erstellung von Azure Stack Edge-/Data Box Gateway-Ressourcen müssen Sie mindestens über Berechtigungen als Mitwirkender auf der Ressourcengruppenebene verfügen. 
    - Außerdem müssen Sie dafür sorgen, dass die Ressourcenanbieter `Microsoft.DataBoxEdge` und `MicrosoftKeyVault` registriert werden. Der `Microsoft.Devices`-Anbieter muss registriert werden, um IoT Hub-Ressourcen zu erstellen. 
        - Zum Registrieren eines Ressourcenanbieters wechseln Sie im Azure-Portal zu **Home > Abonnements > Ihr-Abonnement > Ressourcenanbieter**. 
        - Suchen Sie nach dem jeweiligen Ressourcenanbieter, z. B. `Microsoft.DataBoxEdge`, und registrieren Sie ihn. 
    - Für die Erstellung von Speicherkontoressourcen sind ebenfalls mindestens Berechtigungen als Mitwirkender auf der Ressourcengruppenebene erforderlich. Azure Storage ist standardmäßig als Ressourcenanbieter registriert.
- Sie haben Administrator- oder Benutzerzugriff auf die Azure Active Directory Graph-API zum Generieren von Vorgängen für Aktivierungsschlüssel oder Anmeldeinformationen, z. B. die Erstellung einer Freigabe, bei der ein Speicherkonto verwendet wird. Weitere Informationen finden Sie unter [Azure Active Directory Graph-API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).


### <a name="for-the-azure-stack-edge-pro-device"></a>Für das Azure Stack Edge Pro-Gerät

Stellen Sie Folgendes sicher, bevor Sie ein physisches Gerät bereitstellen:

- Sie haben sich mit den beiliegenden Sicherheitsinformationen vertraut gemacht.
- In Ihrem Rechenzentrum ist für die Rackmontage des Geräts ein freier Einschub (1 HE) in einem 19-Zoll-Standardrack verfügbar.
- Sie haben Zugang zu einer flachen, stabilen und ebenen Arbeitsfläche, auf der das Gerät sicher abgelegt werden kann.
- Der Standort, an dem Sie das Gerät einrichten möchten, hat eine Standardstromversorgung von einer unabhängigen Quelle oder eine Rack-PDU (Power Distribution Unit) mit unterbrechungsfreier Stromversorgung (USV).
- Sie verfügen über ein physisches Gerät.


### <a name="for-the-datacenter-network"></a>Für das Netzwerk des Rechenzentrums

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Das Netzwerk in Ihrem Rechenzentrum ist gemäß den Netzwerkanforderungen für das Azure Stack Edge Pro-Gerät konfiguriert. Weitere Informationen finden Sie unter [Systemanforderungen für Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).

- Für den Normalbetrieb von Azure Stack Edge Pro benötigen Sie Folgendes:

    - Eine Downloadbandbreite von mindestens 10 MBit/s, um sicherzustellen, dass das Gerät auf dem neuesten Stand bleibt.
    - Eine dedizierte Upload- und Downloadbandbreite von mindestens 20 MBit/s zum Übertragen von Dateien.

## <a name="create-a-new-resource"></a>Neue Ressource erstellen

Falls Sie bereits über eine Azure Stack Edge-Ressource für die Verwaltung Ihres physischen Geräts verfügen, überspringen Sie diesen Schritt, und fahren Sie mit [Abrufen des Aktivierungsschlüssels](#get-the-activation-key) fort.

Um eine Azure Stack Edge-Ressource zu erstellen, führen Sie im Azure-Portal die folgenden Schritte aus.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen beim Azure-Portal unter der folgenden URL an: [https://portal.azure.com](https://portal.azure.com).

2. Wählen Sie im linken Bereich **+ Ressource erstellen** aus. Suchen Sie nach **Azure Stack Edge/Data Box Gateway**, und wählen Sie den Eintrag aus. Klicken Sie auf **Erstellen**. 

3. Wählen Sie das Abonnement aus, das Sie für das Azure Stack Edge Pro-Gerät verwenden möchten. Wählen Sie das Land aus, in das das physische Gerät versendet werden soll. Wählen Sie **Geräte anzeigen** aus.

    ![Ressource erstellen 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Wählen Sie den Gerätetyp aus. Wählen Sie unter **Azure Stack Edge Pro** die Option **Azure Stack Edge Pro mit GPU** und dann **Auswählen** aus. Wenn Probleme auftreten oder Sie den Gerätetyp nicht auswählen können, finden Sie unter [Behandeln von Problemen](azure-stack-edge-troubleshoot-ordering.md) weitere Informationen.

    ![Ressource erstellen 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Abhängig von Ihren Anforderungen können Sie Azure Stack Edge Pro mit ein oder zwei GPUs (Graphical Processing Units) von Nvidia auswählen. 

    ![Ressource erstellen 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. Geben Sie auf der Registerkarte **Grundlagen** die folgenden **Projektdetails** ein, bzw. wählen Sie sie aus:
    
    |Einstellung  |Wert  |
    |---------|---------|
    |Subscription    |Dieser Wert wird auf der Grundlage Ihrer zuvor getroffenen Auswahl automatisch aufgefüllt. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. |
    |Resource group  |Wählen Sie eine vorhandene Gruppe aus, oder erstellen Sie eine neue Gruppe.<br>Erfahren Sie mehr über [Azure-Ressourcengruppen](../azure-resource-manager/management/overview.md).     |

7. Geben Sie die folgenden **Instanzendetails** ein, bzw. wählen Sie sie aus:

    |Einstellung  |Wert  |
    |---------|---------|
    |Name   | Ein Anzeigename, der die Ressource identifiziert.<br>Der Name muss 2 bis 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten.<br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.        |
    |Region     |Eine Liste aller Regionen, in denen die Azure Stack Edge-Ressource verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Die für Azure Government verfügbaren Regionen finden Sie bei Bedarf unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist.|

    ![Ressource erstellen5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)

8. Klicken Sie auf **Weiter: Lieferanschrift**.

    - Falls Sie bereits über ein Gerät verfügen, aktivieren Sie das Kontrollkästchen **Ich habe ein Azure Stack Edge Pro-Gerät**.

        ![Ressource erstellen 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Handelt es sich um das neue Gerät, das Sie bestellen möchten, geben Sie Kontaktname, Unternehmen, Lieferadresse für das Gerät sowie Kontaktinformationen ein.

        ![Ressource erstellen 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. Klicken Sie auf **Weiter: Tags**. Geben Sie optional Tags an, um Ressourcen zu kategorisieren und die Abrechnung zu konsolidieren. Klicken Sie auf **Weiter: Überprüfen + erstellen**.

10. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die **Preisdetails**, die **Nutzungsbedingungen** und die Details für Ihre Ressource. Aktivieren Sie das Kontrollkästchen **Ich habe die angegebenen Informationen gelesen und stimme den Datenschutzbestimmungen zu.** .

    ![Ressource erstellen 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png) 

    Sie werden auch darüber informiert, dass während der Ressourcenerstellung eine verwaltete Dienstidentität (Managed Service Identity, MSI) aktiviert wird, mit der Sie sich bei Clouddiensten authentifizieren können. Diese Identität bleibt so lange bestehen, wie die Ressource vorhanden ist.

11. Klicken Sie auf **Erstellen**.

Die Erstellung der Ressource dauert einige Minuten. Außerdem wird eine MSI erstellt, mit der das Azure Stack Edge-Gerät mit dem Ressourcenanbieter in Azure kommunizieren kann.

Nachdem die Ressource erfolgreich erstellt und bereitgestellt wurde, erhalten Sie eine Benachrichtigung. Wählen Sie **Zu Ressource wechseln** aus.

![Navigieren zur Azure Stack Edge Pro-Ressource](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

Nach der Bestellung wird diese von Microsoft geprüft, und Sie erhalten eine E-Mail mit den Versanddetails.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

> [!NOTE]
>Wenn Sie mehrere Bestellungen gleichzeitig erstellen oder eine vorhandene Bestellung klonen möchten, können Sie die [Skripts in den Azure-Beispielen](https://github.com/Azure-Samples/azure-stack-edge-order) verwenden. Weitere Informationen finden Sie in der Infodatei.

Wenn Sie während des Bestellvorgangs auf Probleme stoßen, informieren Sie sich unter [Behandeln von Bestellproblemen](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Abrufen des Aktivierungsschlüssels

Wenn die Azure Stack Edge-Ressource betriebsbereit ist, müssen Sie den Aktivierungsschlüssel abrufen. Dieser Schlüssel wird verwendet, um Ihr Azure Stack Edge Pro-Gerät zu aktivieren und mit der Ressource zu verbinden. Sie können diesen Schlüssel jetzt abrufen, während Sie sich im Azure-Portal befinden.

1. Wählen Sie die von Ihnen erstellte Ressource aus. Wählen Sie **Übersicht** und anschließend **Geräteeinrichtung** aus.

    ![Auswählen von „Geräteeinrichtung“](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)

2. Geben Sie auf der Kachel **Aktivieren** einen Namen für Azure Key Vault an, oder übernehmen Sie den Standardnamen. Der Schlüsseltresorname kann aus 3 bis 24 Zeichen bestehen. 

    Für jede mit Ihrem Gerät aktivierte Azure Stack Edge-Ressource wird ein Schlüsseltresor erstellt. Im Schlüsseltresor können Sie Geheimnisse speichern und darauf zugreifen. So wird beispielsweise der Channel Integrity Key (CIK) für den Dienst im Schlüsseltresor gespeichert. 

    Nachdem Sie einen Schlüsseltresornamen angegeben haben, wählen Sie **Schlüssel generieren** aus, um einen Aktivierungsschlüssel zu erstellen. 

    ![Abrufen des Aktivierungsschlüssels](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

    Warten Sie einige Minuten, während Schlüsseltresor und Aktivierungsschlüssel erstellt werden. Wählen Sie das Kopiersymbol, um den Schlüssel zu kopieren und für die spätere Verwendung zu speichern.


> [!IMPORTANT]
> - Der Aktivierungsschlüssel läuft drei Tage nach seiner Generierung ab.
> - Generieren Sie einen neuen Schlüssel, falls der Schlüssel abgelaufen ist. Der ältere Schlüssel ist nicht mehr gültig.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Azure Stack Edge Pro-Themen behandelt:

> [!div class="checklist"]
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

Im nächsten Tutorial erfahren Sie, wie Sie Azure Stack Edge Pro installieren.

> [!div class="nextstepaction"]
> [Installieren von Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-install.md)