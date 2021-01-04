---
title: Azure VMware Solution by CloudSimple – Sichere private Cloud
description: Informationen zum Absichern einer privaten CloudSimple-Cloud in Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 000e43a7861bd155ebbd7175db96dd323731464b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518210"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Absichern Ihrer privaten Cloudumgebung

Definieren Sie in Azure die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für den CloudSimple-Dienst, das CloudSimple-Portal und die private Cloud.  Benutzer, Gruppen und Rollen für den Zugriff auf vCenter in der privaten Cloud werden mithilfe von VMware SSO angegeben.  

## <a name="azure-rbac-for-cloudsimple-service"></a>Azure RBAC für den CloudSimple-Dienst

Die Erstellung des CloudSimple-Diensts erfordert die Rolle **Besitzer** oder **Mitwirkender** im Azure-Abonnement.  Standardmäßig können alle Besitzer und Mitwirkende einen CloudSimple-Dienst erstellen und auf das CloudSimple-Portal zugreifen, um private Clouds zu erstellen und zu verwalten.  Pro Region kann nur ein CloudSimple-Dienst erstellt werden.  Führen Sie die folgenden Schritte aus, um den Zugriff auf bestimmte Administratoren zu beschränken.

1. Erstellen eines CloudSimple-Diensts in einer neuen **Ressourcengruppe** im Azure-Portal
2. Geben Sie die Azure RBAC für die Ressourcengruppe an.
3. Erwerben Sie Knoten, und verwenden Sie dieselbe Ressourcengruppe wie für den CloudSimple-Dienst.

Nur die Benutzer mit den Berechtigungen **Besitzer** oder **Mitwirkender** für die Ressourcengruppe können den CloudSimple-Dienst sehen und das CloudSimple-Portal aufrufen.

Weitere Informationen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC für private vCenter-Cloud

Der Standardbenutzer `CloudOwner@cloudsimple.local` wird in der vCenter-SSO-Domäne erstellt, sobald eine private Cloud erstellt wird.  Der Benutzer „CloudOwner“ hat Berechtigungen zum Verwalten von vCenter. vCenter SSO werden zusätzliche Identitätsquellen hinzugefügt, um verschiedenen Benutzern Zugriff zu gewähren.  In vCenter werden vordefinierte Rollen und Gruppen eingerichtet, mit deren Hilfe weitere Benutzer hinzugefügt werden können.

### <a name="add-new-users-to-vcenter"></a>Hinzufügen neuer Benutzer zu vCenter

1. [Eskalieren Sie Berechtigungen](escalate-private-cloud-privileges.md) für **CloudOwner\@cloudsimple.local** -Benutzer in der privaten Cloud.
2. Melden Sie sich mit **CloudOwner\@cloudsimple.local** bei vCenter an.
3. [Fügen Sie vCenter SSO-Benutzer hinzu](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Fügen Sie Benutzer zu [SSO-Gruppen in vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html) hinzu.

Weitere Informationen zu vordefinierten Rollen und Gruppen finden Sie unter [Berechtigungsmodell der privaten CloudSimple-Cloud von VMware-vCenter](learn-private-cloud-permissions.md).

### <a name="add-new-identity-sources"></a>Hinzufügen neuer Identitätsquellen

Sie können zusätzliche Identitätsanbieter zur vCenter SSO-Domäne Ihrer privaten Cloud hinzufügen.  Identitätsanbieter ermöglichen die Authentifizierung, und SSO-Gruppen von vCenter die Autorisierung der Benutzer.

* [Verwenden Sie Active Directory als Identitätsanbieter](set-vcenter-identity.md) für die private vCenter-Cloud.
* [Verwenden Sie Azure AD als Identitätsanbieter](azure-ad.md) für die private vCenter-Cloud.

1. [Eskalieren Sie Berechtigungen](escalate-private-cloud-privileges.md) für **CloudOwner\@cloudsimple.local** -Benutzer in der privaten Cloud.
2. Melden Sie sich mit **CloudOwner\@cloudsimple.local** bei vCenter an.
3. Fügen Sie Benutzer aus dem Identitätsanbieter zu [SSO-Gruppen für vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html) hinzu.

## <a name="secure-network-on-your-private-cloud-environment"></a>Absichern des Netzwerks in Ihrer privaten Cloudumgebung

Die Netzwerksicherheit der privaten Cloudumgebung wird durch die Absicherung des Netzwerkzugriffs und die Steuerung des Netzwerkdatenverkehrs zwischen Ressourcen gewährleistet.

### <a name="access-to-private-cloud-resources"></a>Zugreifen auf private Cloudressourcen

Der Zugriff auf die private vCenter-Cloud und Ressourcen erfolgt über eine sichere Netzwerkverbindung:

* **[ExpressRoute-Verbindung](on-premises-connection.md)** . ExpressRoute bietet eine sichere, breitbandige und latenzarme Verbindung mit Ihrer lokalen Umgebung.  Mithilfe der Verbindung können Ihre lokalen Dienste, Netzwerke und Benutzer auf Ihre private vCenter-Cloud zugreifen.
* **[Site-to-Site-VPN-Gateway](vpn-gateway.md)** . Ein Site-to-Site-VPN ermöglicht den lokalen Zugriff auf Ihre privaten Cloudressourcen über einen sicheren Tunnel.  Sie legen fest, welche lokalen Netzwerke den Netzwerkdatenverkehr an Ihre private Cloud senden und von dieser empfangen können.
* **[Point-to-Site-VPN Gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Verwenden Sie eine Point-to-Site-VPN-Verbindung für den schnellen Remotezugriff auf Ihre private vCenter-Cloud.

### <a name="control-network-traffic-in-private-cloud"></a>Steuern des Netzwerkdatenverkehrs in der privaten Cloud

Firewalltabellen und-regeln steuern den Netzwerkdatenverkehr in der privaten Cloud.  Die Firewalltabelle ermöglicht die Steuerung des Netzwerkdatenverkehrs zwischen einem Quellnetzwerk oder einer IP-Adresse und einem Zielnetzwerk oder einer IP-Adresse basierend auf der in der Tabelle definierten Kombination von Regeln.

1. Erstellen Sie eine [Firewalltabelle](firewall.md#add-a-new-firewall-table).
2. Fügen Sie der [Firewalltabelle Regeln hinzu](firewall.md#create-a-firewall-rule).
3. [Fügen Sie eine Firewalltabelle an ein VLAN/Subnetz an](firewall.md#attach-vlans-subnet).
