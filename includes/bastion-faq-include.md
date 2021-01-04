---
title: include file
description: include file
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48a7912b1a6375111b6f9af1496cbbd41966202c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183892"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Welche Regionen sind verfügbar?

[!INCLUDE [Azure Bastion region availability](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Benötige ich eine öffentliche IP-Adresse auf meinem virtuellen Computer, um eine Verbindung über Azure Bastion herzustellen?

Nein. Wenn Sie mithilfe von Azure Bastion eine Verbindung mit einem virtuellen Computer herstellen, benötigen Sie auf diesem virtuellen Azure-Computer keine öffentliche IP-Adresse. Der Bastion-Dienst öffnet die RDP-/SSH-Sitzung oder -Verbindung mit Ihrem virtuellen Computer über die private IP des virtuellen Computers in Ihrem virtuellen Netzwerk.

### <a name="is-ipv6-supported"></a>Wird IPv6 unterstützt?

Derzeit wird IPv6 nicht unterstützt. Azure Bastion unterstützt nur IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Benötige ich einen RDP- oder SSH-Client?

Nein. Sie benötigen keinen RDP- oder SSH-Client, um auf das RDP/SSH Ihres virtuellen Computers über das Azure-Portal zuzugreifen. Verwenden Sie das [Azure-Portal](https://portal.azure.com), um direkt über den Browser RDP-/SSH-Zugriff auf Ihren virtuellen Computer zu erhalten.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Muss auf dem virtuellen Azure-Computer ein Agent ausgeführt werden?

Nein. Sie müssen keinen Agent oder eine andere Software in Ihrem Browser oder auf Ihrem virtuellen Azure-Computer installieren. Für den Bastion-Dienst ist kein Agent und keine zusätzliche Software für RDP/SSH erforderlich.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Wie viele gleichzeitige RDP-und SSH-Sitzungen werden von den einzelnen Azure Bastion-Instanzen unterstütz?

RDP und SSH sind beides nutzungsbasierte Protokolle. Eine umfassende Nutzung von Sitzungen führt dazu, dass der Bastionhost eine niedrigere Gesamtzahl von Sitzungen unterstützt. Bei den folgenden Zahlen wird von normalen täglichen Workflows ausgegangen:

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Welche Funktionen werden in einer RDP-Sitzung unterstützt?

Derzeit wird nur das Kopieren/Einfügen von Text unterstützt. Features wie das Kopieren von Dateien werden nicht unterstützt. Sie können uns Ihr Feedback zu neuen Features auf der [Azure Bastion-Feedbackseite](https://feedback.azure.com/forums/217313-networking?category_id=367303) mitteilen.

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Funktioniert die Bastion-Härtung bei virtuellen Computern, die mit der AADJ VM-Erweiterung verknüpft sind?

Dieses Feature funktioniert nicht bei Computern, die mit der AADJ VM-Erweiterung verknüpft sind, unter Verwendung von Azure AD-Benutzern. Weitere Informationen finden Sie unter [Virtuelle Windows Azure-Computer und Azure AD](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Welche Browser werden unterstützt?

Verwenden Sie unter Windows den Microsoft Edge-Browser oder Google Chrome. Verwenden Sie unter macOS Google Chrome. Microsoft Edge Chromium wird auch für Windows und Mac unterstützt.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Wo speichert Azure Bastion Kundendaten?

Azure Bastion speichert Kundendaten in der Region, in der sie bereitgestellt werden, und verschiebt sie nicht aus dieser Region.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Sind für den Zugriff auf einen virtuellen Computer Rollen erforderlich?

Zum Herstellen einer Verbindung sind die folgenden Rollen erforderlich:

* Rolle „Leser“ auf dem virtuellen Computer
* Rolle „Leser“ auf dem Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Wie sieht die Preisgestaltung aus?

Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Erfordert Azure Bastion auf virtuellen Computern, die von Azure gehostet werden, eine RDS-CAL für administrative Aufgaben?

Nein. Für den Zugriff auf virtuelle Windows Server-Computer durch Azure Bastion ist keine [RDS-CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) erforderlich, wenn sich die Verwendung rein auf administrative Aufgaben beschränkt.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Welche Tastaturlayouts werden während der Bastion-Remotesitzung unterstützt?

Azure Bastion unterstützt derzeit das Tastaturlayout „en-us-qwerty“ innerhalb der VM.  Die Unterstützung anderer Gebietsschemas für das Tastaturlayout ist in Bearbeitung.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Wird benutzerdefiniertes Routing (User-Defined Routing, UDR) in einem Azure Bastion-Subnetz unterstützt?

Nein. UDR wird in einem Azure Bastion-Subnetz nicht unterstützt.

In Szenarien, bei denen Azure Bastion und Azure Firewall bzw. ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) im gleichen virtuellen Netzwerk vorhanden sind, müssen Sie den Datenverkehr zwischen einem Azure Bastion-Subnetz und Azure Firewall nicht erzwingen, da die Kommunikation zwischen Azure Bastion und Ihren VMs privat ist. Weitere Informationen finden Sie unter [Zugreifen auf virtuelle Computer hinter Azure Firewall mit Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Warum erhalte ich vor Beginn der Bastion-Sitzung die Fehlermeldung „Ihre Sitzung ist abgelaufen.“?

Eine Sitzung darf nur über das Azure-Portal initiiert werden. Melden Sie sich beim Azure-Portal an, und starten Sie Ihre Sitzung erneut. Dieser Fehler ist zu erwarten, wenn Sie direkt von einer anderen Browsersitzung oder -registerkarte aus zu der URL navigieren. Er trägt zum Schutz Ihrer Sitzung bei und sorgt dafür, dass nur über das Azure-Portal auf die Sitzung zugegriffen werden kann.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Behandeln von Bereitstellungsfehlern

Überprüfen Sie die Fehlermeldungen, und stellen Sie bei Bedarf eine [Supportanfrage im Azure-Portal](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). Bereitstellungsfehler können durch [Einschränkungen für Azure-Abonnements, Kontingente und sonstige Einschränkungen](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) verursacht werden. Insbesondere können Kunden eine Beschränkung der Anzahl von zulässigen öffentlichen IP-Adressen pro Abonnement feststellen, die zu einem Fehler bei der Azure Bastion-Bereitstellung führen.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Wie integriere ich Azure Bastion in meinen Plan zur Notfallwiederherstellung?

Azure Bastion wird innerhalb von VNets oder VNets mit Peering bereitgestellt und ist einer Azure-Region zugeordnet. Sie sind verantwortlich für die Bereitstellung von Azure Bastion in einem VNet an einem Notfallwiederherstellungsstandort. Führen Sie im Falle eines Ausfalls der Azure-Region einen Failovervorgang für Ihre virtuellen Computer in die Notfallwiederherstellungsregion durch. Verwenden Sie dann den Azure-Bastion-Host, der in der Azure-Region bereitgestellt wird, um eine Verbindung mit den virtuellen Computern herzustellen, die dort jetzt bereitgestellt werden.