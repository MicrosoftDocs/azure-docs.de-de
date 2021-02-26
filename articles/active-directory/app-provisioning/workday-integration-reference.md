---
title: Referenz zur Integration von Azure Active Directory mit Workday
description: Fundierte technische Einblicke in die HR-Bereitstellung mit Workday
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: f260bca196839a091ae7d12be6d5f85912bf92db
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255983"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Integration der Azure Active Directory-Bereitstellung mit Workday

Der [Azure Active Directory-Benutzerbereitstellungsdienst](../app-provisioning/user-provisioning.md) ist mit [Workday HCM](https://www.workday.com) integriert, um den Identitätslebenszyklus von Benutzern zu verwalten. Azure Active Directory bietet drei vorkonfigurierte Integrationen: 

* [Lokale Benutzerbereitstellung von Workday in Active Directory](../saas-apps/workday-inbound-tutorial.md)
* [Benutzerbereitstellung von Workday in Azure Active Directory](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday Writeback](../saas-apps/workday-writeback-tutorial.md)

In diesem Artikel wird erläutert, wie die Integration funktioniert und wie Sie das Bereitstellungsverhalten für verschiedene HR-Szenarien anpassen können. 

## <a name="establishing-connectivity"></a>Herstellen von Verbindungen 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Beschränken des Workday-API-Zugriffs auf Azure AD Endpunkte
Der Azure AD-Bereitstellungsdienst verwendet für Verbindungen mit den Webdienst-API-Endpunkten von Workday die Standardauthentifizierung.  

Um die Konnektivität zwischen Azure AD-Bereitstellungsdienst und Workday noch besser zu schützen, können Sie den Zugriff einschränken, sodass der angegebene Integrationssystembenutzer nur aus den zulässigen Azure AD-IP-Adressbereichen auf die Workday-APIs zugreifen darf. Wenden Sie sich an Ihren Workday-Administrator, um die folgende Konfiguration in Ihrem Workday-Mandanten abzuschließen. 

1. Laden Sie die [aktuellen IP-Adressbereiche](https://www.microsoft.com/download/details.aspx?id=56519) für die öffentliche Azure-Cloud herunter. 
1. Öffnen Sie die Datei, und suchen Sie nach dem Tag **AzureActiveDirectory**. 

   >[!div class="mx-imgBorder"] 
   >![Azure AD-IP-Adressbereich](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Kopieren Sie alle IP-Adressbereiche, die innerhalb des-Elements *addressPrefixes* aufgeführt sind, und verwenden Sie den Bereich, um eine eigene IP-Adressliste zu erstellen.
1. Melden Sie sich beim Workday-Verwaltungsportal an. 
1. Greifen Sie auf die Aufgabe **Maintain IP Ranges** (IP-Adressbereiche verwalten) zu, um einen neuen IP-Adressbereich für die Azure-Rechenzentren zu erstellen. Geben Sie die IP-Adressbereiche (in CIDR-Notation) als eine durch Trennzeichen getrennte Liste an.  
1. Greifen Sie auf die Aufgabe **Manage Authentication Policies** (Authentifizierungsrichtlinien verwalten) zu, um eine neue Authentifizierungsrichtlinie zu erstellen. Verwenden Sie in der Authentifizierungsrichtlinie die Positivliste für die Authentifizierung, um den Azure AD-IP-Adressbereich und die Sicherheitsgruppe anzugeben, der der Zugriff von diesem IP-Adressbereich aus gestattet wird. Speichern Sie die Änderungen. 
1. Greifen Sie auf die Aufgabe **Activate All Pending Authentication Policy Changes** (Alle ausstehenden Änderungen an Authentifizierungsrichtlinien aktivieren) zu, um die Änderungen zu bestätigen.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Einschränken des Zugriffs auf Mitarbeiterdaten in Workday mithilfe von eingeschränkten Sicherheitsgruppen

Mithilfe der Standardschritte zum [Konfigurieren des Systembenutzers für die Workday-Integration](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) gewähren Sie den Zugriff zum Abrufen aller Benutzer in Ihrem Workday-Mandanten. In bestimmten Integrationsszenarien können Sie den Zugriff so einschränken, dass nur Benutzer, die zu bestimmten leitenden Organisationen gehören, durch den API-Aufruf „Get_Workers“ zurückgegeben und vom Azure AD-Connector für Workday verarbeitet werden. 

Arbeiten Sie dazu mit Ihrem Workday-Administrator zusammen, und konfigurieren Sie eingeschränkte Sicherheitsgruppen für das Integrationssystem. Weitere Informationen hierzu finden Sie in [diesem Artikel der Workday-Community](https://community.workday.com/forums/customer-questions/620393) (*Für den Zugriff auf den Artikel sind Anmeldeinformationen der Workday-Community erforderlich.* ).

Diese Strategie zum Einschränken des Zugriffs mithilfe eingeschränkter ISSGs (Integrationssystem-Sicherheitsgruppen) ist insbesondere in den folgenden Szenarien hilfreich: 
* **Rollout in mehreren Schritten:** Sie verfügen über einen großen Workday-Mandanten und planen einen Rollout in mehreren Schritten von Workday für die automatische Bereitstellung mit Azure AD. In diesem Szenario empfiehlt es sich nicht, Benutzer, die sich nicht im Gültigkeitsbereich der aktuellen Phase befinden, mit Azure AD-Bereichsfiltern auszuschließen. Konfigurieren Sie stattdessen eine eingeschränkte ISSG, sodass nur Mitarbeiter im Gültigkeitsbereich in Azure AD sichtbar sind.
* **Mehrere Bereitstellungsaufträge:** Sie verfügen über einen großen Workday-Mandanten und mehrere AD-Domänen, die jeweils andere Geschäftseinheiten/Abteilungen/Unternehmen unterstützen. Zur Unterstützung dieser Topologie möchten Sie mehrere Aufträge zur Bereitstellung von Workday in Azure AD ausführen, um jeweils eine bestimmte Gruppe von Mitarbeitern bereitzustellen. In diesem Szenario sollten Sie die Mitarbeiterdaten nicht mit Azure AD-Bereichsfiltern ausschließen, sondern eingeschränkte ISSGs konfigurieren, damit nur die relevanten Mitarbeiterdaten für Azure AD sichtbar sind. 

### <a name="workday-test-connection-query"></a>Abfragen der Workday-Testverbindung

Zum Testen der Konnektivität mit Workday sendet Azure AD die folgende Workday-Webdienstanforderung *Get_Workers*. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Funktionsweise der vollständigen Synchronisierung

**Vollständige Synchronisierung** bezieht sich im Zusammenhang mit der Workday-gesteuerten Bereitstellung auf den Prozess, mit dem alle Identitäten aus Workday abgerufen werden und festgelegt wird, welche Bereitstellungsregeln auf die einzelnen Mitarbeiterobjekte angewandt werden sollen. Die vollständige Synchronisierung erfolgt, wenn Sie die Bereitstellung zum ersten Mal aktivieren und auch wenn Sie die Bereitstellung über das Azure-Portal oder mithilfe der Graph-APIs *neu starten*. 

Zum Abrufen der Mitarbeiterdaten sendet Azure AD die folgende Workday-Webdienstanforderung *Get_Workers*. Die Abfrage durchsucht das Workday-Transaktionsprotokoll nach allen effektiv veralteten Mitarbeitereinträgen zum Zeitpunkt der vollständigen Synchronisierung. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
Mit dem Knoten *Response_Group* wird angegeben, welche Mitarbeiterattribute aus Workday abgerufen werden sollen. Eine Beschreibung der einzelnen Flags im Knoten *Response_Group* finden Sie in der Dokumentation von Workday zur [Get_Workers-API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType). 

Bestimmte Flagwerte, die im Knoten *Response_Group* angegeben sind, werden basierend auf den in der Azure AD-Bereitstellungsanwendung für Workday konfigurierten Attributen berechnet. Die Kriterien, die zum Festlegen der Flagwerte angewandt werden, finden Sie im Abschnitt *Unterstützten Entitäten*. 

Die Antwort auf *Get_Workers* von Workday für die obige Abfrage enthält die Anzahl der Mitarbeiterdatensätze und die Seitenanzahl.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Zum Abrufen der nächsten Seite des Resultsets wird in der nächsten *Get_Workers*-Abfrage die Seitenzahl als Parameter im *Response_Filter* angegeben.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Der Azure AD-Bereitstellungsdienst verarbeitet während der vollständigen Synchronisierung jede Seite und durchläuft alle gültigen Mitarbeiter. Für jeden aus Workday importierten Mitarbeitereintrag werden folgende Aktionen ausgeführt:
* Der [XPATH-Ausdruck](workday-attribute-reference.md) wird angewandt, um Attributwerte aus Workday abzurufen.
* Die Attributzuordnung und die Vergleichsregeln werden angewandt. 
* Der Dienst legt fest, welcher Vorgang am Ziel (Azure AD/AD) ausgeführt werden soll. 

Sobald die Verarbeitung abgeschlossen wurde, wird der Zeitstempel vom Beginn der vollständigen Synchronisierung als Wasserzeichen gespeichert. Dieses Wasserzeichen dient als Startpunkt für den inkrementellen Synchronisierungszyklus. 

## <a name="how-incremental-sync-works"></a>Funktionsweise der inkrementellen Synchronisierung

Nach der vollständigen Synchronisierung verwaltet der Azure AD-Bereitstellungsdienst einen `LastExecutionTimestamp` und verwendet diesen zum Erstellen von Deltaabfragen für das Abrufen inkrementeller Änderungen. Während der inkrementellen Synchronisierung sendet Azure AD die folgenden Typen von Abfragen an Workday: 

* [Abfrage von manuellen Updates](#query-for-manual-updates)
* [Abfrage von Updates und Kündigungen zu einem bestimmten Datum](#query-for-effective-dated-updates-and-terminations)
* [Abfrage für zukünftige Neueinstellungen](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>Abfrage von manuellen Updates

Die folgende *Get_Workers*-Anforderung fragt manuelle Updates ab, die zwischen der letzten Ausführung und der aktuellen Ausführungszeit aufgetreten sind. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Abfrage von Updates und Kündigungen zu einem bestimmten Datum

Die folgende *Get_Workers*-Anforderung fragt Updates zu bestimmten Daten ab, die zwischen der letzten Ausführung und der aktuellen Ausführungszeit aufgetreten sind. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Abfrage für zukünftige Neueinstellungen

Wenn eine der oben genannten Abfragen eine Einstellung in der Zukunft zurückgibt, werden mithilfe der folgenden *Get_Workers*-Anforderung Informationen zu einer zukünftigen Neueinstellung abgerufen. Das *WID*-Attribut des neu eingestellten Mitarbeiters wird für die Suche verwendet, und als effektives Datum werden das Datum und die Uhrzeit der Einstellung festgelegt. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>Abrufen von Attributen für Mitarbeiterdaten

Die *Get_Workers*-API kann verschiedene Datasets zurückgeben, die einem Mitarbeiter zugeordnet sind. Abhängig von den [XPATH-API-Ausdrücken](workday-attribute-reference.md), die im Bereitstellungsschema konfiguriert sind, legt der Azure AD-Bereitstellungsdienst fest, welche Datasets aus Workday abgerufen werden sollen. Die entsprechenden *Response_Group*-Flags werden in der *Get_Workers*-Anforderung festgelegt. 

In der folgenden Tabelle finden Sie einen Leitfaden für die Zuordnung der Konfiguration zum Abrufen eines bestimmten Datasets. 

| \# | Workday-Entität                       | Standardmäßig inbegriffen | XPATH-Muster zum Angeben der Zuordnung für das Abrufen nicht standardmäßiger Entitäten             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Personenbezogene Daten                        | Yes                 | wd:Worker\_Data/wd:Personal\_Data                                             |
| 2  | Beschäftigungsdaten                      | Yes                 | wd:Worker\_Data/wd:Employment\_Data                                           |
| 3  | Weitere Beschäftigungsdaten                  | Yes                 | wd:Worker\_Data/wd:Employment\_Data/wd:Worker\_Job\_Data\[@wd:Primary\_Job=0\]|
| 4  | Daten zur Organisation                    | Yes                 | wd:Worker\_Data/wd:Organization\_Data                                         |
| 5  | Daten zur Verwaltungskette                | Yes                 | wd:Worker\_Data/wd:Management\_Chain\_Data                                    |
| 6  | Weisungsgefüge             | Yes                 | 'SUPERVISORY'                                                                 |
| 7  | Company                              | Yes                 | 'COMPANY'                                                                     |
| 8  | Geschäftseinheit                        | No                  | 'BUSINESS\_UNIT'                                                              |
| 9  | Hierarchie der Geschäftseinheiten              | No                  | 'BUSINESS\_UNIT\_HIERARCHY'                                                   |
| 10 | Unternehmenshierarchie                    | No                  | 'COMPANY\_HIERARCHY'                                                          |
| 11 | Kostenstelle                          | Nein                  | 'COST\_CENTER'                                                                |
| 12 | Hierarchie der Kostenstellen                | No                  | 'COST\_CENTER\_HIERARCHY'                                                     |
| 13 | Finanzierung                                 | No                  | 'FUND'                                                                        |
| 14 | Finanzierungshierarchie                       | No                  | 'FUND\_HIERARCHY'                                                             |
| 15 | Prämien                                 | No                  | 'GIFT'                                                                        |
| 16 | Prämienhierarchie                       | No                  | 'GIFT\_HIERARCHY'                                                             |
| 17 | Erteilen                                | No                  | 'GRANT'                                                                       |
| 18 | Zuweisungshierarchie                      | No                  | 'GRANT\_HIERARCHY'                                                            |
| 19 | Hierarchie der Geschäftsstandorte              | No                  | 'BUSINESS\_SITE\_HIERARCHY'                                                   |
| 20 | Organisationsmatrix                  | No                  | 'MATRIX'                                                                      |
| 21 | Lohngruppe                            | No                  | 'PAY\_GROUP'                                                                  |
| 22 | Programme                             | No                  | 'PROGRAMS'                                                                    |
| 23 | Programmhierarchie                    | No                  | 'PROGRAM\_HIERARCHY'                                                          |
| 24 | Region                               | No                  | 'REGION\_HIERARCHY'                                                           |
| 25 | Standorthierarchie                   | No                  | 'LOCATION\_HIERARCHY'                                                         |
| 26 | Kontobereitstellungsdaten            | No                  | wd:Worker\_Data/wd:Account\_Provisioning\_Data                                |
| 27 | Hintergrundüberprüfung der Daten                | No                  | wd:Worker\_Data/wd:Background\_Check\_Data                                    |
| 28 | Daten zur Vorteilsberechtigung             | No                  | wd:Worker\_Data/wd:Benefit\_Eligibility\_Data                                 |
| 29 | Daten zur Vorteilsregistrierung              | No                  | wd:Worker\_Data/wd:Benefit\_Enrollment\_Data                                  |
| 30 | Karrieredaten                          | No                  | wd:Worker\_Data/wd:Career\_Data                                               |
| 31 | Vergütung                    | No                  | wd:Worker\_Data/wd:Compensation\_Data                                         |
| 32 | Finanzamtdaten von vorübergehend beschäftigten Mitarbeitern | No                  | wd:Worker\_Data/wd:Contingent\_Worker\_Tax\_Authority\_Form\_Type\_Data       |
| 33 | Entwicklungselementdaten                | No                  | wd:Worker\_Data/wd:Development\_Item\_Data                                    |
| 34 | Mitarbeitervertragsdaten              | No                  | wd:Worker\_Data/wd:Employee\_Contracts\_Data                                  |
| 35 | Daten zur Mitarbeiterüberprüfung                 | No                  | wd:Worker\_Data/wd:Employee\_Review\_Data                                     |
| 36 | Feedbackdaten               | No                  | wd:Worker\_Data/wd:Feedback\_Received\_Data                                   |
| 37 | Daten zum Mitarbeiterziel                     | No                  | wd:Worker\_Data/wd:Worker\_Goal\_Data                                         |
| 38 | Fotodaten                           | No                  | wd:Worker\_Data/wd:Photo\_Data                                                |
| 39 | Daten zur Qualifikation                   | No                  | wd:Worker\_Data/wd:Qualification\_Data                                        |
| 40 | Daten zu verwandten Personen                 | No                  | wd:Worker\_Data/wd:Related\_Persons\_Data                                     |
| 41 | Rollendaten                            | No                  | wd:Worker\_Data/wd:Role\_Data                                                 |
| 42 | Daten zu Fertigkeiten                           | No                  | wd:Worker\_Data/wd:Skill\_Data                                                |
| 43 | Profildaten für Nachfolger              | No                  | wd:Worker\_Data/wd:Succession\_Profile\_Data                                  |
| 44 | Daten zur Talentbeurteilung               | No                  | wd:Worker\_Data/wd:Talent\_Assessment\_Data                                   |
| 45 | Benutzerkontodaten                    | No                  | wd:Worker\_Data/wd:User\_Account\_Data                                        |
| 46 | Mitarbeiterdokumentdaten                 | No                  | wd:Worker\_Data/wd:Worker\_Document\_Data                                     |

>[!NOTE]
>Jede in der Tabelle aufgelistete Workday-Entität wird durch eine **Domänensicherheitsrichtlinie** in Workday geschützt. Wenn Sie nach dem Festlegen des richtigen XPATH keine Attribute abrufen können, die der Entität zugeordnet sind, wenden Sie sich an Ihren Workday-Administrator, um sicherzustellen, dass für den Integrationssystembenutzer, der der Bereitstellungs-App zugeordnet ist, die entsprechende Domänensicherheitsrichtlinie konfiguriert ist. Wenn Sie z. B. *Daten zu Fertigkeiten* abrufen möchten, ist der Zugriff *Abrufen* für die Workday-Domäne *Worker Data: Skills and Experience* (Mitarbeiterdaten: Fertigkeiten und Erfahrungen). 

Im Folgenden finden Sie einige Beispiele dazu, wie Sie die Workday-Integration erweitern können, um bestimmte Anforderungen zu erfüllen. 

**Beispiel 1**

Angenommen, Sie möchten die folgenden Datasets aus Workday abrufen und in Ihren Bereitstellungsregeln verwenden:

* Kostenstelle
* Hierarchie der Kostenstellen
* Lohngruppe

Die oben aufgeführten Datasets sind standardmäßig nicht enthalten. So rufen Sie diese Datasets ab
1. Melden Sie sich beim Azure-Portal an, und öffnen Sie Ihre App für die AD/Azure AD-Benutzerbereitstellung in Workday. 
1. Bearbeiten Sie auf dem Blatt „Bereitstellung“ die Zuordnungen, und öffnen Sie die Liste der Workday-Attribute im Abschnitt „Erweitert“. 
1. Fügen Sie die folgenden Attributdefinitionen hinzu, und markieren Sie sie als „erforderlich“. Diese Attribute werden keinem Attribut in AD oder Azure AD zugeordnet. Sie dienen lediglich als Signale für den Connector, die Informationen zu Kostenstelle, Kostenstellenhierarchie und Lohngruppe abzurufen. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Attributname | XPATH-API-Ausdruck |
     >|---|---|
     >| CostCenterHierarchyFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER_HIERARCHY']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER']/wd:Organization_Data/wd:Organization_Code/text() |
     >| PayGroupFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='PAY_GROUP']/wd:Organization_Data/wd:Organization_Reference_ID/text() |

1. Wenn die Datasets für Kostenstelle und Lohngruppe in der *Get_Workers*-Antwort enthalten sind, können Sie mithilfe der folgenden XPATH-Werte den Namen der Kostenstelle, den Kostenstellencode und die Lohngruppe abrufen. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Attributname | XPATH-API-Ausdruck |
     >|---|---|
     >| CostCenterName  | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Name/text() |
     >| CostCenterCode | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Code/text() |
     >| PayGroup | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Pay Group']/wd:Organization_Name/text() |

**Beispiel 2**

Angenommen, Sie möchten Zertifizierungen abrufen, die einem Benutzer zugeordnet sind. Diese Informationen sind im Dataset mit den *Qualifikationsdaten* enthalten. Verwenden Sie den folgenden XPATH-Ausdruck, um dieses Dataset in der *Get_Workers*-Antwort abzurufen: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Beispiel 3**

Angenommen, Sie möchten die *Bereitstellungsgruppen* abrufen, die einem Mitarbeiter zugewiesen sind. Diese Informationen sind im Dataset mit den *Kontobereitstellungsdaten* enthalten. Verwenden Sie den folgenden XPATH-Ausdruck, um dieses Dataset in der *Get_Workers*-Antwort abzurufen: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>Nächste Schritte

* [Informieren Sie sich über das Konfigurieren der Bereitstellung aus Workday in Active Directory.](../saas-apps/workday-inbound-tutorial.md)
* [Erfahren Sie etwas über das Konfigurieren des Zurückschreibens in Workday.](../saas-apps/workday-writeback-tutorial.md)
* [Weitere Informationen zu unterstützten Workday-Attributen für die eingehende Bereitstellung](workday-attribute-reference.md)

