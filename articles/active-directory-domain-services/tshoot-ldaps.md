---
title: Behandeln von Problemen mit Secure LDAP in Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme mit Secure LDAP (LDAPS) bei einer verwalteten Azure AD Domain Services-Domäne behandeln
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e21ec7d8fdf7a5c7a8ba7a9f22aa5709581cbae6
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618398"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Behandeln von Secure LDAP-Konnektivitätsproblemen bei einer verwalteten Azure AD Domain Services-Domäne

Anwendungen und Dienste, die LDAP (Lightweight Directory Access Protocol) für die Kommunikation mit Azure Active Directory Domain Services (Azure AD DS) verwenden, [können für die Verwendung von Secure LDAP konfiguriert werden](tutorial-configure-ldaps.md). Ein geeignetes Zertifikat muss vorhanden sein, und die erforderlichen Netzwerkports müssen geöffnet sein, damit Secure LDAP ordnungsgemäß funktioniert.

Dieser Artikel hilft Ihnen beim Beheben von Problemen mit dem Zugriff über Secure LDAP in Azure AD DS.

## <a name="common-connection-issues"></a>Allgemeine Verbindungsprobleme

Wenn Sie Probleme beim Herstellen einer Verbindung mit einer verwalteten Azure AD DS-Domäne über Secure LDAP haben, prüfen Sie die folgenden Schritte zur Problembehandlung. Versuchen Sie nach jedem Problembehandlungsschritt, erneut eine Verbindung mit der verwalteten Domäne herzustellen:

* Die Ausstellerkette des Zertifikats für sicheres LDAP muss auf dem Client als vertrauenswürdig eingestuft werden. Sie könnten die Stammzertifizierungsstelle dem vertrauenswürdigen Stammzertifikatspeicher auf dem Client hinzufügen, um die Vertrauensstellung einzurichten.
    * Stellen Sie sicher, dass Sie [das Zertifikat exportieren und auf den Clientcomputern anwenden][client-cert].
* Prüfen Sie, ob das Secure LDAP-Zertifikat für die verwaltete Domäne den DNS-Namen im Attribut *Antragsteller* oder *Alternative Antragstellernamen* enthält.
    * Überprüfen Sie die [Zertifikatanforderungen für Secure LDAP][certs-prereqs], und erstellen Sie bei Bedarf ein Ersatzzertifikat.
* Prüfen Sie, ob der LDAP-Client (z.B. *ldp.exe*) über einen DNS-Namen und nicht über die IP-Adresse eine Verbindung mit dem Secure LDAP-Endpunkt herstellt.
    * Das auf die verwaltete Domäne angewendete Zertifikat enthält keine IP-Adressen des Diensts, sondern nur die DNS-Namen.
* Überprüfen Sie den DNS-Namen, mit dem der LDAP-Client eine Verbindung herstellt. Er muss in die öffentliche IP-Adresse für sicheres LDAP in der verwalteten Domäne aufgelöst werden.
    * Wenn der DNS-Name in die interne IP-Adresse aufgelöst wird, aktualisieren Sie den DNS-Eintrag, damit er in die externe IP-Adresse aufgelöst wird.
* Für die externe Konnektivität muss die Netzwerksicherheitsgruppe eine Regel enthalten, die den Datenverkehr zum TCP-Port 636 vom Internet zulässt.
    * Wenn Sie von Ressourcen, die direkt mit dem virtuellen Netzwerk, aber nicht mit externen Verbindungen verbunden sind, über Secure LDAP eine Verbindung mit der verwalteten Domäne herstellen können, stellen Sie sicher, dass Sie [eine Netzwerksicherheitsgruppen-Regel erstellen, die Secure LDAP-Datenverkehr zulässt][ldaps-nsg].

## <a name="next-steps"></a>Nächste Schritte

Falls weiterhin Probleme auftreten, [öffnen Sie eine Azure-Supportanfrage][azure-support], um weitere Hilfe bei der Problembehandlung zu erhalten.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
