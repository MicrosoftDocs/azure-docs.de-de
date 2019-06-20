---
title: 'Herstellen einer Point-to-Site-Verbindung zwischen einem Computer und einem virtuellen Azure-Netzwerk unter Verwendung der nativen Azure-Zertifikatauthentifizierung: Azure-Portal | Microsoft-Dokumentation'
description: Verbinden Sie Windows-, Mac OS X- und Linux-Clients auf sichere Weise mit einem virtuellen Azure-Netzwerk, indem Sie eine P2S-Verbindung und selbstsignierte oder von einer Zertifizierungsstelle ausgestellte Zertifikate verwenden. In diesem Artikel wird das Azure-Portal verwendet.
services: vpn-gateway
author: cherylmc
tags: azure-resource-manager
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 5/21/2019
ms.author: cherylmc
ms.openlocfilehash: 6b66a9cf28faccf5ba22bc016297f1d53febe533
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157317"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Konfigurieren einer Point-to-Site-Verbindung mit einem VNET unter Verwendung der nativen Azure-Zertifikatauthentifizierung: Azure-Portal

Dieser Artikel enthält Informationen zum sicheren Verbinden von einzelnen Clients, auf denen Windows, Linux oder Mac OS X ausgeführt wird, mit einem Azure-VNET. Eine P2S-VPN-Verbindung ist nützlich, wenn Sie an einem Remotestandort (beispielsweise bei der Telearbeit zu Hause oder in einer Konferenz) eine Verbindung mit Ihrem VNET herstellen möchten. Sie können anstelle einer Site-to-Site-VPN-Verbindung auch P2S verwenden, wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen. Point-to-Site-Verbindungen erfordern weder ein VPN-Gerät noch eine öffentliche IP-Adresse. P2S erstellt die VPN-Verbindung entweder über SSTP (Secure Socket Tunneling Protocol) oder IKEv2. Weitere Informationen zu Point-to-Site-VPN-Verbindungen finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md).

![Herstellen einer Verbindung zwischen einem Computer und einem Azure VNet – Point-to-Site-Verbindungsdiagramm](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)


## <a name="architecture"></a>Architecture

Für native Point-to-Site-Verbindungen mit Azure-Zertifikatauthentifizierung werden die folgenden Komponenten verwendet, die Sie in dieser Übung konfigurieren:

* Ein RouteBased-VPN-Gateway.
* Der öffentliche Schlüssel (CER-Datei) für ein Stammzertifikat (in Azure hochgeladen). Sobald das Zertifikat hochgeladen wurde, wird es als vertrauenswürdiges Zertifikat betrachtet und für die Authentifizierung verwendet.
* Ein Clientzertifikat, das von der Stammzertifizierungsstelle generiert wird. Das auf jedem Clientcomputer, für den eine Verbindung mit dem VNET hergestellt wird, installierte Clientzertifikat. Dieses Zertifikat wird für die Clientauthentifizierung verwendet.
* Eine VPN-Clientkonfiguration. Die VPN-Clientkonfigurationsdateien enthalten die erforderlichen Informationen, damit der Client eine Verbindung mit dem VNET herstellen kann. Die Dateien konfigurieren den vorhandenen nativen VPN-Client des Betriebssystems. Jeder Client, der eine Verbindung herstellt, muss mit den Einstellungen in den Konfigurationsdateien konfiguriert werden.

#### <a name="example"></a>Beispielwerte

Sie können die folgenden Werte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel nutzen:

* **VNet-Name:** VNet1
* **Adressraum:** 192.168.0.0/16<br>In diesem Beispiel verwenden wir nur einen einzelnen Adressraum. Sie können für Ihr VNet aber auch mehrere Adressräume verwenden.
* **Subnetzname:** FrontEnd
* **Subnetzadressbereich:** 192.168.1.0/24
* **Abonnement:** Falls Sie über mehrere Abonnements verfügen, vergewissern Sie sich, dass Sie das richtige Abonnement verwenden.
* **Ressourcengruppe:** TestRG
* **Standort:** USA (Ost)
* **GatewaySubnet:** 192.168.200.0/24<br>
* **DNS-Server:** (optional) IP-Adresse des DNS-Servers, der für die Namensauflösung verwendet werden soll
* **Name des Gateways des virtuellen Netzwerks:** VNet1GW
* **Gatewaytyp**: VPN
* **VPN-Typ:** routenbasiert
* **Öffentliche IP-Adresse:** VNet1GWpip
* **Verbindungstyp:** Punkt-zu-Standort
* **Clientadresspool:** 172.16.201.0/24<br>VPN-Clients, die über diese Point-to-Site-Verbindung eine Verbindung mit dem VNet herstellen, erhalten eine IP-Adresse aus dem Clientadresspool.

## <a name="createvnet"></a>1. Erstellen eines virtuellen Netzwerks

Stellen Sie zunächst sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-p2s-vnet-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>2. Hinzufügen eines Gatewaysubnetzes

Bevor Sie das virtuelle Netzwerk mit einem Gateway verbinden, müssen Sie das Gatewaysubnetz für das virtuelle Netzwerk erstellen, mit dem Sie eine Verbindung herstellen möchten. Für die Gatewaydienste werden die im Gatewaysubnetz angegebenen IP-Adressen verwendet. Erstellen Sie nach Möglichkeit ein Gatewaysubnetz mit einem CIDR-Block vom Typ „/28“ oder „/27“, damit genügend IP-Adressen für zukünftige zusätzliche Konfigurationsanforderungen zur Verfügung stehen.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-p2s-rm-portal-include.md)]

## <a name="dns"></a>3. Angeben eines DNS-Servers (optional)

Nach Erstellung des virtuellen Netzwerks können Sie für die Namensauflösung die IP-Adresse eines DNS-Servers hinzufügen. Der DNS-Server ist für diese Konfiguration optional, für die Namensauflösung jedoch erforderlich. Wenn ein Wert angegeben wird, wird kein neuer DNS-Server erstellt. Die IP-Adresse des angegebenen DNS-Servers muss dazu in der Lage sein, die Namen für die Ressourcen aufzulösen, mit denen Sie eine Verbindung herstellen möchten. In diesem Beispiel wurde eine private IP-Adresse verwendet. Bei dieser IP-Adresse handelt es sich aber wahrscheinlich nicht um die IP-Adresse Ihres DNS-Servers. Achten Sie darauf, dass Sie Ihre eigenen Werte verwenden. Der angegebene Wert wird nicht von der P2S-Verbindung oder dem VPN-Client, sondern von den Ressourcen verwendet, die Sie im VNET bereitstellen.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>4. Erstellen eines Gateways für das virtuelle Netzwerk

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>Die Basic-SKU unterstützt keine IKEv2- oder RADIUS-Authentifizierung. Wenn Sie planen, Verbindungen von Mac-Clients mit Ihrem virtuellen Netzwerk zuzulassen, sollten Sie nicht die Basic-SKU verwenden.
>

## <a name="generatecert"></a>5. Generieren von Zertifikaten

Zertifikate werden von Azure zum Authentifizieren von Clients verwendet, die eine Verbindung mit einem VNET über eine Point-to-Site-VPN-Verbindung herstellen. Nachdem Sie ein Stammzertifikat abgerufen haben, müssen Sie die Informationen des öffentlichen Schlüssels in Azure [hochladen](#uploadfile). Das Stammzertifikat wird dann von Azure als „vertrauenswürdig“ für die Verbindung mit dem virtuellen Netzwerk über P2S betrachtet. Sie generieren darüber hinaus Clientzertifikate aus dem vertrauenswürdigen Stammzertifikat und installieren sie dann auf den einzelnen Clientcomputern. Mit dem Clientzertifikat wird der Client authentifiziert, wenn er eine Verbindung mit dem VNET initiiert. 

### <a name="getcer"></a>1. Beschaffen der CER-Datei für ein Stammzertifikat

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2. Generieren eines Clientzertifikats

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>6. Hinzufügen des Clientadresspools

Der Clientadresspool ist ein Bereich privater IP-Adressen, die Sie angeben. Den Clients, die eine Verbindung über ein P2S-VPN herstellen, wird dynamisch eine IP-Adresse aus diesem Bereich zugewiesen. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten.

1. Navigieren Sie nach Erstellung des Gateways für virtuelle Netzwerke auf dem Blatt des Gateways für virtuelle Netzwerke zum Abschnitt **Einstellungen**. Klicken Sie im Abschnitt **Einstellungen** auf **Point-to-Site-Konfiguration**.

   ![Seite „Point-to-Site“](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/gatewayblade.png) 
2. Klicken Sie auf **Jetzt konfigurieren**, um die Konfigurationsseite zu öffnen.

   ![Jetzt konfigurieren](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configurenow.png)
3. Fügen Sie auf der Konfigurationsseite **Punkt-zu-Standort** im Feld **Adresspool** den privaten IP-Adressbereich hinzu, den Sie verwenden möchten. VPN-Clients wird aus dem von Ihnen angegebenen Bereich dynamisch eine IP-Adresse zugewiesen. Klicken Sie auf **Speichern**, um die Einstellung zu überprüfen und zu speichern.

   ![Clientadresspool](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png)

   >[!NOTE]
   >Wenn der Tunneltyp oder Authentifizierungstyp im Portal auf dieser Seite nicht angezeigt wird, verwendet Ihr Gateway die Basic-SKU. Die Basic-SKU unterstützt keine IKEv2- oder RADIUS-Authentifizierung.
   >

## <a name="tunneltype"></a>7. Konfigurieren des Tunneltyps

Sie können den Tunneltyp auswählen. Die Tunneloptionen sind OpenVPN, SSTP und IKEv2. Der strongSwan-Client unter Android und Linux und der native IKEv2-VPN-Client unter iOS und OSX verwenden nur den IKEv2-Tunnel für die Verbindungsherstellung. Windows-Clients probieren zunächst IKEv2. Wird keine Verbindung hergestellt, verwenden sie SSTP. Sie können den OpenVPN-Client verwenden, um eine Verbindung mit dem OpenVPN-Tunneltyp herzustellen.

![Tunneltyp](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunneltype.png)

## <a name="authenticationtype"></a>8. Konfigurieren des Authentifizierungstyps

Wählen Sie die Option **Azure-Zertifikat**.

  ![Tunneltyp](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authenticationtype.png)

## <a name="uploadfile"></a>9. Hochladen der Daten des öffentlichen Zertifikats für das Stammzertifikat

Sie können weitere vertrauenswürdige Stammzertifikate hochladen (bis zu 20 insgesamt). Nach dem Hochladen der Daten des öffentlichen Zertifikats kann Azure sie verwenden, um Clients zu authentifizieren, auf denen ein aus dem vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installiert ist. Laden Sie die Informationen des öffentlichen Schlüssels für das Stammzertifikat in Azure hoch.

1. Die Zertifikate werden auf der Seite **Point-to-Site-Konfiguration** im Abschnitt **Stammzertifikat** hinzugefügt.
2. Vergewissern Sie sich, dass Sie das Stammzertifikat als X.509-CER-Datei mit Base-64-Codierung exportiert haben. Das Zertifikat muss in diesem Format exportiert werden, damit Sie es mit einem Text-Editor öffnen können.
3. Öffnen Sie das Zertifikat mit einem Text-Editor (beispielsweise mit dem Windows-Editor). Stellen Sie beim Kopieren der Zertifikatsdaten sicher, dass Sie den Text als fortlaufende Zeile ohne Wagenrückläufe oder Zeilenvorschübe kopieren. Es kann erforderlich sein, Ihre Ansicht im Text-Editor so zu ändern, dass die Symbole bzw. alle Zeichen angezeigt werden, damit die Wagenrückläufe bzw. Zeilenvorschübe sichtbar sind. Kopieren Sie nur den folgenden Abschnitt als fortlaufende Zeile:

   ![Zertifikatdaten](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png)
4. Fügen Sie die Zertifikatdaten im Feld **Daten des öffentlichen Zertifikats** ein. Geben Sie dem Zertifikat einen **Namen**, und klicken Sie dann auf **Speichern**. Sie können bis zu 20 vertrauenswürdige Stammzertifikate hinzufügen.

   ![Zertifikatupload](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png)
5. Klicken Sie oben auf der Seite auf **Speichern**, um alle Konfigurationseinstellungen zu speichern.

   ![Speichern](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png)

## <a name="installclientcert"></a>10. Installieren eines exportierten Clientzertifikats

Wenn Sie eine P2S-Verbindung mit einem anderen Clientcomputer als dem für die Generierung der Clientzertifikate verwendeten Computer herstellen möchten, müssen Sie ein Clientzertifikat installieren. Beim Installieren eines Clientzertifikats benötigen Sie das Kennwort, das beim Exportieren des Clientzertifikats erstellt wurde.

Stellen Sie sicher, dass das Clientzertifikat zusammen mit der gesamten Zertifikatkette als PFX-Datei exportiert wurden (Standardeinstellung). Andernfalls sind die Stammzertifikatinformationen nicht auf dem Clientcomputer vorhanden, und der Client kann nicht ordnungsgemäß authentifiziert werden.

Die Installationsschritte finden Sie unter [Installieren eines Clientzertifikats für Point-to-Site-Verbindungen mit Azure-Zertifikatauthentifizierung](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="clientconfig"></a>11. Generieren und Installieren des Konfigurationspakets für VPN-Clients

Die VPN-Clientkonfigurationsdateien enthalten Einstellungen zum Konfigurieren von Geräten, die eine P2S-Verbindung mit einem VNET herstellen. Anweisungen zum Erstellen und Installieren von VPN-Clientkonfigurationsdateien finden Sie unter [Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-Konfigurationen mit nativer Azure-Zertifikatauthentifizierung](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect"></a>12. Herstellen einer Verbindung mit Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>So stellen Sie eine Verbindung von einem Windows-VPN-Client her

>[!NOTE]
>Auf dem Windows-Clientcomputer, von dem aus Sie die Verbindung herstellen, müssen Sie über Administratorrechte verfügen.
>
>

1. Um eine Verbindung mit Ihrem VNet herzustellen, navigieren Sie auf dem Clientcomputer zu „VPN-Verbindungen“ und suchen nach der VPN-Verbindung, die Sie erstellt haben. Sie hat den gleichen Namen wie das virtuelle Netzwerk. Klicken Sie auf **Verbinden**. Möglicherweise wird eine Popupmeldung angezeigt, die sich auf die Verwendung des Zertifikats bezieht. Klicken Sie auf **Weiter**, um erhöhte Rechte zu verwenden.

2. Klicken Sie auf der Statusseite **Verbindung** auf **Verbinden**, um die Verbindung herzustellen. Wenn der Bildschirm **Zertifikat auswählen** angezeigt wird, vergewissern Sie sich, dass das angezeigte Clientzertifikat dem Zertifikat entspricht, die Sie zum Herstellen der Verbindung verwenden möchten. Wenn dies nicht der Fall ist, verwenden Sie den Dropdownpfeil, um das richtige Zertifikat auszuwählen, und klicken Sie dann auf **OK**.

   ![Verbindungsherstellung zwischen VPN-Client und Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. Die Verbindung wurde hergestellt.

   ![Verbindung hergestellt](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

#### <a name="troubleshoot-windows-p2s-connections"></a>Problembehandlung für Windows-P2S-Verbindungen

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>So stellen Sie eine Verbindung von einem Mac-VPN-Client her

Suchen Sie im Dialogfeld „Netzwerk“ nach dem gewünschten Clientprofil, geben Sie die Einstellungen aus der Datei [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac) an, und klicken Sie dann auf **Verbinden**.

Ausführliche Anweisungen finden Sie unter [Installieren – Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac). Wenn beim Herstellen einer Verbindung Probleme auftreten, stellen Sie sicher, dass das Gateway des virtuellen Netzwerks nicht die Basic-SKU verwendet. Die Basic-SKU wird für Mac-Clients nicht unterstützt.

  ![Mac-Verbindung](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>So überprüfen Sie Ihre Verbindung

Diese Anweisungen gelten für Windows-Clients.

1. Um sicherzustellen, dass die VPN-Verbindung aktiv ist, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie *Ipconfig/all*aus.
2. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die erhaltene IP-Adresse eine der Adressen aus dem Clientadresspool des Punkt-zu-Standort-VPN ist, den Sie in Ihrer Konfiguration angegeben haben. Die Ergebnisse sehen in etwa wie in diesem Beispiel aus:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="connectVM"></a>Herstellen einer Verbindung mit einem virtuellen Computer

Diese Anweisungen gelten für Windows-Clients.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>So können Sie vertrauenswürdige Stammzertifikate hinzufügen oder entfernen

Sie können vertrauenswürdige Stammzertifikate hinzufügen und aus Azure entfernen. Wenn Sie ein Stammzertifikat entfernen, können Clients, für die über diesen Stamm ein Zertifikat generiert wurde, nicht authentifiziert werden und somit auch keine Verbindung herstellen. Wenn Sie für einen Client die Authentifizierung und Verbindungsherstellung durchführen möchten, müssen Sie ein neues Clientzertifikat installieren, das aus einem für Azure vertrauenswürdigen (hochgeladenen) Stammzertifikat generiert wurde.

### <a name="to-add-a-trusted-root-certificate"></a>So fügen Sie ein vertrauenswürdiges Stammzertifikat hinzu

Sie können Azure bis zu 20 vertrauenswürdige CER-Stammzertifikatdateien hinzufügen. Eine entsprechende Anleitung finden Sie in diesem Artikel im Abschnitt zum [Hochladen eines vertrauenswürdigen Stammzertifikats](#uploadfile).

### <a name="to-remove-a-trusted-root-certificate"></a>Entfernen eines vertrauenswürdigen Stammzertifikats

1. Navigieren Sie zum Entfernen eines vertrauenswürdigen Zertifikats zur Seite **Punkt-zu-Standort-Konfiguration** für Ihr Gateway für virtuelle Netzwerke.
2. Suchen Sie auf der Seite im Abschnitt **Stammzertifikat** nach dem Zertifikat, das Sie entfernen möchten.
3. Klicken Sie neben dem Zertifikat auf die Auslassungspunkte und anschließend auf „Entfernen“.

## <a name="revokeclient"></a>So sperren Sie ein Clientzertifikat

Sie können Clientzertifikate sperren. Anhand der Zertifikatsperrliste können Sie basierend auf einzelnen Clientzertifikaten selektiv Punkt-zu-Standort-Verbindungen verweigern. Das ist nicht dasselbe wie das Entfernen eines vertrauenswürdigen Stammzertifikats. Wenn Sie ein vertrauenswürdiges Stammzertifikat (CER-Datei) aus Azure entfernen, wird der Zugriff für alle Clientzertifikate gesperrt, die mit dem gesperrten Stammzertifikat generiert oder signiert wurden. Wenn Sie anstelle des Stammzertifikats ein Clientzertifikat sperren, können die anderen Zertifikate, die auf der Grundlage des Stammzertifikats generiert wurden, weiterhin für die Authentifizierung verwendet werden.

Üblicherweise wird das Stammzertifikat zum Verwalten des Zugriffs auf Team- oder Organisationsebene verwendet. Eine genauer abgestufte Steuerung des Zugriffs für einzelne Benutzer erfolgt hingegen mit gesperrten Clientzertifikaten.

### <a name="revoke-a-client-certificate"></a>Sperren eines Clientzertifikats

Sie können ein Clientzertifikat sperren, indem Sie den Fingerabdruck der Sperrliste hinzufügen.

1. Rufen Sie den Fingerabdruck des Clientzertifikats ab. Weitere Informationen finden Sie unter [Vorgehensweise: Abrufen des Fingerabdrucks eines Zertifikats](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopieren Sie ihn in einen Text-Editor, und entfernen Sie alle Leerzeichen, sodass eine fortlaufende Zeichenfolge entsteht.
3. Navigieren Sie zur Seite **Punkt-zu-Standort-Konfiguration** des Gateways für virtuelle Netzwerke. Dies ist die Seite, die Sie zum [Hochladen eines vertrauenswürdigen Stammzertifikats](#uploadfile) verwendet haben.
4. Geben Sie im Abschnitt **Gesperrte Zertifikate** einen Anzeigenamen für das Zertifikat ein. (Dabei muss es sich nicht um den allgemeinen Namen des Zertifikats handeln.)
5. Kopieren Sie die Fingerabdruck-Zeichenfolge, und fügen Sie sie in das Feld **Fingerabdruck** ein.
6. Der Fingerabdruck wird überprüft und automatisch der Sperrliste hinzugefügt. Auf dem Bildschirm wird eine Meldung mit dem Hinweis angezeigt, dass die Liste aktualisiert wird. 
7. Nach Abschluss der Aktualisierung kann das Zertifikat nicht mehr für die Verbindungsherstellung verwendet werden. Clients, die versuchen, unter Verwendung dieses Zertifikats eine Verbindung herzustellen, erhalten eine Meldung mit dem Hinweis, dass das Zertifikat nicht mehr gültig ist.

## <a name="faq"></a>Point-to-Site – Häufig gestellte Fragen

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](https://docs.microsoft.com/azure/) . Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/linux/azure-vm-network-overview.md).

Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Probleme mit Azure P2S-Verbindungen (Point-to-Site)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
