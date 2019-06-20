---
title: Tutorial zum Vorbereiten des Azure-Portals, Datencenterumgebung für das Bereitstellen von Azure Data Box Edge | Microsoft-Dokumentation
description: Im ersten Tutorial über Bereitstellen von Azure Data Box Edge geht es um die Vorbereitung des Azure-Portals.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 364ceabc3c9e7a577bd126b81bcd5256d947bbe3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476775"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Tutorial: Vorbereiten der Bereitstellung von Azure Data Box Edge  

Dies ist das erste Tutorial einer Reihe von Bereitstellungstutorials, die zum vollständigen Bereitstellen von Azure Data Box Edge erforderlich sind. In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal für die Bereitstellung einer Data Box Edge-Ressource vorbereiten.

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Die Vorbereitung des Portals erfordert weniger als 10 Minuten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

### <a name="get-started"></a>Erste Schritte

Führen Sie zum Bereitstellen von Data Box Edge die folgenden Tutorials in der angegebenen Reihenfolge aus.

| **#** | **Schritt** | **Dokumente** |
| --- | --- | --- | 
| 1. |**[Vorbereiten des Azure-Portals für Data Box Edge](data-box-edge-deploy-prep.md)** |Erstellen und konfigurieren Sie Ihre Data Box Edge-Ressource, bevor Sie ein physisches Data Box Edge-Gerät installieren. |
| 2. |**[Installieren von Data Box Edge](data-box-edge-deploy-install.md)**|Packen Sie das physische Data Box Edge-Gerät aus, montieren Sie es in einem Rack, und verkabeln Sie es.  |
| 3. |**[Verbinden, Einrichten und Aktivieren von Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche her, führen Sie die Einrichtung durch, und aktivieren Sie das Gerät. Das Gerät ist für die Einrichtung von SMB- oder NFS-Freigaben bereit.  |
| 4. |**[Übertragen von Daten mit Data Box Edge](data-box-edge-deploy-add-shares.md)** |Fügen Sie Freigaben hinzu, und stellen Sie über SMB oder NFS eine Verbindung mit Freigaben her. |
| 5. |**[Transformieren von Daten mit Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Konfigurieren Sie Computemodule auf dem Gerät, um die Daten bei der Übertragung an Azure zu transformieren. |

Sie können jetzt mit der Einrichtung des Azure-Portals beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Nachstehend finden Sie die Konfigurationsvoraussetzungen für die Data Box Edge-Ressource, das Data Box Edge-Gerät und das Netzwerk des Datencenters.

### <a name="for-the-data-box-edge-resource"></a>Für die Data Box Edge-Ressource

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Ihr Microsoft Azure-Abonnement ist für eine Data Box Edge-Ressource aktiviert. Abonnements mit nutzungsbasierter Bezahlung werden nicht unterstützt.
- Sie verfügen für die Data Box Edge-/Data Box Gateway-, IoT Hub- und Azure Storage-Ressourcen über Zugriff als Besitzer oder Mitwirkender auf der Ressourcengruppenebene.

    - Für die Erstellung von Data Box Edge-/Data Box Gateway-Ressourcen müssen Sie mindestens über Berechtigungen als Mitwirkender auf der Ressourcengruppenebene verfügen. Vergewissern Sie sich außerdem, dass der Anbieter `Microsoft.DataBoxEdge` registriert ist. Eine Registrierungsanleitung finden Sie unter [Register resource provider](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers) (Registrieren des Ressourcenanbieters).
    - Für die Erstellung von IoT Hub-Ressourcen muss der Anbieter „Microsoft.Devices“ registriert sein. Eine Registrierungsanleitung finden Sie unter [Register resource provider](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers) (Registrieren des Ressourcenanbieters).
    - Für die Erstellung von Speicherkontoressourcen sind ebenfalls mindestens Berechtigungen als Mitwirkender auf der Ressourcengruppenebene erforderlich. Azure Storage ist standardmäßig als Ressourcenanbieter registriert.
- Sie haben als Administrator oder Benutzer Zugriff auf die Azure Active Directory Graph-API. Weitere Informationen finden Sie unter [Azure Active Directory Graph-API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.

### <a name="for-the-data-box-edge-device"></a>Für das Data Box Edge-Gerät

Stellen Sie Folgendes sicher, bevor Sie ein physisches Gerät bereitstellen:

- Sie haben sich mit den beiliegenden Sicherheitsinformationen vertraut gemacht.
- Sie verfügen in Ihrem Datencenter über einen freien Einschub (1 HE) in einem 19-Zoll-Standardrack zur Rackmontage des Geräts.
- Sie haben Zugang zu einer flachen, stabilen und ebenen Arbeitsfläche, auf der das Gerät sicher abgelegt werden kann.
- Der Standort, an dem Sie das Gerät einrichten möchten, hat eine Standardstromversorgung von einer unabhängigen Quelle oder eine Rack-PDU (Power Distribution Unit) mit unterbrechungsfreier Stromversorgung (USV).
- Sie verfügen über ein physisches Gerät.


### <a name="for-the-datacenter-network"></a>Für das Netzwerk des Rechenzentrums

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Das Netzwerk in Ihrem Datencenter ist gemäß den Netzwerkanforderungen für das Data Box Edge-Gerät konfiguriert. Weitere Informationen finden Sie unter [Systemanforderungen für Azure Data Box Edge](data-box-edge-system-requirements.md).

- Für den Normalbetrieb von Data Box Edge benötigen Sie Folgendes:

    - Eine Downloadbandbreite von mindestens 10 MBit/s, um sicherzustellen, dass das Gerät auf dem neuesten Stand bleibt.
    - Eine dedizierte Upload- und Downloadbandbreite von mindestens 20 MBit/s zum Übertragen von Dateien.

## <a name="create-a-new-resource"></a>Neue Ressource erstellen

Falls Sie bereits über eine Data Box Edge-Ressource für die Verwaltung Ihres physischen Geräts verfügen, überspringen Sie diesen Schritt, und fahren Sie mit [Abrufen des Aktivierungsschlüssels](#get-the-activation-key) fort.

Um eine Data Box Edge-Ressource zu erstellen, führen Sie die folgenden Schritte im Azure-Portal aus.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen bei einem der folgenden Portale an: 
    
    - Azure-Portal (URL: [https://portal.azure.com](https://portal.azure.com))
    - Azure Government-Portal (URL: [https://portal.azure.us](https://portal.azure.us)). Ausführlichere Informationen finden Sie unter [Quickstart: Connect to Azure Government using portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal) (Schnellstart: Herstellen einer Verbindung mit Azure Government über das Portal).

2. Wählen Sie im linken Bereich **+ Ressource erstellen** aus. Suchen Sie nach **Data Box Edge/Data Box Gateway**. Wählen Sie **Data Box Edge/Data Box Gateway** aus. Klicken Sie auf **Erstellen**.
3. Wählen Sie das Abonnement aus, das Sie für das Data Box Edge-Gerät verwenden möchten. Wählen Sie die Region aus, in der Sie die Data Box Edge-Ressource bereitstellen möchten. Für dieses Release sind „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“ verfügbar. 

    Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist. Die Region speichert nur die Metadaten für die Geräteverwaltung. Die tatsächlichen Daten können in jedem Speicherkonto gespeichert werden. 
    
    Wählen Sie in der Option **Data Box Edge** den Eintrag **Erstellen** aus.

    ![Suchen nach dem Data Box Edge-Dienst](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Geben Sie auf der Registerkarte **Grundlagen** die folgenden **Projektdetails** ein, bzw. wählen Sie sie aus:
    
    |Einstellung  |Wert  |
    |---------|---------|
    |Abonnement    |Dieser Wert wird auf der Grundlage Ihrer zuvor getroffenen Auswahl automatisch aufgefüllt. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. |
    |Ressourcengruppe  |Wählen Sie eine vorhandene Gruppe aus, oder erstellen Sie eine neue Gruppe.<br>Erfahren Sie mehr über [Azure-Ressourcengruppen](../azure-resource-manager/resource-group-overview.md).     |

4. Geben Sie die folgenden **Instanzendetails** ein, bzw. wählen Sie sie aus:

    |Einstellung  |Wert  |
    |---------|---------|
    |NAME   | Ein Anzeigename, der die Ressource identifiziert.<br>Der Name muss 2 bis 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten.<br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.        |
    |Region     |In diesem Release sind „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“ für die Ressourcenbereitstellung verfügbar. Die für Azure Government verfügbaren Regionen finden Sie bei Bedarf unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist.|

    ![Projekt- und Instanzendetails](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Klicken Sie auf **Weiter: Lieferanschrift**.

    - Falls Sie bereits über ein Gerät verfügen, aktivieren Sie das Kontrollkästchen **Ich habe ein Data Box Edge-Gerät.** .
    - Handelt es sich um das neue Gerät, das Sie bestellen möchten, geben Sie Kontaktname, Unternehmen, Lieferadresse für das Gerät sowie Kontaktinformationen ein.

    ![Lieferadresse für neues Gerät](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Klicken Sie auf **Weiter: Überprüfen + erstellen**.

7. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die **Preisdetails**, die **Nutzungsbedingungen** und die Details für Ihre Ressource. Aktivieren Sie das Kontrollkästchen **Ich habe die angegebenen Informationen gelesen und stimme den Datenschutzbestimmungen zu.** .

    ![Überprüfen der Details und Datenschutzrichtlinien für Ihre Data Box Edge-Ressource](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Klicken Sie auf **Erstellen**.

Die Erstellung der Ressource dauert einige Minuten. Nachdem die Ressource erfolgreich erstellt und bereitgestellt wurde, erhalten Sie eine Benachrichtigung. Wählen Sie **Zu Ressource wechseln** aus.

![Navigieren zur Data Box Edge-Ressource](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Nach der Bestellung wird diese von Microsoft geprüft, und Sie erhalten eine E-Mail mit den Versanddetails.

![Benachrichtigung zur Überprüfung der Data Box Edge-Bestellung](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Abrufen des Aktivierungsschlüssels

Wenn die Data Box Edge-Ressource betriebsbereit ist, müssen Sie den Aktivierungsschlüssel abrufen. Dieser Schlüssel wird verwendet, um Ihr Data Box Edge-Gerät zu aktivieren und mit der Ressource zu verbinden. Sie können diesen Schlüssel jetzt abrufen, während Sie sich im Azure-Portal befinden.

1. Wählen Sie die von Ihnen erstellte Ressource aus. Wählen Sie **Übersicht** und anschließend **Geräteeinrichtung** aus.

    ![Auswählen von „Geräteeinrichtung“](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Wählen Sie auf der Kachel **Aktivieren** die Option **Schlüssel generieren** aus, um einen Aktivierungsschlüssel zu erstellen. Wählen Sie das Kopiersymbol, um den Schlüssel zu kopieren und für die spätere Verwendung zu speichern.

    ![Abrufen des Aktivierungsschlüssels](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Der Aktivierungsschlüssel läuft drei Tage nach seiner Generierung ab.
> - Generieren Sie einen neuen Schlüssel, falls der Schlüssel abgelaufen ist. Der ältere Schlüssel ist nicht mehr gültig.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Data Box Edge-Themen behandelt:

> [!div class="checklist"]
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

Im nächsten Tutorial erfahren Sie, wie Sie Data Box Edge installieren.

> [!div class="nextstepaction"]
> [Installieren von Data Box Edge](./data-box-edge-deploy-install.md)



