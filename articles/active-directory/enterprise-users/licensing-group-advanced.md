---
title: Zusätzliche Szenarios für die gruppenbasierte Lizenzierung – Azure AD | Microsoft-Dokumentation
description: Weitere Szenarien für die gruppenbasierte Azure Active Directory-Lizenzierung
services: active-directory
keywords: Azure AD-Lizenzierung
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72a28aebe71809f3e47f9cc6936366cd027a32bb
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546674"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Szenarien, Einschränkungen und bekannte Probleme mit der Verwendung von Gruppen zum Verwalten der Lizenzierung in Azure Active Directory

Verwenden Sie die folgenden Informationen und Beispiele, um ein besseres Verständnis der gruppenbasierten Lizenzierung von Azure Active Directory (Azure AD) zu entwickeln.

## <a name="usage-location"></a>Verwendungsstandort

Einige Microsoft-Dienste sind nicht an allen Standorten verfügbar. Bevor einem Benutzer eine Lizenz zugewiesen werden kann, muss der Administrator die Eigenschaft **Usage location** (Verwendungsstandort) für den Benutzer angeben. Im [Azure-Portal](https://portal.azure.com) können Sie den Verwendungsstandort unter **Benutzer** &gt; **Profil** &gt; **Einstellungen** angeben.

Bei der Gruppenlizenzzuweisung erben alle Benutzer ohne Verwendungsstandort den Standort des Verzeichnisses. Wenn sich Ihre Benutzer an mehreren Standorten befinden, sollten Sie darauf achten, diese in Ihren Benutzerressourcen richtig anzugeben, bevor Sie Gruppen mit Lizenzen Benutzer hinzufügen.

> [!NOTE]
> Bei der Zuweisung von Gruppenlizenzen wird ein vorhandener Verwendungsstandortwert für einen Benutzer nie geändert. Es wird empfohlen, die Angabe des Verwendungsstandorts als Teil des Benutzererstellungsablaufs in Azure AD (z.B. über die AAD Connect-Konfiguration) festzulegen. Hierdurch wird sichergestellt, dass das Ergebnis der Lizenzzuweisung immer korrekt ist und die Benutzer keine Dienste für Standorte empfangen, die nicht zugelassen sind.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Verwenden der gruppenbasierten Lizenzierung mithilfe von dynamischen Gruppen

Sie können die gruppenbasierte Lizenzierung mit jeder beliebigen Sicherheitsgruppe verwenden. Dies bedeutet, dass eine Kombination mit dynamischen Azure AD-Gruppen möglich ist. Dynamische Gruppen führen Regeln für Benutzerattribute aus, um Benutzer automatisch Gruppen hinzufügen und daraus zu entfernen.

Beispielsweise können Sie eine dynamische Gruppe für einen Satz mit Produkten erstellen, die Sie Benutzern zuweisen möchten. Jede Gruppe wird anhand einer Regel aufgefüllt, indem Benutzer nach ihren Attributen hinzugefügt werden, und jeder Gruppe werden die Lizenzen zugewiesen, die Sie jeweils vergeben möchten. Sie können das Attribut lokal zuweisen und mit Azure AD synchronisieren, oder Sie können das Attribut direkt in der Cloud verwalten.

Lizenzen werden dem Benutzer kurz nach dem Hinzufügen zur Gruppe zugewiesen. Wenn das Attribut geändert wird, verlässt der Benutzer die Gruppen, und die Lizenzen werden entfernt.

### <a name="example"></a>Beispiel

Stellen Sie sich beispielsweise eine lokale Identitätsverwaltungslösung vor, die entscheidet, welche Benutzer Zugriff auf Microsoft-Webdienste haben sollen. Die Lösung nutzt **extensionAttribute1** zum Speichern eines Zeichenfolgenwerts, der für die Lizenzen steht, über die Benutzer verfügen sollten. Azure AD Connect synchronisiert diesen Wert mit Azure AD.

Es kann sein, dass Benutzer eine Lizenz benötigen, eine andere Lizenz jedoch nicht, oder dass sie beide Lizenzen benötigen. Hier ist ein Beispiel angegeben, bei dem Sie Lizenzen vom Typ Office 365 Enterprise E5 und Enterprise Mobility + Security (EMS) an Benutzer in Gruppen verteilen:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: Basisdienste

![Screenshot: Office 365 Enterprise E5-Basisdienste](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: Lizenzierte Benutzer

![Screenshot: Lizenzierte Enterprise Mobility + Security-Benutzer](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Ändern Sie für dieses Beispiel einen Benutzer, und legen Sie sein extensionAttribute1 auf den Wert von `EMS;E5_baseservices;` fest, wenn der Benutzer beide Lizenzen erhalten soll. Sie können diese Änderung lokal vornehmen. Im Anschluss an die Synchronisierung der Änderung mit der Cloud wird der Benutzer automatisch beiden Gruppen hinzugefügt, und die Lizenzen werden zugewiesen.

![Screenshot: Festlegen von extensionAttribute1 für den Benutzer](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Beim Ändern der Mitgliedschaftsregel einer vorhandenen Gruppe ist Vorsicht geboten. Wenn eine Regel geändert wird, wird die Mitgliedschaft der Gruppe erneut ausgewertet, und Benutzer, für die sich keine Übereinstimmung mit der neuen Regel mehr ergibt, werden entfernt (Benutzer, die die Vorgaben der neuen Regel erfüllen, sind von diesem Prozess nicht betroffen). Für diese Benutzer werden während des Prozesses die Lizenzen entfernt, was unter Umständen zu einer Nichtverfügbarkeit des Diensts oder in einigen Fällen auch zu Datenverlust führen kann.
> 
> Wenn Sie über eine große dynamische Gruppe verfügen, von der die Lizenzzuweisung abhängig ist, sollten Sie die Überprüfung von größeren Änderungen für eine kleinere Testgruppe erwägen, bevor Sie die Änderungen auf die Hauptgruppe anwenden.

## <a name="multiple-groups-and-multiple-licenses"></a>Mehrere Gruppen und mehrere Lizenzen

Ein Benutzer kann Mitglied mehrerer Gruppen mit Lizenzen sein. Folgende Punkte sollten berücksichtigt werden:

- Mehrere Lizenzen für das gleiche Produkt können sich überlappen und führen dazu, dass alle aktivierten Dienste auf den Benutzer angewendet werden. Das folgende Beispiel zeigt zwei Lizenzierungsgruppen: *E3 base services* (E3-Basisdienste) enthält die grundlegenden Dienste, die wir zuerst für alle Benutzer bereitstellen möchten. *E3 extended services* (Erweiterte E3-Dienste) enthält zusätzliche Dienste (Sway und Planner), die nur für einige Benutzer bereitgestellt werden sollen. In diesem Beispiel wurde der Benutzer beiden Gruppen hinzugefügt:

  ![Screenshot: Aktivierte Dienste](./media/licensing-group-advanced/view-enabled-services.png)

  Ergebnis: Für den Benutzer sind sieben der zwölf Dienste im Produkt aktiviert, und es wird nur eine Lizenz dafür genutzt.

- Bei Auswahl der *E3*-Lizenz werden weitere Details angezeigt, einschließlich der Informationen dazu, welche Dienste für den Benutzer durch die Gruppenlizenzzuweisung aktiviert wurden.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Gleichzeitig bestehende direkte Lizenzen und Gruppenlizenzen

Wenn ein Benutzer eine Lizenz von einer Gruppe erbt, können Sie diese Lizenzzuweisung in den Eigenschaften des Benutzers nicht direkt entfernen oder ändern. Änderungen müssen in der Gruppe vorgenommen und dann an alle Benutzer propagiert werden.

Es ist aber möglich, dem Benutzer zusätzlich zur geerbten Lizenz die gleiche Produktlizenz direkt zuzuweisen. Sie können zusätzliche Dienste aus dem Produkt ohne Auswirkungen auf andere Benutzer nur für einen Benutzer aktivieren.

Direkt zugewiesene Lizenzen können entfernt werden und wirken sich nicht auf geerbte Lizenzen aus. Angenommen, ein Benutzer erbt eine Office 365 Enterprise E3-Lizenz von einer Gruppe.

Zuerst erbt der Benutzer die Lizenz nur von der *Basic E3-Dienste*-Gruppe, sodass sich vier Dienstpläne ergeben.

1. Wählen Sie **Zuweisen** aus, um dem Benutzer eine E3-Lizenz direkt zuzuweisen. In diesem Fall deaktivieren Sie alle Dienstpläne mit Ausnahme von Yammer Enterprise.

    Das Ergebnis ist, dass der Benutzer weiterhin nur eine Lizenz des E3-Produkts verwendet. Mit der direkten Zuweisung wird der Yammer Enterprise-Dienst aber nur für diesen Benutzer aktiviert. Sie sehen, welche Dienste jeweils durch die Gruppenmitgliedschaft und die direkte Zuweisung aktiviert sind.

1. Wenn Sie die direkte Zuweisung verwenden, sind die folgenden Vorgänge zulässig:

   - Yammer Enterprise kann direkt in der Benutzerressource deaktiviert werden. Der Umschalter **Ein/Aus** in der Abbildung war für diesen Dienst aktiviert (im Gegensatz zu den Umschaltern für die anderen Dienste). Da der Dienst direkt für den Benutzer aktiviert wurde, kann er geändert werden.
   - Außerdem können zusätzliche Dienste als Teil der direkt zugewiesenen Lizenz aktiviert werden.
   - Mit der Schaltfläche **Entfernen** kann die direkte Lizenz vom Benutzer entfernt werden. Sie können sehen, dass der Benutzer jetzt nur die geerbte Gruppenlizenz hat und nur die ursprünglichen Dienste aktiviert bleiben:

## <a name="managing-new-services-added-to-products"></a>Verwalten von neuen Diensten, die Produkten hinzugefügt werden

Wenn Microsoft einem Produktlizenzplan einen neuen Dienst hinzufügt, wird er standardmäßig in allen Gruppen aktiviert, denen Sie die Produktlizenz zugewiesen haben. Benutzer in Ihrer Organisation, die Benachrichtigungen zu Produktänderungen abonniert haben, erhalten vorab E-Mails mit der Benachrichtigung über die anstehenden neuen Dienste.

Als Administrator können Sie alle Gruppen überprüfen, die von der Änderung betroffen sind, und Maßnahmen ergreifen, z.B. das Deaktivieren des neuen Diensts in jeder Gruppe. Wenn Sie beispielsweise Gruppen erstellt haben, die nur auf bestimmte Dienste für die Bereitstellung abzielen, können Sie erneut auf diese Gruppen zugreifen und sicherstellen, dass alle neu hinzugefügten Dienste deaktiviert sind.

Hier ist ein Beispiel dafür angegeben, wie dieser Prozess aussehen kann:

1. Ursprünglich haben Sie das Produkt *Office 365 Enterprise E5* mehreren Gruppen zugewiesen. Eine dieser Gruppen mit dem Namen *O365 E5 – Exchange only* wurde dafür ausgelegt, für die jeweiligen Mitglieder nur *Exchange Online (Plan 2)* zu aktivieren.

2. Sie haben eine Benachrichtigung von Microsoft erhalten, dass das E5-Produkt um den neuen Dienst *Microsoft Stream* erweitert wird. Wenn der Dienst in Ihrer Organisation verfügbar ist, können Sie Folgendes ausführen:

3. Navigieren Sie zum Blatt [**Azure Active Directory > Lizenzen > Alle Produkte**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products), und wählen Sie *Office 365 Enterprise E5* und dann **Lizenzierte Gruppen**, um eine Liste mit allen Gruppen mit diesem Produkt anzuzeigen.

4. Klicken Sie auf die Gruppe, die Sie überprüfen möchten (hier *O365 E5 – Exchange only*). Die Registerkarte **Lizenzen** wird geöffnet. Wenn Sie auf die E5-Lizenz klicken, wird ein Blatt geöffnet, auf dem alle aktivierten Dienste aufgeführt sind.
   > [!NOTE]
   > Der Dienst *Microsoft Stream* wurde dieser Gruppe zusätzlich zum Dienst *Exchange Online* automatisch hinzugefügt und aktiviert:

   ![Screenshot mit dem neuen Dienst, der einer Gruppenlizenz hinzugefügt wurde](./media/licensing-group-advanced/manage-new-services.png)

5. Wenn Sie den neuen Dienst in dieser Gruppe deaktivieren möchten, können Sie neben dem Dienst auf den Umschalter **Ein/Aus** und dann auf die Schaltfläche **Speichern** klicken, um die Änderung zu bestätigen. Azure AD verarbeitet jetzt alle Benutzer in der Gruppe, um die Änderung anzuwenden. Für alle neuen Benutzer, die der Gruppe hinzugefügt werden, ist der Dienst *Microsoft Stream* nicht aktiviert.

   > [!NOTE]
   > Es kann sein, dass der Dienst für Benutzer aufgrund einer anderen Lizenzzuweisung aktiviert ist (eine andere Gruppe, in der Benutzer Mitglied sind, oder eine direkte Lizenzzuweisung).

6. Führen Sie diese Schritte bei Bedarf für andere Gruppen aus, denen dieses Produkt zugewiesen ist.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Verwenden von PowerShell, um anzuzeigen, wer über geerbte direkte Lizenzen verfügt
Sie können ein PowerShell-Skript verwenden, um zu prüfen, ob Benutzern eine Lizenz direkt zugewiesen oder ob sie von einer Gruppe geerbt wurde.

1. Führen Sie das Cmdlet `connect-msolservice` aus, um die Authentifizierung durchzuführen und eine Verbindung mit Ihrer Organisation herzustellen.

2. `Get-MsolAccountSku` kann verwendet werden, um alle bereitgestellten Produktlizenzen in der Azure AD-Organisation zu ermitteln.

   ![Screenshot: Get-Msolaccountsku-Cmdlet](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Verwenden Sie den Wert *AccountSkuId* für die gewünschte Lizenz mit [diesem PowerShell-Skript](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Es wird eine Liste mit Benutzern, die über diese Lizenz verfügen, und mit den Informationen zur Art der Lizenzzuweisung angegeben.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Verwenden von Überwachungsprotokollen zum Überwachen von gruppenbasierten Lizenzierungsaktivitäten

Sie können [Azure AD-Überwachungsprotokolle](../reports-monitoring/concept-audit-logs.md#audit-logs) verwenden, um alle Aktivitäten für die gruppenbasierte Lizenzierung anzuzeigen, z. B.:
- Welche Benutzer Lizenzen für Gruppen geändert haben
- Wann das System mit dem Verarbeiten einer Gruppenlizenzänderung begonnen hat und wann der Prozess abgeschlossen wurde
- Welche Lizenzänderungen für einen Benutzer aufgrund der Gruppenlizenzzuweisung vorgenommen wurden

>[!NOTE]
> Überwachungsprotokolle sind auf den meisten Blättern im Azure Active Directory-Abschnitt des Portals verfügbar. Je nachdem, wo Sie darauf zugreifen, werden Filter ggf. vorab angewendet, um nur Aktivitäten anzuzeigen, die für den Kontext des Blatts relevant sind. Gehen Sie wie folgt vor, falls Sie nicht die erwarteten Ergebnisse sehen: Überprüfen Sie die [Filteroptionen](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs), oder greifen Sie unter [**Azure Active Directory > Aktivität > Überwachungsprotokolle**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit) auf die ungefilterten Überwachungsprotokolle zu.

### <a name="find-out-who-modified-a-group-license"></a>Ermitteln, wer eine Gruppenlizenz geändert hat

1. Legen Sie den Filter **Aktivität** auf *Set group license* (Gruppenlizenz festlegen) fest, und klicken Sie auf **Übernehmen**.
2. Die Ergebnisse enthalten alle Fälle von Lizenzen, die für Gruppen festgelegt oder geändert werden.
   >[!TIP]
   > Sie können auch den Namen der Gruppe im Filter *Ziel* eingeben, um die Ergebnisse einzugrenzen.

3. Wählen Sie in der Liste ein Element aus, um die Details zu den Änderungen anzuzeigen. Unter *Geänderte Eigenschaften* werden sowohl alte als auch neue Werte für die Lizenzzuweisung aufgeführt.

Hier ist ein Beispiel für kürzlich vorgenommene Änderungen der Gruppenlizenzen mit den dazugehörigen Details angegeben:

![Screenshot: Seite „Überwachungsprotokolle“ mit ausgewähltem Listenelement und geöffnetem Bereich „Aktivitätsdetails: Überwachungsprotokoll“](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Ermitteln, wann die Verarbeitung der Gruppenänderungen begonnen und geendet hat

Wenn sich für eine Gruppe eine Lizenz ändert, beginnt Azure AD damit, die Änderungen auf alle Benutzer anzuwenden.

1. Um anzuzeigen, wann die Verarbeitung für die Gruppen gestartet wurde, legen Sie den Filter **Aktivität** auf *Start applying group based license to users* (Zuweisung der gruppenbasierten Lizenz an Benutzer starten) fest. Beachten Sie, dass der Akteur für den Vorgang *Microsoft Azure AD Group-Based Licensing* (Gruppenbasierte Microsoft Azure AD-Lizenzierung) lautet. Dies ist ein Systemkonto, das verwendet wird, um alle Gruppenlizenzänderungen durchzuführen.
   >[!TIP]
   > Klicken Sie auf einen Eintrag der Liste, um das Feld *Geänderte Eigenschaften* anzuzeigen. Darin werden die Lizenzänderungen angezeigt, die für die Verarbeitung ausgewählt wurden. Dies ist nützlich, wenn Sie an einer Gruppe mehrere Änderungen vorgenommen haben und nicht sicher sind, welche verarbeitet wurden.

2. Verwenden Sie entsprechend den Filterwert *Finish applying group based license to users* (Gruppenbasierte Lizenzzuweisung zu Benutzern fertig stellen), um anzuzeigen, wann die Verarbeitung für die Gruppen beendet wurde.
   > [!TIP]
   > In diesem Fall enthält das Feld *Geänderte Eigenschaften* eine Zusammenfassung der Ergebnisse. Dies ist nützlich, um schnell zu prüfen, ob die Verarbeitung zu Fehlern geführt hat. Beispielausgabe:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Legen Sie die folgenden Filter fest, um das vollständige Protokoll zur Verarbeitung einer Gruppe mit allen Benutzeränderungen anzuzeigen:
   - **Initiiert von (Akteur)** : „Gruppenbasierte Microsoft Azure AD-Lizenzierung“
   - **Datumsbereich** (optional): Benutzerdefinierter Bereich für Verarbeitungsstart und -ende einer bestimmten Gruppe

Diese Beispielausgabe zeigt den Start der Verarbeitung, alle sich ergebenden Benutzeränderungen und das Ende der Verarbeitung.

![Screenshot: Änderungen der Gruppenlizenzen](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Wenn Sie auf die Elemente von *Change user license* (Benutzerlizenz ändern) klicken, werden Details für Lizenzänderungen angezeigt, die auf die einzelnen Benutzer angewendet wurden.

## <a name="deleting-a-group-with-an-assigned-license"></a>Löschen einer Gruppe mit einer zugewiesenen Lizenz

Es ist nicht möglich, eine Gruppe mit einer aktiven zugewiesenen Lizenz zu löschen. Ein Administrator könnte andernfalls eine Gruppe löschen und nicht bemerken, dass hierdurch Lizenzen von Benutzern entfernt werden. Aus diesem Grund ist es erforderlich, dass Lizenzen zuerst aus der Gruppe entfernt werden, bevor sie gelöscht werden kann.

Beim Versuch, eine Gruppe im Azure-Portal zu löschen, kann eine Fehlerbenachrichtigung wie die folgende angezeigt werden: ![Screenshot „Fehler beim Löschen der Gruppe“](./media/licensing-group-advanced/groupdeletionfailed.png)

Wechseln Sie auf die Registerkarte **Lizenzen** für die Gruppe, und stellen Sie fest, ob Lizenzen zugewiesen sind. Wenn dies der Fall ist, entfernen Sie diese Lizenzen, und versuchen Sie dann erneut, die Gruppe zu löschen.

Ähnliche Fehler können beim Löschen der Gruppe über PowerShell oder die Graph-API angezeigt werden. Wenn Sie eine Gruppe verwenden, die lokal synchronisiert wird, kann Azure AD Connect ebenfalls Fehler melden, wenn die Gruppe in Azure AD nicht gelöscht werden kann. In allen diesen Fällen müssen Sie überprüfen, ob der Gruppe Lizenzen zugewiesen sind und diese zuerst entfernen.

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme

Wenn Sie die gruppenbasierte Lizenzierung verwenden, ist es ratsam, sich mit der folgenden Liste der Einschränkungen und bekannten Probleme vertraut zu machen.

- Die gruppenbasierte Lizenzierung unterstützt derzeit keine Gruppen, die andere Gruppen enthalten (geschachtelte Gruppen). Wenn Sie eine Lizenz auf eine geschachtelte Gruppe anwenden, werden die Lizenzen nur auf unmittelbare Mitglieder der obersten Gruppenebene angewendet.

- Das Feature kann nur bei Sicherheitsgruppen und Microsoft 365-Gruppen verwendet werden, für die „securityEnabled=TRUE“ gilt.

- Das [Microsoft 365 Admin Center](https://admin.microsoft.com) unterstützt derzeit keine gruppenbasierte Lizenzierung. Wenn ein Benutzer eine Lizenz aus einer Gruppe erbt, wird diese Lizenz im Office-Verwaltungsportal als normale Benutzerlizenz angezeigt. Wenn Sie versuchen, diese Lizenz zu ändern oder zu entfernen, wird im Portal eine Fehlermeldung zurückgegeben. Geerbte Gruppenlizenzen können nicht direkt für einen Benutzer geändert werden.

- Wenn Lizenzen für eine große Gruppe zugewiesen oder geändert werden (z.B. 100.000 Benutzer), kann dies die Leistung beeinträchtigen. Vor allem das Volumen der Änderungen, das von der Azure AD-Automation generiert wird, kann sich negativ auf die Leistung Ihrer Verzeichnissynchronisierung zwischen Azure AD und lokalen Systemen auswirken.

- Wenn Sie dynamische Gruppen für die Verwaltung der Benutzermitgliedschaft verwenden, vergewissern Sie sich, dass der Benutzer Mitglied der Gruppe ist. Dies ist für die Lizenzzuweisung erforderlich. Ist er kein Mitglied, [überprüfen Sie den Verarbeitungsstatus für die Mitgliedschaftsregel](groups-create-rule.md) der dynamischen Gruppe.

- In bestimmten Situationen mit hoher Auslastung kann es lange dauern, Lizenzänderungen für Gruppen oder Änderungen der Mitgliedschaft für Gruppen mit vorhandenen Lizenzen zu verarbeiten. Wenn die Verarbeitung Ihrer Änderungen für Gruppen mit einer Größe von mehr als 60.000 Benutzern länger als 24 Stunden dauert, sollten Sie ein [Supportticket erstellen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest), damit wir den Fall untersuchen können. 

- Die Automatisierung der Lizenzverwaltung reagiert nicht automatisch auf alle Typen von Änderungen in der Umgebung. Beispielsweise kann es sein, dass Sie keine verfügbaren Lizenzen mehr haben und für einige Benutzer daher ein Fehlerzustand gilt. Sie können einige direkt zugewiesene Lizenzen von anderen Benutzern entfernen, um mehr verfügbare Arbeitsplätze freizugeben. Das System reagiert aber nicht automatisch auf diese Änderung und entfernt nicht den Fehlerstatus von den Benutzern.

  Als Problemumgehung für diese Art von Einschränkungen können Sie in Azure AD zum Blatt **Gruppe** wechseln und auf **Erneut verarbeiten** klicken. Mit diesem Befehl werden alle Benutzer in dieser Gruppe verarbeitet und die Fehlerzustände nach Möglichkeit behoben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu anderen Szenarien für die gruppenbasierte Lizenzverwaltung finden Sie unter:

* [Was ist die gruppenbasierte Lizenzierung in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Zuweisen von Lizenzen zu einer Gruppe in Azure Active Directory](licensing-groups-assign.md)
* [Bestimmen und Beheben von Lizenzproblemen für eine Gruppe in Azure Active Directory](licensing-groups-resolve-problems.md)
* [Migrieren einzelner lizenzierter Benutzer zur gruppenbasierten Lizenzierung in Azure Active Directory](licensing-groups-migrate-users.md)
* [Sicheres Migrieren von Benutzern zwischen Produktlizenzen mithilfe von gruppenbasierter Lizenzierung in Azure Active Directory](licensing-groups-change-licenses.md)
