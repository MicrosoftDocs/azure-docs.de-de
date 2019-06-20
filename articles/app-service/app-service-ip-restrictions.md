---
title: Einschränken des Zugriffs – Azure App Service | Microsoft-Dokumentation
description: Informationen zu Zugriffseinschränkungen in Azure App Service
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: de898a7ebb9611f469f42bb23774b3b0a0c2410d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65541683"
---
# <a name="azure-app-service-access-restrictions"></a>Azure App Service – Zugriffseinschränkungen #

Mit Zugriffseinschränkungen können Sie eine nach Priorität sortierte Zulassen/Ablehnen-Liste definieren, über die der Zugriff auf Ihre App gesteuert wird. Die Liste kann IP-Adressen oder Azure Virtual Network-Subnetze enthalten. Wenn mindestens ein Eintrag vorhanden ist, enthält die Liste am Ende einen impliziten Eintrag vom Typ „Alle ablehnen“.

Die Funktion „Zugriffseinschränkungen“ funktioniert mit allen von App Service gehosteten Workloads. Hierzu gehören Web-Apps, API-Apps, Linux-Apps, Linux-Container-Apps und Functions.

Wenn eine Anforderung an Ihre App gesendet wird, wird die VON-Adresse anhand der IP-Adressregeln in Ihrer Liste mit den Zugriffseinschränkungen ausgewertet. Wenn die VON-Adresse in einem Subnetz enthalten ist, das mit Dienstendpunkten für Microsoft.Web konfiguriert ist, wird das Quellsubnetz mit den Regeln für das virtuelle Netzwerk in Ihrer Liste mit den Zugriffseinschränkungen verglichen. Wenn der Zugriff für die Adresse basierend auf den Regeln in der Liste nicht zugelassen wird, antwortet der Dienst mit dem Statuscode [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

Die Funktion „Zugriffseinschränkungen“ wird in den App Service-Front-End-Rollen implementiert, die den Workerhosts vorgeschaltet sind, auf denen Ihr Code ausgeführt wird. Es handelt sich somit bei Zugriffseinschränkungen im Grunde um Zugriffssteuerungslisten für das Netzwerk.

Die Möglichkeit, den Zugriff auf Ihre Web-App auf ein Azure Virtual Network (VNET) zu beschränken, wird als [Dienstendpunkte][serviceendpoints] bezeichnet. Mit Dienstendpunkten können Sie den Zugriff auf einen mehrinstanzenfähigen Dienst auf ausgewählte Subnetze beschränken. Dies muss im Netzwerk sowie für den Dienst, für den die Aktivierung gilt, aktiviert sein. Der Datenverkehr für Apps, die in einer App Service-Umgebung gehostet werden, kann dadurch nicht eingeschränkt werden.  In einer App Service-Umgebung können Sie den Zugriff auf Ihre App mit IP-Adressregeln steuern.

![Flow von Zugriffseinschränkungen](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Hinzufügen und Bearbeiten von Regeln für Zugriffseinschränkungen im Portal ##

Um Ihrer App eine Zugriffseinschränkungsregel hinzuzufügen, wählen Sie im Menü **Netzwerk**>**Zugriffseinschränkungen** aus und klicken auf **Zugriffseinschränkungen konfigurieren**.

![App Service-Netzwerkoptionen](media/app-service-ip-restrictions/access-restrictions.png)  

Auf der Benutzeroberfläche für Zugriffseinschränkungen können Sie die Liste mit den Regeln für die Zugriffseinschränkungen überprüfen, die für Ihre App definiert sind.

![Liste mit Zugriffseinschränkungen](media/app-service-ip-restrictions/access-restrictions-browse.png)

In der Liste werden alle aktuellen Einschränkungen für Ihre App angezeigt. Wenn eine VNET-Einschränkung für die App festgelegt ist, wird in der Tabelle angezeigt, ob Dienstendpunkte für Microsoft.Web aktiviert sind. Wenn keine definierten Einschränkungen für die App vorhanden sind, kann von überall aus auf die App zugegriffen werden.  

Klicken Sie auf **[+] Hinzufügen**, um eine neue Zugriffseinschränkungsregel hinzuzufügen. Wenn Sie eine Regel hinzufügen, wird sie sofort wirksam. Regeln werden in der Reihenfolge ihrer Priorität erzwungen, wobei mit der niedrigsten Zahl begonnen wird. Implizit gilt die Regel „Alle ablehnen“, die wirksam ist, sobald Sie die erste Regel hinzugefügt haben.

![Hinzufügen einer IP-Zugriffseinschränkungsregel](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Beim Erstellen einer Regel müssen Sie „Zulassen/Ablehnen“ und außerdem den Typ der Regel auswählen. Sie müssen auch den Prioritätswert festlegen und angeben, worauf der Zugriff beschränkt wird.  Optional können Sie einen Namen und eine Beschreibung für die Regel hinzufügen.  

Um eine auf IP-Adressen basierende Regel hinzuzufügen, wählen Sie einen der Typen IPv4 oder IPv6 aus. Die Notation von IP-Adressen muss für IPv4- und IPv6-Adressen in CIDR-Notation angegeben werden. Zum Angeben einer genauen Adresse können Sie beispielsweise 1.2.3.4/32 verwenden, wobei die ersten vier Oktette für Ihre IP-Adresse stehen und mit /32 die Maske angegeben wird. Die IPv4-CIDR-Notation für alle Adressen ist 0.0.0.0/0. Weitere Informationen zur CIDR-Notation finden Sie unter [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

![Hinzufügen einer VNET-Zugriffseinschränkungsregel](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Um den Zugriff auf ausgewählte Subnetze einzuschränken, wählen Sie den Typ des virtuellen Netzwerks aus. Darunter können Sie das Abonnement, das VNET und das Subnetz auswählen, auf die Sie den Zugriff zulassen oder verweigern möchten. Wenn noch keine Dienstendpunkte mit Microsoft.Web für das ausgewählte Subnetz aktiviert sind, werden sie automatisch aktiviert, es sei denn, Sie aktivieren das Kontrollkästchen, über das dies unterbunden wird. Der Fall, in dem dies für die App, jedoch nicht für das Subnetz aktiviert werden soll, hängt größtenteils davon ab, ob Sie über die Berechtigungen verfügen, Dienstendpunkte für das Subnetz zu aktivieren. Wenn eine andere Person Dienstendpunkte für das Subnetz für Sie aktivieren muss, können Sie das Kontrollkästchen aktivieren, sodass die App für Dienstendpunkte konfiguriert wird, mit der Erwartung, dass diese später für das Subnetz aktiviert werden. 

Der Zugriff auf Apps, die in einer App Service-Umgebung ausgeführt werden, kann nicht mit Dienstendpunkten eingeschränkt werden. Wenn Ihre App in einer App Service-Umgebung enthalten ist, können Sie den Zugriff darauf mit IP-Zugriffsregeln steuern. 

Sie können auf eine beliebige Zeile klicken, um eine vorhandene Zugriffseinschränkungsregel zu bearbeiten. Änderungen sind sofort wirksam. Dies gilt auch für Änderungen der Prioritätsreihenfolge.

![Bearbeiten einer Zugriffseinschränkungsregel](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Wenn Sie eine Regel bearbeiten, können Sie den Typ nicht zwischen einer IP-Adressregel und einer Regel für virtuelle Netzwerke ändern. 

![Bearbeiten einer Zugriffseinschränkungsregel](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Klicken Sie zum Löschen einer Regel für die entsprechende Regel auf **...** und dann auf **Entfernen**.

![Löschen einer Zugriffseinschränkungsregel](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="scm-site"></a>SCM-Website 

Neben der Möglichkeit, den Zugriff auf Ihre App zu steuern, können Sie auch den Zugriff auf die von der App verwendete SCM-Website einschränken. Die SCM-Website ist der Web Deploy-Endpunkt und zudem die Kudu-Konsole. Sie können die Zugriffseinschränkungen für die SCM-Website getrennt von der App zuweisen oder die gleichen Einschränkungen für die App und die SCM-Website verwenden. Wenn Sie das Kontrollkästchen aktivieren, damit die gleichen Einschränkungen wie für die App gelten, wird alles ausgeblendet. Wenn Sie das Kontrollkästchen deaktivieren, werden alle vorherigen Einstellungen für die SCM-Website angewandt. 

![Liste mit Zugriffseinschränkungen](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programmgesteuerte Bearbeitung von Zugriffseinschränkungsregeln ##

Derzeit können Sie weder die CLI noch PowerShell für die neue Funktion „Zugriffseinschränkungen“ verwenden, aber Sie können die Werte manuell per PUT-Vorgang in der App-Konfiguration in Resource Manager festlegen. Sie können beispielsweise „resources.azure.com“ verwenden und den ipSecurityRestrictions-Block bearbeiten, um den erforderlichen JSON-Code hinzuzufügen.

Der Speicherort dieser Informationen im Resource Manager ist:

management.azure.com/subscriptions/**Abonnement-ID**/resourceGroups/**Ressourcengruppen**/providers/Microsoft.Web/sites/**Web-App-Name**/config/web?api-version=2018-02-01

Die JSON-Syntax für das obige Beispiel lautet:

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],

## <a name="function-app-ip-restrictions"></a>IP-Einschränkungen der Funktionen-App

IP-Einschränkungen stehen für beide Funktionen-Apps mit derselben Funktionalität wie App Service-Pläne zur Verfügung. Durch die Aktivierung von IP-Einschränkungen wird der Portalcode-Editor für alle unzulässigen IP-Adressen deaktiviert.

[Hier erhalten Sie weitere Informationen](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
