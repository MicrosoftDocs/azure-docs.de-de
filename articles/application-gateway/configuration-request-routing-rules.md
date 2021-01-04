---
title: Konfigurieren der Routingregeln für Azure Application Gateway-Anforderungen
description: In diesem Artikel wird das Konfigurieren der Routingregeln für Azure Application Gateway-Anforderungen beschrieben.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397534"
---
# <a name="application-gateway-request-routing-rules"></a>Routingregeln für Application Gateway-Anforderungen

Wenn Sie im Azure-Portal ein Anwendungsgateway erstellen, wird dabei eine Standardregel ( *rule1* ) erstellt. Mit dieser Regel wird der Standardlistener ( *appGatewayHttpListener* ) an den Standard-Back-End-Pool ( *appGatewayBackendPool* ) und die Back-End-HTTP-Standardeinstellungen ( *appGatewayBackendHttpSettings* ) gebunden. Nachdem Sie das Gateway erstellt haben, können Sie die Einstellungen der Standardregel bearbeiten oder neue Regeln erstellen.

## <a name="rule-type"></a>Regeltyp

Wenn Sie eine Regel erstellen, wählen Sie zwischen [*grundlegend* und *pfadbasiert*](./application-gateway-components.md#request-routing-rules) aus.

- Wählen Sie „grundlegend“ aus, wenn Sie alle an den zugeordneten Listener gerichteten Anforderungen (z.B. *blog <i></i>.contoso.com/\*)* an einen einzelnen Back-End-Pool weiterleiten möchten.
- Wählen Sie „pfadbasiert“ aus, wenn Sie Anforderungen von bestimmten URL-Pfaden an bestimmte Back-End-Pools weiterleiten möchten. Das Pfadmuster wird nur auf den Pfad der URL angewendet, nicht auf ihre Abfrageparameter.

### <a name="order-of-processing-rules"></a>Verarbeitungsreihenfolge von Regeln

Für die v1- und v2-SKU wird die Musterzuordnung eingehender Anforderungen in der Reihenfolge verarbeitet, in der die Pfade in der URL-Pfadzuordnung der pfadbasierten Regel aufgelistet sind. Wenn eine Anforderung mit dem Muster in mindestens zwei Pfaden in der Pfadzuordnung übereinstimmt, wird der zuerst aufgelistete Pfad abgeglichen. Die Anforderung wird an das Back-End weitergeleitet, das diesem Pfad zugeordnet ist.

## <a name="associated-listener"></a>Zugeordneter Listener

Ordnen Sie der Regel einen Listener zu, damit die *Anforderungsroutingregel* , die dem Listener zugeordnet ist, ausgewertet wird, um den Back-End-Pool zu bestimmen, an den die Anforderung weitergeleitet werden soll.

## <a name="associated-back-end-pool"></a>Zugeordneter Back-End-Pool

Ordnen Sie der Regel den Back-End-Pool zu, der die Back-End-Ziele enthält, die Anforderungen verarbeiten, die der Listener empfängt.

 - Für eine grundlegende Regel ist nur ein einziger Back-End-Pool zulässig. Alle Anforderungen auf dem zugeordneten Listener werden an diesen Back-End-Pool weitergeleitet.

 - Fügen Sie für eine pfadbasierte Regel mehrere Back-End-Pools hinzu, die den einzelnen URL-Pfaden entsprechen. Mit dem eingegebenen URL-Pfad übereinstimmende Anforderungen werden an den entsprechenden Back-End-Pool weitergeleitet. Fügen Sie darüber hinaus einen standardmäßigen Back-End-Pool hinzu. Anforderungen, die keinem URL-Pfad in der Regel entsprechen, werden an diesen Pool weitergeleitet.

## <a name="associated-back-end-http-setting"></a>Zugeordnete Back-End-HTTP-Einstellung

Fügen Sie für jede Regel eine Back-End-HTTP-Einstellung hinzu. Anforderungen werden vom Application Gateway mithilfe der Portnummer, des Protokolls und anderer in dieser Einstellung festgelegten Informationen an die Back-End-Ziele weitergeleitet.

Für eine grundlegende Regel ist nur eine einzige Back-End-HTTP-Einstellung zulässig. Alle Anforderungen auf dem zugeordneten Listener werden mit dieser HTTP-Einstellung an die entsprechenden Back-End-Ziele weitergeleitet.

Fügen Sie für eine pfadbasierte Regel mehrere Back-End-HTTP-Einstellungen hinzu, die den einzelnen URL-Pfaden entsprechen. Anforderungen, die mit dem URL-Pfad in dieser Einstellung übereinstimmen, werden mithilfe der den einzelnen URL-Pfaden entsprechenden HTTP-Einstellungen an die entsprechenden Back-End-Ziele weitergeleitet. Fügen Sie auch eine Standard-HTTP-Einstellung hinzu. Anforderungen, die keinem URL-Pfad in dieser Regel entsprechen, werden mit der Standard-HTTP-Einstellung an den Standard-Back-End-Pool weitergeleitet.

## <a name="redirection-setting"></a>Umleitungseinstellung

Wenn die Umleitung für eine grundlegende Regel konfiguriert ist, werden alle Anforderungen auf dem zugeordneten Listener an das Ziel umgeleitet. Dies ist eine *globale Umleitung*. Wenn die Umleitung für eine pfadbasierte Regel konfiguriert ist, werden nur Anforderungen in einem bestimmten Websitebereich umgeleitet. Ein Beispiel ist ein Warenkorbbereich, der durch */cart/\** gekennzeichnet ist. Dies ist eine *pfadbasierte Umleitung*.

Weitere Informationen zu Umleitungen finden Sie unter [Übersicht über die Umleitung in Application Gateway](redirect-overview.md).

### <a name="redirection-type"></a>Umleitungstyp

Wählen Sie den erforderlichen Umleitungstyp aus: *Dauerhaft(301)* , *Temporär(307)* , *Gefunden(302)* oder *Verweis(303)* .

### <a name="redirection-target"></a>Umleitungsziel

Wählen Sie einen anderen Listener oder eine externe Website als Umleitungsziel aus.

#### <a name="listener"></a>Listener

Wählen Sie einen Listener als Umleitungsziel aus, um Datenverkehr von einem Listener zu einem anderen auf dem Gateway umzuleiten. Diese Einstellung ist erforderlich, wenn Sie HTTP-zu-HTTPS-Umleitung aktivieren möchten. Sie leitet Datenverkehr vom Quelllistener, der prüft, ob HTTP-Anforderungen eingehen, an den Ziellistener um, der prüft, ob HTTPS-Anforderungen eingehen. Sie können auch wählen, die Abfragezeichenfolge und den Pfad der ursprünglichen Anforderung in die Anforderung einzubeziehen, die an das Umleitungsziel weitergeleitet wird.

![Dialogfeld mit Application Gateway-Komponenten](./media/configuration-overview/configure-redirection.png)

Weitere Informationen zur HTTP-zu-HTTPS-Umleitung finden Sie unter:
- [Erstellen eines Anwendungsgateways mit Umleitung von HTTP zu HTTPS über das Azure-Portal](redirect-http-to-https-portal.md)
- [Erstellen eines Anwendungsgateways mit Umleitung von HTTP zu HTTPS mithilfe von Azure PowerShell](redirect-http-to-https-powershell.md)
- [Erstellen eines Anwendungsgateways mit Umleitung von HTTP zu HTTPS über die Azure-Befehlszeilenschnittstelle](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>Externe Website

Wählen Sie „Externe Website“ aus, wenn Sie den Datenverkehr am Listener, der dieser Regel zugeordnet ist, auf eine externe Website umleiten möchten. Sie können wählen, die Abfragezeichenfolge der ursprünglichen Anforderung in die Anforderung einzubeziehen, die an das Umleitungsziel weitergeleitet wird. Sie können den Pfad zu der externen Website in der ursprünglichen Anforderung nicht weiterleiten.

Weitere Informationen zur Umleitung finden Sie unter:
- [Erstellen eines Anwendungsgateways mit externer Umleitung durch Azure PowerShell](redirect-external-site-powershell.md)
- [Erstellen eines Anwendungsgateways mit externer Umleitung durch die Azure CLI](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>Neuschreibung von HTTP-Headern und der URL

Mithilfe von Neuschreibungsregeln können Sie HTTP(S)-Anforderungs- und -Antwortheader sowie den URL-Pfad und Abfragezeichenfolgenparameter hinzufügen, entfernen oder aktualisieren, während die Anforderungs- und Antwortpakete über das Anwendungsgateway zwischen dem Client und den Back-End-Pools weitergeleitet werden.

Die Header und URL-Parameter können auf statische Werte oder auf andere Header und Servervariablen festgelegt werden. Dies ist hilfreich bei wichtigen Anwendungsfällen, z.B. beim Extrahieren von Client-IP-Adressen, beim Entfernen vertraulicher Informationen zum Back-End, beim Verbessern der Sicherheit usw.
Weitere Informationen finden Sie unter

 - [Übersicht: Neuschreibung von HTTP-Headern und der URL](rewrite-http-headers-url.md)
 - [Angeben Ihrer Regelkonfiguration für das erneute Generieren eines HTTP-Headers](rewrite-http-headers-portal.md)
 - [Konfigurieren von URL-Rewrite](rewrite-url-portal.md)

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über HTTP-Einstellungen.](configuration-http-settings.md)