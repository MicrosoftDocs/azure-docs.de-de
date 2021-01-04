---
title: Tutorial zum Konfigurieren von N8 Identity mit Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren des TheAccessHub-Verwaltungstools mit Azure Active Directory B2C für die Migration von Kundenkonten und die Verwaltung von Kundendienstanforderungen (Customer Service Requests, CSR).
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 337275cef0f2159cb5fac40ac0435408baf3bbef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170921"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren des TheAccessHub-Verwaltungstools mit Azure Active Directory B2C

In diesem Beispieltutorial erhalten Sie eine Anleitung zum Integrieren von Azure Active Directory (AD) B2C mit dem [TheAccessHub-Verwaltungstool](https://n8id.com/products/theaccesshub-admintool/) von N8 Identity. Diese Lösung ist für die Migration von Kundenkonten und die Verwaltung von Kundendienstanforderungen (CSR) bestimmt.  

Diese Lösung eignet sich für Sie bei folgenden Anforderungen:

- Ein Standort, der bereits vorhanden ist, soll zu Azure AD B2C migriert werden. Bei der Migration Ihrer Kundenkonten (einschließlich Kennwörter) treten jedoch Probleme auf.

- Sie benötigen ein Tool für Ihre Kundendienstanforderungen (CSR), um die Azure AD B2C-Konten verwalten zu können.

- Sie müssen aus bestimmten Gründen die delegierte Verwaltung für Ihre CSRs verwenden.

- Sie möchten Ihre Daten synchronisieren und aus vielen Repositorys in Azure AD B2C zusammenführen.

## <a name="pre-requisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](./tutorial-create-tenant.md). Der Mandant muss mit Ihrem Azure-Abonnement verknüpft sein.

- Eine Umgebung für das TheAccessHub-Verwaltungstool: Wenden Sie sich an [N8 Identity](https://n8id.com/contact/), um eine neue Umgebung bereitzustellen.

- [Optional] Verbindungs- und Anmeldeinformationen für beliebige Datenbanken oder LDAPs (Lightweight Directory Access Protocols), aus denen Sie Kundendaten migrieren möchten.

- [Optional] Eine für die Verwendung von [benutzerdefinierten Richtlinien](./custom-policy-get-started.md) konfigurierte Azure AD B2C-Umgebung, wenn Sie das TheAccessHub-Verwaltungstool in Ihren Anmelderichtlinienflow integrieren möchten.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Das TheAccessHub-Verwaltungstool wird wie jede andere Anwendung in Azure ausgeführt. Es kann entweder im Azure-Abonnement von N8 Identity oder im Abonnement des Kunden ausgeführt werden. Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Abbildung mit Diagramm der N8 Identity-Architektur](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Schritt | Beschreibung |
|:-----| :-----------|
| 1. | Der Benutzer gelangt auf eine Anmeldeseite. Der Benutzer wählt die Registrierung zum Erstellen eines neuen Kontos aus und gibt Informationen auf der Seite ein. Azure AD B2C erfasst die Benutzerattribute.
| 2. | Azure AD B2C ruft das TheAccessHub-Verwaltungstool auf und übergibt die Benutzerattribute.
| 3. | Das TheAccessHub-Verwaltungstool überprüft Ihre vorhandene Datenbank auf aktuelle Benutzerinformationen.  
| 4. | Der Benutzerdatensatz aus der Datenbank wird mit dem TheAccessHub-Verwaltungstool synchronisiert.
| 5. | Das TheAccessHub-Verwaltungstool gibt die Daten für den delegierten CSR-/Helpdesk-Administrator frei.
| 6. | Das TheAccessHub-Verwaltungstool synchronisiert die Benutzerdatensätze mit Azure AD B2C.
| 7. |Basierend auf der Erfolgs- oder Fehlerantwort des TheAccessHub-Verwaltungstools sendet Azure AD B2C eine angepasste Willkommens-E-Mail an den Benutzer.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Erstellen eines globalen Administrators in Ihrem Azure AD B2C-Mandanten

Das TheAccessHub-Verwaltungstool benötigt Berechtigungen, um im Namen eines globalen Administrators zu agieren und Benutzerinformationen zu lesen sowie Änderungen an Ihrem Azure AD B2C-Mandanten durchzuführen. Änderungen an Ihren regulären Administratoren wirken sich nicht auf die Fähigkeit des TheAccessHub-Verwaltungstools aus, mit dem Mandanten zu interagieren.

Führen Sie zum Erstellen eines globalen Administrators die folgenden Schritte aus:

1. Melden Sie sich im Azure-Portal bei Ihrem Azure AD B2C-Mandanten als Administrator an. Navigieren Sie zu **Azure Active Directory** > **Benutzer**.
2. Wählen Sie **Neuer Benutzer** aus.
3. Wählen Sie **Benutzer erstellen** aus, um einen regulären Verzeichnisbenutzer und keinen Kunden zu erstellen.
4. Füllen Sie das Formular mit den Identitätsangaben aus:

   a. Geben Sie den Benutzernamen ein, z. B. „theaccesshub“.

   b. Geben Sie den Namen ein, z. B. „TheAccessHub-Dienstkonto“.

5. Wählen Sie **Kennwort anzeigen** aus, und kopieren Sie das anfängliche Kennwort zur späteren Verwendung.
6. Weisen Sie die Rolle „Globaler Administrator“ zu:

   a. Wählen Sie die aktuelle Rolle **Benutzer** des Benutzers aus, um sie zu ändern.

   b. Suchen Sie im Datensatz nach „Globaler Administrator“.

   c. **Auswählen** > **Erstellen**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Verbinden des TheAccessHub-Verwaltungstools mit Ihrem Azure AD B2C-Mandanten

Das TheAccessHub-Verwaltungstool verwendet die Microsoft Graph-API, um Ihr Verzeichnis zu lesen und Änderungen daran vorzunehmen. Es handelt in Ihrem Mandanten als globaler Administrator. Für das TheAccessHub-Verwaltungstool sind weitere Berechtigungen erforderlich, die Sie innerhalb des Tools erteilen können.

Führen Sie die folgenden Schritte aus, um das TheAccessHub-Verwaltungstool für den Zugriff auf Ihr Verzeichnis zu autorisieren:

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Systemverwaltung** > **Azure AD B2C-Konfiguration**.

3. Wählen Sie **Verbindung autorisieren** aus.

4. Melden Sie sich im neuen Fenster mit Ihrem globalen Administratorkonto an. Sie werden möglicherweise aufgefordert, Ihr Kennwort zurückzusetzen, wenn Sie sich zum ersten Mal mit dem neuen Dienstkonto anmelden.

5. Befolgen Sie die Eingabeaufforderungen, und wählen Sie **Akzeptieren** aus, um dem TheAccessHub-Verwaltungstool die erforderlichen Berechtigungen zu erteilen.

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>Konfigurieren eines neuen CSR-Benutzers mithilfe Ihrer Unternehmensidentität

Erstellen Sie einen CSR-/Helpdesk-Benutzer, der mit den vorhandenen Unternehmensanmeldeinformationen von Azure Active Directory auf das TheAccessHub-Verwaltungstool zugreift.

Zum Konfigurieren des CSR-/Helpdesk-Benutzers für einmaliges Anmelden (Single Sign-On, SSO) werden die folgenden Schritte empfohlen:

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Managertools** > **Kollegen verwalten**.

3. Wählen Sie **Kollegen hinzufügen** aus.

4. Wählen Sie **Kollegentyp „Azure-Administrator“** aus.

5. Geben Sie die Profilinformationen des Kollegen ein.

   a. Durch die Auswahl einer eigenen Organisation wird gesteuert, wer über die Berechtigung zum Verwalten dieses Benutzers verfügt.

   b. Geben Sie als „Anmelde-ID/Azure AD-Benutzername“ den Benutzerprinzipalnamen aus dem Azure Active Directory-Konto des Benutzers an.

   c. Aktivieren Sie auf der Registerkarte „TheAccessHub-Rollen“ die verwaltete Rolle „Helpdesk“. Dadurch kann der Benutzer auf die Ansicht „Kollegen verwalten“ zugreifen. Der Benutzer muss noch einer Gruppe hinzugefügt werden oder zum Organisationsbesitzer ernannt werden, um auf Kunden reagieren zu können.

6. Wählen Sie **Senden** aus.

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>Konfigurieren eines neuen CSR-Benutzers mithilfe einer neuen Identität

Erstellen Sie einen CSR-/Helpdesk-Benutzer, der mit neuen lokalen Anmeldeinformationen (exklusiv für das TheAccessHub-Verwaltungstool) auf das TheAccessHub-Verwaltungstool zugreift. Dies wird hauptsächlich von Organisationen verwendet, die Azure AD nicht für ihr Unternehmen verwenden.

Führen Sie die folgenden Schritte aus, um einen [CSR-/Helpdesk-Benutzer](https://youtu.be/iOpOI2OpnLI) ohne einmaliges Anmelden (SSO) einzurichten:

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Managertools** > **Kollegen verwalten**.

3. Wählen Sie **Kollegen hinzufügen** aus.

4. Wählen Sie **Kollegentyp „Lokaler Administrator“** aus.

5. Geben Sie die Profilinformationen des Kollegen ein.

   a. Durch die Auswahl einer eigenen Organisation wird gesteuert, wer über die Berechtigung zum Verwalten dieses Benutzers verfügt.

   b. Wählen Sie auf der Registerkarte **TheAccessHub-Rollen** die verwaltete Rolle **Helpdesk** aus. Dadurch kann der Benutzer auf die Ansicht „Kollegen verwalten“ zugreifen. Der Benutzer muss noch einer Gruppe hinzugefügt werden oder zum Organisationsbesitzer ernannt werden, um auf Kunden reagieren zu können.

6. Kopieren Sie die Attribute **Anmelde-ID/E-Mail-Adresse** und **Einmalkennwort**. Stellen Sie diese Informationen für den neuen Benutzer bereit. Er wird sich mit diesen Anmeldeinformationen beim TheAccessHub-Verwaltungstool anmelden. Bei der ersten Anmeldung wird der Benutzer aufgefordert, ein neues Kennwort einzugeben.

7. Wählen Sie **Senden** aus.

## <a name="configure-partitioned-csr-administration"></a>Konfigurieren einer partitionierten CSR-Verwaltung

Die Berechtigungen zum Verwalten von Kunden und CSR-/Helpdesk-Benutzern im TheAccessHub-Verwaltungstool werden mithilfe einer Organisationshierarchie verwaltet. Alle Kollegen und Kunden verfügen über eine eigene Organisation, in der sie sich befinden. Als Besitzer von Organisationen können bestimmte Kollegen oder Kollegengruppen zugewiesen werden.  Organisationsbesitzer können in Organisationen oder Unterorganisationen, die sie besitzen, Kollegen und Kunden verwalten (und Änderungen vornehmen). Damit mehrere Kollegen diverse Benutzer verwalten können, kann eine Gruppe mit vielen Mitgliedern erstellt werden. Die Gruppe kann dann zum Organisationsbesitzer ernannt werden, und alle Mitglieder der Gruppe können Kollegen und Kunden in der Organisation verwalten.

### <a name="create-a-new-group"></a>Erstellen einer neuen Gruppe

1. Melden Sie sich mit den von N8 Identity bereitgestellten **Anmeldeinformationen** beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Organisation > Gruppen verwalten**.

3. Wählen Sie **Gruppe hinzufügen** aus.

4. Geben Sie einen **Gruppennamen**, eine **Gruppenbeschreibung** und einen **Gruppenbesitzer** ein.

5. Suchen Sie nach den Kollegen, die Mitglieder der Gruppe sein sollen, aktivieren Sie die zugehörigen Kontrollkästchen, und wählen Sie dann **Hinzufügen** aus.

6. Am unteren Rand der Seite werden alle Mitglieder der Gruppe angezeigt.

7. Bei Bedarf können Mitglieder auch entfernt werden. Wählen Sie dazu das **x** am Ende der jeweiligen Zeile aus.

8. Wählen Sie **Senden** aus.

### <a name="create-a-new-organization"></a>Erstellen einer neuen Organisation

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Organisation > Organisationen verwalten**.

3. Wählen Sie **Organisation hinzufügen** aus.

4. Geben Sie einen **Organisationsnamen**, **Organisationsbesitzer** und die **übergeordnete Organisation** an.

    a. Der Name der Organisation ist idealerweise ein Wert, der Ihren Kundendaten entspricht. Wenn Sie beim Laden von Kollegen- und Kundendaten den Namen der Organisation im Ladevorgang angeben, kann der Kollege automatisch in die Organisation eingefügt werden.

    b. Der Besitzer stellt die Person oder Gruppe dar, welche die Kunden und Kollegen in dieser Organisation und jeder zugehörigen Unterorganisation verwaltet.

    c. Die übergeordnete Organisation gibt an, welche andere Organisation von Natur aus ebenfalls für diese Organisation verantwortlich ist.

5. Wählen Sie **Senden** aus.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>Ändern der Hierarchie über die Strukturansicht

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Managertools** > **Strukturansicht**.

3. In dieser Darstellung können Sie sehen, welche Kollegen und Gruppen welche Organisationen verwalten können.

4. Die Hierarchien können geändert werden, indem Sie Organisationen ziehen und über den Organisationen einfügen, denen sie übergeordnet sein sollen.

5. Wählen Sie **Speichern** aus, wenn Sie die Änderung der Hierarchie abgeschlossen haben.

## <a name="customize-welcome-notification"></a>Anpassen der Willkommensnachricht

Da Sie TheAccessHub zum Migrieren der Benutzer einer vorherigen Authentifizierungslösung zu Azure AD B2C verwenden, sollten Sie die Willkommensnachricht anpassen, die bei der Migration von TheAccessHub an die Benutzer gesendet wird. Diese Nachricht enthält normalerweise den Link für den Kunden zum Festlegen eines neuen Kennworts im Azure AD B2C-Verzeichnis.

So passen Sie die Nachricht an

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen bei TheAccessHub an.

2. Navigieren Sie zu **Systemverwaltung** > **Benachrichtigungen**

3. Wählen Sie die **Vorlage „Kollegen erstellen“** aus.

4. Wählen Sie **Bearbeiten** aus.

5. Ändern Sie die Felder „Nachricht“ und „Vorlage“ nach Bedarf. Das Feld „Vorlage“ ist HTML-fähig und kann HTML-formatierte Nachrichten an E-Mail-Adressen von Benutzern senden.

6. Wählen Sie abschließend **Speichern** aus.

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Migrieren von Daten aus externen Datenquellen zu Azure AD B2C

Mithilfe des TheAccessHub-Verwaltungstools können Sie Daten aus verschiedenen Datenbanken, LDAPs und CSV-Dateien importieren und diese Daten dann per Push an Ihren Azure AD B2C-Mandanten übertragen. Dies erfolgt durch das Laden von Daten in den Kollegentyp des Azure AD B2C-Benutzers im TheAccessHub-Verwaltungstool.  Wenn nicht Azure selbst die Datenquelle ist, werden die Daten sowohl an das TheAccessHub-Verwaltungstool als auch an Azure AD B2C übertragen. Wenn es sich bei der Quelle der externen Daten nicht um eine einfache CSV-Datei auf Ihrem Computer handelt, richten Sie eine Datenquelle ein, bevor Sie die Daten laden. In den folgenden Schritten wird beschrieben, wie Sie eine Datenquelle erstellen und den Datenladevorgang ausführen.

### <a name="configure-a-new-data-source"></a>Konfigurieren einer neuen Datenquelle

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Systemverwaltung** > **Datenquellen**

3. Wählen Sie **Datenquelle hinzufügen** aus.

4. Geben Sie einen **Namen** und **Typ** für diese Datenquelle an.

5. Füllen Sie die Formulardaten entsprechend dem Datenquellentyp aus:

   **Datenbanken**

   a. **Typ**: Datenbank

   b. **Datenbanktyp**: Wählen Sie eine Datenbank aus einem der unterstützten Datenbanktypen aus.

   c. **Verbindungs-URL**: Geben Sie eine ordnungsgemäß formatierte JDBC-Verbindungszeichenfolge ein. Beispiel: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Benutzername**: Geben Sie den Benutzernamen für den Zugriff auf die Datenbank ein.

   e. **Kennwort**: Geben Sie das Kennwort für den Zugriff auf die Datenbank ein.

   f. **Abfrage**: Geben Sie die SQL-Abfrage zum Extrahieren der Kundendetails ein. Beispiel: ``SELECT * FROM mytable;``

   g. Wählen Sie **Testverbindung** aus. Es wird ein Beispiel mit Ihren Daten angezeigt, um sicherzustellen, dass die Verbindung funktioniert.

   **LDAPs**

   a. **Typ**: LDAP

   b. **Host**: Geben Sie den Hostnamen oder die IP-Adresse für den Computer ein, auf dem der LDAP-Server ausgeführt wird. Beispiel: ``mysite.com``

   c. **Port**: Geben Sie die Nummer des Ports ein, den der LDAP-Server überwacht.

   d. **SSL**: Aktivieren Sie das Kontrollkästchen, wenn das TheAccessHub-Verwaltungstool sicher über SSL mit dem LDAP-Server kommunizieren soll. Die Verwendung von SSL wird dringend empfohlen.

   e. **Anmelde-DN**: Geben Sie den DN des Benutzerkontos ein, um sich anzumelden und die LDAP-Suche auszuführen.

   f. **Kennwort**: Geben Sie das Kennwort für den Benutzer ein.

   g. **Basis-DN**: Geben Sie den DN am Anfang der Hierarchie ein, in der Sie die Suche ausführen möchten.

   h. **Filter**: Geben Sie die LDAP-Filterzeichenfolge ein, mit der Ihre Kundendatensätze abgerufen werden.

   i. **Attribute**: Geben Sie eine durch Komma getrennte Liste mit Attributen aus ihren Kundendatensätzen ein, die an das TheAccessHub-Verwaltungstool übergeben werden sollen.

   j. Wählen Sie **Testverbindung** aus. Es wird ein Beispiel mit Ihren Daten angezeigt, um sicherzustellen, dass die Verbindung funktioniert.

   **OneDrive**

   a. **Typ**: OneDrive for Business

   b. Wählen Sie **Verbindung autorisieren** aus.

   c. In einem neuen Fenster werden Sie aufgefordert, sich bei **OneDrive** anzumelden. Melden Sie sich als Benutzer mit Lesezugriff auf Ihr OneDrive-Konto an. Im Namen dieses Benutzers wird das TheAccessHub-Verwaltungstool zu ladende CSV-Dateien lesen.

   d. Befolgen Sie die Eingabeaufforderungen, und wählen Sie **Akzeptieren** aus, um dem TheAccessHub-Verwaltungstool die erforderlichen Berechtigungen zu erteilen.

6. Wählen Sie abschließend **Speichern** aus.  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Synchronisieren von Daten aus Ihrer Datenquelle mit Azure AD B2C

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Systemverwaltung** > **Datensynchronisierung**.

3. Wählen Sie **Neues Laden** aus.

4. Wählen Sie „Azure AD B2C-Benutzer“ als **Kollegentyp** aus.

5. Wählen Sie zuerst **Quelle** und dann im Popup Dialogfeld Ihre Datenquelle aus. Wenn Sie eine OneDrive-Datenquelle erstellt haben, wählen Sie auch die Datei aus.

6. Wenn Sie mit diesem Ladevorgang keine neuen Kundenkonten erstellen möchten, ändern Sie die erste Richtlinie **WENN Der Kollege wurde in TheAccessHub nicht gefunden, DANN** in **Keine Maßnahmen**.

7. Wenn Sie mit diesem Ladevorgang vorhandene Kundenkonten nicht aktualisieren möchten, ändern Sie die zweite Richtlinie **WENN Quelle und TheAccessHub-Daten stimmen nicht überein, DANN** in **Keine Maßnahmen**.

8. Wählen Sie **Weiter** aus.

9. In der **Such-/Zuordnungskonfiguration** wird definiert, wie Datensätze beim Ladevorgang mit den bereits in das TheAccessHub-Verwaltungstool geladenen Kunden in Beziehung gesetzt werden. Wählen Sie mindestens ein identifizierendes Attribut in der Quelle aus. Ordnen Sie die Attribute einem Attribut im TheAccessHub-Verwaltungstool zu, das dieselben Werte enthält. Wird eine Übereinstimmung gefunden, wird der vorhandene Datensatz überschrieben. Andernfalls wird ein neuer Kunde erstellt. Sie können eine Reihe dieser Überprüfungen sequenzieren. Sie könnten z. B. zuerst die E-Mail-Adresse überprüfen und anschließend den Vor- und Nachnamen.

10. Wählen Sie im Menü auf der linken Seite **Datenzuordnung** aus.

11. Weisen Sie in der Datenzuordnungskonfiguration zu, welche Attribute des TheAccessHub-Verwaltungstools aus Ihren Quellattributen aufgefüllt werden sollen. Es ist nicht erforderlich, alle Attribute zuzuordnen. Nicht zugeordnete Attribute bleiben bei vorhandenen Kunden unverändert erhalten.

12. Wenn Sie dem Attribut „org_name“ einen Wert zuweisen, der dem Namen einer vorhandenen Organisation entspricht, werden neu erstellte Kunden dieser Organisation zugeordnet.

13. Wählen Sie **Weiter** aus.

14. Wenn dieser Ladevorgang wiederholt werden soll, können Sie einen täglichen, wöchentlichen oder monatlichen Zeitplan angeben. Andernfalls übernehmen Sie die Standardeinstellung **Jetzt**.

15. Wählen Sie **Senden** aus.

16. Wenn Sie als Zeitplan **Jetzt** ausgewählt haben, wird dem Datensynchronisierungsbildschirm sofort ein neuer Datensatz hinzugefügt. Wenn die Überprüfungsphase 100 % erreicht hat, wählen Sie den **neuen Datensatz** aus, um das erwartete Ergebnis für den Ladevorgang anzuzeigen. Bei geplanten Ladevorgängen werden diese Datensätze nur nach der im Zeitplan festgelegten Zeit angezeigt.

17. Wenn keine Fehler vorhanden sind, wählen Sie **Ausführen** aus, um die Änderungen zu übertragen. Wählen Sie andernfalls im Menü **Weitere Informationen** die Option **Entfernen** aus, um den Ladevorgang zu entfernen. Sie können dann die Quelldaten oder Zuordnungen für den Ladevorgang korrigieren und den Vorgang wiederholen. Wenn die Anzahl der Fehler gering ist, können Sie stattdessen die Datensätze manuell aktualisieren und für jeden Datensatz **Aktualisieren** auswählen, um die Korrekturen zu übernehmen. Schließlich können Sie den Vorgang auch mit allen Fehlern fortsetzen und die Fehler später als **Supportinterventionen** im TheAccessHub-Verwaltungstool beheben.

18. Wenn der Datensatz für die **Datensynchronisierung** in der Ladephase 100 % erreicht, wurden alle Änderungen, die sich aus dem Ladevorgang ergeben, initiiert. Die Änderungen sollten in Azure AD B2C vorgenommen und den Kunden angezeigt werden.

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Synchronisieren von Azure AD B2C-Kundendaten 

Das TheAccessHub-Verwaltungstool kann einmalig oder fortlaufend alle Kundendaten von Azure AD B2C mit dem TheAccessHub-Verwaltungstool synchronisieren. Dadurch wird sichergestellt, dass CSR-/Helpdesk-Administratoren aktuelle Kundeninformationen angezeigt werden.

So synchronisieren Sie Daten von Azure AD B2C mit dem TheAccessHub-Verwaltungstool

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Systemverwaltung** > **Datensynchronisierung**.

3. Wählen Sie **Neues Laden** aus.

4. Wählen Sie „Azure AD B2C-Benutzer“ als **Kollegentyp** aus.

5. Übernehmen Sie unter **Optionen** die Standardwerte.

6. Wählen Sie **Weiter** aus.

7. Übernehmen Sie unter **Datenzuordnung & Suche** die Standardwerte. Ausnahme: Wenn Sie dem Attribut **org_name** einen Wert zuweisen, der dem Namen einer vorhandenen Organisation entspricht, werden neu erstellte Kunden dieser Organisation zugeordnet.

8. Wählen Sie **Weiter** aus.

9. Wenn dieser Ladevorgang wiederholt werden soll, können Sie einen täglichen, wöchentlichen oder monatlichen Zeitplan angeben. Andernfalls übernehmen Sie die Standardeinstellung **Jetzt**. Wir empfehlen eine regelmäßige Synchronisierung mit Azure AD B2C.

10. Wählen Sie **Senden** aus.

11. Wenn Sie als Zeitplan **Jetzt** ausgewählt haben, wird dem Datensynchronisierungsbildschirm sofort ein neuer Datensatz hinzugefügt. Wenn die Überprüfungsphase 100 % erreicht hat, wählen Sie den neuen Datensatz aus, um das erwartete Ergebnis für den Ladevorgang anzuzeigen. Bei geplanten Ladevorgängen werden diese Datensätze nur nach der im Zeitplan festgelegten Zeit angezeigt.

12. Wenn keine Fehler vorhanden sind, wählen Sie **Ausführen** aus, um die Änderungen zu übertragen. Wählen Sie andernfalls im Menü „Weitere Informationen“ die Option **Entfernen** aus, um den Ladevorgang zu entfernen. Sie können dann die Quelldaten oder Zuordnungen für den Ladevorgang korrigieren und den Vorgang wiederholen. Wenn die Anzahl der Fehler gering ist, können Sie stattdessen die Datensätze manuell aktualisieren und für jeden Datensatz **Aktualisieren** auswählen, um die Korrekturen zu übernehmen. Schließlich können Sie den Vorgang auch mit allen Fehlern fortsetzen und die Fehler später als Supportinterventionen im TheAccessHub-Verwaltungstool beheben.

13. Wenn der Datensatz für die **Datensynchronisierung** in der Ladephase 100 % erreicht, wurden alle Änderungen, die sich aus dem Ladevorgang ergeben, initiiert.

## <a name="configure-azure-ad-b2c-policies"></a>Konfigurieren von Azure AD B2C-Richtlinien

Bei gelegentlichen Synchronisierungen sind die Möglichkeiten des TheAccessHub-Verwaltungstools begrenzt, mit Azure AD B2C auf dem aktuellen Stand zu sein. Mithilfe der API des TheAccessHub-Verwaltungstools und Azure AD B2C-Richtlinien können Sie das TheAccessHub-Verwaltungstool über Änderungen informieren, sobald sie auftreten. Für diese Lösung sind technische Kenntnisse in Bezug auf [Benutzerdefinierte Azure AD B2C-Richtlinien](./custom-policy-get-started.md) erforderlich. Im nächsten Abschnitt lernen Sie die Schritte für eine Beispielrichtlinie und ein sicheres Zertifikat kennen, um das TheAccessHub-Verwaltungstool über neue Konten in Ihren benutzerdefinierten Registrierungsrichtlinien zu benachrichtigen.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>Erstellen sicherer Anmeldeinformationen zum Aufrufen der API des TheAccessHub-Verwaltungstools

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Systemverwaltung** > **Verwaltungstools** > **API-Sicherheit**.

3. Wählen Sie **Generieren** aus.

4. Kopieren Sie das **Kennwort für das Zertifikat**.

5. Wählen Sie **Herunterladen** aus, um das Clientzertifikat abzurufen.

6. Absolvieren Sie dieses [Tutorial](./secure-rest-api.md#https-client-certificate-authentication ), um das Clientzertifikat in Azure AD B2C hinzuzufügen.

### <a name="retrieve-your-custom-policy-examples"></a>Abrufen der Beispiele für benutzerdefinierte Richtlinien

1. Melden Sie sich mit den von N8 Identity bereitgestellten Anmeldeinformationen beim TheAccessHub-Verwaltungstool an.

2. Navigieren Sie zu **Systemverwaltung** > **Verwaltungstools** > **Azure AD B2C-Richtlinien**.

3. Geben Sie Ihre Azure AD B2C-Mandantendomäne und die beiden Identity Experience Framework-IDs Ihrer Identity Experience Framework-Konfiguration an.

4. Wählen Sie **Speichern** aus.

5. Wählen Sie **Herunterladen** aus, um eine ZIP-Datei mit grundlegenden Richtlinien abzurufen, mit denen Kunden beim Registrieren im TheAccessHub-Verwaltungstool als Kunden hinzugefügt werden.

6. Absolvieren Sie dieses [Tutorial](./custom-policy-get-started.md), um mit dem Entwerfen von benutzerdefinierten Richtlinien in Azure AD B2C zu beginnen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)