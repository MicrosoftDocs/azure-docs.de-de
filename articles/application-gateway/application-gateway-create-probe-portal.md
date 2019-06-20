---
title: Erstellen eines benutzerdefinierten Tests – Azure Application Gateway (Azure-Portal) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen benutzerdefinierten Test für ein Anwendungsgateway über das Portal erstellen.
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 90d576fd00a39f7e871cbe0922ce131dfbe38ff0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122386"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Erstellen eines benutzerdefinierten Tests für ein Anwendungsgateway über das Portal

> [!div class="op_single_selector"]
> * [Azure-Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klassische Azure PowerShell](application-gateway-create-probe-classic-ps.md)

In diesem Artikel fügen Sie einen benutzerdefinierten Test zu einem vorhandenen Anwendungsgateway über das Azure-Portal hinzu. Benutzerdefinierte Tests sind für Anwendungen, die über eine bestimmte Seite für die Integritätsprüfung verfügen, oder für Anwendungen hilfreich, die keine erfolgreiche Antwort für die Standardwebanwendung bereitstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Wenn Sie nicht bereits über ein Anwendungsgateway verfügen, besuchen Sie [Erstellen eines Anwendungsgateways](application-gateway-create-gateway-portal.md), um ein Anwendungsgateway zu erstellen, mit dem Sie arbeiten können.

## <a name="createprobe"></a>Erstellen des Tests

Tests werden in einem aus zwei Schritten bestehenden Prozess im Portal konfiguriert. Im ersten Schritt wird der Test erstellt. Im zweiten Schritt fügen Sie den Test den Back-End-HTTP-Einstellungen des Anwendungsgateways hinzu.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an. Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose einmonatige Testversion](https://azure.microsoft.com/free) registrieren.

1. Klicken Sie im Azure-Portal im Bereich „Favoriten“ auf „Alle Ressourcen“. Klicken Sie im Blatt „Alle Ressourcen“ auf das Anwendungsgateway. Falls das ausgewählte Abonnement bereits mehrere Ressourcen enthält, können Sie „partners.contoso.net“ in das Feld „Nach Name filtern...“ eingeben und komfortabel auf das Anwendungsgateway zugreifen.

1. Klicken Sie auf **Tests** und dann auf die Schaltfläche **Hinzufügen**, um einen Test hinzuzufügen.

   ![Hinzufügen eines Blatts für einen Test mit bereitgestellten Informationen][1]

1. Stellen Sie auf dem Blatt **Integritätstest hinzufügen** die erforderlichen Informationen für den Test bereit, und klicken Sie anschließend auf **OK**.

   |**Einstellung** | **Wert** | **Details**|
   |---|---|---|
   |**Name**|customProbe|Dieser Wert ist der Anzeigename für den Test, auf den Sie über das Portal zugreifen können.|
   |**Protokoll**|HTTP oder HTTPS | Das Protokoll, das vom Integritätstest verwendet wird.|
   |**Host**|z.B.: contoso.com|Dieser Wert ist der Hostname, der für den Test verwendet wird. Nur relevant, wenn in Application Gateway mehrere Standorte konfiguriert sind. Andernfalls verwenden Sie 127.0.0.1. Dieser Wert entspricht nicht dem Hostnamen des virtuellen Computers.|
   |**Path**|/ oder ein anderer Pfad|Dies ist der Rest der vollständigen URL für den benutzerdefinierten Test. Ein gültiger Pfad beginnt mit „/“. Verwenden Sie für den Standardpfad von „http:\//contoso.com“ nur „/“. |
   |**Interval (Sek.)**|30|Legen Sie fest, wie oft der Test ausgeführt werden soll, um die Integrität zu prüfen. Es wird nicht empfohlen, einen Wert unter 30 Sekunden einzustellen.|
   |**Timeout (Sek.)**|30|Legen Sie fest, wie lange der Test warten soll, bis ein Timeout auftritt. Das Timeoutintervall muss lang genug sein, damit ein HTTP-Aufruf erfolgen und sichergestellt werden kann, dass die Integritätsseite für das Back-End verfügbar ist.|
   |**Fehlerhafter Schwellenwert**|3|Dies ist die Anzahl erfolgloser Versuche, nach denen der Test als „fehlerhaft“ eingestuft wird. Ein Schwellenwert von 0 bedeutet, dass das Back-End bei einer fehlerhaften Integritätsprüfung sofort als fehlerhaft eingestuft wird.|

   > [!IMPORTANT]
   > Der Hostname ist nicht identisch mit dem Servernamen. Dieser Wert ist der Name des virtuellen Hosts, der auf dem Anwendungsserver ausgeführt wird. Der Test wird an diese Adresse gesendet: http://(Hostname):(Port aus HTTP-Einstellungen)/urlPath.

## <a name="add-probe-to-the-gateway"></a>Hinzufügen des Tests zum Gateway

Nachdem der Test erstellt wurde, können Sie ihn jetzt zum Gateway hinzufügen. Testeinstellungen werden in den Back-End-HTTP-Einstellungen des Anwendungsgateways festgelegt.

1. Klicken Sie auf **HTTP-Einstellungen** für das Anwendungsgateway, und klicken Sie auf die aufgeführten aktuellen Back-End-HTTP-Einstellungen im Fenster, um das Blatt „Konfiguration“ zu öffnen.

   ![Fenster mit HTTPS-Einstellungen][2]

1. Aktivieren Sie auf dem Blatt mit den Einstellungen für **appGatewayBackEndHttpSettings** das Kontrollkästchen **Benutzerdefinierten Test verwenden**, und wählen Sie den Test aus, den Sie im Abschnitt [Erstellen des Tests](#createprobe) in der Dropdownliste **Benutzerdefinierter Test** erstellt haben.
   Wenn Sie fertig sind, klicken Sie auf **Speichern**, um die Einstellungen anzuwenden.

Der Standardtest prüft den Standardzugriff auf die Webanwendung. Nachdem nun ein benutzerdefinierter Test erstellt wurde, verwendet das Anwendungsgateway den benutzerdefinierten Pfad, um die Integrität der Back-End-Server zu überwachen. Das Anwendungsgateway überprüft den im Test angegebenen Pfad basierend auf den definierten Kriterien. Wenn der Aufruf an „host:Port/path“ keine HTTP-Statusantwort 200-399 zurückgibt, wird der Server aus der Rotation herausgenommen, nachdem der Fehlerschwellenwert erreicht ist. Die fehlerhafte Instanz wird weiterhin überprüft, um festzustellen, wann die Integrität wiederhergestellt ist. Sobald die Instanz dem fehlerfreien Serverpool wieder hinzugefügt wurde, wird wieder Datenverkehr an die Instanz übertragen, und die Instanz wird wie üblich in den vom Benutzer angegebenen Intervallen getestet.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren der SSL-Auslagerung mit Azure Application Gateway finden Sie unter [Konfigurieren der SSL-Auslagerung](application-gateway-ssl-portal.md).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

