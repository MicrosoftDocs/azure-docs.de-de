---
title: Traffic Manager-Endpunkttypen | Microsoft Docs
description: In diesem Artikel werden die unterschiedlichen Arten von Endpunkten beschrieben, die mit Azure Traffic Manager verwendet werden können.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051226"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-Endpunkte

Mit Microsoft Azure Traffic Manager können Sie steuern, wie Datenverkehr im Netzwerk auf Anwendungsbereitstellungen in verschiedenen Rechenzentren verteilt wird. Sie konfigurieren jede Anwendungsbereitstellung in Traffic Manager als „Endpunkt“. Wenn Traffic Manager eine DNS-Anforderung empfängt, wählt er einen verfügbaren Endpunkt, der in der DNS-Antwort zurückgegeben wird. Traffic Manager trifft die Auswahl nach dem aktuellen Endpunktstatus und der Methode für das Datenverkehrsrouting. Weitere Informationen finden Sie unter [Funktionsweise von Traffic Manager](traffic-manager-how-it-works.md).

Es gibt drei Arten von Endpunkten, die von Traffic Manager unterstützt werden:

* **Azure-Endpunkte** werden für Dienste verwendet, die unter Azure gehostet werden.
* **Externe Endpunkte** werden für IPv4/IPv6-Adressen, FQDNs oder außerhalb von Azure gehostete Dienste verwendet. Diese Dienste können entweder lokal sein oder von einem anderen Anbieter gehostet werden.
* **Geschachtelte Endpunkte** werden zum Kombinieren von Traffic Manager-Profilen verwendet, um flexiblere Schemas für das Routing von Datenverkehr zu erstellen. So können die Anforderungen von größeren und komplexeren Bereitstellungen erfüllt werden.

Es gibt keine Einschränkung, wie Endpunkte unterschiedlichen Typs in einem Traffic Manager-Profil kombiniert werden können. Jedes Profil kann eine beliebige Mischung von Endpunkttypen enthalten.

In den folgenden Abschnitten werden die einzelnen Endpunkttypen ausführlicher beschrieben.

## <a name="azure-endpoints"></a>Azure-Endpunkte

Azure-Endpunkte werden für Azure-basierte Dienste in Traffic Manager verwendet. Die folgenden Typen von Azure-Ressourcen werden unterstützt:

* PaaS-Clouddienste.
* Web Apps
* Web-App-Slots
* PublicIPAddress-Ressourcen (können entweder direkt oder über einen Azure Load Balancer mit VMs verbunden werden) Der publicIpAddress-Ressource muss ein DNS-Name zugewiesen werden, damit sie in einem Traffic Manager-Profil verwendet werden kann.

PublicIPAddress-Ressourcen sind Azure Resource Manager-Ressourcen. Sie sind im klassischen Bereitstellungsmodell nicht vorhanden und werden nur in Traffic Manager in Verbindung mit Azure Resource Manager unterstützt. Die anderen Endpunkttypen werden über Resource Manager und das klassische Bereitstellungsmodell unterstützt.

Bei der Verwendung von Azure-Endpunkten erkennt Traffic Manager, wenn eine Web-App beendet und gestartet wird. Dieser Status wird im Endpunktstatus wiedergegeben. Weitere Informationen finden Sie unter [Traffic Manager-Endpunktüberwachung](traffic-manager-monitoring.md#endpoint-and-profile-status). Wenn der zugrunde liegende Dienst beendet wird, führt Traffic Manager keine Endpunkt-Systemdiagnosen oder direkten Datenverkehr zum Endpunkt durch. Für die beendete Instanz treten keine Traffic Manager-Abrechnungsereignisse auf. Wenn der Dienst neu gestartet wird, wird die Abrechnung fortgesetzt und der Endpunkt ist berechtigt, Datenverkehr zu empfangen. Diese Erkennung gilt nicht für PublicIpAddress-Endpunkte.

## <a name="external-endpoints"></a>Externe Endpunkte

Externe Endpunkte werden entweder für IPv4/IPv6-Adressen, FQDNs oder für Dienste außerhalb von Azure verwendet. Die Verwendung von IPv4/IPv6-Adressendpunkten ermöglicht es Traffic Manager, den Zustand von Endpunkten zu überprüfen, ohne dass für sie ein DNS-Name erforderlich ist. Dadurch kann Traffic Manager auf Abfragen mit A/AAAA-Einträgen reagieren, wenn dieser Endpunkt in einer Antwort zurückgegeben wird. Dienste außerhalb von Azure können einen Dienst beinhalten, der lokal oder bei einem anderen Anbieter gehostet wird. Externe Endpunkte können allein oder in Kombination mit Azure-Endpunkten in demselben Traffic Manager-Profil verwendet werden. Die Ausnahme gilt für Endpunkte, die als IPv4- oder IPv6-Adressen angegeben werden, bei denen es sich nur um externe Endpunkte handeln kann. Die Kombination von Azure-Endpunkten mit externen Endpunkten ermöglicht unterschiedliche Szenarios:

* Bereitstellen einer erhöhten Redundanz für eine vorhandene lokale Anwendung in einem Aktiv/Aktiv- oder Aktiv/Passiv-Failovermodell mit Azure. 
* Weiterleiten von Datenverkehr an Endpunkte, denen kein DNS-Name zugeordnet ist. Verringert außerdem die allgemeine DNS-Nachschlagelatenzzeit, indem die Notwendigkeit aufgehoben wird, eine zweite DNS-Abfrage auszuführen, um eine IP-Adresse eines DNS-Namens zurückzugeben.
* Verringern der Anwendungslatenz für Benutzer auf der ganzen Welt, Erweitern einer vorhandenen lokalen Anwendung auf andere geografische Standorte in Azure. Weitere Informationen finden Sie unter [Traffic Manager Leistungsdatenverkehrrouting](traffic-manager-routing-methods.md#performance).
* Stellen Sie mit Azure weitere Kapazität für eine vorhandene lokale Anwendung bereit, entweder fortlaufend oder als „Burst-to-Cloud“ für Spitzenlasten.

In bestimmten Fällen ist es hilfreich, externe Endpunkte zum Verweisen auf Azure-Dienste zu verwenden. Beispiele finden Sie in den [häufig gestellten Fragen](traffic-manager-faqs.md#traffic-manager-endpoints). Die Integritätsprüfungen werden mit der Rate der Azure-Endpunkte abgerechnet, nicht mit der Rate der externen Endpunkte. Anders als bei Azure-Endpunkten wird die Abrechnung der Integritätsprüfung fortgesetzt, wenn Sie den zugrunde liegenden Dienst beenden oder löschen. Die Abrechnung wird beendet, sobald Sie den Endpunkt in Traffic Manager deaktivieren oder löschen.

## <a name="nested-endpoints"></a>Geschachtelte Endpunkte

Geschachtelte Endpunkte werden zum Kombinieren von Traffic Manager-Profilen verwendet, um flexiblere Schemas für das Routing von Datenverkehr zu erstellen. So können die Anforderungen von größeren und komplexeren Bereitstellungen erfüllt werden. Bei geschachtelten Endpunkten wird ein „untergeordnetes“ Profil einem „übergeordneten“ Profil als Endpunkt hinzugefügt. Die untergeordneten und übergeordneten Profile können wiederum andere Endpunkte eines beliebigen Typs enthalten, z.B. auch andere geschachtelte Profile. 

Weitere Informationen finden Sie unter [Geschachtelte Traffic Manager-Profile](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web-Apps als Endpunkte

Es gelten noch einige weitere Aspekte in Bezug auf die Konfiguration von Web-Apps als Endpunkte in Traffic Manager:

1. Nur Web-Apps mit der SKU „Standard“ oder höher können mit Traffic Manager verwendet werden. Der Versuch, eine Web App mit einer niedrigeren SKU hinzuzufügen, löst einen Fehler aus. Das Herabstufen der SKU einer vorhandenen Web-App führt dazu, dass Traffic Manager keinen Datenverkehr mehr an diese Web-App sendet. Weitere Informationen zu unterstützten Plänen finden Sie unter [App Service-Pläne](https://azure.microsoft.com/pricing/details/app-service/plans/).
2. Wenn ein Endpunkt eine HTTP-Anforderung empfängt, wird der „Hostheader“ in der Anforderung verwendet, um zu bestimmen, welche Web-App die Anforderung verarbeiten soll. Der Hostheader enthält den DNS-Namen zum Starten der Anforderung, z. B. „contosoapp.azurewebsites.net“. Um einen anderen DNS-Namen mit Ihrer Web-App verwenden zu können, muss der DNS-Name als benutzerdefinierter Domänenname für die App registriert sein. Wenn ein Web-App-Endpunkt als Azure-Endpunkt hinzugefügt wird, wird der DNS-Name des Traffic Manager-Profils automatisch für die App registriert. Diese Registrierung wird automatisch entfernt, wenn der Endpunkt gelöscht wird.
3. Jedes Traffic Manager-Profil kann maximal einen Web-App-Endpunkt aus jeder Azure-Region aufweisen. Zur Umgehung dieser Einschränkung können Sie eine Web-App als externen Endpunkt konfigurieren. Weitere Informationen finden Sie in den [häufig gestellten Fragen](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Aktivieren und Deaktivieren von Endpunkten

Das Deaktivieren eines Endpunkts in Traffic Manager kann sehr hilfreich sein, um Datenverkehr vorübergehend von einem Endpunkt zu entfernen, der sich im Wartungsmodus befindet oder der erneut bereitgestellt wird. Sobald der Endpunkt wieder betriebsbereit ist, kann er erneut aktiviert werden.

Sie können Endpunkte über das Traffic Manager-Portal, PowerShell, die CLI oder die REST-API aktivieren oder deaktivieren.

> [!NOTE]
> Das Deaktivieren eines Azure-Endpunkts hat nichts mit dessen Bereitstellungsstatus in Azure zu tun. Ein Azure-Dienst (z.B. eine VM oder eine Webanwendung) wird weiterhin ausgeführt und kann auch dann Datenverkehr empfangen, wenn er im Traffic Manager deaktiviert ist. Datenverkehr kann nicht über den Traffic Manager-Profil DNS-Namen, sondern direkt an die Dienstinstanz adressiert werden. Weitere Informationen finden Sie unter [Funktionsweise von Traffic Manager](traffic-manager-how-it-works.md).

Die aktuelle Berechtigung jedes Endpunkts zum Empfangen von Datenverkehr hängt von den folgenden Faktoren ab:

* Profilstatus (aktiviert/deaktiviert)
* Endpunktstatus (aktiviert/deaktiviert)
* Ergebnisse der Integritätsüberprüfungen für diesen Endpunkt

Weitere Informationen finden Sie unter [Traffic Manager-Endpunktüberwachung](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Da der Traffic Manager auf DNS-Ebene verwendet wird, kann er vorhandene Verbindungen mit Endpunkten nicht beeinflussen. Wenn ein Endpunkt nicht verfügbar ist, leitet Traffic Manager neue Verbindungen zu einem anderen verfügbaren Endpunkt. Allerdings kann der Host hinter dem deaktivierten oder fehlerhaften Endpunkt unter Umständen weiterhin Datenverkehr über vorhandene Verbindungen empfangen, bis diese Sitzungen beendet werden. Anwendungen sollten die Sitzungsdauer beschränken, damit der Datenverkehr von bestehenden Verbindungen ausgeglichen werden kann.

Wenn alle Endpunkte eines Profils deaktiviert werden oder das Profil selbst deaktiviert wird, beantwortet Traffic Manager eine neue DNS-Abfrage mit einer „NXDOMAIN“-Antwort.

## <a name="faqs"></a>Häufig gestellte Fragen

* [Kann ich Traffic Manager mit Endpunkten mehrerer Abonnements verwenden?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Kann ich Traffic Manager mit „Stagingslots“ des Clouddiensts verwenden?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Unterstützt Traffic Manager IPv6-Endpunkte?](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [Kann ich Traffic Manager mit mehr als einer Web-App in derselben Region verwenden?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Wie werden die Azure-Endpunkte meines Traffic Manager-Profils in eine andere Ressourcengruppe verschoben?](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur [Funktionsweise von Traffic Manager](traffic-manager-how-it-works.md)
* Informationen zu [Endpunktüberwachung und automatisches Failover](traffic-manager-monitoring.md)von Traffic Manager
* Informationen zu Traffic Manager- [Routingmethoden für Datenverkehr](traffic-manager-routing-methods.md)