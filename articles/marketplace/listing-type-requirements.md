---
title: Anforderungen nach Auflistungsart | Azure
description: Dieser Artikel beschreibt die Eignungskriterien und Veröffentlichungsbedingungen für Partner, die sich über die Veröffentlichung von Apps im Azure Marketplace informieren möchten.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: e45346ce8bd23ea8431faf8a123d57ecdee71606
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937889"
---
# <a name="requirements-by-listing-type"></a>Listingtyp-Anforderungen  
Die technischen und inhaltlichen (Marketing) Anforderungen variieren je nach Storefront, Angebotstyp und Auflistungsart. Die folgenden Spezifikationen müssen Ihre Konformität erfüllen:  
1. Storefront-Anforderungen:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Anforderungen an die Auflistungsart und den Angebotstyp:  
    *   Weitere Informationen zu Auflistungsarten und Angebotstypen finden Sie auf der Seite „Bestimmung der Auflistungsart für Ihre Lösung“ unter [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Storefront-Anforderungen: AppSource  
In der folgenden Tabelle sind die Voraussetzungen für das Veröffentlichen auf AppSource angegeben.  

| Anforderung | Details | Erforderlich oder empfohlen |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Ihre App muss einmalige Azure Active Directory-Verbundanmeldung (Azure Active Directory federated SSO, Azure Active Directory federated Single Sign-On) mit aktivierter Zustimmung zulassen.<ul> <li>Weitere Informationen zum Aktivieren der einmaligen Azure AD-Verbundanmeldung erhalten Sie auf der Seite „Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind“ unter [docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Erforderlich |   
| ***Integration in Microsoft Cloud-Dienste*** | Ihre App sollte auch in andere Microsoft Cloud-Dienste wie z.B. Microsoft Power BI, Cortana Intelligence oder in Microsoft Azure-Dienste integriert sein.<ul> <li>Ein Beispiel für einen Microsoft Cloud-Dienst ist Internet der Dinge (IoT).</li> </ul> | Empfohlen |  
| ***Zielgruppe*** | Ihre App muss für Line-of-Business-Benutzer und Geschäftsinhaber gelten. | Erforderlich | 
| ***SaaS-App (Software-as-a-Service) für Unternehmen*** | Ihre App muss die folgenden Anforderungen erfüllen:<ul> <li>eine Line-of-Business-SaaS-App sein</li> <li>den Fokus auf Geschäftsprozesse legen</li> <li>an Geschäftskunden gerichtet sein</li> <li>Benutzern erlauben, ihre Unternehmensanmeldeinformationen für die Anmeldung (Benutzername und Kennwort) zu verwenden</li> </ul> | Erforderlich |  
| ***Kostenloser Testzeitraum und Testversion*** | Ihre App muss eine der folgenden Optionen enthalten, damit ein Kunde Ihre App kostenlos für einen begrenzten Zeitraum verwenden kann.<ul> <li>Stellen Sie eine `try`-Methode bereit, sodass Kunden eine Testversion Ihrer App in AppSource starten können.</li> <li>Stellen Sie eine `request trial`-Option in AppSource bereit, damit Kunden eine Testversion Ihrer App anfordern können.</li> </ul>Die kostenlose Testversion, die Sie bereitstellen, muss dem Kunden eine vorab festgelegte Zeitspanne bieten, in der er Ihre App ohne zusätzliche Kosten ausprobieren kann. | Erforderlich |  
| ***Einfach konfigurierbare und einsatzbereite Lösung*** | Ihre App muss sich einfach und schnell konfigurieren und einrichten lassen (keine Anpassung erforderlich). | Erforderlich |  
| ***Leadverwaltung*** | Aktivieren Sie Ihre CRM-Instanz für den Empfang von Leaddaten, bevor Sie Leads von der Storefront erhalten.<ul> <li>CRM-Beispiele sind Marketo, Microsoft Dynamics oder Salesforce.</li> </ul> | Erforderlich |  
| ***Datenschutz- und Nutzungsrichtlinien*** | Ihre App muss einen Link zu Ihrer Seite mit den Datenschutzrichtlinien (über eine öffentliche URL) bereitstellen. Die Nutzungsbedingungen müssen während der Veröffentlichung als Text bereitgestellt werden. | Erforderlich |  
| ***Unterstützung*** | Ihre App muss einen Link zu Ihrer Kundendienstseite (über eine öffentliche URL) bereitstellen. Wenn es sich bei Ihrer App um eine Testversion handelt, müssen Sie die Unterstützen ohne zusätzliche Kosten während des Testzeitraums bereitstellen. | Erforderlich |  

## <a name="storefront-requirements-azure-marketplace"></a>Storefront-Anforderungen: Azure Marketplace  
Unten sind die Voraussetzungen für die Auflistung von Typen in Azure Marketplace angegeben.  

| Anforderung | Details | Auflistungsart |  
|:--- |:--- |:--- |  
| ***Teilnahmerichtlinien*** | Ihre App muss die Teilnahmerichtlinien von Azure Marketplace befolgen.<ul> <li>Weitere Informationen zu den Teilnahmerichtlinien finden Sie unter den Teilnahmerichtlinien für Microsoft Azure Marketplace unter [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />Transaktion<br />Testversion |  
| ***Integration in Microsoft*** | Ihr Angebot sollte Microsoft Azure-Diensttypen, z.B. Compute, Netzwerk oder Speicher, nutzen bzw. erweitern. Ihr Angebot sollte an einer vorhandenen Azure Marketplace-Kategorie ausgerichtet sein, z.B. Datenbanken, Sicherheit oder Netzwerk.<ul> <li>Weitere Informationen zu den Marketplace-Angeboten finden Sie auf der Marketplace-Apps-Seite unter [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />Transaktion<br />Testversion |  
| ***Zielgruppe*** | Ihr Angebot muss sich an IT-Experten, Cloudentwickler oder andere technische Kundenrollen richten. | list<br />Transaktion<br />Testversion |  
| ***Leadverwaltung*** | Bevor Sie Leads von der Storefront erhalten, müssen Sie Ihr CRM (Marketo, Microsoft Dynamics oder Salesforce) für den Empfang von Leaddaten aktivieren. | list<br />Transaktion<br />Testversion |  
| ***Datenschutz- und Nutzungsrichtlinien*** | Ihre App muss einen Link zu Ihrer Seite mit den Datenschutzrichtlinien (über eine öffentliche URL) bereitstellen. Die Nutzungsbedingungen müssen während der Veröffentlichung als Text bereitgestellt werden. | list<br />Transaktion<br />Testversion |  
| ***Unterstützung*** | Ihr Angebot muss einen Link zu Ihrer Kundendienstseite (über eine öffentliche URL) enthalten. Wenn es sich bei Ihrem Angebot um eine Testversion handelt, müssen Sie die Unterstützen ohne zusätzliche Kosten während des Testzeitraums bereitstellen. | Transaktion<br />Testversion |    

## <a name="non-transact-listings"></a>Nicht-Transaktionsauflistungen  
Dieser Abschnitt beschreibt alle Angebotstypen, die die Auflistungsart „Transaktion“ nicht verwenden. 

### <a name="list"></a>Auflisten  
Der Auflistungsart „Liste“ enthält die folgenden Angebotstypen für die Storefronts im Marketplace.  

| Angebotstyp | Storefront | Details |  
|:---        |:---        |:---     |  
| Beratungsdienste | AppSource | Anforderungen: AppSource: Auflisten: Beratungsdienste |  
| Beratungsdienste | Azure Marketplace | Anforderungen: Azure Marketplace: Auflisten: Beratungsdienste |  
| Kontaktformular | AppSource | [](#) |  
| Kontaktformular | Azure Marketplace | Anforderungen: AppSource: Auflisten: Kontaktformular |  

#### <a name="requirements-appsource-list-consulting-service"></a>Anforderungen: AppSource: Auflisten: Beratungsdienst  

| Requirements (Anforderungen) | Details |  
|:--- |:--- |  
| Merkmale des Dienstangebots | Ihr Beratungsdienst muss die folgenden Kriterien erfüllen:<ul> <li>Angebot mit festem Umfang, fester Dauer und festem Preis (oder kostenlos)</li> <li>Ausrichtung hauptsächlich auf Vertriebsvorbereitungen</li> <li>Beschränkung auf einen einzelnen Kunden</li> <li>Vor Ort durchgeführt</li> </ul> |  
| Partneranforderungen für Beratungsdienste | Sie erfüllen die Kriterien im relevanten Bereich für den Dienst.<table><tr><th>Lösungsbereich</th><th>Kriterien</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Cloud Customer Relationship Management-Kompetenz Silber oder Gold</td></tr><tr><td>Dynamics 365 for Finance and Operations, Enterprise Edition</td><td>Enterprise Resource Planning-Kompetenz Silber oder Gold und ein Umsatz Ihrer Cloudvorgänge von 25.000 USD oder mehr in den letzten 12 Monaten.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Business Edition</td><td>Sie dienen als Cloud-Dienstanbieter (CSP) oder Digital Partner of Record (digitaler Partner des Eintrags des Abonnements, DPOR) für mindestens einen Kunden.</td></tr><tr><td>Power BI</td><td>Erfüllen der Kriterien für Lösungspartner.</td></tr><tr><td>PowerApps</td><td>Vorhandensein einer Partnershowcaselösung.</td></tr></table><ul> <li>Weitere Informationen zu Verwaltung von Kundenbeziehungen finden Sie auf der Cloud Customer Relationship Management-Seite unter [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Weitere Informationen zum Planen von Ressourcen finden Sie auf der Enterprise Resource Planning-Seite unter [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Weitere Informationen zu CSP finden Sie auf der Cloud-Dienstanbieter-Seite unter [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Weitere Informationen zu DPOR finden Sie auf der Seite „Digital Partner of Record and Zusammenarbeit mit dem Partner“ unter [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Weitere Informationen zu den Kriterien für Lösungspartner finden Sie im Dokument „Solution Partner Overview and Incentives“ (Übersicht über Lösungspartner und Incentives) unter [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Weitere Informationen zur Vorstellung der Partner finden Sie auf der Seite „Vorstellung unserer Partner“ unter [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Anforderungen: Azure Marketplace: Auflisten: Beratungsdienst  

| Requirements (Anforderungen) | Details |  
|:--- |:--- |  
| Merkmale des Dienstangebots | Ihr Beratungsdienst muss die folgenden Kriterien erfüllen:<ul> <li>Angebot mit festem Umfang, fester Dauer und festem Preis (oder kostenlos)</li> <li>Ausrichtung hauptsächlich auf Vertriebsvorbereitungen</li> <li>Beschränkung auf einen einzelnen Kunden</li> <li>Vor Ort durchgeführt</li> </ul> |  
| Partneranforderungen für Beratungsdienste | Sie müssen den Silber- oder Gold-Standard in einer der folgenden Kompetenzen im relevanten Bereich für Ihren Dienst besitzen: <table><tr><th>Lösungsbereich</th><th>Kompetenz</th></tr><td>Cloudplattform und -infrastruktur</td><td>Cloudplattform<br />Rechenzentrum</td><tr><td>Anwendungsentwicklung und ISV</td><td>Anwendungsentwicklung<br />Anwendungsintegration<br />DevOps</td></tr><tr><td>Datenverwaltung und -analyse</td><td>Datenanalysen<br />Datenplattform</td></tr></table><ul> <li>Weitere Informationen zu den Kompetenzen finden Sie auf der Seite „Kompetenzpartner“ unter [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Weitere Informationen zur Auflistung finden Sie auf der Seite „Azure Marketplace-Beratungsdienste“ unter [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Testversion  

| Angebotstyp | Storefront | Details |  
|:---        |:---        |:---     |  
| Kostenlose/SaaS-Testversion | AppSource | Anforderungen an die Auflistungsart: Testversion |  
| Kostenlose/SaaS-Testversion | Azure Marketplace | Anforderungen: Azure Marketplace: Testversion: Kostenlose Testversion/SaaS-Testversion |  
| Interaktive Demo | AppSource | Anforderungen an die Auflistungsart: Testversion |  
| Interaktive Demo | Azure Marketplace | [Anforderungen: Azure Marketplace: Testversion: Interaktive Demo](#requirements-azure-marketplace-trial-interactive-demo) |  
| Testversion | AppSource | Anforderungen an die Auflistungsart: Testversion |  
| Testversion | Azure Marketplace | [Anforderungen: Azure Marketplace: Testversion: Testversion](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Anforderungen: Azure Marketplace: Testversion  

| Anforderung | Details |  
|:--- |:--- |  
| Kostenloser Testzeitraum und Testversion | Ein Kunde kann Ihre App für begrenzte Zeit kostenlos verwenden.<br /><br />Ihr Kunde muss keine Lizenz- oder Abonnementgebühren für Ihr Angebot oder Ihre App bezahlen. Ihr Kunde muss nicht für das zugrunde liegende Erstanbieterprodukt bzw. den zugrunde liegenden Erstanbieterdienst von Microsoft bezahlen. Alle Testoptionen werden in Ihrem Azure-Abonnement bereitgestellt. Sie haben alleinige Kontrolle über die Testversion der Kostenoptimierung und -verwaltung.<br /><br />Sie können eine kostenlosen Testversion, eine interaktive Demo oder eine Testversion auswählen. Unabhängig von Ihrer Wahl muss Ihre kostenlose Testversion dem Kunden einen vorab festgelegten Zeitraum lassen, die App ohne zusätzliche Kosten zu testen.<ul> <li>Beginnen Sie mit dem Prozess zur Erstellung einer Testversion, indem Sie eine E-Mail an [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com) senden.</li> </ul>Hinweis: Azure Marketplace-SaaS-Testversionen müssen es Kunden ermöglichen, sich mit geschäftlichen Anmeldeinformationen anzumelden.<ul> <li>Weitere Informationen finden Sie im Abschnitt „AppSource – Testbenutzeroberflächen“ unter [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Einfach konfigurierbare und einsatzbereite Lösungen | Ihre App muss sich einfach und schnell konfigurieren und einrichten lassen. |  
| Verfügbarkeit/Betriebszeit | Ihre SaaS-App oder -Plattform benötigt eine Betriebszeit von mindestens 99,9 %. |  
| Azure Active Directory | Ihr Angebot muss die einmalige Azure Active Directory-Verbundanmeldung (SSO) mit aktivierter Zustimmung zulassen. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Anforderungen: Azure Marketplace: Testversion: Kostenlose Testversion/SaaS-Testversion  

| Vorteil | Anforderung |  
|:--- |:--- |  
| Ermöglicht es einem Kunden, Ihr Produkt vor dem Kauf auszuprobieren, und später kann automatisch die Umstellung auf die kostenpflichtige Nutzung durchgeführt werden. Außerdem können Kunden Proofs of Concept erstellen und sich mit Microsoft-Vertriebsteams vernetzen. | Ihre Lösung ist ein virtueller Computer oder eine Lösungsvorlage.<br /><br />Ihre Lösung ist ein SaaS-Angebot, und Sie bieten ein mehrinstanzenfähiges SaaS-Produkt an.<br /><br />Sie verfügen über eine Umgebung für die erstmalige Ausführung, um Kunden den Einstieg zu erleichtern.<br /><br />Sie verfügen nur über einen Mandanten, aber Sie fügen Kunden als Gastbenutzer hinzu. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Anforderungen: Azure Marketplace: Testversion: Interaktive Demo  

| Vorteil | Anforderung |  
|:--- |:--- |  
| Ermöglicht es Ihren Kunden, Ihre Lösung ohne die Komplexität des Setups in Aktion zu sehen. | Ihre Lösung erfordert komplexe Einstellungen, die in der Testphase schwierig durchzuführen sind. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Anforderungen: Azure Marketplace: Testversion: Testversion  

| Vorteil | Anforderung |  
|:--- |:--- |  
| Ermöglicht es einem Kunden, Ihr Produkt vor dem Kauf auszuprobieren.<br /><br />Stellt eine Anleitungsoberfläche Ihrer Lösung mit vorkonfigurierten Einstellungen bereit.<br /><br />Unten sind zusätzliche Vorteile bei der Verwendung einer Testversion aufgelistet.<ul> <li>27% der Benutzersuchen im Marketplace werden von Benutzern so definiert, dass nur Angebote mit Testversionen angezeigt werden.</li> <li>Aus Angeboten mit Testversion resultieren 38% mehr Leads als aus Angeboten ohne Testversion.</li> <li>36% der neu gewonnenen Kunden im Marketplace haben eine Testversion genutzt.</li> <li>Mit Testversionen können Microsoft-Verkäufer Ihr Produkt besser verstehen und im Co-Selling verwenden.</li> </ul> | Ihre Lösung ist ein virtueller Computer, eine Lösungsvorlage oder eine SaaS-App mit nur einem Mandanten, oder die Bereitstellung ist kompliziert. <br /><br />Sie verfügen nicht über eine Methode, mit der Ihre Testversion in ein kostenpflichtiges Angebot umgewandelt werden kann. |  

---

## <a name="transact-specific-listings"></a>Transaktionsspezifische Auflistungen

### <a name="transact"></a>Transaktion  

| Angebotstyp | Storefront | Details |   
|:---        |:---        | :--- |  
| Azure-Apps: Verwaltete App | Azure Marketplace | Anforderungen: Azure Marketplace: Transaktion: Azure-Apps: Verwaltete App |  
| Azure-Apps: Lösungsvorlage | Azure Marketplace | Anforderungen: Azure Marketplace: Transaktion: Azure-Apps: Lösungsvorlage |  
| Container | Azure Marketplace | [Anforderungen: Azure Marketplace: Transaktion: Container](#requirements-azure-marketplace-transact-container) |  
| SaaS-App  | Azure Marketplace | [Anforderungen: Azure Marketplace: Transaktion: SaaS-App](#requirements-azure-marketplace-transact-saas-app) |  
| Virtueller Computer | Azure Marketplace | [Anforderungen: Azure Marketplace: Transaktion: Virtueller Computer](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Anforderungen: Azure Marketplace: Transaktion: Container  

| Anforderung | Details |  
|:--- |:--- |  
| Abrechnung und Messung | Es wird entweder das kostenlose oder BYOL-Abrechnungsmodell unterstützt. |  
| Docker-basiertes Image | Ihr Containerimage muss auf dem Docker-Imageformat basieren und aus Azure-Containerregistrierungen abgerufen werden. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Anforderungen: Azure Marketplace: Transaktion: SaaS-App  

| Anforderung | Details |  
|:--- |:--- |  
| Abrechnung und Messung | Ihr Angebot wird über eine monatliche Flatrate abgerechnet. Derzeit werden nutzungsbasierte Preise und nutzungsbasierte *Anpassungsoptionen* nicht unterstützt. |  
| Abbruch | Ihr Angebot kann jederzeit vom Kunden storniert werden. |  
| Transaktionszielseite | Hostet eine Transaktion-Zielseite mit Azure-Co-Branding. Ihre Zielseite ermöglicht Ihren Kunden, Ihr SaaS-Dienstkonto zu erstellen und zu verwalten. |  
| SaaS-Abonnement-API | Stellen Sie einen Dienst bereit, der mit dem SaaS-Abonnement interagiert, um Benutzerkonten und Servicepläne erstellen, aktualisieren und löschen zu können. Alle wichtigen API-Änderungen müssen innerhalb von 24 Stunden unterstützt werden. Weniger wichtige API-Änderungen werden regelmäßig aktualisiert. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Anforderungen: Azure Marketplace: Transaktion: Virtueller Computer  

| Anforderung | Details |  
|:--- |:--- | 
| Abrechnung und Messung | Ihre VM muss entweder BYOL oder die monatliche nutzungsbasierte Bezahlung unterstützen. |  
| Azure-kompatible virtuelle Festplatte (VHD) | VMs müssen unter Windows oder Linux erstellt werden.<ul> <li>Weitere Informationen zum Erstellen einer Linux-VHD finden Sie unter [Von Azure unterstützte Distributionen von Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Weitere Informationen zum Erstellen einer Windows-VHD finden Sie unter [Erstellen einer Azure-kompatiblen VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Nächste Schritte
*   Besuchen Sie die Seite [Herausgeberleitfaden für Azure Marketplace und AppSource](./marketplace-publishers-guide.md).  

