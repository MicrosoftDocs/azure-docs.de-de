---
title: Behandeln von Problemen mit ausgehenden SMTP-Verbindungen in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme mit ausgehenden SMTP-Verbindungen in Azure beheben können.
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 385163d791bff0c02a05ee1b27afd82c3afd0ac3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61401918"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Behandeln von Problemen mit ausgehenden SMTP-Verbindungen in Azure

Ab dem 15. November 2017 werden ausgehende E-Mail-Nachrichten, die von einem virtuellen Computer (VM) direkt an externe Domänen (z.B. outlook.com und gmail.com) gesendet werden, nur noch für bestimmte Abonnementtypen in Microsoft Azure verfügbar gemacht. Ausgehende SMTP-Verbindungen, die TCP-Port 25 verwenden, wurden blockiert. Dieser Port wird vor allem für die nicht authentifizierte E-Mail-Zustellung verwendet.

Diese Verhaltensänderung gilt nur für neue Abonnements und neue Bereitstellungen ab dem 15. November 2017.

## <a name="recommended-method-of-sending-email"></a>Empfohlene Methode zum Senden von E-Mails
Es wird empfohlen, authentifizierte SMTP-Relaydienste (die i.d.R. über TCP-Port 587 oder 443 eine Verbindung herstellen, aber auch andere Ports unterstützen) zu verwenden, um E-Mails über Azure-VMs oder Azure App Service zu senden. Mithilfe dieser Dienste wird die Zuverlässigkeit der IP-Adresse oder Domäne bewahrt, um die Möglichkeit zu minimieren, dass Drittanbieter die Nachricht ablehnen. Solche SMTP-Relaydienste umfassen unter anderem [SendGrid](https://sendgrid.com/partners/azure/). Möglicherweise verfügen Sie über einen sicheren und lokal ausgeführten SMTP-Relaydienst, den Sie verwenden können.

Das Verwenden dieser Dienste für die E-Mail-Zustellung ist in Azure nicht eingeschränkt – unabhängig vom Abonnementtyp.

## <a name="enterprise-agreement"></a>Enterprise Agreement
Für Azure-Benutzer mit Enterprise Agreement gibt es keine technischen Änderungen am Senden von E-Mails ohne authentifiziertes Relay. Sowohl neue als auch vorhandene Enterprise Agreement-Benutzer können die Zustellung ausgehender E-Mails von Azure-VMs direkt an externe E-Mail-Anbieter testen, ohne Einschränkungen der Azure-Plattform. Obwohl keine Garantie dafür besteht, dass E-Mail-Anbieter eingehende E-Mails von einem bestimmten Benutzer akzeptieren, werden Zustellversuche von der Azure-Plattform für VMs in Enterprise Agreement-Abonnements nicht blockiert. Sie müssen direkt mit den E-Mail-Anbietern zusammenarbeiten, um Probleme bei der Nachrichtenzustellung oder der Spamfilterung zu beheben, die bestimmte Anbieter betreffen.

## <a name="pay-as-you-go"></a>Nutzungsbasierte Bezahlung
Wenn Sie sich vor dem 15. November 2017 für die nutzungsbasierte Zahlung oder die Microsoft Partner Network-Abonnementangebote registriert haben, gelten für Sie keine technischen Änderungen beim Testen der Zustellung ausgehender E-Mails. Sie können weiterhin die Zustellung ausgehender E-Mails von Azure-VMs in diesen Abonnements direkt an externe E-Mail-Anbieter ohne Einschränkungen der Azure-Plattform testen. Auch hier ist nicht garantiert, dass E-Mail-Anbieter eingehende E-Mails von einem bestimmten Benutzer akzeptieren. Außerdem müssen Benutzer direkt mit E-Mail-Anbietern zusammenarbeiten, um Probleme bei der Nachrichtenzustellung oder der Spamfilterung zu beheben, die bestimmte Anbieter betreffen.

Für die nutzungsbasierte Zahlung oder Microsoft Partner Network-Abonnements, die nach dem 15. November 2017 erstellt wurden, gelten technische Einschränkungen: E-Mails, die direkt von VMs dieser Abonnements gesendet werden, werden blockiert. Wenn Sie E-Mails von einer Azure-VM direkt an externe E-Mail-Anbieter senden möchten (ohne authentifiziertes SMTP-Relay), können Sie die Aufhebung der Einschränkung anfordern. Solche Anfragen werden von Microsoft geprüft und erst nach Durchführung von zusätzlichen Betrugsprüfungen ggf. genehmigt. Um eine Anfrage zu stellen, öffnen Sie eine Supportanfrage mit dem folgenden Problemtyp: **Technisch** > **virtuelles Netzwerk** > **Konnektivität** > **E-Mail kann nicht gesendet werden (SMTP/Port 25)** . Geben Sie unbedingt Informationen dazu an, warum Ihre Bereitstellung E-Mails direkt an E-Mail-Anbieter senden muss, anstatt ein authentifiziertes Relay zu verwenden.

Wenn für ein Abonnement vom Typ nutzungsbasierte Zahlung oder ein Microsoft Partner Network-Abonnement eine Ausnahmeregelung gilt, werden VMs in diesem Abonnement in Zukunft ausgenommen.

> [!NOTE]
> Microsoft behält sich das Recht vor, diese Ausnahmeregelung zu widerrufen, wenn ein Verstoß gegen die Vertragsbedingungen festgestellt wird.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, BizSpark und kostenlose Testversion
Wenn Sie nach dem 15. November 2017 ein Abonnement für MSDN, Azure Pass, Azure in Open, Education, BizSpark, Azure Sponsorship, Azure Student, Visual Studio oder die kostenlose Testversion erstellt haben, gelten für Sie technische Einschränkungen: E-Mails, die von VMs in diesen Abonnements direkt an E-Mail-Anbieter gesendet werden, werden blockiert. Die Einschränkungen sollen Missbrauch verhindern. Anfragen zur Entfernung dieser Einschränkung werden abgelehnt.

Wenn Sie über diese Abonnementtypen verfügen, wird wie oben angemerkt empfohlen, SMTP-Relaydienste zu verwenden.

## <a name="cloud-service-provider-csp"></a>Clouddienstanbieter

Wenn Sie Azure-Ressourcen über Clouddienstanbieter nutzen, können Sie bei Ihrem Clouddienstanbieter einen Supportfall erstellen und anfordern, dass für Sie ein Entsperrfall erstellt wird, wenn kein sicheres SMTP-Relay verwendet werden kann.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.
