---
title: Leadverwaltung für Cloud Marketplace | Azure Marketplace und AppSource
description: Eine Übersicht über verschiedene Themen zur Veröffentlichung von Angeboten und technischen Artefakten für Azure Marketplace und AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 10/05/2018
ms.author: yijenj
ms.openlocfilehash: 4055b1a30c1868f701de281b5de8d648d576219e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937905"
---
# <a name="lead-management-for-cloud-marketplace"></a>Leadverwaltung für Cloud Marketplace


Kunden spielen für erfolgreiche Unternehmen eine entscheidende Rolle. Angesichts des Wandels beim Kauf von Produkten sind Marketingfachleute darauf angewiesen, einen direkten Kontakt mit Kunden herzustellen und eine Beziehung aufzubauen. Aus diesem Grund stellt die Generierung hochwertiger Leads ein unverzichtbares Werkzeug für Ihren Vertriebszyklus dar. Nachdem Sie Ihr Angebot im [Cloud-Partnerportal](https://cloudpartner.azure.com/) gelistet haben, stehen Ihnen Tools zur Verfügung, mit denen Sie programmgesteuert Kundenkontaktinformationen erhalten können, sobald ein Kunde Interesse bekundet oder Ihr Produkt im Marketplace einsetzt. 



## <a name="what-are-leads-in-the-marketplace"></a>Was sind Leads im Marketplace?

Die Leads stammen von Kunden, die interessiert sind oder Ihre Produkte aus dem Marketplace einsetzen. Unabhängig davon, ob Ihr Produkt im Azure Marketplace oder AppSource gelistet ist, können Sie Leads von Kunden erhalten, sobald es von Ihrem CRM richtig für Ihre Angebote im Cloud-Partnerportal eingerichtet ist. 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Vorgehensweise zum Verbinden Ihres CRM-Systems mit dem Cloud-Partnerportal

Um Leads zu erhalten, ist der Leadverwaltungsconnector im Cloud-Partnerportal so konzipiert, dass er einfach mit Ihren CRM-Informationen in eine Liste der verfügbaren CRM-Systeme integriert werden kann. Nun können Sie ohne großen Entwicklungsaufwand für die Integration in ein externes System mühelos die vom Marketplace generierten Leads nutzen.

Nachfolgend finden Sie eine schrittweise Anleitung, wie Sie die einzelnen möglichen Leadziele verbinden:

**Dynamics CRM Online** - [ Klicken Sie hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics), um die Anweisungen zur Konfiguration von Dynamics CRM Online für den Erhalt von Leads abzurufen.

**Marketo** - [ Klicken Sie hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo), um Anweisungen zum Einrichten der Marketo-Leadkonfiguration zum Abrufen von Leads zu erhalten.

**Salesforce** - [ Klicken Sie hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce), um Anweisungen zum Einrichten Ihrer Salesforce-Instanz zum Abrufen von Leads zu erhalten.

**Azure-Tabelle** – [Klicken Sie hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table), um Anweisungen zum Einrichten Ihres Azure Storage-Kontos zum Abrufen von Leads in einer Azure-Tabelle zu erhalten.

**HTTPS-Endpunkt** – [Klicken Sie hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https), um Anweisungen zum Einrichten Ihres HTTPS-Endpunkts zum Abrufen von Leads zu erhalten.

Nachdem Sie Ihr Leadziel ordnungsgemäß konfiguriert und in Ihrem Angebot auf „Veröffentlichen“ geklickt haben, überprüfen wir die Verbindung und senden Ihnen einen Testlead zu. Wenn Sie das Angebot vor der Liveschaltung anzeigen, können Sie Ihre Leadverbindung ebenfalls testen, indem Sie selbst versuchen, das Angebot in der Vorschauumgebung zu erwerben. Es muss unbedingt sichergestellt werden, dass Ihre Leadeinstellungen auf dem neuesten Stand sind, damit Sie keine Leads verlieren. Aktualisieren Sie daher immer diese Verbindungen, wenn sich bei Ihnen etwas geändert hat.


### <a name="what-are-the-next-steps"></a>Welche Schritte sind die nächsten?

Nach der technischen Einrichtung sollten Sie diese Leads in Ihre aktuelle Vertriebs- und Marketingstrategie sowie in Ihre Betriebsprozesse integrieren. Wir sind daran interessiert, einen besseren Überblick über Ihren allgemeinen Vertriebsprozess zu erhalten, und möchten bei der Bereitstellung hochwertiger Leads und ausreichender Daten eng mit Ihnen zusammenarbeiten, um zu Ihrem Erfolg beizutragen. Wir freuen uns, wenn Sie uns Ihr Feedback mitteilen würden, wie wir die Leads, die wir Ihnen zusenden, durch zusätzliche Daten optimieren und verbessern können, um diesen Kunden zum Erfolg zu verhelfen. Teilen Sie uns Ihr Feedback und Ihre Vorschläge mit, damit wir Ihrem Vertriebsteam durch Marketplace-Leads zu größerem Erfolg verhelfen können.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Häufige Leadkonfigurationsfehler bei der Veröffentlichung im Cloud-Partnerportal 

**Lead konnte nicht in Dynamics CRM gespeichert werden. Überprüfen Sie die Einstellungen des Dynamics CRM-Kontos. LastCRMError: Anmeldung bei Dynamics CRM nicht möglich, LastCRMException:** 

> Wenn die O365-Authentifizierung ausgewählt wurde, überprüfen Sie, ob Benutzerkonto und Kennwort gültig sind. Wenn AAD ausgewählt wurde, überprüfen Sie, ob Mandanten-ID, Anwendungs-ID und Anwendungsgeheimnis mit den für AAD eingerichteten Werten übereinstimmen. Folgen Sie den Anweisungen [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Wenn der Benutzername und das Kennwort des Kontos gültig sind, stellen Sie sicher, dass es Zugriff auf Dynamics 365 hat und eine Lizenz zugewiesen ist (Schritte 11-15 bei Verwendung von Azure Active Directory oder Sicherheitseinstellungen bei Verwendung eines Office-Benutzers). 

 
**Lead konnte nicht in Dynamics CRM gespeichert werden. Der Benutzer hat keine Erstellungsberechtigungen für das Attribut „leadsourcecode“ in der Leadentität**. 

> Der Anwendung bzw. dem Benutzer fehlen die Sicherheitsrollen für den Microsoft Marketplace-Leadschreibdienst. Führen Sie die Schritte 11-15 aus, wenn Sie Azure Active Directory verwenden oder Sicherheitseinstellungen, wenn Sie einen Office-Benutzer [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) verwenden.

**Lead konnte nicht mit AAD in Dynamics CRM gespeichert werden. Ausnahme:: Mandant wurde nicht gefunden. Diese Instanz kann auftreten, wenn es für den Mandanten keine aktiven Abonnements gibt.**  

> Die im Abschnitt zur Leadverwaltung angegebene Verzeichnis-ID ist kein gültiges Verzeichnis. Rufen Sie die Verzeichnis-ID basierend auf den Anweisungen in Schritt 2 ab (unter Azure Active Directory, von [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Lead konnte nicht in Dynamics CRM gespeichert werden. LastCRMError: Fehler bei SecLib::RetrievePrivilegeForUser – dem Benutzer sind keine Rollen zugewiesen.**  

> Lösung: Weisen Sie dem Microsoft Marketplace-Leadschreibdienst eine Sicherheitsrolle zu. Folgen Sie den Anweisungen [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) unter Sicherheitseinstellungen. 

**Lead konnte nicht mit AAD in Dynamics CRM gespeichert werden. Ausnahme:: Die Anwendung mit dem Bezeichner wurde im Verzeichnis nicht gefunden**. 

> Die im Abschnitt zur Leadverwaltung angegebene Anwendungs-ID ist kein gültiges Verzeichnis. Rufen Sie die Verzeichnis-ID basierend auf den Anweisungen in Schritt 8 ab (unter Azure Active Directory, von [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Lead konnte nicht mit AAD in Dynamics CRM gespeichert werden. Ausnahme:: Angeforderter Mandantenbezeichner ist nicht gültig und weist kein gültiges externes Domänenformat auf**. 

> Die im Abschnitt zur Leadverwaltung angegebene Verzeichnis-ID ist kein gültiges Verzeichnis. Rufen Sie die Verzeichnis-ID basierend auf den Anweisungen in Schritt 2 ab (unter Azure Active Directory, von [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Lead konnte nicht mit AAD in Dynamics CRM gespeichert werden. Ausnahme:: Fehler beim Überprüfen der Anmeldeinformationen: Ungültiges Clientgeheimnis bereitgestellt.** 

> Lösung: Melden Sie sich beim Azure-Portal an, und überprüfen Sie, ob der Anwendungsschlüssel mit der Angabe im Cloud-Partnerportal übereinstimmt. Generieren Sie das Kennwort basierend auf den Anweisungen in Schritt 10 (unter Azure Active Directory, von [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Lead konnte nicht in Dynamics CRM gespeichert werden. LastCRMError: Timeout beim Anforderungskanal beim Warten auf eine Antwort nach 00:02:00. Erhöhen Sie den Zeitlimitwert, der an den Aufruf der Anforderung übergeben wird, oder erhöhen Sie den SendTimeout-Wert bei der Binding. Der für diesen Vorgang zugewiesene Zeitraum war möglicherweise ein Teil eines längeren Zeitlimits.**  

> Lösung: Melden Sie sich beim Cloud-Partnerportal an, überprüfen Sie „Storefrontdetails“ > „Leadziel“ > „URL“, und überprüfen Sie, ob es sich um eine gültige Dynamic CRM-Instanz handelt.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Was sind Leads und warum sind sie für mich als Herausgeber auf dem Marketplace wichtig?** 

Leads sind Kunden, die Ihre Produkte über Marketplace bereitstellen. Ganz gleich, ob Ihr Produkt im [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) oder in [AppSource](https://appsource.microsoft.com/) aufgeführt wird – Sie erhalten Leads von Kunden, die an Ihrem Produkt interessiert sind, wenn Sie das Leadziel in Ihrem Angebot eingerichtet haben.  


**Wo erhalte ich Hilfe bei der Einrichtung meines Leadziels?** 

Hier finden Sie die Dokumentation: [Rufen Sie die Kundenleads ab](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads), oder übermitteln Sie ein Supportticket über „aka.ms/marketplacepublishersupport“, indem Sie Angebotsart und Leadverwaltung auswählen. 



**Muss ich ein Leadziel konfigurieren, um ein Angebot im Marketplace zu veröffentlichen?**

Ja, wenn Sie eine „Contact Me SaaS-App“ oder Beratungsdienste veröffentlichen.  
 


**Wie kann ich bestätigen, dass die Leadkonfiguration richtig ist?**

Nachdem Sie Ihr Angebot und Ihr Leadziel eingerichtet haben, veröffentlichen Sie Ihr Angebot. Im Rahmen der Leadvalidierung sendet Marketplace einen Testlead an das in Ihrem Angebot konfigurierte Leadziel. 


**Wie finde ich den Testlead?**


Suchen Sie in Ihrem Leadziel nach „MSFT_TEST“. Beispiel für Testleaddaten: 

company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|\<Offer Name> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**Ich verfüge über ein Liveangebot, aber warum sehe ich keine Leads?**

Für jeden Lead werden Daten in Feldern des ausgewählten Leadziels übergeben. Die Leads werden in folgendem Format bereitgestellt: **Quellaktion|Angebot** 

  *Quellen:*

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  *Aktionen:*

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  *Angebote:*

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  *Hier sind einige Beispieldaten der Kundeninformationen*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Weitere Informationen finden Sie unter [Leadinformationen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Ich habe Azure BLOB als Leadziel konfiguriert. Warum sehe ich den Lead nicht?** 

Der Lead wird nur geschrieben, wenn Sie Azure Blob Storage als Leadziel auswählen. Wechsel zur Azure-Tabelle zum Abrufen der Leadechtzeit 


**Ich habe eine E-Mail vom Marketplace erhalten. Warum kann ich den Lead in meinem CRM nicht finden?**  

Es ist möglich, dass die E-Mail-Domäne des Endbenutzers die Endung „.edu“ aufweist. Aus Datenschutzgründen geben wir keine PII-Daten von .edu-Domänen weiter. Übermitteln eines Supporttickets über aka.ms/marketplacepublishersupport 


 **Ich habe Azure-Tabelle/Azure BLOB als Leadziel konfiguriert. Wie kann ich die Leads anzeigen?** 

Sie können auf den Blob oder die Tabelle über das Azure-Portal zugreifen, oder Sie können [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) kostenlos herunterladen und installieren, um die Tabellen/Blobs Ihres Azure Storage-Kontos anzuzeigen. 


**Ich habe die Azure-Tabelle als Leadziel konfiguriert. Kann ich mich benachrichtigen lassen, wenn ein neuer Lead vom Marketplace gesendet wird?** 

Ja, befolgen Sie [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) die Anweisungen zur Einrichtung der Azure-Tabelle und -Funktion für die Dokumentation. 



**Ich habe Salesforce als Leadziel konfiguriert. Warum kann ich die Leads nicht finden?** 

Überprüfen Sie, ob das Web-to-Lead-Formular ein Pflichtfeld auf Grundlage einer Auswahlliste ist. Ändern Sie das Feld in diesem Fall in ein nicht obligatorisches Textfeld.  
 

**Es gab ein Problem mit meinem Leadziel, und ich habe einige Leads verpasst. Kann ich sie mir in einer E-Mail zusenden lassen?** 

Aufgrund von Richtlinien für personenbezogene Informationen (Private Identifiable Information) können wir Leadinformationen nicht über ungesicherte E-Mails weitergeben. 



**Ich habe Azure Storage (BLOB/Tabelle) als Leadziel konfiguriert. Wie viel wird es kosten?** 

Daten für die Leadgenerierung sind niedrig (< 1 GB bei fast allen Herausgebern). Die Kosten hängen von der Anzahl der erhaltenen Leads ab. Wenn 1.000 Leads in einem Monat eingehen, kostet es etwa 50 Cent. 
