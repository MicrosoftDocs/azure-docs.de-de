---
title: Getaktete Abrechnung für verwaltete Anwendungen mit dem Marketplace-Messungsdienst | Azure Marketplace
description: Diese Dokumentation ist ein Leitfaden für ISVs, die Azure-Anwendungen mit flexiblen Abrechnungsmodellen veröffentlichen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d015cec30e516541b50c2acfac38fad898965e1b
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436350"
---
# <a name="managed-application-metered-billing"></a>Getaktete Abrechnung für verwaltete Anwendungen 

Mit dem Marketplace-Messungsdienst können Sie verwaltete Anwendungspläne für Azure-Anwendungsangebote erstellen, die nach nicht standardmäßigen Einheiten abgerechnet werden. Vor der Veröffentlichung dieses Angebots definieren Sie die Abrechnungsdimensionen wie Bandbreite, Tickets oder verarbeitete E-Mails. Kunden zahlen dann auf der Grundlage ihrer Nutzung dieser Dimensionen.  Ihr System informiert Microsoft über die Marketplace-Messungsdienst-API über abrechenbare Ereignisse, sobald diese auftreten.

## <a name="prerequisites-for-metered-billing"></a>Voraussetzungen für die getaktete Abrechnung

Damit ein verwalteter Anwendungsplan die getaktete Abrechnung verwenden kann, ist Folgendes erforderlich:

* Es müssen alle Angebotsanforderungen erfüllt sein, die unter [Erstellen eines Azure-Anwendungsangebots](../create-new-azure-apps-offer.md) beschrieben sind.
* Der **Preis** muss so konfiguriert sein, dass den Kunden die Kosten pro Monat für Ihren Dienst berechnet werden. Der Preis kann null sein, wenn Sie keine feste Gebühr berechnen möchten und stattdessen ausschließlich die getaktete Abrechnung verwenden.
* Es müssen **Abrechnungsdimensionen** für die Messungsereignisse festgelegt werden, die der Kunde zusätzlich zum Pauschalpreis bezahlen soll.
* Es muss in die [Marketplace-Messungsdienst-APIs](./marketplace-metering-service-apis.md) integriert werden, um Microsoft über abrechenbare Ereignisse zu informieren.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Zusammenspiel von getakteter Abrechnung und Preisgestaltung

Bei der Definition des Angebots und seiner Preismodelle ist es wichtig, die Angebotshierarchie zu verstehen.

* Jedes Azure-Anwendungsangebot kann über Lösungsvorlagenpläne oder verwaltete Anwendungspläne verfügen.
* Die getaktete Abrechnung wird nur bei verwalteten Anwendungsplänen implementiert.
* Jedem verwalteten Anwendungsplan ist ein Preismodell zugeordnet. 
* Das Preismodell weist eine laufende Monatsgebühr auf, die auf 0 (null) USD festgelegt sein kann.
* Zusätzlich zur laufenden Gebühr kann der Plan auch optionale Dimensionen enthalten, die dem Kunden für nicht in der Pauschale enthaltene Nutzung berechnet werden. Jede Dimension stellt eine abrechenbare Einheit dar, die Ihr Dienst über die [Marketplace-Messungsdienst-API](marketplace-metering-service-apis.md) an Microsoft übermittelt.

## <a name="sample-offer"></a>Beispielangebot

In diesem Beispiel ist Contoso ein Herausgeber mit einem verwalteten Anwendungsdienst namens Contoso Analytics (CoA). Mit CoA können Kunden große Datenmengen für Berichterstellung und Data Warehousing analysieren. Contoso ist in Partner Center als Herausgeber für das kommerzielle Marketplace-Programm zur Veröffentlichung von Angeboten für Azure-Kunden registriert. CoA sind zwei Pläne zugeordnet:

* Basisplan
    * Analysieren von 100 GB und Generieren von 100 Berichten für 0 USD/Monat
    * Über die 100 GB hinaus sind 10 USD für jeweils 1 GB zu zahlen
    * Über die 100 Berichte hinaus ist 1 USD für jeden Bericht zu zahlen
* Premium-Tarif
    * Analysieren von 1000 GB und Generieren von 1000 Berichten für 350 USD/Monat
    * Über die 1000 GB hinaus sind 100 USD für jeweils 1 TB zu zahlen
    * Über die 1000 Berichte hinaus sind 0,5 USD für jeden Bericht zu zahlen

Ein Azure-Kunde, der den CoA-Dienst abonniert, kann Berichte pro Monat basierend auf dem ausgewählten Plan analysieren und generieren. Die Nutzung bis zur enthaltenen Menge wird von Contoso gemessen, ohne dass Verwendungsereignisse an Microsoft gesendet werden. Wenn Kunden mehr als die enthaltene Menge beanspruchen, müssen sie nicht zu einem anderen Plan wechseln oder ihr Verhalten ändern. Contoso misst die Nutzung, die über die enthaltene Menge hinausgeht, und beginnt damit, über die [Marketplace-Messungsdienst-API](./marketplace-metering-service-apis.md) Nutzungsereignisse für die zusätzliche Nutzung an Microsoft zu senden. Microsoft berechnet dem Kunden wiederum die zusätzliche Nutzung gemäß Angabe des Herausgebers.

## <a name="billing-dimensions"></a>Abrechnungsdimensionen

Abrechnungsdimensionen werden verwendet, um Kunden über die Abrechnung der Softwarenutzung zu informieren.  Diese Dimensionen werden auch verwendet, um Nutzungsereignisse an Microsoft zu übermitteln. Sie sind wie folgt definiert:

* **Dimensionsbezeichner**: Die unveränderliche Kennung, auf die bei der Ausgabe von Nutzungsereignissen verwiesen wird.
* **Dimensionsname**: Der der Dimension zugeordnete Anzeigename (z. B. „Gesendete SMS“).
* **Maßeinheit**: Die Beschreibung der Abrechnungseinheit (z. B. „pro SMS“ oder „pro 100 E-Mails“).
* **Preis pro Einheit**: Der Preis für eine Einheit der Dimension.
* **Enthaltene Menge für monatliche Laufzeit**: Die Menge der pro Monat enthaltenen Dimension für Kunden, die die wiederkehrende monatliche Gebühr bezahlen. Muss eine ganze Zahl sein.

Abrechnungsdimensionen werden in allen Plänen für ein Angebot verwendet. Manche Attribute gelten für die Dimension über alle Pläne hinweg, andere Attribute sind dagegen planspezifisch.

Die Attribute, die die Dimension selbst definieren, werden übergreifend für alle Pläne zu einem Angebot genutzt. Vor der Angebotsveröffentlichung wirkt sich eine Änderung dieser Attribute im Kontext eines beliebigen Plans auf die Dimensionsdefinition für alle Pläne aus. Nachdem Sie das Angebot veröffentlicht haben, können diese Attribute nicht mehr bearbeitet werden. Die Attribute sind:

* Bezeichner
* Name
* Unit of measure

Die anderen Attribute einer Dimension sind planspezifisch und können von Plan zu Plan unterschiedliche Werte haben.  Vor dem Veröffentlichen des Plans können Sie diese Werte bearbeiten, und nur dieser Plan ist betroffen. Nachdem Sie den Plan veröffentlicht haben, können diese Attribute nicht mehr bearbeitet werden. Die Attribute sind:

* Preis pro Einheit
* Enthaltene Menge für monatliche Kunden 
* Enthaltene Menge für jährliche Kunden 

Dimensionen verfügen auch über zwei spezielle Konzepte: „aktiviert“ und „unbegrenzt“:

* **Aktiviert** gibt an, dass dieser Plan Teil dieser Dimension ist.  Lassen Sie diese Option ggf. deaktiviert, wenn Sie einen neuen Plan erstellen, der keine auf dieser Dimension basierenden Nutzungsereignisse sendet. Außerdem werden alle neuen Dimensionen, die nach der ersten Veröffentlichung eines Plans hinzugefügt wurden, für den bereits veröffentlichten Plan als „nicht aktiviert“ angezeigt.  Eine deaktivierte Dimension wird in keiner Dimensionenliste für einen Plan angezeigt, der für Kunden sichtbar ist.
* **Unbegrenzt**, dargestellt durch das Unendlichkeitssymbol „∞“, gibt an, dass dieser Plan Teil dieser Dimension ist, jedoch ohne gemessene Nutzung für diese Dimension. Dadurch können Sie Ihren Kunden vermitteln, dass die durch diese Dimension dargestellte Funktion im Plan enthalten und die Nutzung nicht begrenzt ist.  Eine Dimension mit unbegrenzter Nutzung wird in Dimensionenlisten für einen Plan angezeigt, der für Kunden sichtbar ist.  Für diesen Plan fällt niemals eine Gebühr an.

>[!Note] 
>Folgende Szenarien werden explizit unterstützt:  <br> - Sie können einem neuen Plan eine neue Dimension hinzufügen.  Die neue Dimension wird nicht für bereits veröffentlichte Pläne aktiviert. <br> - Sie können einen Plan mit einer festen Monatsgebühr und ohne Dimensionen veröffentlichen und anschließend einen neuen Plan hinzufügen und eine neue Dimension für diesen Plan konfigurieren. Die neue Dimension wird nicht für bereits veröffentlichte Pläne aktiviert.

## <a name="constraints"></a>Einschränkungen

### <a name="locking-behavior"></a>Sperrverhalten

Eine mit dem Marketplace-Messungsdienst verwendete Dimension bestimmt, wie ein Kunde für den Dienst bezahlt.  Alle Details einer Dimension können nach der Veröffentlichung eines Angebots nicht mehr bearbeitet werden.  Daher ist es wichtig, dass Ihre Dimensionen vor der Veröffentlichung des Angebots vollständig definiert wurden.

Sobald ein Angebot mit einer Dimension veröffentlicht wurde, können die Details der Angebotsebene für diese Dimension nicht mehr geändert werden:

* Bezeichner
* Name
* Unit of measure

Nachdem ein Plan veröffentlicht wurde, können die Details auf der Planebene nicht mehr geändert werden:

* Preis pro Einheit
* Enthaltene Menge für die monatliche Laufzeit
* Angabe, ob die Dimension für den Plan aktiviert ist

>[!Note]
>Die getaktete Abrechnung mithilfe des Marketplace-Messungsdiensts wird in der Azure Government-Cloud noch nicht unterstützt.

### <a name="upper-limits"></a>Obergrenzen

Für ein einzelnes Angebot können maximal 30 individuelle Dimensionen konfiguriert werden.

## <a name="get-support"></a>Support

Sollte eines der folgenden Probleme bei Ihnen auftreten, können Sie ein Supportticket erstellen:

* Technische Probleme mit der Marketplace-Messungsdienst-API
* Ein Problem, das aufgrund eines Fehlers auf Ihrer Seite eskaliert werden muss (beispielsweise ein falsches Nutzungsereignis)
* Ein anderes Problem im Zusammenhang mit der getakteten Abrechnung

Befolgen Sie die Anleitung unter [Support für das Programm „Kommerzieller Marketplace“ in Partner Center](../support.md), um sich mit den Supportoptionen für Herausgeber vertraut zu machen und ein Supportticket bei Microsoft zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Marketplace-Messungsdienst-APIs](./marketplace-metering-service-apis.md).
