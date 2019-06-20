---
title: Häufig gestellte Fragen – Azure Dedicated HSM | Microsoft-Dokumentation
description: Häufig gestellte Fragen zu verschiedenen Themen in Azure Dedicated HSM
services: dedicated-hsm
author: johncdawson
manager: barbkess
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 5/8/2019
ms.author: barclayn
ms.openlocfilehash: b73b6bdc0158591565281ca2e86a9a474c4196d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65467723"
---
# <a name="frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ)

Enthält Antworten auf häufige gestellte Fragen zu Microsoft Azure Dedicated HSM.

## <a name="the-basics"></a>Die Grundlagen

### <a name="q-what-is-a-hardware-security-module-hsm"></a>F: Was ist ein Hardwaresicherheitsmodul (HSM)?

Ein Hardwaresicherheitsmodul (HSM) ist ein physisches Computergerät, das zum Schützen und Verwalten von kryptografischen Schlüsseln verwendet wird. In HSMs gespeicherte Schlüsseln können für kryptografische Vorgänge verwendet werden. Das Schlüsselmaterial verbleibt sicher in manipulationssicheren, manipulationsgeschützten Hardwaremodulen. Das HSM erlaubt nur authentifizierten und autorisierten Anwendungen die Verwendung der Schlüssel. Das Schlüsselmaterial verlässt nie die HSM-Schutzgrenze.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>F: Was bietet Azure Dedicated HSM?

Azure Dedicated HSM ist ein cloudbasierter Dienst, der in Azure-Datencentern gehostete HSMs bereitstellt, die direkt mit dem virtuellen Netzwerk eines Kunden verbunden sind. Diese HSMs sind dedizierte Network Appliances (SafeNet Network HSM 7, Modell A790 von Gemalto). Sie werden direkt für den privaten IP-Adressraum eines Kunden bereitgestellt, und Microsoft besitzt keinen Zugriff auf die kryptografische Funktionalität der HSMs. Nur der Kunde verfügt über vollständige Verwaltungs- und Kryptografiekontrolle für diese Geräte. Kunden sind verantwortlich für die Verwaltung des Geräts, und sie können vollständige Aktivitätsprotokolle direkt von ihren Geräten erhalten. Dedizierte HSMs unterstützen Kunden bei der Erfüllung von Compliance- und gesetzlichen Anforderungen, z.B. von FIPS 140-2 Level 3, HIPAA, PCI-DSS, eIDAS usw.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>F: Welche Hardware wird für dedizierte HSM verwendet?

Microsoft ist eine Partnerschaft mit Gemalto eingegangen, um den Azure Dedicated HSM-Dienst bereitzustellen. Das dabei eingesetzte Gerät ist [SafeNet Luna Network HSM 7, Modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Dieses Gerät bietet nicht nur eine nach FIPS 140-2 Level 3 validierte Firmware, sondern auch niedrige Latenzzeiten, hohe Leistung und hohe Kapazität über 10 Partitionen. 

### <a name="q-what-is-an-hsm-used-for"></a>F: Wofür wird ein HSM verwendet?

HSMs dienen zur Speicherung von Kryptografieschlüsseln, die für Kryptografiefunktionen wie SSL (Secure Sockets Layer), die Verschlüsselung von Daten, die PKI (Public Key-Infrastruktur), die Verwaltung digitaler Rechte (Digital Rights Management, DRM) und die Signatur von Dokumenten verwendet werden.

### <a name="q-how-does-dedicated-hsm-work"></a>F: Wie funktioniert Dedicated HSM?

Kunden können HSMs in bestimmten Regionen über PowerShell oder die Befehlszeilenschnittstelle bereitstellen. Der Kunde gibt an, mit welchem virtuellen Netzwerk die HSMs verbunden werden sollen. Nach der Bereitstellung sind die HSMs im festgelegten Subnetz an einer zugewiesenen IP-Adresse im privaten IP-Adressraum des Kunden verfügbar. Kunden können dann eine Verbindung zwischen SSH für die Applianceverwaltung und den HSMs herstellen, um HSM-Clientverbindungen einzurichten, HSMs zu initialisieren, Partitionen zu erstellen, Rollen wie Partitionsverantwortlicher, Kryptoverantwortlicher und Kryptobenutzer zu definieren und zuzuweisen. Schließlich führt ein Kunde mithilfe von HSM-Clienttools, -SDKs oder -Software, die von Gemalto bereitgestellt werden, kryptografische Vorgänge mithilfe seiner Anwendungen aus.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>F: Welche Software wird mit dem Dedicated HSM-Dienst bereitgestellt?

Gemalto ist Lieferant der gesamten Software für das HSM-Gerät nach der Bereitstellung durch Microsoft. Die Software ist über das [Gemalto-Kundensupportportal](https://supportportal.gemalto.com/csm/) verfügbar. Kunden, die den Dedicated HSM-Dienst nutzen, müssen für den Gemalto-Support registriert sein und über eine Kunden-ID verfügen, die den Zugriff auf Software und den Download von relevanter Software ermöglicht. Die unterstützte Clientsoftware ist Version 7.2, die mit der von FIPS 140-2 Level 3 validierten Firmwareversion 7.0.3 kompatibel ist. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>F: Bietet Azure Dedicated HSM kennwortbasierte und PED-basierte Authentifizierung?

Im Moment stellt Azure Dedicated HSM nur HSMs mit kennwortbasierter Authentifizierung bereit.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>F: Hostet Azure Dedicated HSM meine HSMs für mich?

Microsoft bietet nur das Gemalto SafeNet Luna Network HSM über den Dedicated HSM-Dienst und kann keine vom Kunden bereitgestellten Geräte hosten.

### <a name="q-does-azure-dedicated-hsm-support-payment-pinetf-features"></a>F: Unterstützt Azure Dedicated HSM Zahlungsfunktionen (PIN/ETF)?

Der Azure Dedicated HSM-Dienst verwendet SafeNet Luna Network HSM 7-Geräte (Modell A790). Diese Geräte unterstützen keine für Bezahl-HSM spezifischen Funktionen (z. B. PIN oder ETF) oder Zertifizierungen. Wenn Sie möchten, dass der Azure Dedicated HSM-Dienst in Zukunft Bezahl-HSMs unterstützt, geben Sie das entsprechende Feedback an Ihren zuständigen Microsoft-Kontobeauftragten weiter.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>F: In welchen Azure-Regionen ist Dedicated HSM verfügbar?

Seit Ende März 2019 ist Dedicated HSM in den 14 unten aufgeführten Regionen verfügbar. Weitere Regionen sind geplant und können über Ihren zuständigen Microsoft-Kontobeauftragten besprochen werden.

* USA (Ost)
* USA (Ost) 2
* USA (Westen)
* USA Süd Mitte
* Asien, Südosten
* Asien, Osten
* Nordeuropa
* Europa, Westen
* UK, Süden
* UK, Westen
* Kanada, Mitte
* Kanada, Osten
* Australien (Osten)
* Australien, Südosten

## <a name="interoperability"></a>Interoperabilität

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>F: Wie stellt meine Anwendung eine Verbindung mit einem dedizierten HSM her?

Sie verwenden HSM-Clienttools, -SDKs oder -Software, die von Gemalto bereitgestellt werden, um kryptografische Vorgänge mithilfe Ihrer Anwendungen auszuführen. Die Software ist über das [Gemalto-Kundensupportportal](https://supportportal.gemalto.com/csm/) verfügbar. Kunden, die den Dedicated HSM-Dienst nutzen, müssen für den Gemalto-Support registriert sein und über eine Kunden-ID verfügen, die den Zugriff auf Software und den Download relevanter Software ermöglicht.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>F: Kann eine Anwendung eine Verbindung mit Dedicated HSM aus einem anderen VNET in einer Region oder über Regionen hinweg herstellen?

Ja, Sie müssen [VNET-Peering](../virtual-network/virtual-network-peering-overview.md) innerhalb einer Region verwenden, um eine Verbindung über virtuelle Netzwerke hinweg herzustellen. Für regionsübergreifende Konnektivität müssen Sie [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) verwenden.

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>F: Kann ich Dedicated HSM mit lokalen HSMs synchronisieren?

Ja, Sie können lokale HSMs mit Dedicated HSM synchronisieren. [Point-to-Point-VPN- oder Point-to-Site](../vpn-gateway/vpn-gateway-about-vpngateways.md)-Konnektivität kann verwendet werden, um eine Verbindung mit Ihrem lokalen Netzwerk herzustellen.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>F: Kann ich Daten, die von anderen Azure-Diensten verwendet werden, mit Schlüsseln verschlüsseln, die in Dedicated HSM gespeichert sind?

Nein. Auf Azure Dedicated HSMs kann nur von Ihrem virtuellen Netzwerk aus zugegriffen werden.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>F: Kann ich Schlüssel aus einem vorhandenen lokalen HSM in Dedicated HSM importieren?

Ja, wenn Sie über lokale Gemalto SafeNet-HSMs verfügen. Es gibt mehrere Möglichkeiten. Weitere Informationen finden Sie in der Gemalto HSM-Dokumentation.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>F: Welche Betriebssysteme werden von Dedicated HSM-Clientsoftware unterstützt?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD.
* Virtuell: VMware, HyperV, Xen, KVM.

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>F: Wie konfiguriere ich meine Clientanwendung so, dass eine Hochverfügbarkeitskonfiguration mit mehreren Partitionen aus mehreren HSMs erstellt wird?

Um Hochverfügbarkeit zu erreichen, müssen Sie Ihre HSM-Clientanwendungskonfiguration so einrichten, dass sie Partitionen von jedem HSM verwendet. Weitere Informationen finden Sie in der Dokumentation zur Gemalto HSM-Clientsoftware.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>F: Welche Authentifizierungsmechanismen werden von Dedicated HSM unterstützt?

Azure Dedicated HSM verwendet SafeNet Network HSM 7-Appliances (Modell A790), die kennwortbasierte Authentifizierung unterstützen.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>F: Welche SDKs, APIs und Clientsoftware sind für die Verwendung mit Dedicated HSM verfügbar?

PKCS#11, Java (JCA/JCE), Microsoft CAPI und CNG, OpenSSL.

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>F: Kann ich Schlüssel aus Luna 5/6-HSMs in Azure Dedicated HSMs importieren/migrieren?

Ja. Weitere Informationen finden Sie im Gemalto-Migrationshandbuch. 

## <a name="using-your-hsm"></a>Verwenden des HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>F: Wie kann ich entscheiden, ob Azure Key Vault oder Azure Dedicated HSM verwendet werden sollte?

Azure Dedicated HSM ist die geeignete Wahl für Unternehmen, die zu lokalen Azure-Anwendungen migrieren, die HSMs verwenden. Dedizierte HSMs bieten die Möglichkeit, eine Anwendung mit minimalen Änderungen zu migrieren. Wenn kryptografische Vorgänge im Code der Anwendung ausgeführt werden, der in einem virtuellen Azure-Computer oder in einer Web-App ausgeführt wird, können sie Dedicated HSM verwenden. Im Allgemeinen kann Software auf Datenträgern, die in IaaS-Modellen (Infrastructure-as-a-Service) ausgeführt wird, die HSMs als Schlüsselspeicher unterstützen, Dedicate HSM verwenden, z.B. Application Gateway oder Traffic Manager für schlüsselloses SSL, ADCS (Active Directory-Zertifikatdienste) oder ähnliche PKI-Tools, Tools/Anwendungen für die Dokumentensignierung, Codesignierung oder einen SQL Server (IaaS), der mit TDE (transparente Datenbankverschlüsselung) mit Masterkey in einem HSM unter Verwendung eines EKM-Anbieters (Extensible Key Management) konfiguriert ist. Azure Key Vault eignet sich für „in der Cloud geborene“ Anwendungen oder für Verschlüsselungsszenarien für ruhende Daten, bei denen Kundendaten von PaaS- (Platform-as-a-Service) oder SaaS-Szenarien (Software-as-a-Service) wie Office 365 mit Kundenschlüssel, Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store-Verschlüsselung mit vom Kunden verwaltetem Schlüssel, Azure Storage-Verschlüsselung mit vom Kunden verwaltetem Schlüssel und Azure SQL mit vom Kunden verwaltetem Schlüssel verarbeitet werden.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>F: Welche Verwendungsszenarien eignen sich am besten für Azure Dedicated HSM?

Azure Dedicated HSM eignet sich am besten für Migrationsszenarien. Dies bedeutet, dass Sie lokale Anwendungen zu Azure migrieren, die bereits HSMs verwenden. Dies bietet eine reibungsarme Option für die Migration zu Azure mit minimalen Änderungen an der Anwendung. Wenn kryptografische Vorgänge im Code der Anwendung ausgeführt werden, der in einem virtuellen Azure-Computer oder in einer Web-App ausgeführt wird, kann Dedicated HSM verwendet werden. Im Allgemeinen kann Software auf Datenträgern, die in IaaS-Modellen (Infrastructure-as-a-Service) ausgeführt wird, die HSMs als Schlüsselspeicher unterstützen, Dedicate HSM verwenden. Beispiele dafür sind:

* Application Gateway oder Traffic Manager für schlüsselloses SSL
* ADCS (Active Directory-Zertifikatdienste)
* Ähnliche PKI-Tools
* Tools/Anwendungen für das Signieren von Dokumenten
* Codesignierung
* SQL Server (IaaS), konfiguriert mit TDE (transparente Datenbankverschlüsselung) mit Masterschlüssel in einem HSM unter Verwendung eines EKM-Anbieters (Extensible Key Management).

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>F: Kann Dedicated HSM mit Office 365 mit Kundenschlüssel, mit Azure Information Protection, Azure Data Lake Storage, Disk Encryption, der Azure Storage-Verschlüsselung oder Azure SQL-TDE verwendet werden?

Nein. Dedicated HSM wird direkt im privaten IP-Adressraum eines Kunden bereitgestellt, sodass darauf nicht mit anderen Azure- oder Microsoft-Diensten zugegriffen werden kann.

## <a name="administration-access-and-control"></a>Verwaltung, Zugriff und Steuerung

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>F: Erhält der Kunde mit Dedicated HSMs die vollständige und ausschließliche Kontrolle über die HSMs?

Ja. Jede HSM-Appliance ist vollständig auf einen einzelnen Kunden ausgerichtet, und niemand sonst besitzt nach der Bereitstellung und Änderung des Administratorkennworts die Verwaltungskontrolle.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>F: Über welche Art von Zugriff auf mein HSM verfügt Microsoft?

Microsoft hat keine administrative oder kryptografische Kontrolle über das HSM. Microsoft besitzt über eine serielle Schnittstellenverbindung Zugriff auf die Überwachungsebene, um grundlegende Telemetriedaten wie Temperatur und Komponentenintegrität abzurufen. Dies ermöglicht Microsoft das Bereitstellen von proaktiven Benachrichtigungen bei Problemen mit der Ressourcenintegrität. Wenn erforderlich, kann der Kunde dieses Konto deaktivieren.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>F: Was ist das Konto „tenantadmin“, das von Microsoft verwendet wird? Ich bin daran gewöhnt, dass der Administratorbenutzer bei SafeNet HSMs „Administrator“ ist.

Im Lieferumfang des HSM-Geräts ist ein Standardbenutzer „Administrator“ mit dem üblichen Standardkennwort enthalten. Microsoft wollte nicht, dass Standardkennwörter verwendet werden, während ein Gerät in einem Pool darauf wartet, von Kunden bereitgestellt zu werden. Dies entspräche nicht unseren strengen Sicherheitsanforderungen. Aus diesem Grund legen wir ein sicheres Kennwort fest, das zur Bereitstellungszeit verworfen wird. Außerdem erstellen wird zur Bereitstellungszeit einen neuen Benutzer in der Administratorrolle namens „tenantadmin“. Dieser Benutzer besitzt das Standardkennwort, das die Kunden als erste Aktion bei der ersten Anmeldung bei dem neu bereitgestellten Gerät ändern. Dieser Prozess gewährleistet einen hohen Grad an Sicherheit und erfüllt unser Versprechen der alleinigen administrativen Kontrolle durch unsere Kunden. Anzumerken ist, dass der Benutzer „tenantadmin“ verwendet werden kann, um das Kennwort des Administratorbenutzers zurückzusetzen, wenn ein Kunde die Verwendung dieses Kontos bevorzugt. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>F: Kann Microsoft oder ein Mitarbeiter von Microsoft auf Schlüssel in meinem Dedicated HSM zugreifen?

Nein. Microsoft besitzt keinerlei Zugriff auf die im dem Kunden zugeordneten Dedicated HSM gespeicherten Schlüssel.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>F: Kann ich ein Upgrade der Software/Firmware für mir zugeordnete HSMs vornehmen?

Um den besten Support zu erhalten, empfiehlt Microsoft dringend, keine Software/Firmware auf dem HSM zu aktualisieren. Der Kunde hat jedoch die volle administrative Kontrolle, einschließlich eines Upgrades der Software/Firmware, wenn spezifische Funktionen von verschiedenen Firmwareversionen erforderlich sind. Bevor Änderungen vorgenommen werden, müssen die Auswirkungen verstanden werden, da dies z.B. den Status der FIPS-Validierung beeinflussen kann. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>F: Wie verwalte ich Dedicated HSM?

Sie können Dedicated HSMs verwalten, indem sie darauf über SSH zugreifen.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>F: Wie verwalte ich Partitionen auf dem dedizierten HSM?

Die Gemalto HSM-Clientsoftware wird zum Verwalten der HSMs und Partitionen verwendet.

### <a name="q-how-do-i-monitor-my-hsm"></a>F: Wie überwache ich mein HSM?

Ein Kunde besitzt über Syslog und SNMP vollständigen Zugriff auf die HSM-Aktivitätsprotokolle. Kunden müssen einen Syslog-Server oder SNMP-Server so einrichten, dass sie Protokolle oder Ereignisse von den HSMs erhalten.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>F: Kann ich von Dedicated HSM ein vollständiges Zugriffsprotokoll aller HSM-Vorgänge erhalten?

Ja. Sie können Protokolle von der HSM-Appliance an einen Syslog-Server senden.

## <a name="high-availability"></a>Hochverfügbarkeit

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>F: Ist es möglich, Hochverfügbarkeit in derselben Region oder über mehrere Regionen hinweg zu konfigurieren?

Ja. Die Konfiguration und Einrichtung der Hochverfügbarkeit erfolgt in der von Gemalto bereitgestellten HSM-Clientsoftware. HSMs aus demselben VNET oder aus anderen VNETs in derselben Region oder über Regionen hinweg bzw. lokale HSMs, die mit einem VNET per Site-to-Site- oder Point-to-Point-VPN verbunden sind, können derselben Hochverfügbarkeitskonfiguration hinzugefügt werden. Es sollte beachtet werden, dass hiermit nur wichtiges Material synchronisiert wird, und keine spezifischen Konfigurationselemente, z. B. Rollen.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>F: Kann ich HSMs aus meinem lokalen Netzwerk einer Hochverfügbarkeitsgruppe mit Azure Dedicated HSM hinzufügen?

Ja. Sie müssen die Anforderungen für Hochverfügbarkeit für SafeNet Luna Network HSM 7 erfüllen.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>F: Kann ich Luna 5/6-HSMs aus lokalen Netzwerken einer Hochverfügbarkeitsgruppe mit Azure Dedicated HSM hinzufügen?

Nein.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>F: Wie viele HSMs kann ich der gleichen Hochverfügbarkeitskonfiguration aus einer einzelnen Anwendung hinzufügen?

Für 16 Mitglieder einer Hochverfügbarkeitsgruppe wurde ein vollständiger Test mit hervorragenden Ergebnissen durchgeführt.

## <a name="support"></a>Support

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>F: Welche SLA gilt für den Dedicated HSM-Dienst?

Für den Dedicated HSM-Dienst ist keine spezifische Garantie für die Betriebszeit vorhanden. Microsoft stellt sicher, dass der Zugriff auf das Gerät auf Netzwerkebene gewährleistet ist, sodass Azure-Standardnetzwerk-SLAs gelten.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>F: Wie werden die in Azure Dedicated HSM verwendeten HSMs geschützt?

Azure-Datencenter verfügen über umfassende physische und prozedurale Sicherheitskontrollen. Darüber hinaus werden Dedicated HSMs in einem Bereich mit weiteren Zugangseinschränkungen des Datencenters gehostet. Diese Bereiche verfügen über zusätzliche physische Zugangskontrollen und Videokameraüberwachung für zusätzliche Sicherheit.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>F: Was geschieht, wenn es zu einer Sicherheitsverletzung oder einem Hardwaremanipulationsereignis kommt?

Der Dedicated HSM-Dienst verwendet SafeNet Network HSM 7-Appliances. Diese Appliances unterstützen physische und logische Manipulationserkennung. Wenn es jemals zu einem Manipulationsereignis kommt, werden die HSMs automatisch auf Null zurückgesetzt.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>F: Wie stelle ich sicher, dass die Schlüssel in meinen Dedicated HSMs nicht durch einen Fehler oder einen böswilligen Insiderangriff verloren gehen?

Es wird dringend empfohlen, ein lokales HSM-Sicherungsmedium zu verwenden, um eine regelmäßige periodische Sicherung der HSMs für die Notfallwiederherstellung durchzuführen. Sie müssen eine Peer-to-Peer- oder Site-to-Site-VPN-Verbindung mit einer lokalen Arbeitsstation verwenden, die mit einem HSM-Sicherungsmedium verbunden ist.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>F: Wie erhalte ich Support für Dedicated HSM?

Der Support wird sowohl von Microsoft als auch von Gemalto bereitgestellt.  Falls bei Ihnen ein Problem mit der Hardware oder dem Netzwerkzugriff besteht, können Sie eine Supportanfrage für Microsoft erstellen. Erstellen Sie bei einem Problem mit der HSM-Konfiguration, -Software und -Anwendungsentwicklung eine Supportanfrage für Gemalto. Erstellen Sie bei einem nicht eindeutigen Problem eine Supportanfrage für Microsoft. Falls erforderlich, kann Gemalto dann eingebunden werden. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>F: Wie erhalte ich die Clientsoftware, Dokumentation und Zugriff auf die Integrationsanleitung für das SafeNet Luna 7 HSM?

Nach der Registrierung für den Dienst wird eine Gemalto-Kunden-ID bereitgestellt, die die Registrierung im Gemalto-Portal für Kundensupport ermöglicht. Hiermit ist der Zugriff auf die gesamte Software und Dokumentation möglich, und Supportanfragen können direkt an Gemalto gerichtet werden.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>F: Wenn ein Sicherheitsrisiko gefunden wurde und ein Patch von Gemalto veröffentlicht wird, wer ist dann für das Upgraden/Patchen von Betriebssystemen/Firmware verantwortlich?

Microsoft hat nicht die Möglichkeit, eine Verbindung mit HSMs herzustellen, die Kunden zugeordnet sind. Kunden müssen ihre HSMs aktualisieren und patchen.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>F: Was passiert, wenn ich mein HSM neu starten muss?

Das HSM verfügt über eine Befehlszeilenoption für den Neustart. Es kommt jedoch zeitweilig zu Problemen beim Neustart, der dann hängt. Aus diesem Grund wird als sicherste Neustartmethode empfohlen, dass Sie eine Supportanfrage bei Microsoft stellen, damit das Gerät physisch neu gestartet wird. 

## <a name="cryptography-and-standards"></a>Kryptografie und Standards

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>F: Ist es sicher, Verschlüsselungscodes für meine wichtigsten Daten in Dedicated HSM zu speichern?

Ja, Dedicated HSM stellt SafeNet Network HSM 7-Appliances bereit, die FIPS 140-2 Level 3 validierte HSMs verwenden. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>F: Welche kryptografischen Schlüssel und Algorithmen werden von Dedicated HSM unterstützt?

Der Dedicated HSM-Dienst stellt SafeNet Network HSM 7-Appliances bereit. Sie unterstützen eine Vielzahl von kryptografischen Schlüsseltypen und Algorithmen, einschließlich: Vollständige Suite B-Unterstützung

* Asymmetrisch:
  * RSA
  * DSA
  * Diffie-Hellman
  * Elliptische-Kurven-
  * Kryptografie (ECDSA, ECDH, Ed25519, ECIES) mit benannten, benutzerdefinierten und Brainpool-Kurven, KCDSA
* Symmetrisch:
  * AES-GCM
  * Triple DES
  * DES
  * ARIA, SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Schlüsselableitung: SP800-108 Counter Mode
  * Key-Wrapping: SP800-38F
  * Zufallszahlengenerierung: FIPS 140-2 genehmigter DRBG (SP 800-90, CTR-Modus) unter Einhaltung von BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>F: Ist Dedicated HSM FIPS 140-2 Level 3 validiert?

Ja. Der Dedicated HSM-Dienst stellt SafeNet Network HSM 7-Appliances bereit, die FIPS 140-2 Level 3 validierte HSMs verwenden.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>F: Wie kann ich sicherstellen, dass ich Dedicated HSM im nach FIPS 140-2 Level 3 validierten Modus betreibe?

Der Dedicated HSM-Dienst stellt SafeNet Luna Network HSM 7-Appliances bereit. Diese Appliances verwenden FIPS 140-2 Level 3 validierte HSMs. Die standardmäßig bereitgestellte Konfiguration, das Betriebssystem und die Firmware sind ebenfalls FIPS-geprüft. Sie müssen keine Maßnahmen zur Einhaltung von FIPS 140-2 Level 3 ergreifen.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>F: Wie stellt ein Kunde sicher, dass beim Aufheben der Bereitstellung eines HSM alle Schlüsselmaterialien vernichtet werden?

Bevor ein Kunde die Aufhebung der Bereitstellung anfordert, muss er das HSM mit den von Gemalto bereitgestellten HSM-Clienttools auf Null zurückgesetzt haben.

## <a name="performance-and-scale"></a>Leistung und Skalierbarkeit

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>F: Wie viele kryptografische Vorgänge werden mit Dedicated HSM pro Sekunde unterstützt?

Dedicated HSM stellt SafeNet Network HSM 7-Appliances (Modell A790) bereit. Dies ist eine Zusammenfassung der maximalen Leistung für einige Vorgänge: 

* RSA-2048: 10.000 Transaktionen pro Sekunde
* ECC P256: 20.000 Transaktionen pro Sekunde
* AES-GCM: 17.000 Transaktionen pro Sekunde

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>F: Wie viele Partitionen können in Dedicated HSM erstellt werden?

Das verwendete SafeNet Luna HSM 7-Modell A790 umfasst im Rahmen der Kosten für den Dienst eine Lizenz für zehn Partitionen. Für das Gerät gilt ein Limit von 100 Partitionen. Wenn Partitionen bis zu diesem Limit hinzugefügt werden, fallen zusätzliche Lizenzkosten an, und auf dem Gerät muss eine neue Lizenzdatei installiert werden.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>F: Wie viele Schlüssel können in Dedicated HSM unterstützt werden?

Die maximale Anzahl von Schlüsseln ist eine Funktion des verfügbaren Speichers. Das verwendete SafeNet Luna 7-Modell A790 verfügt über 32 MB Arbeitsspeicher. Die folgenden Angaben gelten auch für Schlüsselpaare, wenn asymmetrische Schlüssel verwendet werden.

* RSA-2048: 19.000
* ECC-P256: 91.000

Die Kapazität ist abhängig von den spezifischen Schlüsselattributen, die in der Schlüsselgenerierungsvorlage festgelegt sind, und der Anzahl der Partitionen unterschiedlich.
