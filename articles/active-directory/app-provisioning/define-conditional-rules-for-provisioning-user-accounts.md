---
title: Bereitstellen von Apps mit Bereichsfiltern | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von Bereichsfiltern verhindern, dass Objekte in Apps mit automatisierter Benutzerbereitstellung bereitgestellt werden, wenn ein Objekt Ihre Geschäftsanforderungen nicht erfüllt.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kenwith
ms.openlocfilehash: 88d004836d5311fc3a971df81a3dc6e6ab605ca9
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861390"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern
In diesem Artikel wird die Verwendung von Bereichsfiltern zum Definieren attributbasierter Regeln beschrieben, die festlegen, welche Benutzer für eine Anwendung bereitgestellt werden.

## <a name="scoping-filter-use-cases"></a>Anwendungsfälle für Bereichsfilter

Durch einen Bereichsfilter kann der Azure AD-Bereitstellungsdienst (Azure Active Directory) Benutzer mit einem bestimmten Attributwert ein- oder ausschließen. Wenn Sie Benutzer beispielsweise über Azure AD für eine SaaS-Anwendung bereitstellen, die von einem Vertriebsteam genutzt wird, können Sie angeben, dass die Bereitstellung nur für Benutzer mit der Einstellung „Sales“ für das Attribut „Department“ durchgeführt werden soll.

Bereichsfilter können abhängig von der Art des Bereitstellungsconnectors unterschiedlich verwendet werden:

* **Ausgehende Bereitstellung aus Azure AD für SaaS-Anwendungen:** Wenn Azure AD das Quellsystem ist, sind [Benutzer- und Gruppenzuweisungen](../manage-apps/assign-user-or-group-access-portal.md) die gängigste Methode, um zu ermitteln, welche Benutzer im Bereich der Bereitstellung enthalten sind. Diese Zuweisungen werden auch verwendet, um das einmalige Anmelden zu ermöglichen und eine zentrale Methode für die Zugriffs- und Bereitstellungsverwaltung bereitzustellen. Bereichsfilter können optional verwendet werden – zusätzlich zu oder anstelle von Zuweisungen –, um Benutzer basierend auf Attributwerten zu filtern.

    >[!TIP]
    > Sie können die Bereitstellung auf der Grundlage von Zuweisungen für eine Unternehmensanwendung deaktivieren, indem Sie Einstellungen im Menü [Bereich](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) unter den Bereitstellungseinstellungen auf **Alle Benutzer und Gruppen synchronisieren** festlegen. 

* **Eingehende Bereitstellung aus HCM-Anwendungen für Azure AD und Active Directory:** Wenn eine [HCM-Anwendung (beispielsweise Workday)](../saas-apps/workday-tutorial.md) das Quellsystem ist, sind Bereichsfilter die Hauptmethode für die Ermittlung, welche Benutzer aus der HCM-Anwendung für Active Directory oder Azure AD bereitgestellt werden sollen.

Standardmäßig sind für Azure AD-Bereitstellungsconnectors keine attributbasierten Bereichsfilter konfiguriert. 

## <a name="scoping-filter-construction"></a>Erstellung von Bereichsfiltern

Ein Bereichsfilter enthält mindestens eine *Klausel*. Klauseln bestimmen, welche Benutzer den Bereichsfilter passieren dürfen. Hierzu werden die Attribute der einzelnen Benutzer ausgewertet. Wenn für eine Klausel also beispielsweise das state-Attribut eines Benutzers „New York“ lauten muss, werden für die Anwendung nur Benutzer aus New York bereitgestellt. 

Mit einer einzelnen Klausel wird eine einzelne Bedingung für einen einzelnen Attributwert definiert. Wenn in einem einzelnen Bereichsfilter mehrere Klauseln erstellt werden, werden sie gemeinsam mittels AND-Logik ausgewertet. Dies bedeutet, dass die Auswertung für alle Klauseln „true“ ergeben muss, damit ein Benutzer bereitgestellt werden kann.

Für eine einzelne Anwendung können außerdem mehrere Bereichsfilter erstellt werden. Mehrere Bereichsfilter werden gemeinsam mittels OR-Logik ausgewertet. Wenn die Auswertung für alle Klauseln der konfigurierten Bereichsfilter „true“ ergibt, wird der Benutzer bereitgestellt.

Die vom Azure AD-Bereitstellungsdienst verarbeiteten Benutzer oder Gruppen werden jeweils einzeln auf der Grundlage der einzelnen Bereichsfilter ausgewertet.

Als Beispiel soll der folgende Bereichsfilter dienen:

![Bereichsfilter](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Gemäß diesem Bereichsfilter müssen Benutzer die folgenden Kriterien erfüllen, damit sie bereitgestellt werden können:

* Sie müssen sich in New York befinden.
* Sie müssen in der Technikabteilung arbeiten.
* Ihre Unternehmensmitarbeiter-ID muss zwischen 1.000.000 und 2.000.000 liegen.
* Ihre Position darf nicht NULL oder leer sein.

## <a name="create-scoping-filters"></a>Erstellen von Bereichsfiltern
Bereichsfilter werden im Rahmen der Attributzuordnungen für jeden Bereitstellungsconnector für Azure AD-Benutzer konfiguriert. In der folgenden Prozedur wird davon ausgegangen, dass Sie bereits die automatische Bereitstellung für [eine der unterstützten Anwendungen](../saas-apps/tutorial-list.md) eingerichtet haben und dafür nun einen Bereichsfilter hinzufügen möchten.

### <a name="create-a-scoping-filter"></a>Erstellen eines Bereichsfilters
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu **Azure Active Directory** > **Unternehmensanwendungen** > **Alle Anwendungen**.

2. Wählen Sie die Anwendung aus, für die Sie die automatische Bereitstellung konfiguriert haben (beispielsweise „ServiceNow“).

3. Wählen Sie die Registerkarte **Bereitstellung**.

4. Wählen Sie im Abschnitt **Zuordnungen** die Zuordnung aus, für die Sie einen Bereichsfilter konfigurieren möchten (beispielsweise „Azure Active Directory-Benutzer mit ServiceNow synchronisieren“).

5. Wählen Sie das Menü **Quellobjektbereich**.

6. Wählen Sie **Bereichsfilter hinzufügen**.

7. Definieren Sie eine Klausel, indem Sie als Quelle einen **Attributnamen**, einen **Operator** und einen **Attributwert** für den Abgleich auswählen. Die folgenden Operatoren werden unterstützt:

   a. **EQUALS**: Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut exakt dem Wert der Eingabezeichenfolge entspricht (Groß-/Kleinschreibung wird berücksichtigt).

   b. **NOT EQUALS**: Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut nicht dem Wert der Eingabezeichenfolge entspricht (Groß-/Kleinschreibung wird berücksichtigt).

   c. **IS TRUE**: Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut den booleschen Wert „true“ enthält.

   d. **IS FALSE**: Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut den booleschen Wert „false“ enthält.

   e. **IS NULL**: Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut leer ist.

   f. **IS NOT NULL**: Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut nicht leer ist.

   g. **REGEX MATCH**: Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut mit einem Muster für reguläre Ausdrücke übereinstimmt. Beispiel: ([1-9][0-9]) ergibt eine Übereinstimmung mit einer beliebigen Zahl zwischen 10 und 99.

   h. **NOT REGEX MATCH**: Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut nicht mit einem Muster für reguläre Ausdrücke übereinstimmt.
   
   i. **Greater_Than.** Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut größer als der Wert ist. Der für den Bereichsfilter angegebene Wert muss eine ganze Zahl sein, und das-Attribut für den Benutzer muss ebenfalls eine ganze Zahl [0, 1, 2,...] sein. 
   
   j. **Greater_Than_OR_EQUALS.** Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut größer oder gleich als dem Wert ist. Der für den Bereichsfilter angegebene Wert muss eine ganze Zahl sein, und das-Attribut für den Benutzer muss ebenfalls eine ganze Zahl [0, 1, 2,...] sein. 
   
   k. **Includes**: Die Klausel gibt „true“ zurück, wenn das ausgewertete Attribut den Zeichenfolgewert enthält, wie [hier](/dotnet/api/system.string.contains) beschrieben. (Bei der Auswertung wird die Groß-/Kleinschreibung berücksichtigt.) 


>[!IMPORTANT] 
> - Der IsMemberOf-Filter wird aktuell nicht unterstützt.
> - EQUALS und NOT EQUALS werden für mehrwertige Attribute nicht unterstützt.

9. Optional: Wiederholen Sie die Schritte 7 und 8, um weitere Bereichsklauseln hinzuzufügen.

10. Fügen Sie unter **Bereichsfiltertitel** einen Namen für Ihren Bereichsfilter hinzu.

11. Klicken Sie auf **OK**.

12. Klicken Sie auf dem Bildschirm **Bereichsfilter** erneut auf **OK**. Optional: Wiederholen Sie die Schritte 6 bis 11, um einen weiteren Bereichsfilter hinzuzufügen.

13. Klicken Sie auf dem Bildschirm **Attributzuordnung** auf **Speichern**. 

>[!IMPORTANT] 
> Beim Speichern eines neuen Bereichsfilters wird eine neue vollständige Synchronisierung für die Anwendung ausgelöst, bei der alle Benutzer im Quellsystem für den neuen Bereichsfilter neu ausgewertet werden. Wenn ein Benutzer in der Anwendung im Bereitstellungsbereich enthalten war und sich dies ändert, wird sein Konto in der Anwendung deaktiviert (bzw. die Bereitstellung aufgehoben). Informationen zum Außerkraftsetzen dieses Standardverhaltens finden Sie unter [Überspringen des Löschens von Benutzerkonten außerhalb des gültigen Bereichs](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Allgemeine Bereichsfilter
| Zielattribut| Operator | Wert | BESCHREIBUNG|
|----|----|----|----|
|userPrincipalName|REGEX MATCH|.\*@domain.com |Alle Benutzer mit dem „userPrincipal“, der die Domäne @domain.com hat, befinden sich im Umfang der Bereitstellung.|
|userPrincipalName|NOT REGEX MATCH|.\*@domain.com|Alle Benutzer mit dem „userPrincipal“, der die Domäne @domain.com hat, befinden sich nicht im Umfang der Bereitstellung.|
|department|EQUALS|Vertrieb|Alle Benutzer der Vertriebsabteilung befinden sich im Umfang der Bereitstellung.|
|workerID|REGEX MATCH|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Alle Mitarbeiter mit workerIDs zwischen 1000000 und 2000000 befinden sich im Umfang der Bereitstellung.|

## <a name="related-articles"></a>Verwandte Artikel
* [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen](../app-provisioning/user-provisioning.md)
* [Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](../app-provisioning/customize-application-attributes.md)
* [Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory](functions-for-customizing-application-data.md)
* [Kontobereitstellungsbenachrichtigungen](../app-provisioning/user-provisioning.md)
* [Verwenden von SCIM (System for Cross-domain Identity Management) für die automatische Bereitstellung von Benutzern und Gruppen aus Azure Active Directory für Anwendungen](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)
