---
title: Sparen von Kosten mit einer reservierten Azure VMware Solution-Instanz
description: Es wird beschrieben, wie Sie eine reservierte Instanz für Azure VMware Solution erwerben.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: bac2497c637a301c7ce8cbc44fc6945c3ef43b06
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370677"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Sparen von Kosten mit Azure VMware Solution

Wenn Sie sich für eine reservierte Instanz von [Azure VMware Solution](introduction.md) entscheiden, sparen Sie Kosten. Der Reservierungsrabatt wird automatisch auf die ausgeführten Azure VMware Solution-Hosts angewendet, die dem Reservierungsbereich und den Reservierungsattributen entsprechen. Sie müssen einem dedizierten Host keine Reservierung zuweisen, um die Rabatte zu erhalten. Bei Erwerb reservierter Instanzen wird nur der Computebereich Ihrer Nutzung abgedeckt, und es werden Softwarelizenzierungskosten berücksichtigt. 


## <a name="purchase-restriction-considerations"></a>Überlegungen zu Kaufeinschränkungen

Reservierte Instanzen sind mit einigen Ausnahmen verfügbar.

-   **Clouds** :Reservierungen sind nur in den Regionen verfügbar, die auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) aufgeführt sind.

-   **Kontingent reicht nicht aus** : Für eine Reservierung, die einem einzelnen/gemeinsamen Abonnement zugeordnet ist, muss im Abonnement ein Hostkontingent für die neue reservierte Instanz verfügbar sein. Um dieses Problem zu beheben, können Sie eine [Anforderung zur Kontingenterhöhung erstellen](enable-azure-vmware-solution.md).

-   **Angebotsberechtigung** : Sie benötigen ein[Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) mit Microsoft.

-   **Kapazitätsbeschränkungen** : In seltenen Fällen beschränkt Azure den Erwerb neuer Reservierungen für Azure VMware Solution-Host-SKUs aufgrund geringer Kapazität in einer Region.

## <a name="buy-a-reservation"></a>Kaufen einer Reservierung

Sie können eine reservierte Instanz einer Azure VMware Solution-Hostinstanz im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) erwerben.

Bezahlen Sie die Reservierung [im Voraus oder monatlich](../cost-management-billing/reservations/prepare-buy-reservation.md).

Diese Anforderungen gelten für den Erwerb einer reservierten Instanz eines dedizierten Hosts:

-   Sie müssen in einer „Besitzer“-Rolle für mindestens ein EA-Abonnement oder ein Abonnement mit einem Satz für nutzungsbasierte Bezahlung sein.

-   Bei EA-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.

So kaufen Sie ein Instanz:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.

3. Wählen Sie **Hinzufügen** aus, um eine neue Reservierung zu erwerben, und anschließend **Azure VMware Solution**.

4. Füllen Sie die Pflichtfelder aus. Ausgeführte Azure VMware Solution-Hosts, die den ausgewählten Attributen entsprechen, führen zur Berechtigung für den Reservierungsrabatt. Die tatsächliche Anzahl Ihrer Azure VMware Solution-Hosts, die den Rabatt erhalten, hängt vom ausgewählten Bereich und der ausgewählten Menge ab.

   Wenn Sie über eine EA-Vereinbarung verfügen, können Sie die Option **Weitere hinzufügen** verwenden, um schnell weitere Instanzen hinzuzufügen. Die Option ist für andere Abonnementtypen nicht verfügbar.

   | Feld        |  Beschreibung |
   | ------------ | ------------ |
   | Subscription | Das zum Bezahlen für die Reservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Zahlungen für die Reservierung belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Microsoft-Kundenvereinbarung“ oder ein einzelnes Abonnement mit Sätzen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Die Gebühren werden vom Guthaben in Bezug auf den Mindestverbrauch abgezogen oder als Überschreitung belastet. Bei einem Abonnement mit Sätzen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung in Rechnung gestellt. |
   | `Scope`        | Der Bereich der Reservierung kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen:<br><ul><li><b>Einzelne Ressourcengruppe: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.</li><li><b>Einzelabonnement: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.</li><li><b>Gemeinsam genutzt: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für EA-Kunden ist der Abrechnungskontext die Registrierung. Für Kunden mit individuellen Abonnements mit nutzungsbasierten Tarifen handelt es sich beim Abrechnungsbereich um alle berechtigten Abonnements, die vom Kontoadministrator erstellt wurden.</li></ul>       |
   | Region       | Die Azure-Region, die durch die Reservierung abgedeckt wird   |
   | Hostgröße    | AV36    |
   | Begriff         | Ein Jahr oder drei Jahre  |
   | Menge     | Die Anzahl von Instanzen, die innerhalb der Reservierung erworben werden. Die Menge ist die Anzahl von ausgeführten Azure VMware Solution-Hosts, die den Abrechnungsrabatt erhalten können.    |

## <a name="usage-data-and-reservation-usage"></a>Nutzungsdaten und Reservierungsnutzung

Ihre Nutzung, für die der Reservierungsrabatt gilt, hat effektiv den Preis „0“. Sie können sehen, welche Azure VMware Solution-Instanz den Reservierungsrabatt für jede Reservierung erhalten hat.

Weitere Informationen zur Anzeige von Reservierungsrabatten in Nutzungsdaten:

- Informationen für EA-Kunden finden Sie unter [Grundlegendes zur Nutzung von Azure-Reservierungen für die Enterprise-Registrierung](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md).
- Für Einzelabonnements lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen für Ihr Abonnement mit nutzungsbasierter Zahlung](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Ändern einer Reservierung nach dem Kauf

Nach dem Kauf können Sie folgende Änderungen an einer Reservierung vornehmen:

-   Reservierungsumfang aktualisieren

-   Instanzgrößenflexibilität (sofern zutreffend)

-   Besitz

Sie können eine Reservierung auch in kleinere Blöcke aufteilen oder Reservierungen zusammenführen. Keine dieser Änderungen führt zu einer neuen Geschäftstransaktion, und das Enddatum der Reservierung bleibt von den Änderungen unberührt.

>[!NOTE]
>Nachdem Sie Ihre Reservierung erworben haben, können Sie die folgenden Arten von Änderungen nicht mehr direkt vornehmen:
>
> - Die Region einer vorhandenen Reservierung
> - SKU
> - Menge
> - Duration
>
>Sie können jedoch eine Reservierung *umtauschen* , wenn Sie Änderungen vornehmen möchten.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).