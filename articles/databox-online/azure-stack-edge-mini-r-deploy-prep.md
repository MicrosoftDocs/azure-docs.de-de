---
title: Tutorial zum Vorbereiten des Azure-Portals und der Rechenzentrumsumgebung für die Bereitstellung eines Azure Stack Edge Mini R-Geräts | Microsoft-Dokumentation
description: Im ersten Tutorial zum Bereitstellen eines Azure Stack Edge Mini R-Geräts geht es um die Vorbereitung des Azure-Portals.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Mini R device so I can use it to transfer data to Azure.
ms.openlocfilehash: 54ac683dcc2b124c4a6410b2e8449fa7e969ce8c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464190"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Tutorial: Vorbereiten der Bereitstellung von Azure Stack Edge Mini R

Dies ist das erste Tutorial einer Reihe von Bereitstellungstutorials, die zum vollständigen Bereitstellen eines Azure Stack Edge Mini R-Geräts erforderlich sind. In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal für die Bereitstellung einer Azure Stack Edge-Ressource vorbereiten.

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Die Vorbereitung des Portals erfordert weniger als 10 Minuten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

### <a name="get-started"></a>Erste Schritte

Führen Sie zum Bereitstellen von Azure Stack Edge Mini R die folgenden Tutorials in der angegebenen Reihenfolge aus.

| Schritt | BESCHREIBUNG |
| --- | --- |
| **Vorbereitung** |Diese Schritte müssen zur Vorbereitung der anstehenden Bereitstellung ausgeführt werden. |
| **[Konfigurationsprüfliste für die Bereitstellung](#deployment-configuration-checklist)** |Verwenden Sie diese Prüfliste, um vor und während der Bereitstellung Informationen zu erfassen und aufzuzeichnen. |
| **[Voraussetzungen für die Bereitstellung](#prerequisites)** |Mit diesen Schritten überprüfen Sie, ob die Umgebung für die Bereitstellung vorbereitet ist. |
|  | |
|**Tutorials für die Bereitstellung** |Diese Tutorials sind erforderlich, um Ihr Azure Stack Edge Mini R-Gerät in der Produktionsumgebung bereitzustellen. |
|**[1. Vorbereiten des Azure-Portals für das Gerät](azure-stack-edge-mini-r-deploy-prep.md)** |Erstellen und konfigurieren Sie Ihre Azure Stack Edge-Ressource, bevor Sie das physische Gerät installieren. |
|**[2. Installieren des Geräts](azure-stack-edge-mini-r-deploy-install.md)**|Packen Sie Ihr physisches Gerät aus, installieren Sie es in einem Rack, und verkabeln Sie es.  |
|**[3. Herstellen einer Verbindung mit dem Gerät](azure-stack-edge-mini-r-deploy-connect.md)** |Sobald das Gerät installiert ist, stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts her.  |
|**[4. Konfigurieren der Netzwerkeinstellungen](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Konfigurieren Sie das Netzwerk einschließlich Computenetzwerk und Webproxyeinstellungen für Ihr Gerät.   |
|**[5. Konfigurieren der Geräteeinstellungen](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Weisen Sie einen Gerätenamen und eine DNS-Domäne zu, und konfigurieren Sie den Updateserver und die Gerätezeit. |
|**[6. Konfigurieren der Sicherheitseinstellungen](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Konfigurieren Sie Zertifikate unter Verwendung Ihrer eigenen Zertifikate, richten Sie VPN ein, und konfigurieren Sie die Verschlüsselung ruhender Daten für Ihr Gerät.   |
|**[7. Aktivieren des Geräts](azure-stack-edge-mini-r-deploy-activate.md)** |Verwenden Sie den Aktivierungsschlüssel des Diensts, um das Gerät zu aktivieren. Das Gerät ist bereit, SMB- oder NFS-Freigaben einzurichten oder eine Verbindung über REST herzustellen. |
|**[8. Konfigurieren von Compute](azure-stack-edge-gpu-deploy-configure-compute.md)** |Konfigurieren Sie die Computerolle auf Ihrem Gerät. Dabei wird auch ein Kubernetes-Cluster erstellt. |

Sie können jetzt mit der Einrichtung des Azure-Portals beginnen.

## <a name="deployment-configuration-checklist"></a>Konfigurationsprüfliste für die Bereitstellung

Vor der Bereitstellung Ihres Geräts müssen zunächst Informationen gesammelt werden, um die Software auf Ihrem Azure Stack Edge Mini R-Gerät konfigurieren zu können. Wenn Sie einige dieser Informationen im Voraus sammeln, können Sie das Gerät in Ihrer Umgebung schneller bereitstellen. Verwenden Sie die [Konfigurationsprüfliste für die Bereitstellung von Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-checklist.md), um die Konfigurationsdetails während der Bereitstellung Ihres Geräts zu notieren.

## <a name="prerequisites"></a>Voraussetzungen

Nachstehend finden Sie die Konfigurationsvoraussetzungen für die Azure Stack Edge-Ressource, das Azure Stack Edge-Gerät und das Netzwerk des Rechenzentrums.

### <a name="for-the-azure-stack-edge-resource"></a>Für die Azure Stack Edge-Ressource

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Für das Azure Stack Edge-Gerät

Stellen Sie Folgendes sicher, bevor Sie ein physisches Gerät bereitstellen:

- Sie haben sich anhand der [Sicherheitsrichtlinien für Ihr Azure Stack Edge-Gerät](azure-stack-edge-mini-r-safety.md) mit den Sicherheitsinformationen für dieses Gerät vertraut gemacht.
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Für das Netzwerk des Rechenzentrums

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Das Netzwerk in Ihrem Rechenzentrum ist gemäß den Netzwerkanforderungen für das Azure Stack Edge-Gerät konfiguriert. Weitere Informationen finden Sie unter [Systemanforderungen für Azure Stack Edge Mini R](azure-stack-edge-mini-r-system-requirements.md).

- Für den Normalbetrieb von Azure Stack Edge benötigen Sie Folgendes:

    - Eine Downloadbandbreite von mindestens 10 MBit/s, um sicherzustellen, dass das Gerät auf dem neuesten Stand bleibt.
    - Eine dedizierte Upload- und Downloadbandbreite von mindestens 20 MBit/s zum Übertragen von Dateien.

## <a name="create-a-new-resource"></a>Neue Ressource erstellen

Falls Sie bereits über eine Azure Stack Edge-Ressource für die Verwaltung Ihres physischen Geräts verfügen, überspringen Sie diesen Schritt, und fahren Sie mit [Abrufen des Aktivierungsschlüssels](#get-the-activation-key) fort.

Um eine Azure Stack Edge-Ressource zu erstellen, führen Sie die folgenden Schritte im Azure-Portal aus:

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen beim Azure-Portal unter der folgenden URL an: [https://portal.azure.com](https://portal.azure.com).


2. Wählen Sie im linken Bereich **+ Ressource erstellen** aus. Suchen Sie nach **Azure Stack Edge/Data Box Gateway**, und wählen Sie den Eintrag aus. Klicken Sie auf **Erstellen**. 

3. Wählen Sie das Abonnement aus, das Sie für das Azure Stack Edge Pro-Gerät verwenden möchten. Wählen Sie das Land/die Region aus, in das/die das physische Gerät versendet werden soll. Wählen Sie **Geräte anzeigen** aus.

    ![Ressource erstellen 1](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. Wählen Sie den Gerätetyp aus. Wählen Sie unter **Azure Stack Edge** die Option **Azure Stack Edge Mini R** und anschließend **Auswählen** aus. Wenn Probleme auftreten oder Sie den Gerätetyp nicht auswählen können, finden Sie unter [Behandeln von Problemen](azure-stack-edge-troubleshoot-ordering.md) weitere Informationen.

    [![Ressource erstellen 2](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


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

    ![Ressource erstellen 4](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


8. Klicken Sie auf **Weiter: Lieferanschrift**.

    - Falls Sie bereits über ein Gerät verfügen, aktivieren Sie das Kontrollkästchen **Ich besitze bereits ein Gerät.** .

        ![Ressource erstellen5](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

    - Handelt es sich um das neue Gerät, das Sie bestellen möchten, geben Sie Kontaktname, Unternehmen, Lieferadresse für das Gerät sowie Kontaktinformationen ein.

        ![Ressource erstellen 6](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

9. Klicken Sie auf **Weiter: Tags**. Geben Sie optional Tags an, um Ressourcen zu kategorisieren und die Abrechnung zu konsolidieren. Klicken Sie auf **Weiter: Überprüfen + erstellen**.

10. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die **Preisdetails**, die **Nutzungsbedingungen** und die Details für Ihre Ressource. Aktivieren Sie das Kontrollkästchen **Ich habe die angegebenen Informationen gelesen und stimme den Datenschutzbestimmungen zu.** .

    ![Ressource erstellen 7](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png) 

    Sie werden auch darüber informiert, dass während der Ressourcenerstellung eine verwaltete Dienstidentität (Managed Service Identity, MSI) aktiviert wird, mit der Sie sich bei Clouddiensten authentifizieren können. Diese Identität bleibt so lange bestehen, wie die Ressource vorhanden ist.

8. Klicken Sie auf **Erstellen**.

    Die Erstellung der Ressource dauert einige Minuten. Außerdem wird eine MSI-Datei erstellt, die die Kommunikation des Azure Stack Edge-Geräts mit dem Ressourcenanbieter in Azure ermöglicht.
    
    Nachdem die Ressource erfolgreich erstellt und bereitgestellt wurde, erhalten Sie eine Benachrichtigung. Wählen Sie **Zu Ressource wechseln** aus.
    
    ![Navigieren zur Azure Stack Edge Pro-Ressource](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
    Nach der Bestellung wird diese von Microsoft geprüft, und Sie erhalten eine E-Mail mit den Versanddetails.

   Wenn Sie während des Bestellvorgangs auf Probleme stoßen, informieren Sie sich unter [Behandeln von Bestellproblemen](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Abrufen des Aktivierungsschlüssels

Wenn die Azure Stack Edge-Ressource betriebsbereit ist, müssen Sie den Aktivierungsschlüssel abrufen. Dieser Schlüssel wird verwendet, um Ihr Azure Stack Edge Mini R-Gerät zu aktivieren und mit der Ressource zu verbinden. Sie können diesen Schlüssel jetzt abrufen, während Sie sich im Azure-Portal befinden.

1. Wählen Sie die von Ihnen erstellte Ressource aus. Wählen Sie **Übersicht** und anschließend **Geräteeinrichtung** aus.

    ![Auswählen von „Geräteeinrichtung“](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. Geben Sie auf der Kachel **Aktivieren** einen Namen für Azure Key Vault an, oder übernehmen Sie den Standardnamen. Der Schlüsseltresorname kann aus 3 bis 24 Zeichen bestehen. 

    Für jede mit Ihrem Gerät aktivierte Azure Stack Edge-Ressource wird ein Schlüsseltresor erstellt. Im Schlüsseltresor können Sie Geheimnisse speichern und darauf zugreifen. So wird beispielsweise der Channel Integrity Key (CIK) für den Dienst im Schlüsseltresor gespeichert. 

    Nachdem Sie einen Schlüsseltresornamen angegeben haben, wählen Sie **Schlüssel generieren** aus, um einen Aktivierungsschlüssel zu erstellen. 

    [![Abrufen des Aktivierungsschlüssels](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    Warten Sie einige Minuten, während Schlüsseltresor und Aktivierungsschlüssel erstellt werden. Wählen Sie das Kopiersymbol, um den Schlüssel zu kopieren und für die spätere Verwendung zu speichern.

> [!IMPORTANT]
> - Der Aktivierungsschlüssel läuft drei Tage nach seiner Generierung ab.
> - Generieren Sie einen neuen Schlüssel, falls der Schlüssel abgelaufen ist. Der ältere Schlüssel ist nicht mehr gültig.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Azure Stack Edge-Themen behandelt:

> [!div class="checklist"]
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

Im nächsten Tutorial erfahren Sie, wie Sie Azure Stack Edge installieren.

> [!div class="nextstepaction"]
> [Installieren von Azure Stack Edge](./azure-stack-edge-mini-r-deploy-install.md)