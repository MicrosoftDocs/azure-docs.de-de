---
title: Bereitstellen eines Azure Firewall Manager-Sicherheitspartneranbieters
description: Erfahren Sie, wie Sie über das Azure-Portal einen vertrauenswürdigen Sicherheitspartneranbieter für Azure Firewall Manager erstellen.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 906687e08c9f31890a9ecec9154079e704512832
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485721"
---
# <a name="deploy-a-security-partner-provider"></a>Bereitstellen eines Sicherheitspartneranbieters

Mit *Sicherheitspartneranbietern* in Azure Firewall Manager können Sie Ihre vertrauten erstklassigen SECaaS-Angebote (Security-as-a-Service) von Drittanbietern verwenden, um den Internetzugriff für Ihre Benutzer zu schützen.

Weitere Informationen zu unterstützten Szenarien und Best Practices finden Sie unter [Was sind vertrauenswürdige Sicherheitspartner (Vorschau)?](trusted-security-partners.md).


Integrierte SECaaS-Drittanbieterpartner (Security-as-a-Service) sind jetzt verfügbar: 

- **Zscaler**
- **[Check Point](check-point-overview.md)**
- **iboss**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Bereitstellen eines Drittanbieters für die Sicherheit in einem neuen Hub

Überspringen Sie diesen Abschnitt, wenn Sie einen Drittanbieter in einem vorhandenen Hub bereitstellen.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
2. Geben Sie im Feld **Suche** den Text **Firewall Manager** ein, und wählen Sie diesen Eintrag unter **Dienste** aus.
3. Navigieren Sie zu **Erste Schritte**. Wählen Sie **Geschützte virtuelle Hubs anzeigen**.
4. Wählen Sie **Neuen geschützten virtuellen Hub erstellen**.
5. Geben Sie Ihr Abonnement und Ihre Ressourcengruppe ein, wählen Sie eine unterstützte Region aus, und fügen Sie Informationen zu Ihrem Hub und Virtual WAN hinzu. 
6. Wählen Sie **VPN Gateway zum Aktivieren von Sicherheitspartneranbietern einbeziehen**.
7. Wählen Sie die **Gatewayskalierungseinheiten** Ihren Anforderungen entsprechend aus.
8. Klicken Sie auf **Weiter: Azure Firewall**.
   > [!NOTE]
   > Sicherheitspartneranbieter stellen über VPN-Gateway-Tunnel eine Verbindung mit Ihrem Hub her. Wenn Sie das VPN-Gateway löschen, gehen die Verbindungen mit Ihren Sicherheitspartneranbietern verloren.
9. Wenn Sie zusätzlich zu einer Sicherheitslösung eines Drittanbieters zum Filtern des Internetdatenverkehrs Azure Firewall bereitstellen möchten, um privaten Datenverkehr zu filtern, wählen Sie eine Richtlinie für Azure Firewall aus. Informationen dazu finden Sie in den [unterstützten Szenarien](trusted-security-partners.md#key-scenarios).
10. Wenn Sie nur die Sicherheitslösung eines Drittanbieters im Hub bereitstellen möchten, wählen Sie Folgendes aus: **Azure Firewall: aktiviert/deaktiviert**, und legen Sie die Einstellung auf **Deaktiviert** fest. 
11. Klicken Sie auf **Weiter: Sicherheitspartneranbieter**.
12. Setzen Sie **Sicherheitspartneranbieter** auf **Aktiviert**. 
13. Wählen Sie einen Partner aus. 
14. Klicken Sie auf **Weiter: Überprüfen + erstellen**. 
15. Überprüfen Sie den Inhalt, und klicken Sie dann auf **Erstellen**.

Die Bereitstellung des VPN-Gateways kann mehr als 30 Minuten in Anspruch nehmen.

Um zu überprüfen, ob das Hub erstellt wurde, navigieren Sie zu „Azure Firewall Manager“ > „Geschützte Hubs“. Wählen Sie den Hub und darin die Seite „Übersicht“ aus, um den Partnernamen und den Status **Sicherheitsverbindung ausstehend** anzuzeigen.

Wenn der Hub erstellt und der Sicherheitspartner eingerichtet wurde, stellen Sie eine Verbindung zwischen dem Sicherheitsanbieter und dem Hub her.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Bereitstellen eines Drittanbieters für die Sicherheit in einem vorhandenen Hub

Sie können auch einen vorhandenen Hub in einem Virtual WAN auswählen und diesen in einen *geschützten virtuellen Hub* konvertieren.

1. Wählen Sie in **Erste Schritte** die Option **Geschützte virtuelle Hubs anzeigen**.
2. Wählen Sie **Vorhandene Hubs konvertieren**.
3. Wählen Sie ein Abonnement und einen vorhandenen Hub aus. Führen Sie die restlichen Schritte in der Vorgehensweise zum Bereitstellen eines Drittanbieters in einem neuen Hub aus.

Denken Sie daran, dass ein VPN-Gateway bereitgestellt sein muss, um einen vorhandenen Hub in einen geschützten Hub mit Drittanbietern zu konvertieren.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Konfigurieren von Drittanbietern für die Sicherheit zum Herstellen einer Verbindung mit einem geschützten Hub

Um Tunnel zum VPN-Gateway Ihres virtuellen Hubs einzurichten, benötigen Drittanbieter Zugriffsrechte für Ihren Hub. Weisen Sie zu diesem Zweck einen Dienstprinzipal mit Ihrem Abonnement oder Ihrer Ressourcengruppe zu, und gewähren Sie diesem Zugriffsrechte. Diese Anmeldeinformationen müssen Sie dann dem Drittanbieter über dessen Portal mitteilen.

### <a name="create-and-authorize-a-service-principal"></a>Erstellen und Autorisieren eines Dienstprinzipals

1. Erstellen Sie einen Azure Active Directory-Dienstprinzipal: Sie können die Umleitungs-URL überspringen. 

   [Vorgehensweise: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Fügen Sie Zugriffsrechte und einen Bereich für den Dienstprinzipal hinzu.
   [Vorgehensweise: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > Zur genaueren Steuerung können Sie den Zugriff auf Ihre Ressourcengruppe beschränken.

### <a name="visit-partner-portal"></a>Besuch im Partnerportal

1. Befolgen Sie die Anweisungen Ihres Partners, um das Setup abzuschließen. Dies umfasst das Übermitteln von AAD-Informationen zum Erkennen des Hubs und Herstellen einer Verbindung mit ihm, zum Aktualisieren der Richtlinien für ausgehenden Datenverkehr und zum Überprüfen von Konnektivitätsstatus und Protokollen.

   - [Zscaler: Konfigurieren einer Microsoft Azure Virtual WAN-Integration](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point: Konfigurieren einer Microsoft Azure Virtual WAN-Integration](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [iboss: Konfigurieren einer Microsoft Azure Virtual WAN-Integration](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. Im Azure Virtual WAN-Portal in Azure können Sie den Status der Tunnelerstellung anzeigen. Sobald die Tunnel sowohl im Azure-Portal als auch im Partnerportal **verbunden** anzeigen, fahren Sie mit den nächsten Schritten fort, um Routen einzurichten, mit denen ausgewählt wird, welche Zweigstellen und VNETs Internetdatenverkehr an den Partner senden sollen.

## <a name="configure-route-settings"></a>Konfigurieren von Routeneinstellungen

1. Navigieren Sie zu „Azure Firewall Manager“ > „Geschützte Hubs“. 
2. Wählen Sie einen Hub aus. Der Hubstatus sollte jetzt als **Bereitgestellt** angezeigt werden, nicht mehr als **Sicherheitsverbindung ausstehend**.

   Stellen Sie sicher, dass der Drittanbieter eine Verbindung mit dem Hub herstellen kann. Die Tunnel im VPN-Gateway sollten den Status **Verbunden** aufweisen. Dieser Status spiegelt die Verbindungsintegrität zwischen Hub und Drittanbieter besser wider als der vorherige Status.
3. Wählen Sie den Hub aus, und navigieren Sie zu **Routeneinstellungen**.

   Wenn Sie einen Drittanbieter im Hub bereitstellen, wird der Hub in einen *geschützten virtuellen Hub* konvertiert. So ist sichergestellt, dass der Drittanbieter eine (standardmäßige) Route von 0.0.0.0/0 an den Hub sendet. VNET-Verbindungen und mit dem Hub verbundene Standorte erhalten diese Route jedoch nicht, es sei denn, Sie wählen die Verbindungen aus, die diese Standardroute erhalten sollen.
4. Wählen Sie unter **Internetdatenverkehr** die Option **VNET-to-Internet** oder **Branch-to-Internet** oder beide aus, sodass Routen zum Senden über den Drittanbieter konfiguriert sind.

   Hiermit wird nur angegeben, welche Art von Datenverkehr an den Hub geleitet werden soll, es gibt noch keine Auswirkungen auf die Routen in VNETs oder Zweigstellen. Diese Routen werden nicht standardmäßig an alle VNETs/Zweigstellen weitergegeben, die an den Hub angeschlossen sind.
5. Sie müssen auf **sichere Verbindungen** klicken und diejenigen Verbindungen auswählen, für die diese Routen festgelegt werden sollen. Dies gibt an, welche VNETs oder Zweigstellen Internetdatenverkehr an den Drittanbieter senden dürfen.
6. Wählen Sie bei den **Routeneinstellungen** unter „Internetdatenverkehr“ die Option **Sichere Verbindungen** aus, und wählen Sie dann das VNET oder die Zweigstellen (*Standorte* in Virtual WAN) aus, die gesichert werden sollen. Wählen Sie **Sicherer Internetdatenverkehr** aus.
   ![Sicherer Internetdatenverkehr](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Kehren Sie zur Seite „Hubs“ zurück. Der Status **Sicherheitspartneranbieter** des Hubs sollte jetzt **Gesichert** sein.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Zweigstellen- oder VNET-Internetdatenverkehr über Drittanbieterdienst

Als Nächstes können Sie überprüfen, ob die virtuellen Computer im VNET oder am Zweigstellenstandort auf das Internet zugreifen können und ob der Datenverkehr an den Drittanbieterdienst geleitet wird.

Nachdem die Schritte zur Routenfestlegung ausgeführt wurden, wird sowohl an die virtuellen VNET-Computer als auch an die Zweigstellenstandorte eine 0/0-Route zum Drittanbieterdienst gesendet. Sie können keine RDP- oder SSH-Verbindung mit diesen virtuellen Computern herstellen. Für die Anmeldung können Sie den [Azure Bastion](../bastion/bastion-overview.md)-Dienst in einem Peering-VNET bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Schützen Ihres Cloudnetzwerks mithilfe von Azure Firewall Manager unter Verwendung des Azure-Portals](secure-cloud-network.md)