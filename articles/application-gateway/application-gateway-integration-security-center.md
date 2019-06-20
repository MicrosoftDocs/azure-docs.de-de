---
title: Application Gateway-Integration in Azure Security Center | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen darüber, wie Application Gateway in Azure Security Center integriert werden kann.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: victorh
ms.openlocfilehash: 10f115b64f0bd3f7e557da2bedbf3327d0ef483d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122288"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Übersicht über die Integration von Application Gateway in Azure Security Center

In diesem Artikel wird erläutert, wie Sie von Application Gateway und Security Center beim Schutz Ihrer Webanwendungsressourcen unterstützt werden. Die Web Application Firewall (WAF) für Anwendungsgateways lässt sich in [Security Center](../security-center/security-center-intro.md) integrieren und bietet einen nahtlosen Überblick – so können Sie Bedrohungen für ungeschützte Webanwendungen in Ihrer Umgebung erkennen, verhindern und darauf reagieren.

## <a name="overview"></a>Übersicht

Application Gateway-WAF wird von Security Center zum Schutz von Webanwendungen vor Sicherheitslücken und -risiken empfohlen. Webfähige Ressourcen, die nicht mit WAF geschützt sind, werden in Security Center als Empfehlungen mit hohem Schweregrad angezeigt. Die Firewall-Empfehlungen für Webanwendungen werden auf der **Übersichtsseite** unter **Anwendungen** angezeigt.

![Integration in Security Center][1]

Wenn Sie auf eine der Empfehlungen für Web Application Firewall klicken, öffnet sich eine neue Seite mit genaueren Informationen zu dieser Empfehlung.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Hinzufügen einer Firewall für eine Webanwendung zu einer vorhandenen Ressource

Navigieren Sie zu **Alle Dienste** > **Sicherheit + Identität** > **Security Center**, und klicken Sie unter **Security Center – Übersicht** auf **Anwendungen**. Die Tabelle in **Security Center – Anwendungen** enthält eine Liste der Anwendungen, die Security Center in Ihrem Abonnement erkannt hat.

![Webanwendungen][3]

Wenn Sie auf eine Webanwendung mit einem kritische Problem klicken, wird die Seite **Sicherheitsintegrität der Anwendung** geöffnet. In der folgenden Abbildung wird eine Webanwendung angezeigt, die nicht mit einer Firewall für Webanwendungen geschützt wird. 

![Ungeschützte Webressourcen][2]

Klicken Sie unter **Empfehlungen** auf **Web Application Firewall hinzufügen**, um die Seite **Web Application Firewall hinzufügen** zu öffnen.

Wenn Sie nicht über ein vorhandenes Anwendungsgateway verfügen oder ein neues erstellen möchten, klicken Sie auf **Neu erstellen**, und klicken Sie dann unter **Web Application Firewall erstellen** auf **Microsoft – Application Gateway**. Sie werden dann durch die Schritte für das Erstellen eines Anwendungsgateways geführt. An dieser Stelle wird Ihre Webanwendung als geschützte Ressource hinzugefügt. Security Center verfolgt nun, dass diese Ressource durch eine WAF geschützt ist. Die Webanwendung wird dadurch nicht als Back-End-Poolmitglied hinzugefügt.

Wenn Sie über ein vorhandenes Gateway verfügen, können Sie dieses unter **Vorhandene Lösung verwenden** auswählen.

![Seite zum Hinzufügen einer Web Application Firewall][4]

Beim Hinzufügen einer Webanwendung zu einem Anwendungsgateway über Security Center wird diese Ressource nicht als Mitglied des Back-End-Pools hinzugefügt. Dies muss direkt in der Anwendungsgatewayressource erfolgen.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Hinzufügen einer Ressource zu einer vorhandenen Firewall für Webanwendungen

Navigieren Sie zu **Alle Dienste** > **Sicherheit + Identität** > **Security Center**, und klicken Sie unter **Security Center – Übersicht** auf **Partnerlösungen**. Vorhandene Anwendungsgateways, die Security Center unterstützen, werden auf der Seite **Partnerlösungen** angezeigt.

![Partnerlösungen][7]

Klicken Sie auf **App verknüpfen**, um die Seite **Anwendungen verknüpfen** zu öffnen. Hier erhalten Sie die Optionen für die Auswahl vorhandener Anwendungen. Wählen Sie die zu schützenden Anwendungen, und klicken Sie auf **OK**. Dadurch wird die Webanwendung nicht zum Back-End-Pool des Anwendungsgateways hinzugefügt. Dies legt die Ressourcen als geschützte Ressourcen fest, die von Security Center verfolgt werden können. Um die Ressource als Back-End-Poolmitglied hinzuzufügen, muss das Anwendungsgateway verwendet werden. Sie können hierfür auf der aktuellen Seite auf **Lösungskonsole** klicken, um zu der Anwendungsgateway-Ressource zu wechseln, in der Sie die Webanwendung zum Back-End-Pool hinzufügen können.

![Anwendungen für Partnerlösungen][6]

## <a name="finalize-configuration"></a>Abschließen der Konfiguration

Sicherheitscenter verfolgt die Anwendungen, die zu einem Anwendungsgateway hinzugefügt werden, als geschützte Ressourcen.  Es überwacht die Integrität dieser Ressource und stellt sicher, dass sie durch ein Anwendungsgateway geschützt ist. Der nächste Schritt besteht darin, die private IP-Adresse, öffentliche IP-Adresse oder NIC Ihres virtuellen Computers zum Back-End-Pool des Anwendungsgateways hinzuzufügen. Solange dies nicht erledigt ist, wird die zusätzliche Empfehlung **Anwendungsschutz abschließen** angezeigt.

![Seite zum Hinzufügen einer Web Application Firewall][5]

## <a name="security-alerts"></a>Sicherheitswarnungen

Navigieren Sie in Security Center zu **ERKENNUNG** > **Sicherheitswarnungen**.  Hier finden Sie die WAF-Warnungen für Ihre Anwendungsgateways. Die Warnungen sind nach der WAF-Regel aufgeschlüsselt.

![Sicherheitswarnungen][8]

Wenn Sie auf eine Regel klicken, wird eine Warnungsliste für diese spezifische WAF-Regel angezeigt. Jede Warnung enthält zusätzliche Details zu dem erkannten Problem. Die Details enthalten einen Link zum Anwendungsgateway.
 
![Warnungsdetails][9]

## <a name="next-steps"></a>Nächste Schritte

Um zu erfahren, wie Sie eine Web Application Firewall für ein vorhandenes Anwendungsgateway aktivieren, lesen Sie den Artikel [Erstellen eines Anwendungsgateways mit der Web Application Firewall über das Portal](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png