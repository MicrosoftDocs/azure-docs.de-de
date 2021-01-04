---
title: Behandeln von Problemen mit ausgehenden SMTP-Verbindungen in Azure | Microsoft-Dokumentation
description: Lernen Sie die empfohlene Methode zum Versenden von E-Mails kennen und erfahren Sie, wie Sie Probleme mit der ausgehenden SMTP-Konnektivität in Azure beheben können.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: d2e5996da5a1fe3f5b154d57ee509f25e54e30ac
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862359"
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

Wenn Sie sich vor dem 15. November 2017 für ein Abonnement mit nutzungsbasierter Bezahlung registriert haben, gelten für Sie keine technischen Änderungen beim Testen der Zustellung ausgehender E-Mails. Sie können weiterhin die Zustellung ausgehender E-Mails von Azure-VMs in diesen Abonnements direkt an externe E-Mail-Anbieter ohne Einschränkungen der Azure-Plattform testen. Auch hier ist nicht garantiert, dass E-Mail-Anbieter eingehende E-Mails von einem bestimmten Benutzer akzeptieren. Außerdem müssen Benutzer direkt mit E-Mail-Anbietern zusammenarbeiten, um Probleme bei der Nachrichtenzustellung oder der Spamfilterung zu beheben, die bestimmte Anbieter betreffen.

Für Abonnements mit nutzungsbasierter Bezahlung, die nach dem 15. November 2017 erstellt wurden, gelten technische Einschränkungen: E-Mails, die direkt von VMs dieser Abonnements gesendet werden, werden blockiert. Wenn Sie E-Mails von Azure-VMs direkt an externe E-Mail-Anbieter senden möchten (ohne authentifiziertes SMTP-Relay) und Sie über ein ordnungsgemäß geführtes Konto mit Zahlungsverlauf verfügen, können Sie das Aufheben dieser Einschränkung im Abschnitt **Konnektivität** auf dem Blatt **Diagnose und Problembehandlung** für eine Azure Virtual Network-Ressource im Azure-Portal anfordern. Wenn Ihr Abonnement qualifiziert ist, wird es aktiviert, andernfalls erhalten Sie Anweisungen zu den nächsten Schritten. 

Sobald für ein Abonnement mit nutzungsbasierter Bezahlung eine Ausnahmeregelung gilt und die VMs im Azure-Portal beendet und gestartet wurden, werden alle VMs in diesem Abonnement in Zukunft ausgenommen. Die Ausnahme gilt nur für das angeforderte Abonnement und ausschließlich für den VM-Datenverkehr, der direkt an das Internet weitergeleitet wird.

> [!NOTE]
> Microsoft behält sich das Recht vor, diese Ausnahmeregelung zu widerrufen, wenn ein Verstoß gegen die Vertragsbedingungen festgestellt wird.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, Microsoft Azure for Students, Visual Studio und Testversion

Wenn Sie nach dem 15. November 2017 ein Abonnement für MSDN, Azure Pass, Azure in Open, Education, Azure for Students, Visual Studio oder die kostenlose Testversion erstellt haben, gelten für Sie technische Einschränkungen: E-Mails, die von VMs in diesen Abonnements direkt an E-Mail-Anbieter gesendet werden, werden blockiert. Die Einschränkungen sollen Missbrauch verhindern. Anfragen zur Entfernung dieser Einschränkung werden abgelehnt.

Wenn Sie diese Abonnementtypen verwenden, sollten Sie – wie oben angemerkt – SMTP-Relaydienste verwenden oder Ihren Abonnementtyp ändern.

## <a name="cloud-service-provider-csp"></a>Clouddienstanbieter

Wenn Sie Azure-Ressourcen über CSP verwenden, können Sie die Aufhebung dieser Einschränkung im Abschnitt **Konnektivität** auf dem Blatt **Diagnose und Problembehandlung** für eine Virtual Network-Ressource im Azure-Portal anfordern. Wenn Ihr Abonnement qualifiziert ist, wird es aktiviert, andernfalls erhalten Sie Anweisungen zu den nächsten Schritten.

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network (MPN), BizSpark Plus oder Azure Sponsorship

Für Microsoft Partner Network- (MPN), BizSpark Plus- oder Azure Sponsorship-Abonnements, die nach dem 15. November 2017 erstellt wurden, gelten technische Einschränkungen: Direkt von VMs dieser Abonnements gesendete E-Mails werden blockiert. Wenn Sie E-Mails von einer Azure-VM direkt an externe E-Mail-Anbieter senden möchten (ohne authentifiziertes SMTP-Relay), können Sie die Aufhebung der Einschränkung anfordern. Erstellen Sie dazu eine Supportanfrage mit dem folgenden Problemtyp: **Technisch** > **virtuelles Netzwerk** > **Konnektivität** > **E-Mail kann nicht gesendet werden (SMTP/Port 25)** . Geben Sie unbedingt Informationen dazu an, warum Ihre Bereitstellung E-Mails direkt an E-Mail-Anbieter senden muss, anstatt ein authentifiziertes Relay zu verwenden. Die Anfragen werden geprüft und nach Ermessen von Microsoft genehmigt. Möglicherweise werden die Anforderungen erst nach zusätzlichen Betrugsüberprüfungen erteilt. 

Sobald für ein Abonnement eine Ausnahmeregelung gilt und die VMs im Azure-Portal beendet und gestartet wurden, werden alle VMs in diesem Abonnement in Zukunft ausgenommen. Die Ausnahme gilt nur für das angeforderte Abonnement und ausschließlich für den VM-Datenverkehr, der direkt an das Internet weitergeleitet wird.

## <a name="restrictions-and-limitations"></a>Einschränkungen

- Das Routing von Port 25-Datenverkehr über Azure PaaS-Dienste wie [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) wird nicht unterstützt.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen, und verwenden Sie folgenden Problemtyp: **Technisch** > **virtuelles Netzwerk** > **Konnektivität** > **E-Mail kann nicht gesendet werden (SMTP/Port 25)** .
