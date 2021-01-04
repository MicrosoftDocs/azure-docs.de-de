---
title: Azure-Glossar – Azure-Wörterbuch | Microsoft-Dokumentation
description: Verwenden Sie das Azure-Glossar, um die Cloudterminologie für die Azure Platform zu verstehen. Dieses kompakte Azure-Wörterbuch bietet Definitionen für gängige Cloudbegriffe für Azure.
keywords: Azure dictionary, cloud terminology, Azure glossary, terminology definitions, cloud terms
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 33672e90406b94a1b5788803911c072cdc2577f7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186387"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure-Glossar: Ein Wörterbuch der Cloudterminologie auf der Azure Platform

Das Microsoft Azure-Glossar ist ein kompaktes Wörterbuch der Cloudterminologie für die Azure Platform. Weitere Informationen:

* [Microsoft Azure und Amazon Web Services:](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) Definitionen von Azure-Diensten und ihre AWS-Entsprechungen<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Cloud Computing-Terminologie:](https://azure.microsoft.com/overview/cloud-computing-dictionary/) Allgemeine branchenübliche Cloudbegriffe.

## <a name="account"></a>account
Ein Konto für den Zugriff auf ein Azure-Abonnement und dessen Verwaltung. Es wird häufig als Azure-Konto bezeichnet, kann aber ein vorhandenes Geschäfts-, Schul- oder Unikonto oder ein persönliches Microsoft-Konto sein. Sie können auch ein Konto für die Verwaltung eines Azure-Abonnements erstellen, wenn Sie sich für die [kostenlose Testversion](https://azure.microsoft.com) registrieren.  
Weitere Informationen finden Sie unter [Registrieren für ein Azure-Abonnement mit Ihrem Microsoft 365-Konto](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md) sowie unter [Konten, die für die Anmeldung verwendet werden können](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>API-App
Ein anderer Name für [App Service-App](#app-service-app).

## <a name="app-service-app"></a>App Service-App
Die Computeressourcen, die [Azure App Service](app-service/overview.md) zum Hosten einer Website oder Webanwendung, einer Web-API oder eines [Back-Ends für mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) bereitstellt. App Service-Apps werden auch als *App Services*, *Web-Apps*, *API-Apps* und *mobile Apps* bezeichnet.

## <a name="availability-set"></a>Verfügbarkeitsgruppe
Eine Sammlung virtueller Computer, die gemeinsam verwaltet werden, um Anwendungsredundanz und -zuverlässigkeit zu bieten. Durch die Verwendung einer Verfügbarkeitsgruppe wird sichergestellt, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist.  
Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Windows-Computer](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sowie unter [Verwalten der Verfügbarkeit virtueller Linux-Computer](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Klassisches Azure-Bereitstellungsmodell
Eines der zwei [Bereitstellungsmodelle](./azure-resource-manager/management/deployment-models.md), die in Azure zum Bereitstellen von Ressourcen verwendet werden. (Das neue Modell ist Azure Resource Manager.) Einige Azure-Dienste unterstützen nur das Resource Manager-Bereitstellungsmodell, andere nur das klassische Bereitstellungsmodell und wieder andere unterstützen beide Modelle. Welches Modell bzw. welche Modelle der jeweilige Azure-Dienst unterstützt, können Sie der Dokumentation des entsprechenden Diensts entnehmen.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Azure-Befehlszeilenschnittstelle (CLI)
Eine Befehlszeilenschnittstelle zum Verwalten von Azure-Diensten unter Windows, macOS und Linux.  Einige Dienste oder Dienstfeatures können nur über PowerShell oder die CLI verwaltet werden. Weitere Informationen finden Sie unter [Azure CLI](/cli/azure).

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Eine Befehlszeilenschnittstelle zum Verwalten von Azure-Diensten über eine Befehlszeile auf einem Windows-PC. Einige Dienste oder Dienstfeatures können nur über PowerShell oder die CLI verwaltet werden.
Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Azure Resource Manager-Bereitstellungsmodell
Eines der zwei [Bereitstellungsmodelle](./azure-resource-manager/management/deployment-models.md), die in Microsoft Azure zum Bereitstellen von Ressourcen verwendet werden. (Das andere ist das klassische Bereitstellungsmodell.) Einige Azure-Dienste unterstützen nur das Resource Manager-Bereitstellungsmodell, andere nur das klassische Bereitstellungsmodell und wieder andere unterstützen beide Modelle. Welches Modell bzw. welche Modelle der jeweilige Azure-Dienst unterstützt, können Sie der Dokumentation des entsprechenden Diensts entnehmen.

## <a name="fault-domain"></a>Fehlerdomäne
Die Sammlung virtueller Computer in einer Verfügbarkeitsgruppe, die theoretisch zur selben Zeit ausfallen können. Ein Beispiel ist eine Gruppe von Computern in einem Rack, die gemeinsam eine Stromquelle und denselben Netzwerkswitch verwenden. In Azure werden die virtuellen Computer in einer Verfügbarkeitsgruppe automatisch auf mehrere Fehlerdomänen verteilt.  
Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Windows-Computer](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sowie unter [Verwalten der Verfügbarkeit virtueller Linux-Computer](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="geo"></a>Geografischer Raum
Eine definierte Grenze für den Speicherort von Daten, der typischerweise zwei oder mehr Regionen umfasst. Die Grenzen können innerhalb nationaler Grenzen liegen oder über diese hinausgehen und verschiedenen steuerlichen Bestimmungen unterliegen. Jeder geografische Raum umfasst mindestens eine Region. Beispiele für geografische Räume sind Asien-Pazifik und Japan. Auch bezeichnet als *Geografie*.  
Weitere Informationen finden Sie unter [Azure-Regionen](best-practices-availability-paired-regions.md).

## <a name="geo-replication"></a>Georeplikation
Der Vorgang der automatischen Replikation von Inhalten wie beispielsweise Blobs, Tabellen und Warteschlangen innerhalb eines Regionspaars.  
Weitere Informationen finden Sie unter [Aktive Georeplikation für Azure SQL-Datenbank](./azure-sql/database/auto-failover-group-overview.md).
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Eine Datei, die das Betriebssystem und die Anwendungskonfiguration enthält, und die zum Erstellen einer beliebigen Anzahl von virtuellen Computern verwendet werden kann. In Azure können zwei Arten von Images verwendet werden: VM-Images und Betriebssystem-Images. Ein VM-Image enthält ein Betriebssystem und alle Datenträger, die an einen virtuellen Computer angefügt sind, wenn das Image erstellt wird. Ein Betriebssystem-Image enthält nur ein generalisiertes Betriebssystem ohne Datenträgerkonfigurationen.  
Weitere Informationen finden Sie unter [Navigieren zu und Auswählen von Images virtueller Windows-Computer in Azure mithilfe von PowerShell oder der Befehlszeilenschnittstelle](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="limits"></a>Grenzwerte
Die Anzahl von Ressourcen, die erstellt werden können oder der Leistungsbenchmark, der erzielt werden kann. Grenzwerte werden üblicherweise Abonnements, Diensten und Angeboten zugeordnet.  
Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="load-balancer"></a>Lastenausgleich
Eine Ressource, die den eingehenden Datenverkehr auf die Computer in einem Netzwerk verteilt. In Azure verteilt ein Load Balancer den Datenverkehr auf virtuelle Computer, die in einer Lastenausgleichsgruppe definiert sind. Ein [Load Balancer](load-balancer/load-balancer-overview.md) kann mit Internetverbindung oder intern eingerichtet werden.  

## <a name="mobile-app"></a>Mobile App
Ein anderer Name für [App Service-App](#app-service-app).

## <a name="offer"></a>offer
Die Preise, Gutschriften und Bedingungen, die für ein Azure-Abonnement gelten.  
Siehe [Azure-Angebotsdetails](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>Portal
Das sichere Webportal zum Bereitstellen und Verwalten von Azure-Diensten.

## <a name="region"></a>region
Ein Bereich innerhalb eines geografischen Raums ohne Überschreitung nationaler Grenzen, der mindestens ein Rechenzentrum umfasst. Preise, regionale Dienste und Angebotstypen werden auf Regionsebene zur Verfügung gestellt. Eine Region bildet üblicherweise ein Paar mit einer anderen Region, die mehrere Hundert Kilometer entfernt sein kann. Regionspaare können als Mechanismus zur Notfallwiederherstellung und in Szenarien mit Hochverfügbarkeit eingesetzt werden. Wird auch als *Standort* bezeichnet.  
Weitere Informationen finden Sie unter [Azure-Regionen](best-practices-availability-paired-regions.md).

## <a name="resource"></a>resource
Ein Element, das Bestandteil Ihrer Azure-Lösung ist. Jeder Azure-Dienst ermöglicht Ihnen das Bereitstellen unterschiedlicher Ressourcentypen, z. B. Datenbanken oder virtuelle Computer.   
Weitere Informationen finden Sie unter [Übersicht über den Azure Resource Manager](azure-resource-manager/management/overview.md).

## <a name="resource-group"></a>Ressourcengruppe
Eine Container, der verwandte Ressourcen für eine Anwendung enthält. Die Ressourcengruppe kann alle Ressourcen für eine Anwendung enthalten, oder nur die Ressourcen, die logisch gruppiert sind. Je nachdem, was für Ihre Organisation am sinnvollsten ist, können Sie entscheiden, wie Sie die Ressourcen den Ressourcengruppen zuordnen möchten.  
Weitere Informationen finden Sie unter [Übersicht über den Azure Resource Manager](azure-resource-manager/management/overview.md).

## <a name="resource-manager-template"></a><a name="arm-template"></a>Resource Manager-Vorlage
Eine JSON-Datei, die deklarativ eine oder mehrere Azure-Ressourcen definiert, und die Abhängigkeiten zwischen den bereitgestellten Ressourcen definiert. Die Vorlage kann zum konsistenten und wiederholten Bereitstellen der Ressourcen verwendet werden.  
Weitere Informationen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](./azure-resource-manager/templates/template-syntax.md).

## <a name="resource-provider"></a>Ressourcenanbieter
Ein Dienst, über den Sie die Ressourcen beziehen, die Sie über Resource Manager bereitstellen und verwalten können. Jeder Ressourcenanbieter bietet Vorgänge zum Arbeiten mit den bereitgestellten Ressourcen. Auf Ressourcenanbieter kann über das Azure-Portal, Azure PowerShell und verschiedene SDKs für die Programmierung zugegriffen werden.  
Weitere Informationen finden Sie unter [Übersicht über den Azure Resource Manager](azure-resource-manager/management/overview.md).

## <a name="role"></a>Rolle (role)
Eine Möglichkeit zum Steuern des Zugriffs, die Benutzern, Gruppen und Diensten zugewiesen werden kann. Rollen können verschiedene Aktionen ausführen und so beispielsweise Azure-Ressourcen erstellen, verwalten und lesen.  
Siehe [RBAC: Integrierte Rollen](role-based-access-control/built-in-roles.md).

## <a name="service-level-agreement-sla"></a><a name="sla"></a>Vereinbarung zum Servicelevel (SLA)
In dieser Vereinbarung ist die garantierte Verfügbarkeit und Konnektivität beschrieben, die Microsoft zusichert. Jeder Azure-Dienst verfügt über eine bestimmte Vereinbarung zum Servicelevel.  
Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/).

## <a name="shared-access-signature-sas"></a><a name="sas"></a>Shared Access Signature (SAS)
Eine Signatur, mit der Sie eingeschränkten Zugriff auf eine Ressource gewähren können, ohne Ihren Kontoschlüssel offenzulegen. Ein Beispiel: [Azure Storage verwendet SAS](./storage/common/storage-sas-overview.md), um Clients Zugriff auf Objekte (beispielsweise Blobs) zu gewähren. [IoT Hub verwendet SAS](iot-hub/iot-hub-devguide-security.md#security-tokens), um Geräten Berechtigungen zum Senden von Telemetriedaten zu erteilen.

## <a name="storage-account"></a>Speicherkonto
Ein Konto, das den Zugriff auf die Blob-, Warteschlangen-, Tabellen- und Dateidienste in Azure Storage ermöglicht. Der Name des Speicherkontos definiert den eindeutigen Namespace für Azure Storage-Datenobjekte.  
Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](./storage/common/storage-account-create.md).

## <a name="subscription"></a>Abonnement
Eine Vereinbarung des Kunden mit Microsoft, die die Inanspruchnahme von Azure-Diensten ermöglicht. Die Abonnementpreise und -bedingungen richten sich nach dem ausgewählten Angebot für das Abonnement.
Weitere Informationen finden Sie unter [Microsoft Online-Abonnementvertrag](https://azure.microsoft.com/support/legal/subscription-agreement/) sowie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="tag"></a>tag
Ein Begriff aus dem Bereich der Indizierung, mit dem Sie Ressourcen gemäß Ihren Anforderungen für die Verwaltung oder Abrechnung kategorisieren können. Die Verwendung von Tags kann hilfreich sein, wenn Sie über eine komplexe Sammlung von Ressourcen verfügen und diese auf möglichst sinnvolle Weise visualisieren möchten. Beispielsweise können Sie Ressourcen markieren, die in Ihrer Organisation eine ähnliche Funktion haben oder zu derselben Abteilung gehören.  
Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](./azure-resource-manager/management/tag-resources.md).

## <a name="update-domain"></a>Updatedomäne
Die Sammlung virtueller Computer in einer Verfügbarkeitsgruppe, die gleichzeitig aktualisiert werden. Virtuelle Computer in derselben Updatedomäne werden während einer geplanten Wartung gemeinsam neu gestartet. Azure startet nie mehr als eine Updatedomäne gleichzeitig. Wird auch als Upgradedomäne bezeichnet.  
Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Windows-Computer](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sowie unter [Verwalten der Verfügbarkeit virtueller Linux-Computer](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="virtual-machine"></a><a name="vm"></a>Virtueller Computer
Die Softwareimplementierung eines physischen Computers, der ein Betriebssystem ausführt. Auf derselben Hardware können mehrere virtuelle Computer gleichzeitig ausgeführt werden. In Azure stehen virtuelle Computer in unterschiedlichsten Größen zur Verfügung.  
Weitere Informationen finden Sie in der [Dokumentation zu virtuellen Computern](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>VM-Erweiterung
Eine Ressource, die Verhalten oder Features implementiert, die entweder andere Programme bei der Arbeit unterstützen oder Ihnen die Möglichkeit bieten, mit einem ausgeführten Computer zu interagieren. Beispielsweise können Sie die VM-Erweiterung für den Zugriff dazu verwenden, die Werte für den Remotezugriff auf einem virtuellen Azure-Computer zurückzusetzen oder zu ändern.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Weitere Informationen finden Sie unter [Informationen zu Erweiterungen und Features für virtuelle Computer (Windows)](./virtual-machines/extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sowie unter [Informationen zu Erweiterungen und Features für virtuelle Computer (Linux)](./virtual-machines/extensions/features-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="virtual-network"></a><a name="vnet"></a>Virtuelles Netzwerk
Ein Netzwerk, das für Konnektivität zwischen Ihren Azure-Ressourcen sorgt und von allen anderen Azure-Mandanten isoliert ist. Mit einem [Azure-VPN-Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) können Sie Verbindungen zwischen virtuellen Netzwerken und zwischen einem virtuellen und einem lokalen Netzwerk herstellen. Sie haben uneingeschränkte Kontrolle über die IP-Adressblöcke, DNS-Einstellungen, Sicherheitsrichtlinien und Routentabellen innerhalb eines virtuellen Netzwerks.  
Weitere Informationen finden Sie unter [Virtuelle Netzwerke im Überblick](virtual-network/virtual-networks-overview.md).  

## <a name="web-app"></a>Web-App
Ein anderer Name für [App Service-App](#app-service-app).

## <a name="see-also"></a>Weitere Informationen

* [Erste Schritte mit Azure](https://azure.microsoft.com/get-started/)
* [Ressourcen für Ihren Einstieg in die Cloud](https://azure.microsoft.com/resources/)  
* [Azure für Ihre Geschäftsanwendungen](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure in Ihrem Rechenzentrum](https://azure.microsoft.com/overview/business-apps-on-azure/)