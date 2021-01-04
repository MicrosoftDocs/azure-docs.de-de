---
title: 'Tutorial: Anpassen von Attributzuordnungen in Azure Active Directory'
description: Erfahren Sie, was Attributzuordnungen für SaaS-Apps in Azure Active Directory sind und wie Sie sie an Ihre geschäftlichen Anforderungen anpassen können.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: kenwith
ms.openlocfilehash: 012038399796a0f2dc87acfb350043542268379a
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936980"
---
# <a name="tutorial---customize-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Tutorial: Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory

Microsoft Azure AD bietet Unterstützung für die Benutzerbereitstellung für SaaS-Anwendungen von Drittanbietern wie Salesforce, G Suite usw. Wenn Sie die Benutzerbereitstellung für eine SaaS-Anwendung eines Drittanbieters aktivieren, steuert das Azure-Portal deren Attributwerte mithilfe von Attributzuordnungen.

Es ist eine vorkonfigurierte Sammlung von Attributen und Attributzuordnungen zwischen Azure AD-Benutzerobjekten und den Benutzerobjekten der einzelnen SaaS-Apps verfügbar. Einige Apps verwalten neben Benutzern andere Objekttypen wie beispielsweise Gruppen.

Sie können die Standardattributzuordnungen den Anforderungen Ihres Unternehmens entsprechend anpassen. Dies bedeutet, dass Sie vorhandene Attributzuordnungen ändern oder löschen und neue Attributzuordnungen erstellen können.

## <a name="editing-user-attribute-mappings"></a>Bearbeiten von Benutzerattributzuordnungen

Führen Sie diese Schritte aus, um auf die Funktion **Zuordnungen** für die Benutzerbereitstellung zuzugreifen:

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com) an.
1. Wählen Sie im linken Bereich die Option **Unternehmensanwendungen** aus. Eine Liste mit allen konfigurierten Apps wird angezeigt, einschließlich Apps, die aus dem Katalog hinzugefügt wurden.
1. Wählen Sie eine beliebige App aus, um den App-Verwaltungsbereich zu laden, in dem Sie Berichte anzeigen und App-Einstellungen verwalten können.
1. Wählen Sie **Bereitstellung** aus, um die Einstellungen für die Bereitstellung von Benutzerkonten für die ausgewählte App zu verwalten.
1. Erweitern Sie **Zuordnungen**, um die Benutzerattribute anzuzeigen und zu bearbeiten, die zwischen Azure AD und Zielanwendung übertragen werden. Wenn die Zielanwendung dies unterstützt, können Sie in diesem Abschnitt auch optional die Bereitstellung von Gruppen und Benutzerkonten konfigurieren.

   ![Verwenden von Zuordnungen zum Anzeigen und Bearbeiten von Benutzerattributen](./media/customize-application-attributes/21.png)

1. Wählen Sie eine **Zuordnungskonfiguration** aus, um den zugehörigen Bildschirm **Attributzuordnung** zu öffnen. Einige Attributzuordnungen sind erforderlich, damit eine SaaS-Anwendung ordnungsgemäß funktioniert. Für die erforderlichen Attribute ist das Feature **Löschen** nicht verfügbar.

   ![Verwenden von „Attributzuordnung“ zum Konfigurieren von Attributzuordnungen für Apps](./media/customize-application-attributes/22.png)

   In diesem Screenshot können Sie sehen, dass das Attribut **Benutzername** eines verwalteten Objekts in Salesforce mit dem Wert **userPrincipalName** des verknüpften Azure Active Directory-Objekts aufgefüllt wird.

1. Wählen Sie eine vorhandene **Attributzuordnung** aus, um den Bildschirm **Attribut bearbeiten** zu öffnen. Hier können Sie die Benutzerattribute bearbeiten, die zwischen Azure AD und Zielanwendung übertragen werden.

   ![Verwenden von „Attribut bearbeiten“ zum Bearbeiten von Benutzerattributen](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Grundlegendes zu Attributzuordnungstypen

Mit Attributzuordnungen steuern Sie, wie die Attribute in einer SaaS-Anwendung eines Drittanbieters mit Daten aufgefüllt werden.
Vier verschiedene Zuordnungstypen werden unterstützt:

- **Direkt** : Das Zielattribut wird mit dem Wert eines Attributs des verknüpften Objekts in Azure AD aufgefüllt.
- **Konstante**: Das Zielattribut wird mit einer bestimmten Zeichenfolge aufgefüllt, die Sie angegeben haben.
- **Ausdruck** : Das Zielattribut wird abhängig vom Ergebnis eines skriptähnlichen Ausdrucks mit Daten aufgefüllt.
  Weitere Informationen finden Sie unter [Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Kein** : Das Zielattribut bleibt unverändert. Wenn das Zielattribut allerdings leer ist, wird es mit dem von Ihnen angegebenen Standardwert aufgefüllt.

Zusätzlich zu diesen vier Basistypen unterstützen benutzerdefinierte Attributzuordnungen das Konzept einer optionalen Zuordnung von **Standardwerten**. Die Standardwertzuordnung stellt sicher, dass ein Zielattribut mit einem Wert aufgefüllt wird, wenn weder in Azure AD noch für das Zielobjekt ein Wert vorhanden ist. Bei der üblichen Konfiguration bleibt dieses Feld leer.

### <a name="understanding-attribute-mapping-properties"></a>Grundlegendes zu Attributzuordnungseigenschaften

Im vorherigen Abschnitt haben Sie bereits die Attributzuordnungstyp-Eigenschaft kennengelernt.
Zusätzlich zu dieser Eigenschaft unterstützen Attributzuordnungen auch die folgenden Attribute:

- **Quellattribut:** Das Benutzerattribut aus dem Quellsystem (Beispiel: Azure Active Directory).
- **Zielattribut**: Das Benutzerattribut im Zielsystem (Beispiel: ServiceNow).
- **Standardwert bei Null (optional)** : Der Wert, der an das Zielsystem übermittelt wird, wenn das Quellattribut den Wert NULL hat. Dieser Wert wird nur beim Erstellen eines Benutzers bereitgestellt. Beim Aktualisieren eines vorhandenen Benutzers wird „Standardwert bei Null“ nicht bereitgestellt. Wenn Sie z. B. alle vorhandenen Benutzer im Zielsystem mit einer bestimmten Position (die im Quellsystem den Wert NULL hat) bereitstellen möchten, können Sie den folgenden [Ausdruck](../app-provisioning/functions-for-customizing-application-data.md)verwenden: Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Stellen Sie sicher, dass Sie „Default Value“ durch den Wert ersetzen, den Sie beim Wert NULL im Quellsystem bereitstellen möchten. 
- **Objekte mit diesem Attribut abgleichen**: Gibt an, ob diese Zuordnung zum eindeutigen Bestimmen von Benutzern zwischen Quell- und Zielsystem verwendet werden soll. Diese Einstellung wird in der Regel auf das „userPrincipalName“- oder „mail“-Attribut in Azure AD festgelegt, das üblicherweise einem Benutzernamenfeld in einer Zielanwendung zugeordnet ist.
- **Rangfolge für Abgleich**: Es können mehrere Attribute für den Abgleich festgelegt werden. Falls mehrere vorhanden sind, werden sie entsprechend der in diesem Feld festgelegten Reihenfolge ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet. Obwohl Sie beliebig viele übereinstimmende Attribute festlegen können, sollten Sie berücksichtigen, ob die Attribute, die Sie als übereinstimmende Attribute verwenden, tatsächlich eindeutig sind und übereinstimmen müssen. Im Allgemeinen verfügen Kunden in Ihrer Konfiguration über 1 oder 2 übereinstimmende Attribute. 
- **Diese Zuordnung anwenden**
  - **Immer**: Wenden Sie diese Zuordnung sowohl bei der Aktion zum Erstellen eines Benutzers als auch bei der zum Aktualisieren eines Benutzers an.
  - **Nur während der Erstellung**: Wenden Sie diese Zuordnung nur bei der Aktion zum Erstellen eines Benutzers an.

## <a name="matching-users-in-the-source-and-target--systems"></a>Abgleichen von Benutzern im Quell- und Zielsystem
Der Azure AD-Bereitstellungsdienst kann sowohl in „Greenfield“-Szenarien (Benutzer sind im Zielsystem nicht vorhanden) als auch in „Brownfield“-Szenarien (Benutzer sind im Zielsystem bereits vorhanden) bereitgestellt werden. Zur Unterstützung beider Szenarien verwendet der Bereitstellungsdienst das Konzept des Attributabgleichs. Anhand eines oder mehrerer übereinstimmender Attribute können Sie bestimmen, wie ein Benutzer im Quellsystem eindeutig identifiziert und mit dem Benutzer im Zielsystem abgeglichen wird. Identifizieren Sie im Rahmen der Planung Ihrer Bereitstellung das Attribut, das zur eindeutigen Identifizierung eines Benutzers im Quell-und Zielsystem verwendet werden kann. Hinweise:

- **Übereinstimmende Attribute sollten eindeutig sein:** Kunden verwenden häufig Attribute wie „userPrincipalName“, „mail“ oder „objectID“ als übereinstimmendes Attribut.
- **Als übereinstimmende Attribute können auch mehrere Attribute verwendet werden:** Sie können mehrere Attribute festlegen, die beim Abgleich von Benutzern ausgewertet werden sollen, und auch die Auswertungsreihenfolge angeben (auf der Benutzeroberfläche als „Rangfolge für Abgleich“ definiert). Wenn Sie z. B. drei Attribute als übereinstimmende Attribute definieren und ein Benutzer nach der Auswertung der ersten beiden Attribute eindeutig zugeordnet werden kann, wertet der Dienst das dritte Attribut nicht mehr aus. Der Dienst wertet übereinstimmende Attribute in der angegebenen Reihenfolge aus und beendet die Auswertung, wenn eine Übereinstimmung gefunden wird.  
- **Der Wert im Quell- und Zielsystem muss nicht genau übereinstimmen:** Der Wert im Zielsystem kann eine einfache Funktion des Werts im Quellsystem sein. Daher könnten die Attribute „emailAddress“ im Quellsystem und „userPrincipalName“ im Zielsystem anhand einer Funktion des Attributs „emailAddress“ abgeglichen werden, bei der einige Zeichen durch einen konstanten Wert ersetzt werden.  
- **Der Abgleich anhand einer Kombination von Attributen wird nicht unterstützt:** Die meisten Anwendungen unterstützen keine Abfragen, die auf zwei Eigenschaften basieren. Daher ist es nicht möglich, eine Übereinstimmung basierend auf einer Kombination von Attributen zu finden. Es ist jedoch möglich, einzelne Eigenschaften nacheinander auszuwerten.
- **Alle Benutzer müssen über einen Wert für mindestens ein übereinstimmendes Attribut verfügen:** Wenn Sie ein übereinstimmendes Attribut definieren, müssen im Quellsystem alle Benutzer einen Wert für dieses Attribut aufweisen. Wenn Sie beispielsweise „userPrincipalName“ als übereinstimmendes Attribut definieren, müssen alle Benutzer über das Attribut „userPrincipalName“ verfügen. Wenn Sie mehrere übereinstimmende Attribute (z.B. „extensionAttribute1“ und „mail“) definieren, müssen nicht alle Benutzer das gleiche übereinstimmende Attribut aufweisen. Ein Benutzer könnte über das Attribut „extensionAttribute1“, aber nicht über das Attribut „mail“, ein anderer Benutzer über „mail“, aber nicht über „extensionAttribute1“ verfügen. 
- **Die Zielanwendung muss das Filtern nach dem übereinstimmenden Attribut unterstützen:** Anwendungsentwickler können bei ihrer Benutzer- oder Gruppen-API das Filtern nach einer Teilmenge von Attributen erlauben. Bei Anwendungen im Katalog stellen wir sicher, dass die Standardattributzuordnung für ein Attribut gilt, das die API der Zielanwendung beim Filtern unterstützt. Wenn Sie das standardmäßige übereinstimmende Attribut für die Zielanwendung ändern, müssen Sie anhand der Dokumentation für die Drittanbieter-API sicherstellen, dass nach dem Attribut gefiltert werden kann.  

## <a name="editing-group-attribute-mappings"></a>Bearbeiten von Gruppenattributzuordnungen

Einige ausgewählte Anwendungen (z. B. ServiceNow, Box und G Suite) bieten die Möglichkeit, neben Benutzerobjekten auch Gruppenobjekte bereitzustellen. Gruppenobjekte können zusätzlich zu den Gruppenmitgliedern Gruppeneigenschaften wie Anzeigenamen und E-Mail-Aliase enthalten.

![Im Beispiel wird ServiceNow mit bereitgestellten Gruppen- und Benutzerobjekten gezeigt.](./media/customize-application-attributes/24.png)

Die Gruppenbereitstellung kann optional aktiviert oder deaktiviert werden, indem Sie die Gruppenzuordnung unter **Zuordnungen** auswählen und die Einstellung **Aktiviert** auf dem Bildschirm **Attributzuordnung** auf die gewünschte Option festlegen.

Die Attribute, die zusammen mit den Gruppenobjekten bereitgestellt werden, können wie Benutzerobjekte angepasst werden (siehe obige Beschreibung). 

> [!TIP]
> Die Bereitstellung von Gruppenobjekten (Eigenschaften und Mitglieder) unterscheidet sich vom Konzept der [Zuweisung von Gruppen](../manage-apps/assign-user-or-group-access-portal.md) zu einer Anwendung. Es ist möglich, eine Gruppe einer Anwendung zuzuweisen, aber nur die in der Gruppe enthaltenen Benutzerobjekte bereitzustellen. Die Bereitstellung vollständiger Gruppenobjekte ist nicht erforderlich, um Gruppen in Zuweisungen verwenden zu können.

## <a name="editing-the-list-of-supported-attributes"></a>Bearbeiten der Liste unterstützter Attribute

Die unterstützen Attribute für eine bestimmte Anwendung sind vorkonfiguriert. Die Benutzerverwaltungs-APIs der meisten Anwendungen unterstützen nicht die Schemaerkennung. Daher ist der Azure AD-Bereitstellungsdienst nicht in der Lage, die Liste der unterstützten Attribute dynamisch zu generieren, indem er Aufrufe an die Anwendung richtet.

Einige Anwendungen unterstützen jedoch benutzerdefinierte Attribute, die der Azure AD-Bereitstellungsdienst lesen und in die er schreiben kann. Um ihre Definitionen in das Azure-Portal einzugeben, aktivieren Sie unter auf dem Bildschirm **Attributzuordnung** das Kontrollkästchen **Erweiterte Optionen anzeigen** und wählen dann für Ihre App **Attributliste bearbeiten** aus.

Folgende Anwendungen und Systeme unterstützen die Anpassung der Attributliste:

- Salesforce
- ServiceNow
- Workday in Active Directory/Workday in Azure Active Directory
- SuccessFactors in Active Directory/SuccessFactors in Azure Active Directory
- Azure Active Directory ([Azure AD Graph-API-Standardattribute](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) und benutzerdefinierte Verzeichniserweiterungen werden unterstützt)
- Apps, die [SCIM 2.0](https://tools.ietf.org/html/rfc7643) unterstützen
- Für das Azure Active Directory-Rückschreiben in Workday oder SuccessFactors wird das Aktualisieren relevanter Metadaten für unterstützte Attribute (XPATH und JSONPath) unterstützt. Das Hinzufügen neuer Workday- oder SuccessFactors-Attribute, die über die im Standardschema enthaltenen Attribute hinausgehen, wird jedoch nicht unterstützt.


> [!NOTE]
> Das Bearbeiten der Liste unterstützter Attribute wird nur für Administratoren empfohlen, die das Schema ihrer Anwendungen und Systeme angepasst haben und aus erster Hand wissen, wie ihre benutzerdefinierten Attribute definiert wurden. Dies erfordert mitunter Kenntnisse der von einer Anwendung oder einem System bereitgestellten APIs und Entwicklertools. Die Möglichkeit zum Bearbeiten der Liste unterstützter Attribute ist standardmäßig gesperrt, aber Kunden können die Funktion aktivieren, indem sie zur folgenden URL navigieren: https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true. Anschließend können Sie zu Ihrer Anwendung navigieren, um die Attributliste wie [oben](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes) beschrieben anzuzeigen. 

Beim Bearbeiten der Liste unterstützter Attribute sind die folgenden Eigenschaften verfügbar:

- **Name**: Der im Schema des Zielobjekts definierte Systemname des Attributs.
- **Typ**: Der im Schema des Zielobjekts definierte Datentyp, den das Attribut speichert. Möglich ist einer der folgenden Typen:
  - *Binary* – Das Attribut enthält Binärdaten.
  - *Boolean* – Das Attribut enthält einen Wert „True“ oder „False“.
  - *DateTime* – Das Attribut enthält eine Datumszeichenfolge.
  - *Integer* – Das Attribut enthält eine ganze Zahl.
  - *Reference* – Das Attribut enthält eine ID, die auf einen in einer anderen Tabelle in der Zielanwendung gespeicherten Wert verweist.
  - *String* – Das Attribut enthält eine Textzeichenfolge.
- **Primärschlüssel?** : Gibt an, ob das Attribut als Primärschlüsselfeld im Schema des Zielobjekts definiert ist.
- **Erforderlich?** Gibt an, ob das Attribut in der Zielanwendung oder im Zielsystem aufgefüllt werden muss.
- **Mehrwertig?** : Gibt an, ob das Attribut mehrere Werte unterstützt.
- **Exact case?** (Groß-/Kleinschreibung beachten?): Gibt an, ob die Attributwerte unter Berücksichtigung der Groß-/Kleinschreibung ausgewertet werden.
- **API-Ausdruck**: Verwenden Sie diese Eigenschaft nur, wenn Sie in der Dokumentation für einen bestimmten Bereitstellungsconnector (z. B. Workday) dazu aufgefordert werden.
- **Referenced Object Attribute** (Referenziertes Objektattribut): Im Fall eines Attributs vom Typ „Reference“ können Sie in diesem Menü die Tabelle und das Attribut in der Zielanwendung auswählen, die den zugehörigen Wert für das Attribut enthält. Bei einem Attribut mit dem Namen „Abteilung“, dessen gespeicherter Wert auf ein Objekt in einer separaten Tabelle „Abteilungen“ verweist, würden Sie beispielsweise „Abteilungen.Name“ auswählen. Die unterstützten Verweistabellen und Felder für die primäre ID für eine bestimmte Anwendung sind vorkonfiguriert und können derzeit nicht im Azure-Portal, aber mit der [Microsoft Graph-API](/graph/api/resources/synchronization-configure-with-custom-target-attributes) bearbeitet werden.

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Bereitstellen eines benutzerdefinierten Erweiterungsattributs für eine SCIM-konforme Anwendung
Die SCIM-RFC definiert ein zentrales Benutzer- und Gruppenschema, erlaubt aber gleichzeitig die Erweiterung des Schemas, um spezifische Anwendungsanforderungen zu erfüllen. So fügen Sie einer SCIM-Anwendung ein benutzerdefiniertes Attribut hinzu
   1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com) an, wählen Sie **Unternehmensanwendungen**, anschließend Ihre Anwendung und dann **Bereitstellung** aus.
   2. Wählen Sie unter **Zuordnungen** das Objekt (Benutzer oder Gruppe) aus, für das Sie ein benutzerdefiniertes Attribut hinzufügen möchten.
   3. Wählen Sie am unteren Rand der Seite die Option **Erweiterte Optionen anzeigen** aus.
   4. Wählen Sie **Attributliste für Anwendungsname bearbeiten** aus.
   5. Geben Sie unten in der Attributliste Informationen zum benutzerdefinierten Attribut in den angezeigten Feldern ein. Wählen Sie dann **Attribut hinzufügen** aus.

Für SCIM-Anwendungen muss der Attributname dem Muster im folgenden Beispiel folgen. „CustomExtensionName“ und „CustomAttribute“ können gemäß den Anforderungen Ihrer Anwendung angepasst werden. Beispiel: urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 

Diese Anweisungen gelten nur für SCIM-konforme Anwendungen. Anwendungen wie ServiceNow und Salesforce sind nicht per SCIM mit Azure AD integriert. Aus diesem Grund benötigen sie nicht diesen spezifischen Namespace, wenn ein benutzerdefiniertes Attribut hinzugefügt wird.

Benutzerdefinierte Attribute können keine referenziellen Attribute, komplexe Attribute oder Attribute mit mehreren Werten sein. Benutzerdefinierte Erweiterungsattribute, die komplex sind oder mehrere Werten beinhalten, werden derzeit nur für Anwendungen im Katalog unterstützt.  
 
**Beispieldarstellung eines Benutzers mit einem Erweiterungsattribut:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Bereitstellen einer Rolle für eine SCIM-App
Führen Sie die folgenden Schritte aus, um für Ihre Anwendung Rollen für einen Benutzer bereitzustellen. Beachten Sie, dass die folgende Beschreibung speziell für benutzerdefinierte SCIM-Anwendungen gilt. Verwenden Sie bei Kataloganwendungen wie Salesforce und ServiceNow die vordefinierten Rollenzuordnungen. In der folgenden Auflistung wird beschrieben, wie Sie das Attribut „AppRoleAssignments“ in das von Ihrer Anwendung erwartete Format umwandeln.

- Das Zuordnen des Attributs „appRoleAssignment“ in Azure AD zu einer Rolle in Ihrer Anwendung setzt voraus, dass Sie das Attribut mithilfe eines [Ausdrucks](../app-provisioning/functions-for-customizing-application-data.md) umwandeln. Das Attribut „appRoleAssignment“ sollte einem Rollenattribut **nicht direkt** und ohne Verwendung eines Ausdrucks für die Analyse der Rollendetails zugeordnet werden. 

- **SingleAppRoleAssignment** 
  - **Einsatzgebiete:** Verwenden Sie den Ausdruck „SingleAppRoleAssignment“, um eine einzelne Rolle für einen Benutzer bereitzustellen und die primäre Rolle anzugeben. 
  - **Vorgehensweise zur Konfiguration:** Navigieren Sie anhand der oben beschriebenen Schritte zur Seite mit den Attributzuordnungen, und verwenden Sie für die Zuordnung zum Rollenattribut den Ausdruck „SingleAppRoleAssignment“. Zur Auswahl stehen drei Rollenattribute: „roles[primary eq „True“].display“, „roles[primary eq „True“].type“ und „roles[primary eq „True“].value“. Sie können ein beliebiges Rollenattribut oder alle Attribute in Ihre Zuordnungen einbeziehen. Wenn Sie mehr als ein Attribut einbeziehen möchten, fügen Sie einfach eine neue Zuordnung hinzu und schließen das Attribut als Zielattribut ein.  
  
  ![„SingleAppRoleAssignment“ hinzufügen](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Zu beachtende Aspekte**
    - Stellen Sie sicher, dass einem Benutzer nicht mehrere Rollen zugeordnet sind. Wir können nicht garantieren, welche Rolle bereitgestellt wird.
    
  - **Beispielanforderung (POST)** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
  - **Beispielausgabe (PATCH)** 
    
   ```
   "Operations": [
   {
   "op": "Add",
   "path": "roles",
   "value": [
   {
   "value": "{\"id\":\"06b07648-ecfe-589f-9d2f-6325724a46ee\",\"value\":\"25\",\"displayName\":\"Role1234\"}"
   }
   ]
   ```  
Die PATCH- und die POST-Anforderung haben ein unterschiedliches Anforderungsformat. Um sicherzustellen, dass die PATCH- und die POST-Anforderung im selben Format gesendet werden, können Sie das Featureflag verwenden, das [hier](./application-provisioning-config-problem-scim-compatibility.md#flags-to-alter-the-scim-behavior) beschrieben ist. 

- **AppRoleAssignmentsComplex** 
  - **Einsatzgebiete:** Verwenden Sie den Ausdruck „AppRoleAssignmentsComplex“, um mehrere Rollen für einen Benutzer bereitzustellen. 
  - **Vorgehensweise zur Konfiguration:** Bearbeiten Sie die Liste der unterstützten Attribute, wie oben beschrieben, um ein neues Attribut für Rollen einzubeziehen: 
  
    ![Hinzufügen von Rollen](./media/customize-application-attributes/add-roles.png)<br>

    Verwenden Sie dann den Ausdruck „AppRoleAssignmentsComplex“, um das benutzerdefinierte Rollenattribut zuzuordnen, wie in der folgenden Abbildung gezeigt:

    ![„AppRoleAssignmentsComplex“ hinzufügen](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Zu beachtende Aspekte**
    - Alle Rollen werden mit „primary = false“ bereitgestellt.
    - Die POST-Anforderung enthält den Rollentyp. Die PATCH-Anforderung enthält keinen Typ. Wir arbeiten daran, den Typ sowohl in POST- als auch in PATCH-Anforderungen zu senden.
    
  - **Beispielausgabe** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Bereitstellen eines Attributs mit mehreren Werten
Bestimmte Attribute wie „phoneNumbers“ und „emails“ sind mehrwertige Attribute, bei denen Sie möglicherweise verschiedene Arten von Telefonnummern oder E-Mails angeben müssen. Verwenden Sie den folgenden Ausdruck für Attribute mit mehreren Werten. Mit ihm können Sie den Attributtyp angeben und diesen dem entsprechenden Azure AD-Benutzerattribut für den Wert zuordnen. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Wiederherstellen der Standardattribute und Attributzuordnungen

Sollten Sie noch einmal von vorn anfangen und Ihre vorhandenen Zuordnungen auf den Standardzustand zurücksetzen müssen, können Sie das Kontrollkästchen **Standardzuordnungen wiederherstellen** aktivieren und die Konfiguration speichern. Dadurch werden alle Zuordnungen und Bereichsfilter auf den Zustand zurückgesetzt, den sie aufweisen, wenn die Anwendung dem Azure AD-Mandanten gerade aus dem Anwendungskatalog hinzugefügt wurde.

Durch die Auswahl dieser Option wird eine erneute Synchronisierung aller Benutzer erzwungen, während der Bereitstellungsdienst ausgeführt wird.

> [!IMPORTANT]
> Es wird dringend empfohlen, vor dem Aufrufen dieser Option **Bereitstellungsstatus** auf **Aus** festzulegen.

## <a name="what-you-should-know"></a>Wichtige Informationen

- Microsoft Azure AD stellt eine effiziente Implementierung eines Synchronisierungsprozesses zur Verfügung. In einer initialisierten Umgebung werden nur Objekte, die eine Aktualisierung erfordern, während eines Synchronisierungszyklus verarbeitet.
- Das Aktualisieren von Attributzuordnungen hat Auswirkungen auf die Leistung eines Synchronisierungszyklus. Nach einer Aktualisierung der Attributzuordnungskonfiguration müssen alle verwalteten Objekte erneut ausgewertet werden.
- Es hat sich bewährt, die Anzahl der aufeinanderfolgenden Änderungen an Attributzuordnungen so gering wie möglich zu halten.
- Das Hinzufügen eines Fotoattributs, das für eine App bereitgestellt werden soll, wird derzeit nicht unterstützt, da das Format zur Synchronisierung des Fotos nicht angegeben werden kann. Sie können das Feature über [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory) anfordern.
- Das Attribut „IsSoftDeleted“ ist häufig Teil der Standardzuordnungen für eine Anwendung. „IsSoftdeleted“ kann in einem von vier Szenarien „true“ sein (der Benutzer ist außerhalb des gültigen Bereichs, weil seine Zuweisung zur Anwendung aufgehoben wurde, der Benutzer ist außerhalb des gültigen Bereichs, da er nicht dem Bereichsfilter entspricht, der Benutzer wurde in Azure AD vorläufig gelöscht, oder die Eigenschaft „AccountEnabled“ ist für den Benutzer auf „false“ festgelegt). Es wird nicht empfohlen, das IsSoftDeleted-Attribut aus den Attributzuordnungen zu entfernen.
- Der Azure AD-Bereitstellungsdienst unterstützt nicht die Bereitstellung von NULL-Werten.
- Der Primärschlüssel (in der Regel „ID“) sollte nicht als Zielattribut in den Attributzuordnungen enthalten sein. 
- Das role-Attribut muss in der Regel mithilfe eines Ausdrucks zugeordnet werden, nicht direkt. Weitere Informationen zu Rollenzuordnungen finden Sie im Abschnitt weiter oben. 
- Sie können zwar Gruppen in Ihren Zuordnungen deaktivieren, Benutzer hingegen nicht. 

## <a name="next-steps"></a>Nächste Schritte

- [Automatisieren der Bereitstellung/Bereitstellungsaufhebung von Benutzern für SaaS-Apps](user-provisioning.md)
- [Schreiben von Ausdrücken für Attributzuordnungen](functions-for-customizing-application-data.md)
- [Bereichsfilter für die Benutzerbereitstellung](define-conditional-rules-for-provisioning-user-accounts.md)
- [Verwenden von SCIM für die automatische Bereitstellung von Benutzern und Gruppen aus Azure Active Directory für Anwendungen](use-scim-to-provision-users-and-groups.md)
- [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)
