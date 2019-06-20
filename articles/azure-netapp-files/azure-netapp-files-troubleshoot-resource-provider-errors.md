---
title: Beheben von Fehlern mit Azure NetApp Files-Ressourcenanbietern | Microsoft-Dokumentation
description: Beschreibt Ursachen, Lösungen und Problemumgehungen für häufige Fehler bei Azure NetApp Files-Ressourcenanbietern.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: d4e06429aa1efec7c3301c7d0f0e7e17800fd520
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "63767651"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Beheben von Fehlern mit Azure NetApp Files-Ressourcenanbietern
In diesem Artikel werden häufige Fehler von Azure NetApp Files-Ressourcenanbietern, deren Ursachen, Lösungen und Problemumgehungen beschrieben. 

<a name="error_01"></a>***Azure Key Vault nicht konfiguriert.***   
Azure Key Vault speichert die erforderlichen Anmeldeinformationen für den Zugriff auf die zugrunde liegende API. Dieser Fehler zeigt an, dass Azure Key Vault nicht die vollständigen Anmeldeinformationen für den Zugriff auf die zugrunde liegende API erhalten hat.

* Ursache  
Azure Key Vault hat nicht die richtigen Anmeldeinformationen erhalten, oder die Anmeldeinformationen sind unvollständig.  

* Lösung   
Der Azure NetApp Files-Dienst verwendet Azure Key Vault. Azure Key Vault führt die Authentifizierung mithilfe eines Azure Active Directory-Tokens durch. Der Besitzer der Anwendung muss daher zunächst die Anwendung in Azure Active Directory registrieren.

* Problemumgehung   
None (Keine):  Azure Key Vault muss für die Verwendung von Azure NetApp Files ordnungsgemäß eingerichtet sein.  

<a name="error_02"></a>***Erstellungstoken können nicht geändert werden.***   
Dieser Fehler tritt bei dem Versuch auf, das Erstellungstoken zu ändern, nachdem das Volume erstellt wurde.
Das Erstellungstoken muss beim Erstellen des Volumes festgelegt werden und kann später nicht mehr geändert werden.

* Ursache   
Sie versuchen, das Erstellungstoken nach dem Erstellen des Volumes zu ändern. Dies ist kein unterstützter Vorgang.

* Lösung   
Nachdem das Volume erstellt wurde, können Sie den Parameter aus der Anforderung entfernen, um die Fehlermeldung zu schließen.

* Problemumgehung   
Wenn Sie das Erstellungstoken ändern müssen, können Sie ein neues Volume mit einem neuen Erstellungstoken erstellen und die Daten dann auf das neue Volume migrieren.


<a name="error_03"></a>***Erstellungstoken müssen mindestens 16 Zeichen umfassen.***   
Dieser Fehler tritt auf, wenn das Erstellungstoken nicht der erforderlichen Länge entspricht. Die Länge des Erstellungstokens muss mindestens 16 Zeichen umfassen.

* Ursache   
Das Erstellungstoken entspricht nicht der erforderlichen Länge.  Wenn Sie ein Volume über die API erstellen, ist ein Erstellungstoken erforderlich. Wenn Sie das Portal verwenden, kann das Token automatisch generiert werden.

* Lösung   
Erhöhen Sie die Länge des Erstellungstokens. Sie können z. B. ein anderes Wort am Anfang oder am Ende des Erstellungstokens hinzufügen.

* Problemumgehung   
Die Mindestlänge des Erstellungstokens kann nicht umgangen werden.  Sie können die Länge der Erstellungstoken mit einem Präfix oder Suffix erweitern.


<a name="error_04"></a>***Fehler beim Löschen eines Volumes, das bei Azure NetApp Files nicht gefunden wurde.***   
Dieser Fehler ist aufgetreten, da die interne Registrierung von Ressourcen nicht synchronisiert ist.

* Ursache   
Das Volume kann nach dem Löschen noch einige Zeit im Portal angezeigt werden. Wenn Sie das Volume über die API löschen, wurde das Volume möglicherweise nicht ordnungsgemäß angegeben. Der Fehler kann auch durch veralteten Browsercache verursacht werden.

* Lösung   
Leeren Sie den Browsercache, wenn Sie das Portal verwenden. Es gibt auch einen internen Cache, der alle 10 Minuten aktualisiert wird.  Sie können erneut versuchen, den Inhalt des Caches zu löschen.  Wenn das Problem nach 10 Minuten weiterhin besteht, können Sie ein Supportticket erstellen.

* Problemumgehung   
Verwenden Sie in der Zwischenzeit ein anderes Volume und ignorieren Sie das vorhandene.


<a name="error_05"></a>***Fehler beim Einfügen eines neuen Volumes, das bei Azure NetApp Files gefunden wurde.***   
Dieser Fehler tritt auf, da die interne Registrierung von Ressourcen nicht synchronisiert ist.

* Ursache   
Das Volume wird nach dem Löschen möglicherweise noch einige Zeit im Portal angezeigt. Wenn Sie das Volume über die API löschen, wurde das Volume möglicherweise nicht ordnungsgemäß angegeben.

* Lösung   
Wenn Sie das Portal verwenden, wurde das Volume bereits erstellt.  Das Volume sollte automatisch angezeigt werden. Wenn das Problem weiterhin besteht, können Sie ein Supportticket erstellen.

* Problemumgehung   
Sie können ein Volume mit einem anderen Namen und einem anderen Erstellungstoken erstellen.


<a name="error_06"></a>***Der Dateipfadname darf nur Buchstaben, Zahlen und Bindestriche („-“) enthalten.***   
Dieser Fehler tritt auf, wenn der Dateipfad nicht unterstützte Zeichen enthält, z. B. einen Punkt („.“), ein Komma („,“), einen Unterstrich („\_“) oder ein Dollarzeichen („$“).

* Ursache   
Dieser Dateipfad enthält nicht unterstützte Zeichen, z. B. einen Punkt („.“), ein Komma („,“), einen Unterstrich („\_“) oder ein Dollarzeichen („$“).

* Lösung   
Entfernen Sie Zeichen, die keine alphabetischen Buchstaben, Zahlen oder Bindestriche („-“) sind, aus dem von Ihnen angegebenen Dateipfad.

* Problemumgehung   
Sie können einen Unterstrich durch einen Bindestrich ersetzen oder Großbuchstaben anstelle von Leerzeichen verwenden, um den Anfang neuer Wörter anzuzeigen (z. B. „NeuesVolume“ anstelle von „neues Volume“).


<a name="error_07"></a>***Volume-ID kann nicht geändert werden.***   
Dieser Fehler tritt auf, wenn Sie versuchen, die Volume-ID zu ändern.  Das Ändern der Volume-ID ist kein unterstützter Vorgang.

* Ursache   
Die ID des Dateisystems wird beim Erstellen des Volumes festgelegt. Die Volume-ID kann nachträglich nicht mehr geändert werden.

* Lösung   
None (Keine):

* Problemumgehung   
None (Keine):  Die Volume-ID wird beim Erstellen des Volumes generiert und kann nachträglich nicht mehr geändert werden.


<a name="error_08"></a>***Ein ungültiger Wert '{0}' wurde für {1} empfangen.***   
Diese Meldung zeigt einen Fehler in den Feldern für „RuleIndex“, „AllowedClients“, „UnixReadOnly“, „UnixReadWrite“, „Nfsv3“ und „Nfsv4“ an.

* Ursache   
Bei mindestens einem der folgenden Felder ist bei der Eingabeüberprüfungsanforderung ein Fehler aufgetreten: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 und Nfsv4.

* Lösung   
Stellen Sie sicher, dass Sie alle erforderlichen und konfliktfreien Parameter über die Befehlszeile festlegen. Sie können z. B. nicht gleichzeitig die Parameter „UnixReadOnly“ und „UnixReadWrite“ festlegen.

* Problemumgehung   
Weitere Informationen finden Sie im Abschnitt „Lösung“.  


<a name="error_09"></a>***Fehlender Wert für '{0}'.***   
Dieser Fehler zeigt an, dass in der Anforderung für mindestens einen der folgenden Parameter ein erforderliches Attribut fehlt: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 und Nfsv4.

* Ursache   
Bei mindestens einem der folgenden Felder ist bei der Eingabeüberprüfungsanforderung ein Fehler aufgetreten: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 und Nfsv4.

* Lösung   
Stellen Sie sicher, dass Sie alle erforderlichen und konfliktfreien Parameter über die Befehlszeile festlegen. Sie können z. B. nicht gleichzeitig die Parameter „UnixReadOnly“ und „UnixReadWrite“ festlegen.

* Problemumgehung   
Weitere Informationen finden Sie im Abschnitt „Lösung“.  


<a name="error_10"></a> ***{0} wird bereits verwendet.***   
Dieser Fehler zeigt an, dass der Name der Ressource bereits verwendet wurde.

* Ursache   
Sie versuchen, ein Volume mit einem Namen zu erstellen, der mit einem vorhandenen Volume identisch ist.

* Lösung   
Verwenden Sie beim Erstellen eines Volumes einen eindeutigen Namen.

* Problemumgehung   
Bei Bedarf können Sie den Namen des vorhandenen Volumes ändern, sodass das neue Volume den vorgesehenen Namen verwenden kann.


<a name="error_11"></a> ***{0} ist zu kurz.***   
Dieser Fehler zeigt an, dass der Volumenname nicht der erforderlichen Mindestlänge entspricht.

* Ursache   
Der Name des Volumes ist zu kurz.

* Lösung   
Erweitern Sie die Länge des Volumennamens.  

* Problemumgehung   
Sie können dem Volumennamen ein allgemeines Präfix oder Suffix hinzufügen.


<a name="error_12"></a>***Azure NetApp Files-API nicht erreichbar.***   
Die Azure API basiert zur Verwaltung von Volumes auf der Azure NetApp Files-API.  Dieser Fehler zeigt ein Problem mit der API-Verbindung an.

* Ursache   
Die zugrunde liegende API antwortet nicht, was zu einem internen Fehler führt. Dieser Fehler ist wahrscheinlich nur temporär.

* Lösung   
Das Problem ist wahrscheinlich nur temporär.  Die Anforderung sollte nach einiger Zeit erfolgreich durchgeführt werden.

* Problemumgehung   
None (Keine): Die zugrunde liegende API ist für die Volumenverwaltung unerlässlich.  


<a name="error_13"></a>***Keine Anmeldeinformationen gefunden für Abonnement '{0}'.***   
Dieser Fehler zeigt an, dass die angegebenen Anmeldeinformationen entweder ungültig sind oder im Abonnement nicht ordnungsgemäß festgelegt wurden.

* Ursache   
Ungültige oder falsch festgelegte Anmeldeinformationen verhindern den Zugriff auf den Dienst zum Verwalten von Volumes.

* Lösung   
Stellen Sie sicher, dass die Anmeldeinformationen auf der Befehlszeile ordnungsgemäß festgelegt und eingegeben wurden.

* Problemumgehung   
None (Keine):  Die ordnungsgemäße Einstellung der Anmeldeinformationen ist für die Verwendung von Azure NetApp Files von entscheidender Bedeutung.  


<a name="error_14"></a>***Keine Vorgangsergebnis-ID gefunden für '{0}'.***   
Dieser Fehler zeigt an, dass ein interner Fehler den Abschluss des Vorgangs verhindert.

* Ursache   
Ein interner Fehler ist aufgetreten und hat verhindert, dass der Vorgang abgeschlossen wurde.

* Lösung   
Dieser Fehler ist wahrscheinlich nur temporär.  Warten Sie einige Minuten, und versuchen Sie erneut. Wenn das Problem weiterhin besteht, erstellen Sie ein Ticket, damit der technische Support das Problem untersuchen kann.

* Problemumgehung   
Warten Sie einige Minuten und überprüfen Sie, ob das Problem weiterhin besteht.


<a name="error_15"></a>***Vorgang '{0}' wird nicht unterstützt.***   
Dieser Fehler zeigt an, dass der Befehl für das aktive Abonnement oder die aktive Ressource nicht verfügbar ist.

* Ursache   
Der Vorgang ist für das Abonnement oder die Ressource nicht verfügbar.

* Lösung   
Stellen Sie sicher, dass der Befehl ordnungsgemäß eingegeben wurde und für die Ressource und das Abonnement, das Sie verwenden, verfügbar ist.

* Problemumgehung   
Weitere Informationen finden Sie im Abschnitt „Lösung“.  


<a name="error_16"></a>***Patchvorgang wird für diesen Ressourcentyp nicht unterstützt.***   
Dieser Fehler tritt auf, wenn Sie versuchen, das Einbindungsziel oder die Momentaufnahme zu ändern.

* Ursache   
Das Einbindungsziel wird bereits beim Erstellen definiert und kann nachträglich nicht mehr geändert werden.

* Lösung   
None (Keine):  Das Einbindungsziel kann nach der Erstellung des Volumes nicht mehr geändert werden.

* Problemumgehung   
None (Keine):


<a name="error_17"></a>***Wert für schreibgeschützte Eigenschaft '{0}' empfangen.***   
Dieser Fehler tritt auf, wenn Sie einen Wert für eine Eigenschaft definieren, der nicht geändert werden kann. So können Sie z. B. die Volume-ID nicht ändern.

* Ursache   
Sie haben versucht, einen Parameter (z. B. die Volume-ID) zu ändern, der nicht geändert werden kann.

* Lösung   
None (Keine): Der Parameter für die Volume-ID kann nicht geändert werden.

* Problemumgehung   
Die Volume-ID darf keine Änderung erfordern.  Daher ist eine Problemumgehung nicht erforderlich.

<a name="error_18"></a>***Die angeforderte {0} wurde nicht gefunden.***   
Dieser Fehler tritt auf, wenn Sie versuchen, auf eine nicht vorhandene Ressource zu verweisen, z. B. ein Volume oder eine Momentaufnahme. Die Ressource wurde möglicherweise gelöscht oder weist einen falsch geschriebenen Ressourcennamen auf.

* Ursache   
Sie versuchen, auf eine nicht vorhandene Ressource (z. B. ein Volume oder eine Momentaufnahme) zu verweisen, die bereits gelöscht wurde oder einen falsch geschriebenen Ressourcennamen aufweist.

* Lösung   
Überprüfen Sie die Anforderung auf Rechtschreibfehler, um sicherzustellen, dass sie ordnungsgemäß referenziert wird.

* Problemumgehung   
Weitere Informationen finden Sie im Abschnitt „Lösung“.

<a name="error_19"></a>***Anmeldeinformationen für Abonnement '{0}' können nicht abgerufen werden.***   
Dieser Fehler zeigt an, dass die angegebenen Anmeldeinformationen entweder ungültig sind oder im Abonnement nicht ordnungsgemäß festgelegt wurden.

* Ursache   
Im Abonnement ungültige oder falsch festgelegte Anmeldeinformationen verhindern den Zugriff auf den Dienst zum Verwalten von Volumes.

* Lösung   
Stellen Sie sicher, dass die Anmeldeinformationen auf der Befehlszeile ordnungsgemäß festgelegt und eingegeben wurden.

* Problemumgehung   
None (Keine):  Ordnungsgemäß festgelegte Anmeldeinformationen sind für die Verwendung von Azure NetApp Files von entscheidender Bedeutung.

<a name="error_20"></a>***Unbekannter Azure NetApp Files-Fehler.***   
Die Azure API basiert zur Verwaltung von Volumes auf der Azure NetApp Files-API. Der Fehler zeigt ein Problem bei der Kommunikation mit der API an.

* Ursache   
Die zugrunde liegende API sendet einen unbekannten Fehler.  Dieser Fehler ist wahrscheinlich nur temporär.

* Lösung   
Das Problem ist wahrscheinlich nur temporär, und die Anforderung sollte nach einiger Zeit erfolgreich durchgeführt werden. Wenn das Problem weiterhin besteht, erstellen Sie ein Supportticket, um das Problem untersuchen zu lassen.

* Problemumgehung   
None (Keine):  Die zugrunde liegende API ist für die Volumenverwaltung unerlässlich.

<a name="error_21"></a>***Wert empfangen für unbekannte Eigenschaft '{0}'.***   
Dieser Fehler tritt auf, wenn nicht vorhandene Eigenschaften für eine Ressource wie Volume, Momentaufnahme oder Einbindungsziel bereitgestellt werden.

* Ursache   
Die Anforderung verfügt über eine Reihe von Eigenschaften, die für die einzelnen Ressourcen verwendet werden können.  Sie können keine nicht vorhandenen Eigenschaften in die Anforderung aufnehmen.

* Lösung   
Stellen Sie sicher, dass alle Eigenschaftsnamen richtig geschrieben und die Eigenschaften für das Abonnement und die Ressource verfügbar sind.

* Problemumgehung   
Verringern Sie die Anzahl der in der Anforderung definierten Eigenschaften, um die Eigenschaft zu beseitigen, die den Fehler verursacht.


<a name="error_22"></a>***Aktualisierungsvorgang wird für diesen Ressourcentyp nicht unterstützt.***   
Es können nur Volumes aktualisiert werden. Dieser Fehler tritt auf, wenn Sie versuchen, einen nicht unterstützten Aktualisierungsvorgang durchzuführen, z. B. das Aktualisieren einer Momentaufnahme.

* Ursache   
Die Ressource, die Sie zu aktualisieren versuchen, unterstützt den Aktualisierungsvorgang nicht.  Lediglich die Eigenschaften von Volumes können geändert werden.

* Lösung   
None (Keine):  Die Ressource, die Sie zu aktualisieren versuchen, unterstützt den Aktualisierungsvorgang nicht. Somit kann sie nicht geändert werden.

* Problemumgehung   
Erstellen Sie für ein Volume eine neue Ressource mit vorhandenem Update, und migrieren Sie die Daten.


<a name="error_23"></a>***Anzahl der Elemente: {0} für Objekt: {1} liegt außerhalb des Min-Max-Bereichs.***   
Dieser Fehler tritt auf, wenn die Regeln der Exportrichtlinien nicht den Anforderungen an den minimalen oder maximalen Bereich entsprechen.  Wenn Sie die Exportrichtlinie definieren, muss sie mindestens über eine Exportrichtlinienregel und darf maximal über fünf Exportrichtlinienregeln verfügen.

* Ursache   
Die von Ihnen definierte Exportrichtlinie entspricht nicht dem erforderlichen Bereich.  

* Lösung   
Stellen Sie sicher, dass der Index nicht bereits verwendet wird und im Bereich von 1 bis 5 liegt.

* Problemumgehung   
Es ist nicht zwingend erforderlich, die Exportrichtlinien auf die Volumes anzuwenden. Daher können Sie die Exportrichtlinie vollständig auslassen, wenn Sie keine Exportrichtlinienregeln benötigen.


<a name="error_24"></a>***Doppelter Wertfehler für Objekt {0}.***   
Dieser Fehler tritt auf, wenn die Exportrichtlinie nicht mit einem eindeutigen Index definiert ist.  Wenn Sie Exportrichtlinien definieren, müssen alle Exportrichtlinienregeln einen eindeutigen Index zwischen 1 und 5 aufweisen.

* Ursache   
Die definierte Exportrichtlinie erfüllt nicht die Anforderungen an die Exportrichtlinienregeln. Sie müssen mindestens über eine und dürfen höchstens über fünf Exportrichtlinienregeln verfügen.  

* Lösung   
Stellen Sie sicher, dass der Index nicht bereits verwendet wird und im Bereich von 1 bis 5 liegt.

* Problemumgehung   
Verwenden Sie einen anderen Index für die Regel, die Sie festzulegen versuchen.


