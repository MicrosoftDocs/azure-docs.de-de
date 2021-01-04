---
title: Bewährte Methoden für Datensicherheit und Datenverschlüsselung – Microsoft Azure
description: Dieser Artikel bietet eine Reihe von empfohlenen Vorgehensweisen für die Datensicherheit und Verschlüsselung unter Verwendung der integrierten Azure-Funktionen.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: c88a2cf33cf1149a17af4a82dcf2858822f0c6d4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696148"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Bewährte Methoden für Datensicherheit und Datenverschlüsselung in Azure
In diesem Artikel werden bewährte Methoden für die Datensicherheit und -verschlüsselung beschrieben.

Die bewährten Methoden basieren auf einer gemeinsamen Linie und eignen sich für aktuelle Funktionen und Features der Azure-Plattform. Meinungen und Technologien ändern sich im Laufe der Zeit. Dieser Artikel wird daher regelmäßig aktualisiert, um diese Änderungen widerzuspiegeln.

## <a name="protect-data"></a>Schützen von Daten
Zum Schutz von Daten in der Cloud müssen Sie die möglichen Zustände berücksichtigen, in denen Ihre Daten auftreten können. Außerdem sollten Sie die Steuerungsmöglichkeiten beachten, die für diesen Zustand verfügbar sind. Bewährte Methoden für Datensicherheit und Verschlüsselung in Azure beziehen sich auf die folgenden Zustände von Daten:

- Ruhende Daten: Dies umfasst alle Informationsspeicherobjekte, Container und Typen, die statisch auf physischen Medien existieren – ob auf Magnetspeichern oder optischen Datenträgern.
- Daten während der Übertragung: Wenn Daten zwischen Komponenten, Speicherorten oder Programmen übertragen werden, weisen sie den Zustand „während der Übertragung“ auf. Beispiele sind die Übertragung über das Netzwerk, über einen Service Bus (von dem lokalen Computer in die Cloud und umgekehrt, inklusive Hybridverbindungen wie ExpressRoute) oder während eines Eingabe-/Ausgabevorgangs.

## <a name="choose-a-key-management-solution"></a>Auswählen einer Schlüsselverwaltungslösung

Das Schützen Ihrer Schlüssel ist ausschlaggebend für den Schutz Ihrer Daten in der Cloud.

[Azure Key Vault](../../key-vault/general/overview.md) unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Der Schlüsseltresor optimiert die Schlüsselverwaltung und ermöglicht es Ihnen, die Kontrolle über Schlüssel zu behalten, die für den Datenzugriff und die Verschlüsselung Ihrer Daten verwendet werden. Entwickler können Schlüssel für Tests und Entwicklung innerhalb von Minuten erstellen und diese später in Schlüssel für die Produktion migrieren. Sicherheitsadministratoren können nach Bedarf Berechtigungen für Schlüssel erteilen (und widerrufen).

Mit Key Vault können Sie mehrere sichere Container (sogenannte Tresore) erstellen. Diese Tresore werden von HSMs unterstützt. Tresore zentralisieren die Speicherung von Anwendungsgeheimnissen und verringern so die Gefahr, dass Sicherheitsinformationen abhandenkommen. Darüber hinaus steuern und protokollieren Schlüsseltresore auch den Zugriff auf alle darin gespeicherten Daten. Azure Key Vault kann Anforderungen und Verlängerungen von TLS-Zertifikaten (Transport Layer Security) abwickeln. Es stellt Features für eine zuverlässige Zertifikatlebenszyklus-Verwaltungslösung bereit.

Azure Key Vault ist für die Unterstützung von Anwendungsschlüsseln und -geheimnissen konzipiert. Key Vault ist jedoch nicht als Speicher für Benutzerkennwörter vorgesehen.

Nachfolgend sind bewährte Methoden für die Sicherheit bei Verwendung von Key Vault aufgeführt.

**Bewährte Methode**: Gewähren Sie Benutzern, Gruppen und Anwendungen Zugriff in einem bestimmten Umfang.   
**Detail**: Verwenden Sie vordefinierte Azure RBAC-Rollen. Wenn Sie beispielsweise einem Benutzer Zugriff für die Verwaltung von Key Vault-Instanzen gewähren möchten, weisen Sie ihm für einen bestimmten Bereich die vordefinierte Rolle [Key Vault-Mitwirkender](../../role-based-access-control/built-in-roles.md) zu. Der Bereich wäre in diesem Fall ein Abonnement, eine Ressourcengruppe oder einfach eine bestimmte Key Vault-Instanz. Wenn die vordefinierten Rollen nicht Ihren Anforderungen entsprechen, können Sie [eigene Rollen definieren](../../role-based-access-control/custom-roles.md).

**Bewährte Methode**: Steuern Sie, worauf Benutzer Zugriff haben.   
**Detail**: Der Zugriff auf eine Key Vault-Instanz wird über zwei separate Schnittstellen gesteuert: die Verwaltungsebene und die Datenebene. Die Zugriffsteuerungen für die Verwaltungs- und die Datenebene sind voneinander unabhängig.

Verwenden Sie Azure RBAC, um zu steuern, worauf Benutzer Zugriff haben. Wenn Sie also beispielsweise einer Anwendung die Verwendung von Schlüsseln in einer Key Vault-Instanz ermöglichen möchten, müssen Sie ihr lediglich mithilfe von Key Vault-Zugriffsrichtlinien Zugriffsberechtigungen für die Datenebene gewähren, da die Anwendung keinen Zugriff auf die Verwaltungsebene benötigt. Umgekehrt gilt: Wenn ein Benutzer Lesezugriff auf Tresoreigenschaften und Tags, aber keinen Zugriff auf Schlüssel, Geheimnisse oder Zertifikate haben soll, können Sie ihm mithilfe von Azure RBAC Lesezugriff gewähren, da in diesem Fall kein Datenebenenzugriff erforderlich ist.

**Bewährte Methode**: Speichern Sie Zertifikate in Ihrem Schlüsseltresor. Ihre Zertifikate sind sehr wertvoll. Wenn sie in die falschen Hände gelangen, kann die Sicherheit Ihrer Anwendung oder die Sicherheit Ihrer Daten gefährdet sein.   
**Detail**: Azure Resource Manager kann virtuellen Azure-Computern bei deren Bereitstellung Zertifikate, die in Azure Key Vault gespeichert sind, sicher bereitstellen. Durch Festlegung entsprechender Zugriffsrichtlinien für den Schlüsseltresor können Sie auch steuern, wer Zugriff auf das Zertifikat erhält. Ein weiterer Vorteil ist, dass Sie alle Ihre Zertifikate an einer zentralen Stelle in Azure Key Vault verwalten. Weitere Informationen finden Sie unter [Bereitstellen von Zertifikaten für VMs über einen vom Kunden verwalteten Schlüsseltresor](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).

**Bewährte Methode**: Stellen Sie sicher, dass gelöschte Schlüsseltresore oder Schlüsseltresorobjekte wiederhergestellt werden können.   
**Detail**: Das Löschen von Schlüsseltresoren oder Schlüsseltresorobjekten kann unbeabsichtigt oder böswillig geschehen. Aktivieren Sie die Funktionen für vorläufiges Löschen und Bereinigungsschutz von Key Vault, insbesondere für Schlüssel, die zum Verschlüsseln ruhender Daten verwendet werden. Das Löschen dieser Schlüssel ist gleichbedeutend mit einem Datenverlust, sodass Sie bei Bedarf in der Lage sein müssen, gelöschte Tresore und Tresorobjekte wiederherzustellen. Üben Sie regelmäßig Wiederherstellungsvorgänge in Key Vault.

> [!NOTE]
> Falls ein Benutzer über Mitwirkungsberechtigungen (Azure RBAC) für eine Schlüsseltresor-Verwaltungsebene verfügt, kann er sich durch Festlegen einer Schlüsseltresor-Zugriffsrichtlinie selbst Zugriff auf die Datenebene gewähren. Es wird empfohlen, sehr genau darauf zu achten, wer als Mitwirkender Zugriff auf Ihre Key Vault-Instanzen hat, um sicherzustellen, dass nur autorisierte Benutzer auf Ihre Key Vault-Instanzen, Schlüssel, Geheimnisse und Zertifikate zugreifen und diese verwalten können.
>
>

## <a name="manage-with-secure-workstations"></a>Verwalten mit sicheren Arbeitsstationen

> [!NOTE]
> Der Abonnementadministrator oder -besitzer sollte eine Arbeitsstation mit sicherem Zugriff oder eine Arbeitsstation mit privilegiertem Zugriff verwenden.
>
>

Da die große Mehrzahl der Angriffe auf den Endbenutzer zielt, ist der Endpunkt einer der Hauptangriffspunkte. Ein Angreifer, der den Endpunkt zum Ziel hat, kann die Anmeldeinformationen des Benutzers verwenden, um Zugriff auf die Daten der Organisation zu erhalten. Die meisten Angriffe auf Endpunkte können sich zunutze machen, dass Benutzer Administratoren ihrer lokalen Arbeitsstationen sind.

**Bewährte Methode**: Verwenden Sie eine sichere Verwaltungsarbeitsstation, um sensible Konten, Aufgaben und Daten zu schützen.   
**Detail**: Verwenden Sie eine [Arbeitsstation mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations), um die Angriffsfläche bei Arbeitsstationen zu reduzieren. Diese sicheren Verwaltungsarbeitsstationen helfen Ihnen, einige dieser Angriffe zu minimieren und damit Ihre Daten sicherer zu machen.

**Bewährte Methode**: Sorgen Sie für Endpunktsicherheit.   
**Detail**: Erzwingen Sie die Einhaltung der Sicherheitsrichtlinien auf allen Geräten, die die Daten verwenden, unabhängig vom Speicherort der Daten (in der Cloud oder lokal).

## <a name="protect-data-at-rest"></a>Schutz gespeicherter Daten

Die [Verschlüsselung ruhender Daten](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) ist eine obligatorische Maßnahme für Datenschutz, Compliance und Datenhoheit.

**Bewährte Methode**: Wenden Sie Datenträgerverschlüsselung zum Schutz Ihrer Daten an.   
**Detail**: Verwenden Sie [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md). Damit können IT-Administratoren die Datenträger virtueller IaaS-Computer unter Windows und Linux verschlüsseln. Disk Encryption kombiniert die Branchenstandardfeatures BitLocker (Windows) und dm-crypt (Linux), um Volumeverschlüsselung für das Betriebssystem und die Datenträger bereitzustellen.

In Azure Storage und Azure SQL-Datenbank werden ruhende Daten standardmäßig verschlüsselt, und viele Dienste bieten eine Verschlüsselung als Option an. Mithilfe von Azure Key Vault können Sie die Kontrolle über Schlüssel für den Zugriff und das Verschlüsseln Ihrer Daten behalten. Weitere Informationen finden Sie unter [Unterstützung für Verschlüsselungsmodelle von Azure-Ressourcenanbietern](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Bewährte Methoden:** Verwenden Sie die Verschlüsselung, um das Risiko unberechtigter Datenzugriffe zu reduzieren.   
**Detail**: Verschlüsseln Sie Ihre Laufwerke, bevor diese mit sensiblen Daten beschrieben werden.

Organisationen, die keine Datenverschlüsselung erzwingen, sind anfälliger für Datenintegritätsprobleme. So können beispielsweise nicht autorisierte Benutzer Daten aus kompromittierten Konten entwenden oder sich unberechtigt Zugang zu unverschlüsselten Daten verschaffen. Unternehmen müssen außerdem nachweisen, dass sie gewissenhaft sind und die richtigen Sicherheitsprotokolle zur Verbesserung ihrer Datensicherheit verwenden, um den Branchenbestimmungen zu entsprechen.

## <a name="protect-data-in-transit"></a>Schützen der Daten während der Übertragung

Der Schutz von Daten während der Übertragung sollte ein wesentlicher Bestandteil Ihrer Datenschutzstrategie sein. Da die Daten zwischen vielen verschiedenen Speicherorten übertragen werden, wird generell empfohlen, immer SSL/TLS-Protokolle zu verwenden, um Daten zwischen verschiedenen Speicherorten auszutauschen. In einigen Fällen sollten Sie den gesamten Kommunikationskanal zwischen Ihrer lokalen und Ihrer Cloudinfrastruktur isolieren, indem Sie ein VPN verwenden.

Für Daten, die sich zwischen Ihrer lokalen Infrastruktur und Azure bewegen, sollten Sie passende Sicherheitsmaßnahmen in Betracht ziehen, beispielsweise HTTPS oder VPN. Beim Senden von verschlüsseltem Datenverkehr zwischen einem virtuellen Azure-Netzwerk und einem lokalen Standort über das öffentliche Internet verwenden Sie [Azure VPN Gateway](../../vpn-gateway/index.yml).

Nachfolgend sind bewährte Methoden für die Sicherheit bei Verwendung von Azure VPN Gateway, SSL/TLS und HTTPS aufgeführt.

**Bewährte Methode**: Sichern Sie den Zugriff von mehreren lokalen Arbeitsstationen auf ein virtuelles Azure-Netzwerk.   
**Detail**: Verwenden Sie [Site-to-Site-VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Bewährte Methode**: Sichern Sie den Zugriff von einer einzelnen lokalen Arbeitsstation auf ein virtuelles Azure-Netzwerk.   
**Detail**: Verwenden Sie [Point-to-Site-VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

**Bewährte Methode**: Verschieben Sie größere Datasets über eine dedizierte Hochgeschwindigkeits-WAN-Verbindung.   
**Detail**: Verwenden Sie [ExpressRoute](../../expressroute/expressroute-introduction.md). Falls Sie sich für die Verwendung von ExpressRoute entscheiden, können Sie die Daten auch auf Anwendungsebene verschlüsseln, indem Sie SSL/TLS oder andere Protokolle für zusätzlichen Schutz verwenden.

**Bewährte Methode**: Führen Sie die Interaktion mit Azure Storage über das Azure-Portal aus.   
**Detail**: Alle Transaktionen erfolgen über HTTPS. Für die Interaktion mit [Azure Storage](https://azure.microsoft.com/services/storage/) können Sie auch die [Storage-REST-API](/rest/api/storageservices/) über HTTPS verwenden.

Organisationen, die die Daten während der Übertragung nicht schützen, sind anfälliger für [Man-in-the-Middle-Angriffe](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)), [Abhöraktionen](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14)) und Session Hijacking. Bei diesen Angriffen kann es sich um den ersten Schritt zur Zugriffsgewinnung auf vertrauliche Daten handeln.

## <a name="secure-email-documents-and-sensitive-data"></a>Schützen von E-Mails, Dokumenten und vertraulichen Daten

Sie möchten E-Mails, Dokumente und vertrauliche Daten, die Sie außerhalb Ihres Unternehmens freigeben, kontrollieren und schützen. [Azure Information Protection](/azure/information-protection/) ist eine cloudbasierte Lösung, mit der eine Organisation ihre eigenen Dokumente und E-Mails klassifizieren, bezeichnen und schützen kann. Dies kann automatisch von Administratoren durchgeführt werden, die auch die Regeln und Bedingungen definieren: entweder manuell nach Benutzer oder eine Kombination, bei der Benutzer Empfehlungen erhalten.

Die Klassifizierung ist jederzeit identifizierbar, unabhängig davon, wo die Daten gespeichert oder mit wem sie geteilt werden. Die Bezeichnungen umfassen visuelle Markierungen wie Kopfzeilen, Fußzeilen oder Wasserzeichen. Metadaten werden Dateien und E-Mail-Headern als Klartext hinzugefügt. Der Klartext stellt sicher, dass andere Dienste, z.B. Lösungen zum Verhindern von Datenverlust, die Klassifizierung identifizieren und entsprechende Maßnahmen ergreifen können.

Die Schutztechnologie nutzt Azure Rights Management (Azure RMS). Diese Technologie ist in andere Microsoft-Clouddienste und Anwendungen integriert, z. B. Microsoft 365 und Azure Active Directory. Diese Schutztechnologie verwendet Verschlüsselungs-, Identitäts- und Autorisierungsrichtlinien. Der Schutz, der durch Azure RMS angewendet wird, verbleibt innerhalb der Dokumente und E-Mails. Der Speicherort spielt dabei keine Rolle: innerhalb oder außerhalb Ihrer Organisation, Netzwerke, Dateiserver oder Anwendungen.

Mit dieser Lösung für den Schutz von Informationen behalten Sie die Kontrolle über Ihre Daten auch dann, wenn diese für andere Personen freigegeben werden. Sie können Azure RMS auch für Ihre eigenen Branchenanwendungen und Informationsschutzlösungen von Softwareanbietern verwenden, ganz gleich ob diese Anwendungen und Lösungen lokal oder in der Cloud bereitstehen.

Wir empfehlen Folgendes:

- [Stellen Sie Azure Information Protection](/azure/information-protection/deployment-roadmap) für Ihre Organisation bereit.
- Wenden Sie Bezeichnungen entsprechend Ihrer Geschäftsanforderungen an. Beispiel: Verwenden Sie die Bezeichnung „streng vertraulich“ für alle Dokumente und E-Mails, die streng vertrauliche Daten enthalten, um diese Daten zu klassifizieren und zu schützen. Dann können nur autorisierte Benutzer mit den von Ihnen angegebenen Einschränkungen auf diese Daten zugreifen.
- Konfigurieren Sie die [Verwendungsprotokollierung für Azure RMS](/azure/information-protection/log-analyze-usage), damit Sie überwachen können, wie Ihre Organisation den Schutzdienst verwendet.

Organisationen, die in Hinsicht auf die [Datenklassifizierung](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) und den Dateischutz Schwächen haben, sind möglicherweise anfälliger für Datenlecks oder Datenmissbrauch. Mit geeignetem Dateischutz können Sie Datenflüsse analysieren, um Einblicke in Ihre Geschäftsabläufe zu erhalten, riskante Verhalten zu erkennen und Abhilfemaßnahmen zu ergreifen, den Zugriff auf Dokumente nachzuverfolgen usw.

## <a name="next-steps"></a>Nächste Schritte

Weitere bewährte Methoden für die Sicherheit, die Sie beim Entwerfen, Bereitstellen und Verwalten Ihrer Cloudlösungen mithilfe von Azure verwenden können, finden Sie unter [Sicherheit in Azure: bewährte Methoden und Muster](best-practices-and-patterns.md).

Die folgenden Ressourcen enthalten allgemeinere Informationen zur Sicherheit in Azure und verwandten Microsoft-Diensten:
* [Blog des Azure-Sicherheitsteams](/archive/blogs/azuresecurity/): Hier finden Sie Informationen über den aktuellen Stand der Azure-Sicherheit.
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): Hier können Sie Microsoft-Sicherheitsrisiken, z.B. Probleme mit Azure, melden oder eine E-Mail an secure@microsoft.com schreiben.