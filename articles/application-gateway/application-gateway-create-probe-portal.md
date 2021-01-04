---
title: Erstellen eines benutzerdefinierten Tests mithilfe des Portals
titleSuffix: Azure Application Gateway
description: Erfahren Sie, wie Sie einen benutzerdefinierten Test für ein Anwendungsgateway über das Portal erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95975954"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Erstellen eines benutzerdefinierten Tests für ein Anwendungsgateway über das Portal

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klassische Azure PowerShell](application-gateway-create-probe-classic-ps.md)

In diesem Artikel fügen Sie einen benutzerdefinierten Integritätstest zu einem vorhandenen Anwendungsgateway über das Azure-Portal hinzu. Azure Application Gateway überwacht mithilfe der Integritätstests die Integrität der Ressourcen im Back-End-Pool.

## <a name="before-you-begin"></a>Voraussetzungen

Wenn Sie nicht bereits über ein Anwendungsgateway verfügen, besuchen Sie [Erstellen eines Anwendungsgateways](./quick-create-portal.md), um ein Anwendungsgateway zu erstellen, mit dem Sie arbeiten können.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Erstellen eines Tests für eine Application Gateway v2-SKU

Tests werden in einem aus zwei Schritten bestehenden Prozess im Portal konfiguriert. Der erste Schritt besteht in der Eingabe der für die Testkonfiguration erforderlichen Werte. Im zweiten Schritt testen Sie die Back-End-Integrität mithilfe dieser Testkonfiguration und speichern den Test. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Eingeben der Testeigenschaften

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose einmonatige Testversion](https://azure.microsoft.com/free) registrieren.

2. Klicken Sie im Azure-Portal im Bereich „Favoriten“ auf „Alle Ressourcen“. Klicken Sie im Blatt „Alle Ressourcen“ auf das Anwendungsgateway. Falls das ausgewählte Abonnement bereits mehrere Ressourcen enthält, können Sie „partners.contoso.net“ in das Feld „Nach Name filtern...“ eingeben und komfortabel auf das Anwendungsgateway zugreifen.

3. Wählen Sie **Integritätstests** und dann **Hinzufügen** aus, um einen neuen Integritätstest hinzuzufügen.

   ![Hinzufügen eines neues Tests][4]

4. Stellen Sie auf der Seite **Integritätstest hinzufügen** die erforderlichen Informationen für den Test bereit, und klicken Sie anschließend auf **OK**.

   |**Einstellung** | **Wert** | **Details**|
   |---|---|---|
   |**Name**|customProbe|Dieser Wert ist der Anzeigename für den Test, auf den Sie über das Portal zugreifen können.|
   |**Protokoll**|HTTP oder HTTPS | Das Protokoll, das vom Integritätstest verwendet wird. |
   |**Host**|z.B.: contoso.com|Dieser Wert ist der Name des virtuellen Hosts (nicht der VM-Hostname), der auf dem Anwendungsserver ausgeführt wird. Der Test wird an \<protocol\>://\<host name\>:\<port\>/\<urlPath\> gesendet.|
   |**Hostnamen aus Back-End-Adresse auswählen**|Ja oder Nein|Legt den Header *host* im Test auf den Hostnamen aus den HTTP-Einstellungen fest, denen dieser Test zugeordnet ist. Dies ist insbesondere im Falle mehrinstanzenfähiger Back-Ends, z. B. Azure App Service, erforderlich. [Weitere Informationen](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Port aus Back-End-HTTP-Einstellungen auswählen**| Ja oder Nein|Legt den *Port* des Tests auf den Port aus den HTTP-Einstellungen fest, denen dieser Test zugeordnet ist. Wenn Sie „Nein“ auswählen, können Sie einen benutzerdefinierten Zielport eingeben. |
   |**Port**| 1-65535 | Benutzerdefinierter Port für die Integritätstests | 
   |**Pfad**|„/“ oder beliebiger gültiger Pfad|Dies ist der Rest der vollständigen URL für den benutzerdefinierten Test. Ein gültiger Pfad beginnt mit „/“. Verwenden Sie für den Standardpfad von „http:\//contoso.com“ nur „/“. |
   |**Interval (Sek.)**|30|Legen Sie fest, wie oft der Test ausgeführt werden soll, um die Integrität zu prüfen. Es wird nicht empfohlen, einen Wert unter 30 Sekunden einzustellen.|
   |**Timeout (Sek.)**|30|Legen Sie fest, wie lange der Test warten soll, bis ein Timeout auftritt. Die Überprüfung wird als fehlerhaft markiert, wenn innerhalb des Zeitraums für die Zeitüberschreitung keine gültige Antwort empfangen wird. Das Timeoutintervall muss lang genug sein, damit ein HTTP-Aufruf erfolgen und sichergestellt werden kann, dass die Integritätsseite für das Back-End verfügbar ist. Beachten Sie, dass der Timeoutwert nicht größer als der in dieser Testeinstellung verwendete Wert für „Intervall“ oder der Wert für „Anforderungstimeout“ in der HTTP-Einstellung für diesen Test sein sollte.|
   |**Fehlerhafter Schwellenwert**|3|Dies ist die Anzahl aufeinanderfolgender erfolgloser Versuche, nach denen der Test als „fehlerhaft“ eingestuft wird. Der Schwellenwert kann auf 1 oder mehr festgelegt werden.|
   |**Übereinstimmungsbedingungen für Test verwenden**|Ja oder Nein|Standardmäßig gilt eine HTTP(S)-Antwort mit einem Statuscode zwischen 200 und 399 als fehlerfrei. Sie können den zulässigen Bereich von Back-End-Antwortcode oder Back-End-Antworttext ändern. [Weitere Informationen](./application-gateway-probe-overview.md#probe-matching)|
   |**HTTP-Einstellungen**|Auswahl aus Dropdownmenü|Der Test wird den hier ausgewählten HTTP-Einstellungen zugeordnet und überwacht daher die Integrität dieses Back-End-Pools, der der ausgewählten HTTP-Einstellung zugeordnet ist. Er verwendet den gleichen Port für die Testanforderung wie den Port, der in der ausgewählten HTTP-Einstellung verwendet wird. Sie können nur HTTP-Einstellungen auswählen, die keinem anderen benutzerdefinierten Test zugeordnet sind. <br>Beachten Sie, dass nur HTTP-Einstellungen zugeordnet werden können, die über dasselbe Protokoll wie das in dieser Testkonfiguration gewählte Protokoll verfügen und für den Schalter *Hostnamen aus Back-End-Adresse auswählen* denselben Status aufweisen.|
   
   > [!IMPORTANT]
   > Der Test überwacht die Integrität des Back-Ends nur dann, wenn er mindestens einer HTTP-Einstellung zugeordnet ist. Dabei werden Back-End-Ressourcen dieser Back-End-Pools überwacht, die den HTTP-Einstellungen für diesen Test zugeordnet sind. Die Testanforderung wird als \<protocol\>://\<hostName\>:\<port\>/\<urlPath\> gesendet.

### <a name="test-backend-health-with-the-probe"></a>Testen der Back-End-Integrität mit dem Test

Nachdem Sie die Testeigenschaften eingegeben haben, können Sie die Integrität der Back-End-Ressourcen testen, um zu überprüfen, ob die Testkonfiguration korrekt ist und die Back-End-Ressourcen erwartungsgemäß ausgeführt werden.

1. Wählen Sie **Test** aus, und notieren Sie sich das Ergebnis des Tests. Das Application Gateway testet die Integrität aller Back-End-Ressourcen in den Back-End-Pools, die den für diesen Test verwendeten HTTP-Einstellungen zugeordnet sind. 

   ![Testen der Back-End-Integrität][5]

2. Wenn fehlerhafte Back-End-Ressourcen vorhanden sind, überprüfen Sie die Spalte **Details**, um die Ursache für den fehlerhaften Zustand der Ressource zu ermitteln. Wenn die Ressource aufgrund einer falschen Testkonfiguration als fehlerhaft gekennzeichnet wurde, klicken Sie auf den Link **Zurück zum Test**, und bearbeiten Sie die Testkonfiguration. Wenn die Ressource aufgrund eines Problems beim Back-End als fehlerhaft gekennzeichnet wurde, beheben Sie die Probleme bei der Back-End-Ressource, und testen Sie dann das Back-End erneut, indem Sie auf den Link **Zurück zum Test** klicken und **Test** auswählen.

   > [!NOTE]
   > Sie können den Test auch mit fehlerhaften Back-End-Ressourcen speichern, dies wird jedoch nicht empfohlen. Von Application Gateway werden keine als fehlerhaft identifizierten Anforderungen aus dem Back-End-Pool an die Back-End-Server weitergeleitet. Sollten in einem Back-End-Pool keine fehlerfreien Ressourcen vorhanden sein, können Sie nicht auf die Anwendung zugreifen, und es wird der HTTP-Fehler 502 ausgegeben.

   ![Anzeigen des Testergebnisses][6]

3. Wählen Sie **Hinzufügen** aus, um den Test zu speichern. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Erstellen eines Tests für eine Application Gateway v1-SKU

Tests werden in einem aus zwei Schritten bestehenden Prozess im Portal konfiguriert. Im ersten Schritt wird der Test erstellt. Im zweiten Schritt fügen Sie den Test den Back-End-HTTP-Einstellungen des Anwendungsgateways hinzu.

### <a name="create-the-probe"></a><a name="createprobe"></a>Erstellen des Tests

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose einmonatige Testversion](https://azure.microsoft.com/free) registrieren.

2. Wählen Sie im Azure-Portal im Bereich „Favoriten“ die Option **Alle Ressourcen** aus. Wählen Sie auf der Seite **Alle Ressourcen** das Anwendungsgateway aus. Falls das ausgewählte Abonnement bereits mehrere Ressourcen enthält, können Sie „partners.contoso.net“ in das Feld „Nach Name filtern...“ eingeben und komfortabel auf das Anwendungsgateway zugreifen.

3. Wählen Sie **Tests** und dann **Hinzufügen** aus, um einen Test hinzuzufügen.

   ![Hinzufügen eines Blatts für einen Test mit bereitgestellten Informationen][1]

4. Stellen Sie auf dem Blatt **Integritätstest hinzufügen** die erforderlichen Informationen für den Test bereit, und klicken Sie anschließend auf **OK**.

   |**Einstellung** | **Wert** | **Details**|
   |---|---|---|
   |**Name**|customProbe|Dieser Wert ist der Anzeigename für den Test, auf den Sie über das Portal zugreifen können.|
   |**Protokoll**|HTTP oder HTTPS | Das Protokoll, das vom Integritätstest verwendet wird. |
   |**Host**|z.B.: contoso.com|Dieser Wert ist der Name des virtuellen Hosts (nicht der VM-Hostname), der auf dem Anwendungsserver ausgeführt wird. Der Test wird an diese Adresse gesendet: (Protokoll)://(Hostname):(Port aus HTTP-Einstellungen)/urlPath.  Dies ist relevant, wenn in Application Gateway mehrere Standorte konfiguriert sind. Wenn Application Gateway für einen einzelnen Standort konfiguriert ist, geben Sie „127.0.0.1“ ein.|
   |**Hostnamen aus Back-End-Adresse auswählen**|Ja oder Nein|Legt den *host*-Header im Test auf den Hostnamen der Back-End-Ressource im Back-End-Pool fest, der der HTTP-Einstellung für den Test zugeordnet ist. Dies ist insbesondere im Falle mehrinstanzenfähiger Back-Ends, z. B. Azure App Service, erforderlich. [Weitere Informationen](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Pfad**|„/“ oder beliebiger gültiger Pfad|Dies ist der Rest der vollständigen URL für den benutzerdefinierten Test. Ein gültiger Pfad beginnt mit „/“. Verwenden Sie für den Standardpfad von „http:\//contoso.com“ nur „/“. |
   |**Interval (Sek.)**|30|Legen Sie fest, wie oft der Test ausgeführt werden soll, um die Integrität zu prüfen. Es wird nicht empfohlen, einen Wert unter 30 Sekunden einzustellen.|
   |**Timeout (Sek.)**|30|Legen Sie fest, wie lange der Test warten soll, bis ein Timeout auftritt. Die Überprüfung wird als fehlerhaft markiert, wenn innerhalb des Zeitraums für die Zeitüberschreitung keine gültige Antwort empfangen wird. Das Timeoutintervall muss lang genug sein, damit ein HTTP-Aufruf erfolgen und sichergestellt werden kann, dass die Integritätsseite für das Back-End verfügbar ist. Beachten Sie, dass der Timeoutwert nicht größer als der in dieser Testeinstellung verwendete Wert für „Intervall“ oder der Wert für „Anforderungstimeout“ in der HTTP-Einstellung für diesen Test sein sollte.|
   |**Fehlerhafter Schwellenwert**|3|Dies ist die Anzahl aufeinanderfolgender erfolgloser Versuche, nach denen der Test als „fehlerhaft“ eingestuft wird. Der Schwellenwert kann auf 1 oder mehr festgelegt werden.|
   |**Übereinstimmungsbedingungen für Test verwenden**|Ja oder Nein|Standardmäßig gilt eine HTTP(S)-Antwort mit einem Statuscode zwischen 200 und 399 als fehlerfrei. Sie können den zulässigen Bereich von Back-End-Antwortcode oder Back-End-Antworttext ändern. [Weitere Informationen](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > Der Hostname ist nicht identisch mit dem Servernamen. Dieser Wert ist der Name des virtuellen Hosts, der auf dem Anwendungsserver ausgeführt wird. Der Test wird an \<protocol\>://\<hostName\>:\<port from http settings\>/\<urlPath\> gesendet.

### <a name="add-probe-to-the-gateway"></a>Hinzufügen des Tests zum Gateway

Nachdem der Test erstellt wurde, können Sie ihn jetzt zum Gateway hinzufügen. Testeinstellungen werden in den Back-End-HTTP-Einstellungen des Anwendungsgateways festgelegt.

1. Klicken Sie auf **HTTP-Einstellungen** für das Anwendungsgateway, und klicken Sie auf die aufgeführten aktuellen Back-End-HTTP-Einstellungen im Fenster, um das Blatt „Konfiguration“ zu öffnen.

   ![Fenster mit HTTPS-Einstellungen][2]

2. Aktivieren Sie auf der Seite mit den Einstellungen für **appGatewayBackEndHttpSettings** das Kontrollkästchen **Benutzerdefinierten Test verwenden**, und wählen Sie den Test aus, den Sie im Abschnitt [Erstellen des Tests](#createprobe) in der Dropdownliste **Benutzerdefinierter Test** erstellt haben.
   Wenn Sie fertig sind, klicken Sie auf **Speichern**, um die Einstellungen anzuwenden.

## <a name="next-steps"></a>Nächste Schritte

Anzeigen der durch den Test bestimmten Integrität der Back-End-Ressourcen durch [Anzeigen der Back-End-Integrität](./application-gateway-diagnostics.md#back-end-health)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
