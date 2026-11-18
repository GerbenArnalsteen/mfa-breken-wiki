---
title: "De vier paden"
nav_order: 5
has_children: true
---

# De vier paden

De opdracht somt dertien losse items op. Methoden met dezelfde zwakte delen één aanvalstechniek, dus groepeerden we het werk in vier paden.

| Pad | Wat het test | Hoofdtooling | Status |
|---|---|---|---|
| [A — AiTM via de browser]({{ '/paden/pad-a/' | relative_url }}) | SMS, software-OTP, hardware-OTP, push | Evilginx3 | Aan te vullen |
| [B — Tokendiefstal en WHFB]({{ '/paden/pad-b/' | relative_url }}) | PRT, refresh- en access-token; PIN tegenover wachtwoord | roadtx, AADInternals | Getest |
| [C — Proximity bij passkeys]({{ '/paden/pad-c/' | relative_url }}) | Passkey in Authenticator, dongle, Tenbeo | python-fido2, Wireshark | Aan te vullen |
| [D — PKI en CBA]({{ '/paden/pad-d/' | relative_url }}) | Certificate-based authentication | Certipy | Getest; documentatie aan te vullen |

Elke padpagina heeft dezelfde opbouw: kernvraag, opstelling, aanvallen, resultaten en status.
