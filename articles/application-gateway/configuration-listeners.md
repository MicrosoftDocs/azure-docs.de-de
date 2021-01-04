---
title: Azure Application Gateway-Listenerkonfiguration
description: In diesem Artikel wird das Konfigurieren von Azure Application Gateway-Listeners beschrieben.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 15f68e8cbca65e7b970944f7ca5ef1952140cc6b
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397638"
---
# <a name="application-gateway-listener-configuration"></a>Application Gateway-Listenerkonfiguration

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ein Listener ist eine logische Entität, die mithilfe von Port, Protokoll, Host und IP-Adresse auf eingehende Verbindungsanforderungen prüft. Wenn Sie den Listener konfigurieren, müssen Sie Werte für diese eingeben, die den entsprechenden Werten in der eingehenden Anforderung auf dem Gateway entsprechen.

Wenn Sie ein Application Gateway mithilfe des Azure-Portals erstellen, erstellen Sie außerdem einen Standardlistener durch Auswählen von Protokoll und Port für den Listener. Sie können wahlweise HTTP2-Unterstützung auf dem Listener aktivieren. Nach der Erstellung des Application Gateways können Sie die Einstellungen dieses Standardlisteners ( *appGatewayHttpListener* ) bearbeiten oder neue Listener erstellen.

## <a name="listener-type"></a>Listenertyp

Wenn Sie einen neuen Listener erstellen, wählen Sie zwischen [*grundlegend* und *mehreren Standorten*](./application-gateway-components.md#types-of-listeners) aus.

- Wenn alle Ihre Anforderungen (für eine beliebige Domäne) akzeptiert und an Back-End-Pools weitergeleitet werden sollen, wählen Sie „Basic“ aus. Erfahren Sie, [wie Sie ein Application Gateway mit einem grundlegenden Listener erstellen](./quick-create-portal.md).

- Wenn Sie Anforderungen an verschiedene Back-End-Pools weiterleiten möchten, die auf dem *Hostheader* oder den Hostnamen basieren, wählen Sie den Listener für mehrere Standorte aus, bei dem Sie auch einen Hostnamen angeben müssen, der mit der eingehenden Anforderung übereinstimmt. Das hat den Grund, dass Application Gateway HTTP 1.1-Hostheader verwendet, um mehrere Websites an der gleichen öffentlichen IP-Adresse und dem gleichen Port zu hosten. Weitere Informationen finden Sie unter [Anwendungsgateways – Hosten mehrerer Websites](multiple-site-overview.md).

### <a name="order-of-processing-listeners"></a>Verarbeitungsreihenfolge von Listenern

Bei der v1-SKU werden Anforderungen entsprechend der Reihenfolge der Regeln und des Listenertyps abgeglichen. Wenn eine Regel mit dem Basislistener in der Reihenfolge an erster Stelle steht, wird sie zuerst verarbeitet und akzeptiert alle Anforderungen für diese Port- und IP-Kombination. Um dies zu vermeiden, konfigurieren Sie die Regeln zuerst mit Listenern für mehrere Standorte, und verschieben Sie die Regel mit dem Basislistener an die letzte Stelle in der Liste.

Für v2 SKUs werden Listener für mehrere Standorte vor grundlegenden Listenern verarbeitet.

## <a name="front-end-ip-address"></a>Front-End-IP-Adresse

Wählen Sie die Front-End-IP-Adresse aus, die Sie diesem Listener zuordnen möchten. Der Listener lauscht auf eingehende Anforderungen für diese IP-Adresse.

## <a name="front-end-port"></a>Front-End-Port

Wählen Sie den Front-End-Port aus. Wählen Sie einen vorhandenen Port aus, oder erstellen Sie einen neuen. Wählen Sie einen beliebigen Wert aus dem [zulässigen Portbereich](./application-gateway-components.md#ports) aus. Sie können nicht nur die bekannten Ports wie 80 und 443 verwenden, sondern jeden geeigneten zulässigen benutzerdefinierten Port. Ein Port kann für öffentlich zugängliche Listener oder für nur privat zugängliche Listener verwendet werden.

## <a name="protocol"></a>Protocol

Wählen Sie HTTP oder HTTPS aus:

- Wenn Sie sich für HTTP entscheiden, ist der Datenverkehr zwischen dem Client und dem Application Gateway unverschlüsselt.

- Wählen Sie HTTPS aus, wenn Sie die [TLS-Beendigung](features.md#secure-sockets-layer-ssltls-termination) oder [End-to-End-TLS-Verschlüsselung](./ssl-overview.md) verwenden möchten. Der Datenverkehr zwischen dem Client und dem Application Gateway ist verschlüsselt. Außerdem wird die TLS-Verbindung am Application Gateway getrennt. Wenn Sie die End-to-End-TLS-Verschlüsselung wünschen, müssen Sie HTTPS auswählen und die Einstellung des **Back-End-HTTP** konfigurieren. Dadurch wird sichergestellt, dass der Datenverkehr auf dem Weg vom Application Gateway zum Back-End erneut verschlüsselt wird.


Zum Konfigurieren der TLS-Beendigung und End-to-End-TLS-Verschlüsselung müssen Sie ein Zertifikat zum Listener hinzufügen, um Application Gateway das Ableiten eines symmetrischen Schlüssels zu ermöglichen. Dies wird durch die TLS-Protokollspezifikation vorgegeben. Der symmetrische Schlüssel wird zum Verschlüsseln und Entschlüsseln des an das Gateway gesendeten Datenverkehrs verwendet. Das Gatewayzertifikat muss im PFX-Format (Personal Information Exchange, privater Informationsaustausch) vorliegen. Mit diesem Format können Sie den privaten Schlüssel exportieren, den das Gateway zum Verschlüsseln und Entschlüsseln von Datenverkehr verwendet.

## <a name="supported-certificates"></a>Unterstützte Zertifikate

Siehe [Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway](ssl-overview.md#certificates-supported-for-tls-termination).

## <a name="additional-protocol-support"></a>Unterstützung zusätzlicher Protokolle

### <a name="http2-support"></a>HTTP2-Unterstützung

Die Unterstützung des HTTP/2-Protokolls ist nur für Clients verfügbar, die mit Application Gateway-Listenern verbunden sind. Die Kommunikation mit Back-End-Serverpools erfolgt über HTTP/1.1. Die HTTP/2-Unterstützung ist standardmäßig deaktiviert. Der folgende Azure PowerShell-Codeausschnitt zeigt, wie Sie sie aktivieren:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>WebSocket-Unterstützung

Die WebSocket-Unterstützung ist standardmäßig aktiviert. Sie kann von Benutzern nicht aktiviert oder deaktiviert werden. Sie können das WebSocket-Protokoll sowohl mit HTTP- als auch mit HTTPS-Listenern verwenden.

## <a name="custom-error-pages"></a>Benutzerdefinierte Fehlerseiten

Sie können benutzerdefinierte Fehler auf globaler Ebene oder Listenerebene definieren. Doch das Erstellen von benutzerdefinierten Fehlerseiten auf globaler Ebene über das Azure-Portal wird derzeit nicht unterstützt. Sie können auf Listenerebene eine benutzerdefinierte Fehlerseite für einen 403-WAF-Fehler oder eine 502-Wartungsseite konfigurieren. Außerdem müssen Sie eine öffentlich zugängliche Blob-URL für den bestimmten Fehlerstatuscode angeben. Weitere Informationen finden Sie unter [Create Application Gateway custom error pages](./custom-error.md) (Erstellen von benutzerdefinierten Application Gateway-Fehlerseiten).

![Application Gateway-Fehlercodes](/azure/application-gateway/media/custom-error/ag-error-codes.png)

Wie Sie eine globale benutzerdefinierte Fehlerseite konfigurieren, erfahren Sie unter [Azure PowerShell-Konfiguration](./custom-error.md#azure-powershell-configuration).

## <a name="tls-policy"></a>TLS-Richtlinie

Sie können die TLS/SSL-Zertifikatverwaltung zentralisieren sowie den Ver- und Entschlüsselungsaufwand für eine Back-End-Serverfarm verringern. Diese zentralisierte TLS-Behandlung ermöglicht auch die Angabe einer zentralen TLS-Richtlinie, die auf Ihre Sicherheitsanforderungen abgestimmt ist. Sie können zwischen einer *standardmäßig vorhandenen* , *vordefinierten* oder *benutzerdefinierten* TLS-Richtlinie auswählen.

Sie konfigurieren die TLS-Richtlinie, um die TLS-Protokollversionen zu steuern. Sie können eine Application Gateway-Instanz so konfigurieren, dass es eine Mindestprotokollversion für TLS-Handshakes von TLS 1.0, TLS 1.1 und TLS 1.2 verwendet. Standardmäßig sind SSL 2.0 und 3.0 deaktiviert und nicht konfigurierbar. Weitere Informationen finden Sie unter [TLS-Richtlinienübersicht für Azure Application Gateway](./application-gateway-ssl-policy-overview.md).

Nachdem Sie einen Listener erstellt haben, ordnen Sie ihm eine Anforderungsroutingregel zu. Diese Regel bestimmt, wie vom Listener empfangene Anforderungen an das Back-End weitergeleitet werden.

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zu Anforderungsroutingregeln](configuration-request-routing-rules.md).