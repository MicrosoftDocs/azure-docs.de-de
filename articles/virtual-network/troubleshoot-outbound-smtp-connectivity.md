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
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: 518b8e33ac9adfdd1aa121e0cb8d1558545980e7
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054853"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Behandeln von Problemen mit ausgehenden SMTP-Verbindungen in Azure

Ab dem 15. November 2017 werden ausgehende E-Mail-Nachrichten, die von einem virtuellen Computer (VM) direkt an externe Domänen (z. B. outlook.com und gmail.com) gesendet werden, nur noch für bestimmte Abonnementtypen in Azure verfügbar gemacht. Ausgehende SMTP-Verbindungen, die TCP-Port 25 verwenden, wurden blockiert. (Der Port 25 wird in erster Linie für die E-Mail-Zustellung ohne Authentifizierung verwendet.)

Diese Änderung im Verhalten gilt nur für Abonnements und Bereitstellungen, die nach dem 15. November 2017 erstellt wurden.

## <a name="recommended-method-of-sending-email"></a>Empfohlene Methode zum Senden von E-Mails

Es wird empfohlen, authentifizierte SMTP-Relaydienste zu verwenden, um E-Mails von Azure-VMs oder von Azure App Service zu senden. (Diese Relaydienste stellen in der Regel eine Verbindung über den TCP-Port 587 her, sie unterstützen aber auch andere Ports.) Mithilfe dieser Dienste wird die Zuverlässigkeit der IP-Adresse oder Domäne bewahrt, um die Möglichkeit zu minimieren, dass Drittanbieter Nachrichten ablehnen. [SendGrid](https://sendgrid.com/partners/azure/) ist ein solcher SMTP-Relaydienst, aber es gibt auch andere. Möglicherweise verfügen Sie auch über einen sicheren SMTP-Relaydienst, der lokal ausgeführt wird und den Sie verwenden können.

Das Verwenden dieser Dienste für die E-Mail-Zustellung ist in Azure nicht eingeschränkt – unabhängig vom Abonnementtyp.

## <a name="enterprise-agreement"></a>Enterprise Agreement

Für Azure-Benutzer mit Enterprise Agreement gibt es keine technischen Änderungen am Senden von E-Mails ohne authentifiziertes Relay. Sowohl neue als auch vorhandene Enterprise Agreement-Benutzer können die Zustellung ausgehender E-Mails von Azure-VMs direkt an externe E-Mail-Anbieter testen, ohne Einschränkungen der Azure-Plattform. Es gibt keine Garantie, dass E-Mail-Anbieter eingehende E-Mails von einem bestimmten Benutzer akzeptieren. Die Azure-Plattform blockiert jedoch nicht die Bereitstellungsversuche für VMs innerhalb von Enterprise Agreement-Abonnements. Sie müssen direkt mit den E-Mail-Anbietern zusammenarbeiten, um Probleme bei der Nachrichtenzustellung oder der Spamfilterung zu beheben, die bestimmte Anbieter betreffen.

## <a name="pay-as-you-go"></a>Nutzungsbasierte Bezahlung

Wenn Sie sich vor dem 15. November 2017 für ein Abonnement mit nutzungsbasierter Bezahlung registriert haben, gelten für Sie keine technischen Änderungen beim Testen der Zustellung ausgehender E-Mails. Sie können weiterhin die Zustellung ausgehender E-Mails von Azure-VMs in diesen Abonnements direkt an externe E-Mail-Anbieter ohne Einschränkungen der Azure-Plattform testen. Es gibt auch hier keine Garantie, dass E-Mail-Anbieter eingehende E-Mails von einem bestimmten Benutzer akzeptieren. Benutzer müssen direkt mit den E-Mail-Anbietern zusammenarbeiten, um Probleme bei der Nachrichtenzustellung oder der Spamfilterung zu beheben, die bestimmte Anbieter betreffen.

Für Abonnements mit nutzungsbasierter Bezahlung, die nach dem 15. November 2017 erstellt wurden, gelten technische Einschränkungen: E-Mails, die direkt von VMs in den Abonnements gesendet werden, werden blockiert. Wenn Sie in der Lage sein möchten, E-Mails von Azure-VMs direkt an externe E-Mail-Anbieter zu senden (ohne ein authentifiziertes SMTP-Relay zu verwenden), und Sie über ein ordnungsgemäß geführtes Konto mit Zahlungsverlauf verfügen, können Sie anfordern, dass die Einschränkung entfernt wird. Sie können dies im Abschnitt **Konnektivität** des Blatts **Diagnose und Problembehandlung** für eine Azure Virtual Network-Ressource im Azure-Portal vornehmen. Wenn Ihre Anforderung akzeptiert wird, wird Ihr Abonnement aktiviert, oder Sie erhalten Anweisungen für die nächsten Schritte. 

Sobald für ein Abonnement mit nutzungsbasierter Bezahlung eine Ausnahmeregelung gilt und die VMs im Azure-Portal beendet und gestartet wurden, werden alle VMs in diesem Abonnement in Zukunft ausgenommen. Die Ausnahme gilt nur für das angeforderte Abonnement und ausschließlich für den VM-Datenverkehr, der direkt an das Internet weitergeleitet wird.

> [!NOTE]
> Microsoft behält sich das Recht vor, diese Ausnahmeregelungen zu widerrufen, wenn ein Verstoß gegen die Vertragsbedingungen festgestellt wird.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, Microsoft Azure for Students, Visual Studio und Testversion

Wenn Sie einen der folgenden Abonnementtypen nach dem 15. November 2017 erstellt haben, gelten technische Einschränkungen, die E-Mails blockieren, die von VMs innerhalb des Abonnements direkt an E-Mail-Anbieter gesendet werden:
- MSDN
- Azure Pass
- Azure in Open
- Education
- Azure for Students
- Kostenlose Testversion
- Beliebiges Visual Studio-Abonnement  

Die Einschränkungen sollen Missbrauch verhindern. Anforderungen zum Entfernen dieser Einschränkungen werden nicht erteilt.

Wenn Sie diese Abonnementtypen verwenden, sollten Sie – wie oben in diesem Artikel angemerkt – SMTP-Relaydienste verwenden oder Ihren Abonnementtyp ändern.

## <a name="cloud-solution-provider"></a>Cloud Solution Provider

Wenn Sie Azure-Ressourcen über Cloud Solution Provider verwenden, können Sie die Aufhebung dieser Einschränkung im Abschnitt **Konnektivität** des Bereichs **Diagnose und Problembehandlung** für eine Virtual Network-Ressource im Azure-Portal anfordern. Wenn Ihre Anforderung akzeptiert wird, wird Ihr Abonnement aktiviert, oder Sie erhalten Anweisungen für die nächsten Schritte.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus oder Azure Sponsorship

Für Abonnements der folgenden Typen, die nach dem 15. November 2017 erstellt wurden, gelten technische Einschränkungen: E-Mails, die direkt von VMs in den Abonnements gesendet werden, werden blockiert:

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure Sponsorship

Wenn Sie E-Mails von einer Azure-VM direkt an externe E-Mail-Anbieter senden möchten (ohne authentifiziertes SMTP-Relay), können Sie die Aufhebung der Einschränkung anfordern. Erstellen Sie dazu eine Supportanfrage mit dem folgenden Problemtyp: **Technisch** > **virtuelles Netzwerk** > **Konnektivität** > **E-Mail kann nicht gesendet werden (SMTP/Port 25)** . Geben Sie unbedingt Informationen dazu an, warum Ihre Bereitstellung E-Mails direkt an E-Mail-Anbieter senden muss, anstatt ein authentifiziertes Relay zu verwenden. Die Anfragen werden geprüft und nach Ermessen von Microsoft genehmigt. Die Anforderungen werden erst nach zusätzlichen Betrugsüberprüfungen erteilt. 

Sobald für ein Abonnement eine Ausnahmeregelung gilt und die VMs im Azure-Portal beendet und erneut gestartet wurden, werden alle VMs in diesem Abonnement in Zukunft ausgenommen. Die Ausnahme gilt nur für das angeforderte Abonnement und ausschließlich für den VM-Datenverkehr, der direkt an das Internet weitergeleitet wird.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen. Verwenden Sie diesen Problemtyp: **Technisch** > **virtuelles Netzwerk** > **Konnektivität** > **E-Mail kann nicht gesendet werden (SMTP/Port 25)** .
