---
title: Verwenden des E-Mail-Diensts SendGrid (Java) | Microsoft Docs
description: Erfahren Sie, wie Sie E-Mails mit dem SendGrid-E-Mail-Dienst in Azure senden. Die Codebeispiele wurden in Java geschrieben.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.custom: devx-track-java
ms.openlocfilehash: 9ff006b74b6202b02a2767aee4d853b1206ce60d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015450"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Senden von E-Mails mit SendGrid aus Java
Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in Java geschrieben. Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen**, **Verwenden von Filtern** und **Aktualisieren von Eigenschaften**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Was ist der SendGrid-E-Mail-Dienst?
SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

* Automatisches Versenden von Empfangsbestätigungen an Kunden
* Verwalten von Verteilungslisten zum Senden von monatlichen e-Flyern und speziellen Angeboten an Kunden
* Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder Kundenreaktionsfähigkeit
* Generieren von Berichten zur Identifizierung von Trends
* Weiterleiten von Kundenanfragen
* E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Erstellen eines SendGrid-Kontos
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Vorgehensweise: javax.mail-Bibliotheken verwenden
Rufen Sie die javax.mail-Bibliotheken ab, z.B. von <https://www.oracle.com/technetwork/java/javamail>, und importieren Sie sie in Ihren Code. Auf oberer Ebene dient der Prozess zur Verwendung der javax.mail-Bibliothek zum Senden von E-Mails per SMTP für Folgendes:

1. Geben Sie die SMTP-Werte einschließlich des SMTP-Servers an, welcher bei SendGrid unter smtp.sendgrid.net geführt wird.

    ```java
    import java.util.Properties;
    import javax.mail.*;
    import javax.mail.internet.*;

    public class MyEmailer {
        private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
        private static final String SMTP_AUTH_USER = "your_sendgrid_username";
        private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

        public static void main(String[] args) throws Exception{
            new MyEmailer().SendMail();
        }

        public void SendMail() throws Exception
        {
            Properties properties = new Properties();
                properties.put("mail.transport.protocol", "smtp");
                properties.put("mail.smtp.host", SMTP_HOST_NAME);
                properties.put("mail.smtp.port", 587);
                properties.put("mail.smtp.auth", "true");
                // …
    ```

1. Erweitern Sie die Klasse *javax.mail.Authenticator*. Geben Sie außerdem in der Implementierung der Methode *getPasswordAuthentication* den SendGrid-Benutzernamen und das dazugehörige Kennwort an.  

    ```java
    private class SMTPAuthenticator extends javax.mail.Authenticator {
    public PasswordAuthentication getPasswordAuthentication() {
        String username = SMTP_AUTH_USER;
        String password = SMTP_AUTH_PWD;
        return new PasswordAuthentication(username, password);
    }
    ```
2. Richten Sie eine authentifizierte E-Mail-Sitzung über ein *javax.mail.Session* -Objekt ein.  

    ```java
    Authenticator auth = new SMTPAuthenticator();
    Session mailSession = Session.getDefaultInstance(properties, auth);
    ```
3. Erstellen Sie Ihre Nachricht, und ordnen Sie die Werte für **An**, **Von**, **Betreff** und die Inhalte zu. Die entsprechende Vorgehensweise wird im Abschnitt [Erstellen einer E-Mail](#how-to-create-an-email) erläutert.
4. Versenden Sie eine Nachricht über ein *javax.mail.Transport* -Objekt. Die entsprechende Vorgehensweise wird im Abschnitt [Senden von E-Mails][#how-to-send-an-email] erläutert.

## <a name="how-to-create-an-email"></a>Erstellen einer E-Mail
Im Folgenden ist dargestellt, wie Sie Werte für eine E-Mail angeben.

```java
MimeMessage message = new MimeMessage(mailSession);
Multipart multipart = new MimeMultipart("alternative");
BodyPart part1 = new MimeBodyPart();
part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
BodyPart part2 = new MimeBodyPart();
part2.setContent(
    "<p>Hello,</p>
    <p>Your Contoso order has <b>shipped</b>.</p>
    <p>Thank you,<br>John</br></p>",
    "text/html");
multipart.addBodyPart(part1);
multipart.addBodyPart(part2);
message.setFrom(new InternetAddress("john@contoso.com"));
message.addRecipient(Message.RecipientType.TO,
    new InternetAddress("someone@example.com"));
message.setSubject("Your recent order");
message.setContent(multipart);
```

## <a name="how-to-send-an-email"></a>Vorgehensweise: Eine E-Mail senden
Im Folgenden ist dargestellt, wie Sie eine E-Mail versenden.

```java
Transport transport = mailSession.getTransport();
// Connect the transport object.
transport.connect();
// Send the message.
transport.sendMessage(message, message.getAllRecipients());
// Close the connection.
transport.close();
```

## <a name="how-to-add-an-attachment"></a>Hinzufügen eines Anhangs
Der folgende Code zeigt, wie Sie eine Anlage hinzufügen.

```java
// Local file name and path.
String attachmentName = "myfile.zip";
String attachmentPath = "c:\\myfiles\\";
MimeBodyPart attachmentPart = new MimeBodyPart();
// Specify the local file to attach.
DataSource source = new FileDataSource(attachmentPath + attachmentName);
attachmentPart.setDataHandler(new DataHandler(source));
// This example uses the local file name as the attachment name.
// They could be different if you prefer.
attachmentPart.setFileName(attachmentName);
multipart.addBodyPart(attachmentPart);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Vorgehensweise: Filter zur Aktivierung der Fußzeilen, der Nachverfolgung und der Analyse verwenden
SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von *Filtern*. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics, Abonnement-Tracking usw. zu ermöglichen. Eine vollständige Liste der Filter finden Sie unter [Filtereinstellungen][Filter Settings].

* Im Folgenden ist dargestellt, wie Sie einen Fußzeilen-Filter einfügen können. Dieser bewirkt, dass HTML-Text unten in einer zu versendenden E-Mail angezeigt wird.

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"footer\":
        {\"settings\":
        {\"enable\":1,\"text/html\":
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
    ```
* Ein weiteres Beispiel für einen Filter ist die Klickprotokollierung. Angenommen, Ihr E-Mail-Text enthält einen Link (wie etwa den folgenden), und Sie möchten die Klickrate nachverfolgen:

    ```java
    messagePart.setContent(
        "Hello,
        <p>This is the body of the message. Visit
        <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
        Thank you.",
        "text/html");
    ```
* Verwenden Sie den folgenden Code, um die Klickprotokollierung zu aktivieren:

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"clicktrack\":
        {\"settings\":
        {\"enable\":1}}}}");
    ```

## <a name="how-to-update-email-properties"></a>Vorgehensweise: E-Mail-Eigenschaften aktualisieren
Einige E-Mail-Eigenschaften können mit **set Property** überschrieben oder mit **add Property** angefügt werden.

Um z.B. die **ReplyTo**-Adressen anzugeben, können Sie folgenden Code verwenden:

```java
InternetAddress addresses[] =
    { new InternetAddress("john@contoso.com"),
        new InternetAddress("wendy@contoso.com") };

message.setReplyTo(addresses);
```

Um einen **Cc** -Empfänger hinzuzufügen, können Sie folgenden Code verwenden:

```java
message.addRecipient(Message.RecipientType.CC, new
InternetAddress("john@contoso.com"));
```

## <a name="how-to-use-additional-sendgrid-services"></a>Vorgehensweise: Zusätzliche SendGrid-Dienste verwenden
SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation][SendGrid API documentation].

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen des SendGrid-E-Mail-Dienstes vertraut gemacht haben, finden Sie unter diesen Links weitere Informationen.

* Ein Beispiel, das die Verwendung von SendGrid in einer Azure-Bereitstellung darstellt finden Sie unter [Senden von E-Mails mit SendGrid aus Java in einer Azure-Bereitstellung](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid-Java-SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid-API-Dokumentation: <https://sendgrid.com/docs/API_Reference/index.html>
* Spezielles SendGrid-Angebot für Azure-Kunden: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[cloudbasierter E-Mail-Dienst]: https://sendgrid.com/email-solutions
[transaktionale E-Mail-Übermittlung]: https://sendgrid.com/transactional-email
