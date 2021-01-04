---
title: Bekannte Probleme bei der Anwendungsbereitstellung in Azure AD
description: Hier finden Sie Informationen über bekannte Probleme bei der Arbeit mit der automatisierten Anwendungsbereitstellung in Azure AD.
author: kenwith
ms.author: kenwith
manager: celestedg
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.reviewer: arvinh
ms.openlocfilehash: e0a77e1e4757c8951756c3e41479628c293dfd8f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174831"
---
# <a name="known-issues-application-provisioning"></a>Bekannte Probleme: Bereitstellung von Anwendungen
In diesem Artikel werden bekannte Probleme behandelt, die Sie bei der App-Bereitstellung berücksichtigen sollten. Sie können Ihr Feedback zum Anwendungsbereitstellungsdienst über UserVoice angeben. Weitere Informationen finden Sie auf der [UserVoice-Seite zur Azure AD-Anwendungsbereitstellung](https://aka.ms/appprovisioningfeaturerequest). Wir beobachten UserVoice genau, damit wir den Dienst verbessern können. 

> [!NOTE]
> Hierbei handelt es sich nicht um eine vollständige Liste bekannter Probleme. Wenn Sie ein Problem kennen, das hier nicht aufgeführt wird, teilen Sie Ihr Feedback unten auf der Seite mit.

## <a name="authorization"></a>Authorization 

**Speichern ist nach erfolgreichem Verbindungstest nicht möglich**

Wenn Sie eine Verbindung erfolgreich testen können, aber anschließend keinen Speichervorgang durchführen können, haben Sie die zulässige Speicherbeschränkung für Anmeldeinformationen überschritten. Weitere Informationen finden Sie unter [Problem beim Speichern der Administratoranmeldeinformationen](./user-provisioning.md).

**Speichervorgang kann nicht ausgeführt werden**

Die Mandanten-URL, das Geheimnistoken und die Benachrichtigungs-E-Mail-Adresse müssen angegeben sein, damit der Speichervorgang ausgeführt werden kann. Sie können nicht nur eine der Informationen angeben. 

**Bereitstellungsmodus kann nicht wieder in „manuell“ geändert werden**

Nachdem Sie die Bereitstellung zum ersten Mal konfiguriert haben, werden Sie feststellen, dass der Bereitstellungsmodus von „manuell“ in „automatisch“ geändert wurde. Sie können ihn nicht wieder in „manuell“ ändern. Allerdings können Sie die Bereitstellung über die Benutzeroberfläche deaktivieren. Indem Sie die Bereitstellung über die Benutzeroberfläche deaktivieren, erzielen Sie dasselbe Ergebnis, wie durch das Festlegen der Dropdownoption auf „manuell“.  


## <a name="attribute-mappings"></a>Attributzuordnungen 

**Das Attribut „SamAccountName“ oder „userType“ ist nicht als Quellattribut verfügbar**

Die Attribute „SamAccountName“ und „userType“ sind nicht standardmäßig als Quellattribut verfügbar. Erweitern Sie Ihr Schema, um das Attribut hinzuzufügen. Sie können die Attribute zur Liste der verfügbaren Quellattribute hinzufügen, indem Sie Ihr Schema erweitern. Weitere Informationen finden Sie unter [Fehlendes Quellattribut](user-provisioning-sync-attributes-for-mapping.md). 

**Dropdownmenü für Quellattribute fehlt für die Schemaerweiterung**

Erweiterungen an Ihrem Schema fehlen manchmal im Dropdownmenü für Quellattribute in der Benutzeroberfläche. Öffnen Sie die erweiterten Einstellungen Ihrer Attributzuordnungen, und fügen Sie die Attribute manuell hinzu. Weitere Informationen finden Sie unter [Anpassen von Attributzuordnungen](customize-application-attributes.md).

**NULL-Attribut kann nicht bereitgestellt werden**

Azure AD kann derzeit keine NULL-Attribute bereitstellen. Wenn ein Attribut für das Benutzerobjekt NULL lautet, wird es übersprungen. 

**Maximale Zeichenanzahl für Ausdrücke für Attributzuordnungen**

Ausdrücke für die Attributzuordnung können über maximal 10.000 Zeichen verfügen. 


## <a name="service-issues"></a>Dienstprobleme 

**Nicht unterstützte Szenarien**

- Das Bereitstellen von Kennwörtern wird nicht unterstützt. 
- Das Bereitstellen von geschachtelten Gruppen wird nicht unterstützt. 
- Die Bereitstellung von B2C-Mandanten wird aufgrund der Größe der Mandanten nicht unterstützt. 

**Die automatische Bereitstellung ist in meiner OICD-basierten Anwendung nicht verfügbar**

Wenn Sie eine App-Registrierung erstellen, wird der entsprechende Dienstprinzipal in Unternehmens-Apps nicht für die automatische Benutzerbereitstellung aktiviert. Sie müssen entweder das Hinzufügen der App zum Katalog anfordern, wenn sie von mehreren Organisationen genutzt werden soll, oder eine zweite Nicht-Katalog-App für die Bereitstellung erstellen. 

**Das Bereitstellungsintervall ist festgelegt**

Die [Zeit](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) zwischen den Bereitstellungszyklen ist derzeit nicht konfigurierbar. 

**Änderungen werden nicht von der Ziel-App zu Azure AD migriert**

Der App-Bereitstellungsdienst erkennt keine Änderungen an externen Apps. Daher wird keine Rollbackaktion ausgeführt. Der App-Bereitstellungsdienst verlässt sich auf Änderungen, die in Azure AD vorgenommen werden. 

**Bereitstellungszyklus wird bis zum Abschluss fortgesetzt**

Beim Festlegen von `enabled = off` für die Bereitstellung oder beim Beenden wird der aktuelle Bereitstellungszyklus bis zum Abschluss fortgesetzt. Der Dienst führt keine zukünftigen Zyklen aus, bis Sie die Bereitstellung wieder aktivieren.

**Mitglied der Gruppe wird nicht bereitgestellt**

Wenn sich eine Gruppe im Gültigkeitsbereich und ein Mitglied außerhalb des Gültigkeitsbereichs befindet, wird die Gruppe bereitgestellt. Der unzulässige Benutzer wird nicht bereitgestellt. Wenn das Mitglied den Gültigkeitsbereich wieder betritt, erkennt der Dienst die Änderung nicht sofort. Dieses Problem wird durch einen Neustart der Bereitstellung behoben. Es wird empfohlen, den Dienst regelmäßig neu zu starten, um sicherzustellen, dass alle Benutzer ordnungsgemäß bereitgestellt werden.  

**Verwalter nicht bereitgestellt**

Wenn sich ein Benutzer und der entsprechende Verwalter im Gültigkeitsbereich für die Bereitstellung befinden, stellt der Dienst den Benutzer bereit und aktualisiert dann den Verwalter. Sollte sich jedoch am ersten Tag der Benutzer im Gültigkeitsbereich befinden, der Verwalter aber nicht, stellen wir den Benutzer ohne Verwalterverweis bereit. Wenn der Verwalter sich dann zu einem späteren Zeitpunkt im Gültigkeitsbereich befindet, wird der Verwalterverweis erst aktualisiert, wenn Sie die Bereitstellung neu starten und den Dienst veranlassen, alle Benutzer erneut zu bewerten. 

## <a name="next-steps"></a>Nächste Schritte
- [Funktionsweise der Bereitstellung](how-provisioning-works.md)