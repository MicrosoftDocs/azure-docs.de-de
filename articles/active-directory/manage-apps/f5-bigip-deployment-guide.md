---
title: Bereitstellungshandbuch für sicheren Hybridzugriff mit F5 und Azure AD | Microsoft-Dokumentation
description: Tutorial zum Bereitstellen von VMs mit F5 BIG-IP Virtual Edition (VE) in Azure IaaS für sicheren Hybridzugriff
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c03009b08dcf33bf4b84bc91232af96e7ba2c71
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095184"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Tutorial zum Bereitstellen von VMs mit F5 BIG-IP Virtual Edition in Azure IaaS für sicheren Hybridzugriff

In diesem Tutorial finden Sie alle Schritte für die Bereitstellung von BIG-IP Virtual Edition (VE) in Azure IaaS. Am Ende dieses Tutorials verfügen Sie über Folgendes:

- Eine vollständig vorbereitete BIG-IP-VM zum Modellieren eines Proof of Concept für den sicheren Hybridzugriff (Secure Hybrid Access, SHA)

- Eine Staginginstanz zum Testen neuer BIG-IP-Systemupdates und -Hotfixes

## <a name="prerequisites"></a>Voraussetzungen

Kenntnisse über F5 BIG-IP sind nicht erforderlich. Es wird jedoch empfohlen, sich mit der [F5 BIG-IP-Terminologie](https://www.f5.com/services/resources/glossary) vertraut zu machen. Zum Bereitstellen von BIG-IP in Azure für den sicheren Hybridzugriff (SHA) ist Folgendes erforderlich:

- Ein kostenpflichtiges Azure-Abonnement oder ein kostenloses 12-Monats-[Testabonnement](https://azure.microsoft.com/free/)

- Eine der folgenden F5 BIG-IP-Lizenz-SKUs

  - F5 BIG-IP® Best Bundle

  - Eigenständige Lizenz für F5 BIG-IP Access Policy Manager™ (APM)

  - Add-On-Lizenz für F5 BIG-IP Access Policy Manager™ (APM) für eine bereits vorhandene Instanz von F5 BIG-IP® Local Traffic Manager™ (LTM)
  
  - 90-Tage-[Testlizenz](https://www.f5.com/trial/big-ip-trial.php) für sämtliche Features von BIG-IP

- Ein Platzhalter- oder SAN-Zertifikat (Subject Alternative Name, alternativer Antragstellername) zum Veröffentlichen von Webanwendungen über SSL (Secure Socket Layer). [Let’s Encrypt](https://letsencrypt.org/) bietet ein kostenloses Testzertifikat für 90 Tage an.

- Ein SSL-Zertifikat zum Schützen der Verwaltungsschnittstelle von BIG-IP. Sie können ein zum Veröffentlichen von Web-Apps verwendetes Zertifikat verwenden, wenn der Antragsteller dem vollqualifizierten Domänennamen (FQDN) von BIG-IP entspricht. So wäre beispielsweise ein Platzhalterzertifikat für den Antragsteller „*.contoso.com“ für `https://big-ip-vm.contoso.com:8443` geeignet.

Die VM-Bereitstellung und die grundlegende Systemkonfigurationen dauern ca. 30 Minuten. Zu diesem Zeitpunkt ist Ihre BIG-IP-Plattform für die Implementierung der [hier](f5-aad-integration.md) aufgeführten Szenarien für den sicheren Hybridzugriff (SHA) bereit.

Zum Testen der Szenarien wird in diesem Tutorial davon ausgegangen, dass BIG-IP in einer Azure-Ressourcengruppe bereitgestellt wird, die eine Active Directory-Umgebung (AD) enthält. Die Umgebung sollte aus einem Domänencontroller (DC) und Webhost-VMs (IIS) bestehen. Es ist auch möglich, diese Server an anderen Standorten als die BIG-IP-VM bereitzustellen, sofern BIG-IP direkten Zugriff auf die für die Unterstützung eines bestimmten Szenarios erforderlichen Komponenten hat. Darüber hinaus werden auch Szenarien unterstützt, in denen die BIG-IP-VM über eine VPN-Verbindung mit einer anderen Umgebung verbunden ist.

Wenn Sie nicht über die hier genannten Komponenten für die Tests verfügen, können Sie mit [diesem Skript](https://github.com/Rainier-MSFT/Cloud_Identity_Lab) eine vollständige AD-Domänenumgebung in Azure bereitstellen. Eine Sammlung von Beispieltestanwendungen kann auch programmgesteuert mit [dieser skriptgesteuerten Automatisierung](https://github.com/jeevanbisht/DemoSuite) auf einem IIS-Webhost bereitgestellt werden.

>[!NOTE]
>Das [Azure-Portal](https://portal.azure.com/#home) wird ständig weiterentwickelt, sodass sich einige der Schritte in diesem Tutorial vom tatsächlichen Layout im Azure-Portal unterscheiden können.

## <a name="azure-deployment"></a>Azure-Bereitstellung

BIG-IP kann in verschiedenen Topologien bereitgestellt werden. In diesem Leitfaden wird eine Bereitstellung mit einer einzelnen Netzwerkschnittstelle (NIC) verwendet. Wenn Ihre BIG-IP-Bereitstellung jedoch mehrere Netzwerkschnittstellen für Hochverfügbarkeit, Netzwerktrennung oder mehr als 1 GB Durchsatz erfordert, sollten Sie die Verwendung der vorkompilierten [ARM-Vorlagen (Azure Resource Manager)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html) von F5 in Erwägung ziehen.

Führen Sie die folgenden Schritte aus, um BIG-IP VE aus dem [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) bereitzustellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#home) mit einem Konto an, das über die erforderlichen Berechtigungen zum Erstellen von VMs verfügt, z. B. „Mitwirkender“.

2. Geben Sie im Suchfeld auf dem oberen Menüband **Marketplace** ein, und drücken Sie die **EINGABETASTE**.

3. Geben Sie **F5** in den Marketplace-Filter ein, und drücken Sie erneut die **EINGABETASTE**.

4. Wählen Sie auf dem oberen Menüband **+ Hinzufügen** aus, geben Sie **F5** in den Marketplace-Filter ein, und drücken Sie die **EINGABETASTE**.

5. Wählen Sie **F5 BIG-IP Virtual Edition (BYOL)**  > **Select a plan** (Plan auswählen) > **F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations)** (F5 BIG-IP VE – ALLE (BYOL, 2 Startstandorte)) aus.

6. Klicken Sie auf **Erstellen**.

![Darstellung der Schritte zum Auswählen eines Softwareplans](./media/f5ve-deployment-plan/software-plan.png)

7. Wenden Sie im Menü **Grundlagen** die folgenden Einstellungen an:

 |  Projektdetails     |  Wert     |
 |:-------|:--------|
 |Subscription|Zielabonnement für die BIG-IP-VM-Bereitstellung|
 |Resource group | Verwenden Sie eine vorhandene Azure-Ressourcengruppe, in der die BIG-IP-VM bereitgestellt wird, oder erstellen Sie eine neue. Es sollte dieselbe Ressourcengruppe sein, der auch Ihre DC- und IIS-VMs angehören.|
 | **Instanzendetails**|  |
 |VM-Name| Beispiel: BIG-IP-VM |
 |Region | Azure-Zielregion für die BIG-IP-VM |
 |Verfügbarkeitsoptionen| Nur aktivieren, wenn die VM in einer Produktionsumgebung verwendet wird|
 |Image| F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations)|
 |Azure Spot-Instanz| Nicht erforderlich, kann bei Bedarf aber aktiviert werden |
 |Size| Die minimale Spezifikation sollte 2 vCPUs und 8 GB Arbeitsspeicher sein.|
 |**Administratorkonto**|  |
 |Authentifizierungsart|Wählen Sie ein anfängliches Kennwort aus. Sie können später auf ein Schlüsselpaar umstellen. |
 |Username|Die Identität, die als lokales BIG-IP-Konto für den Zugriff auf die Verwaltungsschnittstellen erstellt wird. Beim Benutzernamen wird die Groß- und Kleinschreibung berücksichtigt.|
 |Kennwort|Sicherer Administratorzugriff mit einem sicheren Kennwort|
 |**Regeln für eingehende Ports**|  |
 |Öffentliche Eingangsports|Keine|

8. Klicken Sie auf **Weiter: Datenträger**, übernehmen Sie die Standardeinstellungen, und wählen Sie **Weiter: Netzwerk** aus.

9. Vervollständigen Sie im Menü **Netzwerk** die folgenden Einstellungen:

 |Netzwerkschnittstelle|      Wert |
 |:--------------|:----------------|
 |Virtuelles Netzwerk|Verwenden Sie dasselbe Azure-VNET wie für Ihre DC- und IIS-VMs, oder erstellen Sie ein neues.|
 |Subnet| Verwenden Sie dasselbe interne Azure-Subnetz wie für Ihre DC- und IIS-VMs, oder erstellen Sie ein neues.|
 |Öffentliche IP-Adresse |  Keine|
 |NIC-Netzwerksicherheitsgruppe| Wählen Sie „Keine“ aus, wenn das in den vorherigen Schritten ausgewählte Azure-Subnetz bereits einer Netzwerksicherheitsgruppe (NSG) zugeordnet ist. Wählen Sie andernfalls „Standard“ aus.|
 |Beschleunigter Netzwerkbetrieb| Aus |
 |**Lastenausgleich**|     |
 |Lastenausgleich für VMs| Nein|

10. Klicken Sie auf **Weiter: Verwaltung**, und legen Sie die folgenden Einstellungen fest:

 |Überwachung|    Wert |
 |:---------|:-----|
 |Detaillierte Überwachung aktivieren| Aus|
 |Startdiagnose|Mit benutzerdefiniertem Speicherkonto aktivieren Ermöglicht das Herstellen einer Verbindung mit der SSH-Schnittstelle (Secure Shell) von BIG-IP über die serielle Konsole im Azure-Portal. Wählen Sie ein vorhandenes Azure Storage-Konto aus.|
 |**Identität**|  |
 |Systemzugewiesene verwaltete Identität|Aus|
 |Azure Active Directory|Diese Option wird von BIG-IP zurzeit nicht unterstützt.|
 |**Automatisches Herunterfahren**|    |
 |Automatisches Herunterfahren aktivieren| Für die Tests sollten Sie festlegen, dass die BIG-IP-VM täglich heruntergefahren wird.|

11. Klicken Sie auf **Weiter: Erweitert**, übernehmen Sie die Standardeinstellungen, und wählen Sie **Weiter: Tags**.

12. Klicken Sie auf **Weiter: Überprüfen und erstellen**, um die Konfigurationen der BIG-IP-VM zu überprüfen, bevor Sie **Erstellen** auswählen, um die Bereitstellung zu starten.

13. Die vollständige Bereitstellung einer BIG-IP-VM dauert in der Regel 5 Minuten. Wählen Sie nach Abschluss der Bereitstellung nicht **Zu Ressource wechseln** aus, sondern erweitern Sie das Menü auf der linken Seite des Azure-Portals, und wählen Sie **Ressourcengruppen** aus, um zu Ihrer neuen BIG-IP-VM zu navigieren. Wenn bei der VM-Erstellung ein Fehler auftritt, wählen Sie **Zurück** und **Weiter** aus.

## <a name="network-configuration"></a>Netzwerkkonfiguration

Beim ersten Start der BIG-IP-VM wird der zugehörigen NIC vom DHCP-Dienst (Dynamic Host Configuration-Protokoll) des Azure-Subnetzes, in dem sie bereitgestellt wird, eine **primäre** private IP-Adresse bereitgestellt. Diese IP-Adresse wird vom Traffic Manager-Betriebssystem (TMOS) von BIG-IP für die folgende Kommunikation verwendet:

- Kommunikation mit anderen Hosts und Diensten

- Ausgehender Zugriff auf das öffentliche Internet

- Eingehender Zugriff auf die Schnittstellen für die BIG-IP-Webkonfiguration und -SSH-Verwaltung

Wenn Sie eine dieser Verwaltungsschnittstellen für das Internet verfügbar machen, erhöht sich die Angriffsfläche von BIG-IP. Aus diesem Grund wurde als primäre IP-Adresse von BIG-IP während der Bereitstellung keine öffentliche IP-Adresse bereitgestellt. Stattdessen werden für Veröffentlichungsdienste eine sekundäre interne IP-Adresse und eine zugehörige öffentliche IP-Adresse bereitgestellt.
Durch diese 1:1-Zuordnung zwischen der öffentlichen IP-Adresse einer VM und der privaten IP-Adresse kann externer Datenverkehr an die VM weitergeleitet werden. Es ist allerdings auch eine Azure-NSG-Regel erforderlich, um den Datenverkehr auf die gleiche Weise wie bei einer Firewall zuzulassen.

Diagramm einer Bereitstellung von BIG-IP VE mit einer einzelnen NIC in Azure, die mit einer primären IP-Adresse für allgemeine Vorgänge und die Verwaltung sowie einer separaten IP-Adresse für den virtuellen Server zum Veröffentlichen von Diensten konfiguriert ist.
Bei dieser Anordnung ermöglicht eine NSG-Regel die Weiterleitung von Remotedatenverkehr, der für `intranet.contoso.com` bestimmt ist, an die öffentliche IP-Adresse für den veröffentlichten Dienst, bevor er an den virtuellen BIG-IP-Server weitergeleitet wird.

![Abbildung einer Bereitstellung mit einer einzelnen NIC.](./media/f5ve-deployment-plan/single-nic-deployment.png) Standardmäßig sind private und öffentliche IP-Adressen, die an Azure-VMs ausgegeben werden, immer dynamisch und ändern sich daher sehr wahrscheinlich bei jedem Neustart einer VM. Sie können unvorhergesehene Konnektivitätsprobleme vermeiden, indem Sie die Verwaltungs-IP-Adresse von BIG-IP und die sekundären IP-Adressen für die Veröffentlichung von Diensten in statische ändern.

1. Wechseln Sie im Menü Ihrer BIG-IP-VM zu **Einstellungen** > **Netzwerk**.

2. Wählen Sie in der Netzwerkansicht den Link rechts neben **Netzwerkschnittstelle** aus.

![Abbildung der Netzwerkkonfiguration](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>VM-Namen werden während der Bereitstellung zufällig generiert.

3. Wählen Sie im linken Bereich **IP-Konfigurationen** und dann die Zeile **ipconfig1** aus.

4. Legen Sie die Option **IP-Zuweisung** auf „Statisch“ fest, und ändern Sie ggf. die primäre IP-Adresse der BIG-IP-VMs. Wählen Sie dann **Speichern** aus, und schließen Sie das Menü „ipconfig1“.

>[!NOTE]
>Sie verwenden diese primäre IP-Adresse, um eine Verbindung mit der BIG-IP-VM herzustellen und diese zu verwalten.

5. Wählen Sie auf dem oberen Menüband **+ Hinzufügen** aus, und geben Sie einen Namen für eine sekundäre private IP-Adresse ein, z. B. „ipconfig2“.

6. Legen Sie die Option **Zuteilung** der Einstellungen für die private IP-Adresse auf **Statisch** fest. Wenn Sie die nächsthöhere oder -niedrigere IP-Adresse angeben, können Sie einfacher den Überblick behalten.

7. Legen Sie die öffentliche IP-Adresse auf **Zuordnen** fest, und wählen Sie **Erstellen** aus.

8. Geben Sie einen Namen für die neue öffentliche IP-Adresse ein, z. B. „BIG-IP-VM_ipconfig2_Public“.

9. Wenn Sie dazu aufgefordert werden, legen Sie die **SKU** auf „Standard“ fest.

10. Wenn Sie dazu aufgefordert werden, legen Sie den **Tarif** auf **Global** fest.

11. Legen Sie die Option **Zuweisung** auf **Statisch** fest, und wählen Sie dann zweimal **OK** aus.

Ihre BIG-IP-VM kann nun eingerichtet werden:

- **Primäre private IP-Adressen:** Dediziert für die Verwaltung der BIG-IP-VM über das Hilfsprogramm für die Webkonfiguration und SSH. Sie wird vom BIG-IP-System als **Eigene IP-Adresse** verwendet, wenn eine Verbindung mit den veröffentlichten Back-End-Diensten hergestellt wird. Außerdem wird damit eine Verbindung mit externen Diensten wie NTP, AD und LDAP hergestellt.

- **Sekundäre private IP-Adressen:** Wird verwendet, wenn ein virtueller BIG-IP APM-Server erstellt wird, um auf eingehende Anforderungen an veröffentlichte Dienste zu lauschen.

- **Öffentliche IP**: Über die zugewiesene sekundäre private IP-Adresse kann Clientdatenverkehr aus dem öffentlichen Internet an den virtuellen BIG-IP-Server für die veröffentlichten Dienste weitergeleitet werden.

Das Beispiel veranschaulicht die 1:1-Beziehung zwischen der öffentlichen und der privaten IP-Adresse einer VM. Die NIC einer Azure-VM kann nur über eine primäre IP-Adresse verfügen – alle zusätzlich erstellten IP-Adressen werden immer als sekundär bezeichnet.

>[!NOTE]
>Sie benötigen die Zuordnungen der sekundären IP-Adressen für das Veröffentlichen von BIG-IP-Diensten.

![Abbildung aller sekundären IP-Adressen](./media/f5ve-deployment-plan/secondary-ips.png)

Zum Implementieren des sicheren Hybridzugriffs (SHA) mithilfe der **zugriffsgesteuerten Konfiguration** von BIG-IP wiederholen Sie die Schritte 5–11, um zusätzliche Paare privater und öffentlicher IP-Adressen für jeden weiteren Dienst zu erstellen, den Sie über den BIG-IP APM veröffentlichen möchten. Der gleiche Ansatz kann auch angewandt werden, wenn Dienste mit der **erweiterten Konfiguration** von BIG-IP veröffentlicht werden. In diesem Szenario haben Sie jedoch die Möglichkeit, überflüssige öffentliche IP-Adressen zu vermeiden, indem Sie eine [SNI-Konfiguration](https://support.f5.com/csp/#/article/K13452) (Servernamensanzeige) verwenden. Bei einer solchen Konfiguration akzeptiert ein virtueller BIG-IP-Server den gesamten empfangenen Clientdatenverkehr und leitet ihn an das Ziel weiter.

## <a name="dns-configuration"></a>DNS-Konfiguration

Es ist von entscheidender Bedeutung, DNS für Clients so zu konfigurieren, dass Ihre veröffentlichten SHA-Dienste mit den öffentlichen IP-Adressen Ihrer BIG-IP-VMs aufgelöst werden.

In den folgenden Schritten wird davon ausgegangen, dass die DNS-Zone der öffentlichen Domäne, die für Ihre SHA-Dienste verwendet wird, in Azure verwaltet wird. Die DNS-Prinzipien für das Erstellen des Locatordatensatzes gelten jedoch unabhängig davon, wo die DNS-Zone verwaltet wird.

1. Erweitern Sie das Menü auf der linken Seite des Portals, wenn es noch nicht geöffnet ist, und navigieren Sie über die Option **Ressourcengruppen** zu Ihrer BIG-IP-VM.

2. Wechseln Sie im Menü Ihrer BIG-IP-VM zu **Einstellungen** > **Netzwerk**.

3. Wählen Sie in der Netzwerkansicht der BIG-IP-VMs in der Dropdownliste für die IP-Konfiguration die erste sekundäre IP-Adresse aus. Wählen Sie anschließend den Link für die zugehörige **öffentliche IP-Adresse der NIC** aus.

![Screenshot zum Anzeigen der öffentlichen IP-Adresse der NIC](./media/f5ve-deployment-plan/networking.png)

4. Wählen Sie im linken Bereich unter dem Abschnitt **Einstellungen** die Option **Konfiguration** aus, um das Menü für die öffentliche IP-Adresse und die DNS-Eigenschaften für die ausgewählte sekundäre IP-Adresse anzuzeigen.

5. Wählen Sie die Option zum **Erstellen** eines Aliasdatensatzes und dann in der Dropdownliste Ihre **DNS-Zone** aus. Wenn noch keine DNS-Zone vorhanden ist, wird sie möglicherweise außerhalb von Azure verwaltet. Sie können aber auch eine Zone für das Domänensuffix erstellen, das in Azure AD überprüft werden soll.

6. Verwenden Sie zum Erstellen des ersten DNS-Aliasdatensatzes die folgenden Details:

 | Feld | Wert |
 |:-------|:-----------|
 |Subscription| Dasselbe Abonnement wie für die BIG-IP-VM|
 |DNS-Zone| Die geltende DNS-Zone für das überprüfte Domänensuffix, das von Ihren veröffentlichten Websites verwendet wird, z. B. www.contoso.com |
 |Name | Der angegebene Hostname wird in die öffentliche IP-Adresse aufgelöst, die der ausgewählten sekundären IP-Adresse zugeordnet ist. Achten Sie darauf, die richtige DNS-zu-IP-Zuordnung zu definieren. Ein Beispiel finden Sie in der letzten Abbildung im Abschnitt zu Netzwerkkonfigurationen für „intranet.contoso.com“ > „13.77.148.215“.|
 | TTL | 1 |
 |TTL-Einheiten | Stunden |

7. Wählen Sie **Erstellen** aus, damit Azure diese Einstellungen im öffentlichen DNS speichert.

8. Belassen Sie das Feld **DNS-Namenbezeichnung (optional)** unverändert, und wählen Sie **Speichern** aus, bevor Sie das Menü „Öffentliche IP-Adresse“ schließen.

9. Wiederholen Sie die Schritte 1 bis 6, um weitere DNS-Einträge für jeden Dienst zu erstellen, den Sie mithilfe der interaktiven Konfiguration von BIG-IP veröffentlichen möchten.

  Wenn DNS-Einträge vorhanden sind, können Sie Onlinetools wie [DNS checker](https://dnschecker.org/) verwenden, um zu überprüfen, ob ein erstellter Datensatz erfolgreich an alle globalen öffentlichen DNS-Server verteilt wurde.

  Wenn Sie den DNS-Domänennamespace mit einem externen Anbieter wie [GoDaddy](https://www.godaddy.com/) verwalten, müssen Sie die Datensätze mit den dort bereitgestellten DNS-Verwaltungsfunktionen erstellen.

>[!NOTE]
>Sie können auch die lokale Hostdatei eines PC verwenden, wenn Sie DNS-Einträge testen und häufig ändern möchten. Sie können auf die Datei „localhost“ auf einem Windows-Computer zugreifen, indem Sie auf der Tastatur „Windows+R“ drücken und im Feld „Ausführen“ das Wort **Treiber** übergeben. Beachten Sie, dass ein Localhost-Eintrag die DNS-Auflösung nur für den lokalen Computer, nicht jedoch für andere Clients bereitstellt.

## <a name="client-traffic"></a>Clientdatenverkehr

Standardmäßig sind Azure-VNETs und die zugehörigen Subnetze private Netzwerke, die keinen Internetdatenverkehr empfangen können. Die NIC Ihrer BIG-IP-VM sollte an die NSG angefügt werden, die Sie während der Bereitstellung angegeben haben. Damit externer Webdatenverkehr die BIG-IP-VM erreichen kann, müssen Sie eine eingehende NSG-Regel definieren, um die Ports 443 (HTTPS) und 80 (HTTP) aus dem öffentlichen Internet zuzulassen.

1. Wählen Sie im Hauptmenü **Übersicht** der BIG-IP-VM die Option **Netzwerk** aus.

2. Wählen Sie **Hinzufügen** aus, um eine eingehende Regel hinzuzufügen, und geben Sie die folgenden Eigenschaften für die NSG-Regel ein:

 |     Feld   |   Wert        |
 |:------------|:------------|
 |`Source`| Any|
 |Source port ranges| *|
 |Ziel-IP-Adressen|Durch Trennzeichen getrennte Liste aller sekundären privaten IP-Adressen der BIG-IP-VM|
 |Zielports| 80, 443|
 |Protokoll| TCP |
 |Aktion| Zulassen|
 |Priorität|Der niedrigste verfügbare Wert zwischen 100 und 4096|
 |Name | Ein aussagekräftiger Name, z. B. `BIG-IP-VM_Web_Services_80_443`|

3. Wählen Sie **Hinzufügen** aus, um die Änderungen anzuwenden, und schließen Sie das Menü **Netzwerk**.

Ab sofort erreicht der gesamte HTTP- und HTTPS-Datenverkehr von beliebigen Standorten aus alle sekundären Schnittstellen Ihrer BIG-IP-VMs. Das Zulassen von Port 80 ist hilfreich, damit der BIG-IP APM Benutzer automatisch von HTTP auf HTTPS umleiten kann. Sie können diese Regel bei Bedarf bearbeiten, um Ziel-IP-Adressen hinzuzufügen oder zu entfernen.

## <a name="manage-big-ip"></a>Verwalten von BIG-IP

BIG-IP-Systeme werden über die Benutzeroberfläche für die Webkonfiguration verwaltet, auf die mithilfe einer der folgenden empfohlenen Methoden zugegriffen werden kann:

- Von einem Computer im internen Netzwerk von BIG-IP

- Von einem VPN-Client, der mit dem internen Netzwerk der BIG-IP-VM verbunden ist

- Veröffentlicht über den [Azure AD-Anwendungsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

Sie müssen sich für die am besten geeignete Methode entscheiden, bevor Sie mit der restlichen Konfigurationen fortfahren können. Falls erforderlich, können Sie auch eine direkte Verbindung mit der Webkonfiguration über das Internet herstellen, indem Sie als primäre IP-Adresse von BIG-IP eine öffentliche IP-Adresse konfigurieren. Fügen Sie dann eine NSG-Regel hinzu, um Datenverkehr an diese primäre IP-Adresse über Port 8443 zuzulassen. Stellen Sie sicher, dass Sie die Quelle auf Ihre eigene vertrauenswürdige IP-Adresse beschränken. Andernfalls kann jeder Benutzer eine Verbindung herstellen.

Vergewissern Sie sich anschließend, dass Sie eine Verbindung mit der Webkonfiguration der BIG-IP-VM herstellen und sich mit den bei der VM-Bereitstellung angegebenen Anmeldeinformationen anmelden können.

- Wenn Sie eine Verbindung von einer VM im internen Netzwerk oder über VPN herstellen, verwenden Sie eine direkte Verbindung mit der primären IP-Adresse der BIG-IP-VMs und dem Port für die Webkonfiguration. Beispiel: `https://<BIG-IP-VM_Primary_IP:8443`. In Ihrem Browser wird die Meldung angezeigt, dass die Verbindung unsicher ist. Sie können diese Meldung jedoch ignorieren, bis BIG-IP konfiguriert ist. Wenn der Browser den Zugriff weiterhin blockiert, löschen Sie seinen Cache, und wiederholen Sie den Vorgang.

- Wenn Sie die Webkonfiguration über den Anwendungsproxy veröffentlicht haben, verwenden Sie die für den externen Zugriff auf die Webkonfiguration definierte URL, ohne den Port anzuhängen (z. B. `https://big-ip-vm.contoso.com`). Die interne URL muss mithilfe des Ports für die Webkonfiguration definiert werden, z. B. `https://big-ip-vm.contoso.com:8443` 

Ein BIG-IP-System kann auch über die zugrunde liegende SSH-Umgebung verwaltet werden, die in der Regel für Befehlszeilenaufgaben (CLI) und den root-Zugriff verwendet wird. Zum Herstellen einer Verbindung mit der Befehlszeilenschnittstelle gibt es mehrere Optionen:

- [Azure Bastion-Dienst:](https://docs.microsoft.com/azure/bastion/bastion-overview) Ermöglicht schnelle und sichere Verbindungen mit beliebigen VMs in einem VNET von jedem beliebigen Standort aus

- Direkte Verbindungen über einen SSH-Client wie PuTTY über den JIT-Ansatz

- Serielle Konsole: Diese Option finden Sie ganz unten im Abschnitt „Support und Problembehandlung“ des VM-Menüs im Portal. Dateiübertragungen werden dafür nicht unterstützt.

- Wie bei der Webkonfiguration können Sie auch hier eine direkte Verbindung mit der Befehlszeilenschnittstelle über das Internet herstellen, indem Sie als primäre IP-Adresse der BIG-IP-VM eine öffentliche IP-Adresse konfigurieren und eine NSG-Regel hinzufügen, mit der SSH-Datenverkehr zugelassen wird. Stellen Sie auch bei dieser Methode wieder sicher, dass Sie als Quelle nur eigene vertrauenswürdige IP-Adressen zulassen.  

## <a name="big-ip-license"></a>BIG-IP-Lizenz

Ein BIG-IP-System muss aktiviert und mit dem APM-Modul bereitgestellt werden, bevor es für die Veröffentlichung von Diensten und SHA (sicherer Hybridzugriff) konfiguriert werden kann.

1. Melden Sie sich wieder bei der Webkonfiguration an, und wählen Sie auf der Seite **Allgemeine Eigenschaften** die Option **Aktivieren** aus.

2. Geben Sie im Feld **Base Registration key** (Basisregistrierungsschlüssel) den von F5 bereitgestellten Schlüssel ein (beachten Sie die Groß-/Kleinschreibung).

3. Behalten Sie als **Activation Method** (Aktivierungsmethode) die Einstellung **Automatic** bei, und wählen Sie **Weiter** aus. BIG-IP überprüft die Lizenz und zeigt den Endbenutzer-Lizenzvertrag (EULA) an.

4. Wählen Sie **Accept** (Akzeptieren) aus, und warten Sie auf den Abschluss der Aktivierung, bevor Sie **Continue** (Fortsetzen) auswählen.

5. Melden Sie sich erneut an, und wählen Sie unten auf der Seite mit der Lizenzzusammenfassung die Option **Weiter** aus. BIG-IP zeigt nun eine Liste von Modulen an, die die verschiedenen für SHA erforderlichen Funktionen bereitstellen. Wenn sie nicht angezeigt wird, navigieren Sie auf der Hauptregisterkarte zu **System** > **Resource Provisioning** (System > Ressourcenbereitstellung).

6. Überprüfen Sie die Bereitstellungsspalte für die Zugriffsrichtlinie (APM):

![Abbildung der Zugriffsbereitstellung](./media/f5ve-deployment-plan/access-provisioning.png)

7. Wählen Sie **Submit** (Übermitteln) aus, und bestätigen Sie die Warnung.

8. Warten Sie, bis BIG-IP die neuen Funktionen aktiviert. Dieser Vorgang kann mehrmals durchlaufen werden, bis alles vollständig initialisiert ist. 

9. Wenn Sie bereit sind, wählen Sie **Continue** (Fortsetzen) und dann auf der Registerkarte **About** (Info) die Option **Run the setup utility** (Setuphilfsprogramm ausführen) aus.

>[!IMPORTANT]
>F5-Lizenzen sind auf jeweils eine einzelne BIG-IP VE-Instanz beschränkt. Es kann notwendig werden, eine Lizenz von einer Instanz zu einer anderen zu migrieren. Stellen Sie in diesem Fall sicher, dass Sie Ihre Testlizenz zunächst auf der aktiven Instanz [widerrufen](https://support.f5.com/csp/article/K41458656), bevor Sie sie außer Betrieb setzen. Andernfalls geht die Lizenz dauerhaft verloren.

## <a name="provision-big-ip"></a>Bereitstellen von BIG-IP

Das Absichern des Verwaltungsdatenverkehrs zu und von der BIG-IP-Webkonfiguration ist von größter Wichtigkeit. Konfigurieren Sie ein Zertifikat für die Geräteverwaltung, um den Webkonfigurationskanal vor Kompromittierung zu schützen.

1. Wechseln Sie auf der Navigationsleiste auf der linken Seite zu **System** > **Certificate Management** > **Traffic Certificate Management** > **SSL Certificate List** > **Import** (System > Zertifikatverwaltung > Datenverkehrszertifikate verwalten > SSL-Zertifikatliste > Importieren).

2. Wählen Sie in der Dropdownliste **Import Type** (Importtyp) die Option **PKCS 12 (IIS)** und dann **Choose File** (Datei auswählen) aus. Suchen Sie ein SSL-Webzertifikat mit einem Antragstellernamen oder SAN entsprechend dem vollqualifizierten Domänennamen, den Sie der BIG-IP-VM in den nächsten Schritten zuweisen.

3. Geben Sie das Kennwort für das Zertifikat an, und wählen Sie **Import** (Importieren) aus.

4. Wechseln Sie auf der linken Navigationsleiste zu **System** > **Platform**.

5. Konfigurieren Sie die BIG-IP-VM mit einem vollqualifizierten Hostnamen und der Zeitzone für ihre Umgebung. Wählen Sie dann **Update** (Aktualisieren) aus.

![Abbildung der allgemeinen Eigenschaften](./media/f5ve-deployment-plan/general-properties.png)

6. Wechseln Sie auf der linken Navigationsleiste zu **System** > **Configuration** > **Device** > **NTP** (System > Konfiguration > Gerät > NTP).

7. Geben Sie eine zuverlässige NTP-Quelle an, und wählen Sie **Add** (Hinzufügen) und dann **Update** (Aktualisieren) aus. Beispiel: `time.windows.com`

Sie benötigen nun einen DNS-Eintrag, um den in den vorherigen Schritten angegebenen FQDN von BIG-IP in seine primäre private IP-Adresse aufzulösen. Der Eintrag sollte dem internen DNS Ihrer Umgebung oder der Localhost-Datei eines PC hinzugefügt werden, der zum Herstellen der Verbindung mit der Webkonfiguration von BIG-IP verwendet wird. In jedem Fall sollte die Browserwarnung nicht mehr angezeigt werden, wenn Sie eine direkte Verbindung mit der Webkonfiguration herstellen. Das bedeutet: nicht über den Anwendungsproxy oder einen anderen Reverseproxy.

## <a name="ssl-profile"></a>SSL-Profil

Als Reverseproxy kann ein BIG-IP-System als einfacher Weiterleitungsdienst fungieren, der auch als transparenter Proxy bezeichnet wird. Es kann aber auch als vollständiger Proxy eingesetzt werden, der aktiv am Austausch zwischen den Clients und Servern teilnimmt.
Ein vollständiger Proxy richtet zwei separate Verbindungen ein: eine Front-End-TCP-Clientverbindung und eine separate Back-End-TCP-Serververbindung, die beide durch eine flexible Lücke miteinander gekoppelt sind. Clients stellen auf der einen Seite eine Verbindung mit dem Proxylistener her, der auch als **virtueller Server** bezeichnet wird, und der Proxy stellt eine weitere unabhängige Verbindung mit dem Back-End-Server her. Die Verbindungen auf beiden Seiten sind bidirektional.
In diesem Modus mit vollständigem Proxy kann das F5-BIG-IP-System den Datenverkehr überprüfen und daher mit den Anforderungen und Antworten interagieren. Dies ist für bestimmte Funktionen wie Lastenausgleich und die Optimierung der Webleistung sowie erweiterte Dienste zur Datenverkehrsverwaltung notwendig, etwa Sicherheit auf Anwendungsebene, Webbeschleunigung, Seitenrouting und sicheren Remotezugriff.
Beim Veröffentlichen von SSL-basierten Diensten werden die Entschlüsselung und Verschlüsselung des Datenverkehrs zwischen den Clients und den Back-End-Diensten über BIG-IP-SSL-Profile verarbeitet.

Es gibt zwei Profiltypen:

- **Client-SSL:** Die gängigste Methode zum Einrichten eines BIG-IP-Systems zum Veröffentlichen interner Dienste über SSL besteht darin, ein Client-SSL-Profil zu erstellen. Bei einem Client-SSL-Profil kann das BIG-IP-System eingehende Clientanforderungen entschlüsseln, bevor es diese an einen Downstreamdienst sendet. Die ausgehenden Antworten vom Back-End verschlüsselt es dann vor dem Weiterleiten an die Clients.

- **Server-SSL:** Bei Back-End-Diensten, die mit HTTPS konfiguriert sind, können Sie BIG-IP auch für die Verwendung eines Server-SSL-Profils konfigurieren. Mit einem Server-SSL-Profil verschlüsselt BIG-IP die Clientanforderung erneut, bevor es sie an den Back-End-Zieldienst sendet. Wenn der Server eine verschlüsselte Antwort zurückgibt, entschlüsselt das BIG-IP-System die Antwort vor dem Senden an den Client über das konfigurierte Client-SSL-Profil und verschlüsselt sie erneut.

Wenn Sie sowohl Client-SSL- als auch Server-SSL-Profile bereitstellen, ist BIG-IP für alle SHA-Szenarien vorkonfiguriert und einsatzbereit.

1. Wechseln Sie auf der Navigationsleiste auf der linken Seite zu **System** > **Certificate Management** > **Traffic Certificate Management** > **SSL Certificate List** > **Import** (System > Zertifikatverwaltung > Datenverkehrszertifikate verwalten > SSL-Zertifikatliste > Importieren).

2. Wählen Sie in der Dropdownliste **Import Type** (Importtyp) die Option **PKCS 12 (IIS)** aus.

3. Geben Sie einen Namen für das importierte Zertifikat an, z. B. `ContosoWilcardCert`.

4. Wählen Sie **Choose File** (Datei auswählen) aus, um zum SSL-Webzertifikat zu navigieren, dessen Antragstellernamen dem gewünschten Domänensuffix für veröffentlichte Dienste entspricht.

5. Geben Sie das **Kennwort** für das importierte Zertifikat an, und wählen Sie **Import** (Importieren) aus.

6. Wechseln Sie auf der linken Navigationsleiste zu **Local Traffic** > **Profiles** > **SSL** > **Client** (Lokaler Datenverkehr > Profile > SSL > Client), und wählen Sie dann **Create** (Erstellen) aus.

7. Geben Sie auf der Seite **New Client SSL Profile** (Neues Client-SSL-Profil) einen eindeutigen Anzeigenamen für das neue Client-SSL-Profil an, und stellen Sie sicher, dass das übergeordnete Profil auf **clientssl** festgelegt ist.

![Abbildung der BIG-IP-Aktualisierung](./media/f5ve-deployment-plan/client-ssl.png)

8. Aktivieren Sie das Kontrollkästchen ganz rechts in der Zeile **Certificate Key Chain** (Zertifikatschlüsselkette), und wählen Sie **Add** (Hinzufügen) aus.

9. Wählen Sie in den drei Dropdownlisten das Platzhalterzertifikat aus, das Sie ohne Passphrase importiert haben, und wählen Sie dann **Add** > **Finished** (Hinzufügen > Fertig) aus.

![Abbildung der BIG-IP-Aktualisierung für den contoso-Platzhalter](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Wiederholen Sie die Schritte 6–9, um ein **SSL-Serverzertifikatprofil** zu erstellen. Wählen Sie auf dem oberen Menüband **SSL** > **Server** > **Create** (Erstellen) aus.

11. Geben Sie auf der Seite **New Server SSL Profile** (Neues Server-SSL-Profil) einen eindeutigen Anzeigenamen für das neue Server-SSL-Profil an, und stellen Sie sicher, dass das übergeordnete Profil auf **serverssl** festgelegt ist.

12. Aktivieren Sie in den Zeilen „Certificate“ (Zertifikat) und „Key“ (Schlüssel) jeweils das Kontrollkästchen ganz rechts, und wählen Sie in der Dropdownliste Ihr importiertes Zertifikat und dann **Finished** (Fertig) aus.

![Abbildung der BIG-IP-Aktualisierung für das Serverprofil](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Wenn kein SSL-Zertifikat abgerufen werden kann, können Sie die integrierten, selbstsignierten Server- und Client-SSL-Zertifikate von BIG-IP verwenden. Im Browser wird dann allerdings ein Zertifikatfehler angezeigt.

Ein letzter Schritt bei der Vorbereitung von BIG-IP für den sicheren Hybridzugriff (SHA) besteht darin, sicherzustellen, dass das System die veröffentlichten Ressourcen und auch den Verzeichnisdienst für SSO finden kann. BIG-IP verfügt über zwei Quellen für die Namensauflösung. Das System beginnt mit der lokalen Datei „/.../hosts“, und wenn kein Eintrag gefunden wird, verwendet es den DNS-Dienst, der für das BIG-IP-System konfiguriert wurde. Die Methode mit der Datei „hosts“ ist nicht für APM-Knoten und -Pools geeignet, die einen FQDN verwenden.

1. Wechseln Sie in der Webkonfiguration zu **System** > **Configuration** > **Device** > **DNS** (System > Konfiguration > Gerät > DNS).

2. Geben Sie in **DNS Lookup Server List** (Liste der DNS-Lookupserver) die IP-Adresse des DNS-Servers Ihrer Umgebung ein.

3. Wählen Sie **Add** > **Update** (Hinzufügen > Aktualisieren) aus.

Als optionalen Schritt können Sie eine [LDAP-Konfiguration](https://somoit.net/f5-big-ip/authentication-using-active-directory) in Erwägung ziehen, um BIG-IP-Systemadministratoren mit AD zu authentifizieren, anstatt lokale BIG-IP-Konten zu verwalten.

## <a name="update-big-ip"></a>Aktualisieren von BIG-IP

Ihre BIG-IP-VM sollte aktualisiert werden, damit alle im [szenariobasierten Leitfaden](f5-aad-integration.md) behandelten neuen Funktionen entsperrt werden. Sie können die TMOS-Version des Systems überprüfen, indem Sie den Mauszeiger oben links auf der Hauptseite über den BIG-IP-Hostnamen bewegen. Es wird empfohlen, mindestens Version 15.x ausführen, die über die [Downloadseite von F5](https://downloads.f5.com/esd/productlines.jsp) abgerufen werden kann. Verwenden Sie den [Leitfaden](https://support.f5.com/csp/article/K34745165), um das Betriebssystem des Hauptsystems (TMOS) zu aktualisieren.

Wenn das Haupt-TMOS nicht aktualisiert werden kann, sollten Sie zumindest die interaktive Konfiguration upgraden, indem Sie diese Schritte ausführen.

1. Wechseln Sie in der BIG-IP-Webkonfiguration auf der Hauptregisterkarte zu **Access** > **Guided Configuration** (Zugriff > Interaktive Konfiguration).

2. Wählen Sie auf der Seite **Guided Configuration** (Interaktive Konfiguration) die Option **Upgrade Guided Configuration** (Interaktive Konfiguration upgraden) aus.

![Abbildung der BIG-IP-Aktualisierung](./media/f5ve-deployment-plan/update-big-ip.png)

3. Wählen Sie im Dialogfeld **Upgrade Guided Configuration** (Interaktive Konfiguration upgraden) die Option **Choose File** (Datei auswählen) aus, um das heruntergeladene Anwendungsfallpaket hochzuladen, und wählen Sie dann **Upload and Install** (Hochladen und installieren) aus.

4. Wenn das Upgrade abgeschlossen ist, wählen Sie **Continue** (Fortsetzen) aus.

## <a name="backup-big-ip"></a>Sichern von BIG-IP

Nachdem die Bereitstellung des BIG-IP-Systems nun abgeschlossen ist, sollten Sie eine vollständige Sicherung der Konfiguration erstellen:

1. Wechseln Sie zu **System** > **Archives** > **Create** (System > Archive > Erstellen).

2. Geben Sie unter **File Name** einen eindeutigen Dateinamen an, und aktivieren Sie unter **Encryption** die Verschlüsselung mit einer Passphrase.

3. Legen Sie die Option **Private Keys** (Private Schlüssel) auf **Include** (Einschließen) fest, damit auch die Geräte- und SSL-Zertifikate gesichert werden.

4. Wählen Sie **Finished** (Fertig) aus, und warten Sie, bis der Vorgang abgeschlossen ist.

5. Es wird ein Vorgangsstatus für die Sicherungsergebnisse angezeigt. Wählen Sie **OK** aus.

6. Speichern Sie das Archiv für den Benutzerkonfigurationssatz lokal, indem Sie den Link der Sicherung und dann **Download** (Herunterladen) auswählen.

Als optionalen Schritt können Sie auch eine Sicherung des gesamten Systemdatenträgers mithilfe einer [Azure-Momentaufnahme](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk) erstellen. Dies würde im Gegensatz zur Webkonfigurationssicherung eine gewisse Notfalllösung für Tests zwischen TMOS-Versionen oder ein Rollback zu einem neuen System bereitstellen.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>Wiederherstellen von BIG-IP

Das Wiederherstellen eines BIG-IP-Systems folgt einem ähnlichen Verfahren wie die Sicherung. Der Vorgang kann auch für die Migration von Konfigurationen zwischen BIG-IP-VMs verwendet werden. Die Einzelheiten der unterstützten Upgradepfade sollten vor dem Importieren einer Sicherung untersucht werden.

1. Wechseln Sie zu **System** > **Archives** (System > Archive).

2. Wählen Sie entweder den Link einer Sicherung aus, die Sie wiederherstellen möchten, oder wählen Sie die Schaltfläche **Upload** aus, um zu einem zuvor gespeicherten Archiv mit Benutzerkonfigurationssatz zu navigieren, das nicht in der Liste enthalten ist.

3. Geben Sie die Passphrase für die Sicherung an, und wählen Sie **Restore** (Wiederherstellen) aus.

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>Zum aktuellen Zeitpunkt kann das Cmdlet AzVmSnapshot nur die letzte Momentaufnahme basierend auf dem Datum wiederherstellen. Momentaufnahmen werden im Stammverzeichnis der Ressourcengruppe der VM gespeichert. Beachten Sie, dass eine Azure-VM beim Wiederherstellen von Momentaufnahmen neu gestartet wird. Sie sollten diese Vorgänge daher sorgfältig planen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

-   [Zurücksetzen des BIG-IP VE-Kennworts in Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Zurücksetzen des Kennworts ohne das Portal](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Ändern der für die BIG-IP VE-Verwaltung verwendeten NIC](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Informationen zu Routen in einer einzelnen NIC-Konfiguration](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Nächste Schritte

Wählen Sie ein [Bereitstellungsszenario](f5-aad-integration.md) aus, und starten Sie die Implementierung.