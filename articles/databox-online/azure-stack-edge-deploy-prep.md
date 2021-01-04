---
title: Tutorial zum Vorbereiten des Azure-Portals und der Rechenzentrumsumgebung für das Bereitstellen von Azure Stack Edge Pro | Microsoft-Dokumentation
description: Im ersten Tutorial zum Bereitstellen von Azure Stack Edge Pro-Geräten geht es um die Vorbereitung des Azure-Portals.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/11/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 3943caba5249432b3a0a4b7c2e63b2b818e2b7a1
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575702"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro"></a>Tutorial: Vorbereiten der Bereitstellung von Azure Stack Edge Pro  

Dies ist das erste Tutorial einer Reihe von Bereitstellungstutorials, die zum vollständigen Bereitstellen von Azure Stack Edge Pro erforderlich sind. In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal für die Bereitstellung einer Azure Stack Edge-Ressource vorbereiten.

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Die Vorbereitung des Portals erfordert weniger als 10 Minuten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="get-started"></a>Erste Schritte

Führen Sie zum Bereitstellen von Azure Stack Edge Pro die folgenden Tutorials in der angegebenen Reihenfolge aus.

| **#** | **Schritt** | **Dokumente** |
| --- | --- | --- | 
| 1. |**[Vorbereiten des Azure-Portals für Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)** |Erstellen und konfigurieren Sie Ihre Azure Stack Edge-Ressource, bevor Sie ein physisches Azure Stack Edge-Gerät installieren. |
| 2. |**[Installieren von Azure Stack Edge Pro](azure-stack-edge-deploy-install.md)**|Packen Sie das physische Azure Stack Edge Pro-Gerät aus, montieren Sie es in einem Rack, und verkabeln Sie es.  |
| 3. |**[Verbinden, Einrichten und Aktivieren von Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md)** |Stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche her, führen Sie die Einrichtung durch, und aktivieren Sie das Gerät. Das Gerät ist für die Einrichtung von SMB- oder NFS-Freigaben bereit.  |
| 4. |**[Übertragen von Daten mit Azure Stack Edge Pro](azure-stack-edge-deploy-add-shares.md)** |Fügen Sie Freigaben hinzu, und stellen Sie über SMB oder NFS eine Verbindung mit Freigaben her. |
| 5. |**[Transformieren von Daten mit Azure Stack Edge Pro](azure-stack-edge-deploy-configure-compute.md)** |Konfigurieren Sie Computemodule auf dem Gerät, um die Daten bei der Übertragung an Azure zu transformieren. |

Sie können jetzt mit der Einrichtung des Azure-Portals beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Im Folgenden finden Sie die Konfigurationsvoraussetzungen für die Azure Stack Edge-Ressource, das Azure Stack Edge Pro-Gerät und das Rechenzentrumsnetzwerk.

### <a name="for-the-azure-stack-edge-resource"></a>Für die Azure Stack Edge-Ressource

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Ihr Microsoft Azure-Abonnement ist für eine Azure Stack Edge-Ressource aktiviert. Stellen Sie sicher, dass Sie ein unterstütztes Abonnement verwendet haben, z. B. [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) oder [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Abonnements mit nutzungsbasierter Bezahlung werden nicht unterstützt.

* Sie verfügen für die Azure Stack Edge-/Data Box Gateway-, IoT Hub- und Azure Storage-Ressourcen über Zugriff als Besitzer oder Mitwirkender auf der Ressourcengruppenebene.

  * Sie müssen ein **Besitzer** auf der Abonnementebene sein, um Zugriff vom Typ „Mitwirkender“ gewähren zu können. Gehen Sie wie folgt vor, um einer anderen Person den Zugriff vom Typ „Mitwirkender“ zu gewähren: Navigieren Sie im Azure-Portal zu **Alle Dienste** > **Abonnements** > **Zugriffssteuerung (IAM)**  >  **+ Hinzufügen** > **Rollenzuweisung hinzufügen**. Weitere Informationen finden Sie im [Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für einen Benutzer über das Azure-Portal](../role-based-access-control/quickstart-assign-role-user-portal.md).

  * Für die Erstellung von Azure Stack Edge-/Data Box Gateway-Ressourcen müssen Sie mindestens über Berechtigungen als Mitwirkender auf der Ressourcengruppenebene verfügen. Vergewissern Sie sich außerdem, dass der Ressourcenanbieter `Microsoft.DataBoxEdge` registriert ist. Informationen zum Registrieren eines Ressourcenanbieters finden Sie unter [Registrieren des Ressourcenanbieters](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Für die Erstellung von IoT Hub-Ressourcen muss der Anbieter „Microsoft.Devices“ registriert sein. Eine Registrierungsanleitung finden Sie unter [Register resource provider](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers) (Registrieren des Ressourcenanbieters).
  * Für die Erstellung von Speicherkontoressourcen sind ebenfalls mindestens Berechtigungen als Mitwirkender auf der Ressourcengruppenebene erforderlich. Azure Storage ist standardmäßig als Ressourcenanbieter registriert.
* Sie haben als Administrator oder Benutzer Zugriff auf die Azure Active Directory Graph-API. Weitere Informationen finden Sie unter [Azure Active Directory Graph-API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.
* Sie werden durch Azure-Richtlinien, die von Ihrem Systemadministrator eingerichtet werden, nicht blockiert. Weitere Informationen zu Richtlinien finden Sie unter [Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen](../governance/policy/assign-policy-portal.md).

### <a name="for-the-azure-stack-edge-pro-device"></a>Voraussetzungen für die Bereitstellung des Azure Stack Edge Pro-Geräts

Stellen Sie Folgendes sicher, bevor Sie ein physisches Gerät bereitstellen:

* Sie haben sich mit den beiliegenden Sicherheitsinformationen vertraut gemacht.
* In Ihrem Rechenzentrum ist für die Rackmontage des Geräts ein freier Einschub (1 HE) in einem 19-Zoll-Standardrack verfügbar.
* Sie haben Zugang zu einer flachen, stabilen und ebenen Arbeitsfläche, auf der das Gerät sicher abgelegt werden kann.
* Der Standort, an dem Sie das Gerät einrichten möchten, hat eine Standardstromversorgung von einer unabhängigen Quelle oder eine Rack-PDU (Power Distribution Unit) mit unterbrechungsfreier Stromversorgung (USV).
* Sie verfügen über ein physisches Gerät.

### <a name="for-the-datacenter-network"></a>Für das Netzwerk des Rechenzentrums

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Das Netzwerk in Ihrem Rechenzentrum ist gemäß den Netzwerkanforderungen für das Azure Stack Edge Pro-Gerät konfiguriert. Weitere Informationen finden Sie unter [Systemanforderungen für Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).

* Für den Normalbetrieb von Azure Stack Edge Pro benötigen Sie Folgendes:

  * Eine Downloadbandbreite von mindestens 10 MBit/s, um sicherzustellen, dass das Gerät auf dem neuesten Stand bleibt.
  * Eine dedizierte Upload- und Downloadbandbreite von mindestens 20 MBit/s zum Übertragen von Dateien.

## <a name="create-a-new-resource"></a>Neue Ressource erstellen

Falls Sie bereits über eine Azure Stack Edge-Ressource für die Verwaltung Ihres physischen Geräts verfügen, überspringen Sie diesen Schritt, und fahren Sie mit [Abrufen des Aktivierungsschlüssels](#get-the-activation-key) fort.

Um eine Azure Stack Edge-Ressource zu erstellen, führen Sie die folgenden Schritte im Azure-Portal aus:

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen bei einem der folgenden Portale an: 

    - Azure-Portal (URL: [https://portal.azure.com](https://portal.azure.com))
    - Azure Government-Portal (URL: [https://portal.azure.us](https://portal.azure.us)). Ausführlichere Informationen finden Sie unter [Quickstart: Connect to Azure Government using portal](../azure-government/documentation-government-get-started-connect-with-portal.md) (Schnellstart: Herstellen einer Verbindung mit Azure Government über das Portal).

2. Wählen Sie im linken Bereich **+ Ressource erstellen** aus. Suchen Sie nach **Azure Stack Edge/Data Box Gateway**, und wählen Sie den Eintrag aus. Klicken Sie auf **Erstellen**.
3. Wählen Sie das Abonnement aus, das Sie für das Azure Stack Edge Pro-Gerät verwenden möchten. Wählen Sie die Region aus, in der Sie die Azure Stack Edge-Ressource bereitstellen möchten. Eine Liste aller Regionen, in denen die Azure Stack Edge-Ressource verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist. Die Region speichert nur die Metadaten für die Geräteverwaltung. Die tatsächlichen Daten können in jedem Speicherkonto gespeichert werden.
    
    Klicken Sie in der Option **Azure Stack Edge Pro** auf **Erstellen**.

    ![Suchen nach dem Azure Stack Edge-Dienst](media/azure-stack-edge-deploy-prep/data-box-edge-sku.png)

3. Geben Sie auf der Registerkarte **Grundlagen** die folgenden **Projektdetails** ein, bzw. wählen Sie sie aus:
    
    |Einstellung  |Wert  |
    |---------|---------|
    |Subscription    |Dieser Wert wird auf der Grundlage Ihrer zuvor getroffenen Auswahl automatisch aufgefüllt. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. |
    |Resource group  |Wählen Sie eine vorhandene Gruppe aus, oder erstellen Sie eine neue Gruppe.<br>Erfahren Sie mehr über [Azure-Ressourcengruppen](../azure-resource-manager/management/overview.md).     |

4. Geben Sie die folgenden **Instanzendetails** ein, bzw. wählen Sie sie aus:

    |Einstellung  |Wert  |
    |---------|---------|
    |Name   | Ein Anzeigename, der die Ressource identifiziert.<br>Der Name muss 2 bis 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten.<br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.        |
    |Region     |Eine Liste aller Regionen, in denen die Azure Stack Edge-Ressource verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Die für Azure Government verfügbaren Regionen finden Sie bei Bedarf unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist.|

    ![Projekt- und Instanzendetails](media/azure-stack-edge-deploy-prep/data-box-edge-resource.png)

5. Klicken Sie auf **Weiter: Lieferanschrift**.

    - Falls Sie bereits über ein Gerät verfügen, aktivieren Sie das Kombinationsfeld für **I have a Azure Stack Edge Pro device** (Ich habe ein Azure Stack Edge Pro-Gerät).
    - Handelt es sich um das neue Gerät, das Sie bestellen möchten, geben Sie Kontaktname, Unternehmen, Lieferadresse für das Gerät sowie Kontaktinformationen ein.

    ![Lieferadresse für neues Gerät](media/azure-stack-edge-deploy-prep/data-box-edge-resource1.png)

6. Klicken Sie auf **Weiter: Überprüfen + erstellen**.

7. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die **Preisdetails**, die **Nutzungsbedingungen** und die Details für Ihre Ressource. Aktivieren Sie das Kontrollkästchen **Ich habe die angegebenen Informationen gelesen und stimme den Datenschutzbestimmungen zu.** .

    ![Überprüfen der Details und Datenschutzrichtlinien für Ihre Azure Stack Edge-Ressource](media/azure-stack-edge-deploy-prep/data-box-edge-resource2.png)

8. Klicken Sie auf **Erstellen**.

Die Erstellung der Ressource dauert einige Minuten. Nachdem die Ressource erfolgreich erstellt und bereitgestellt wurde, erhalten Sie eine Benachrichtigung. Wählen Sie **Zu Ressource wechseln** aus.

![Navigieren zur Azure Stack Edge-Ressource](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Nach der Bestellung wird diese von Microsoft geprüft, und Sie erhalten eine E-Mail mit den Versanddetails.

![Benachrichtigung zur Überprüfung der Azure Stack Edge Pro-Bestellung](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Abrufen des Aktivierungsschlüssels

Wenn die Azure Stack Edge-Ressource betriebsbereit ist, müssen Sie den Aktivierungsschlüssel abrufen. Dieser Schlüssel wird verwendet, um Ihr Azure Stack Edge Pro-Gerät zu aktivieren und mit der Ressource zu verbinden. Sie können diesen Schlüssel jetzt abrufen, während Sie sich im Azure-Portal befinden.

1. Wählen Sie die von Ihnen erstellte Ressource aus. Wählen Sie **Übersicht** und anschließend **Geräteeinrichtung** aus.

    ![Auswählen von „Geräteeinrichtung“](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Wählen Sie auf der Kachel **Aktivieren** die Option **Schlüssel generieren** aus, um einen Aktivierungsschlüssel zu erstellen. Wählen Sie das Kopiersymbol, um den Schlüssel zu kopieren und für die spätere Verwendung zu speichern.

    ![Abrufen des Aktivierungsschlüssels](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * Der Aktivierungsschlüssel läuft drei Tage nach seiner Generierung ab.
> * Generieren Sie einen neuen Schlüssel, falls der Schlüssel abgelaufen ist. Der ältere Schlüssel ist nicht mehr gültig.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Azure Stack Edge Pro-Themen behandelt:

> [!div class="checklist"]
>
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

Im nächsten Tutorial erfahren Sie, wie Sie Azure Stack Edge Pro installieren.

> [!div class="nextstepaction"]
> [Installieren von Azure Stack Edge Pro](./azure-stack-edge-deploy-install.md)