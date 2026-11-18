---
title: "PB-08 TPM-lockout van de WHFB-PIN"
nav_order: 8
parent: "Playbooks"
---

# PB-08 TPM-lockout van de WHFB-PIN

| | |
|---|---|
| **Pad** | B, onderzoeksvraag |
| **Doel** | Meten wanneer en hoe de TPM vergrendelt bij foute PINs |
| **Tooling** | `Get-Tpm` |
| **Waar** | Victim met vTPM |
| **Status** | Getest |

## Stappen

1. Maak een snapshot van de victim-VM.
2. Leg de baseline vast met het commando hieronder.
3. Vergrendel het scherm en voer bewust foute PINs in. Noteer per poging het tijdstip en wat Windows toont.
4. Meld na elke reeks correct aan en voer het commando opnieuw uit.
5. Ga door tot `LockedOut: True`.
6. Wacht 10 minuten en meet opnieuw.

**Startcondities.** Victim-VM met vTPM, `28_tu_whbf`, WHFB-policy met TPM vereist, PowerShell 7 als beheerder. Datum: 17/11/2026.

```
Get-Tpm | Select-Object TpmPresent,TpmReady,LockedOut,LockoutCount,LockoutMax,LockoutHealTime
```

| Meetpunt | LockedOut | LockoutCount | LockoutMax | LockoutHealTime | Wat Windows toont |
|---|---|---|---|---|---|
| Baseline | False | 0 | 31 | 10 minutes | — |
| Na de eerste reeks foute PINs (1111, 2222, 3333, 4444, 5555) | False | 6 | 31 | 10 minutes | "You've entered an incorrect PIN too many times. To try again, restart your device." |
| Na 31 pogingen | True | 31 | 31 | 10 minutes | "Deze aanmeldingsoptie is uitgeschakeld vanwege mislukte aanmeldingspogingen (...) laat uw apparaat ten minste 10 minuten ingeschakeld." |
| Na aanmelden met het wachtwoord | True | 31 | 31 | 10 minutes | Aangemeld; PIN nog uitgeschakeld |
| Ongeveer 10 minuten later | False | 30 | 31 | 10 minutes | PIN weer beschikbaar |

![Figuur 7.1 — Baseline van de TPM vóór de test]({{ '/assets/img/image1.png' | relative_url }})

*Figuur 7.1 — Baseline van de TPM vóór de test*

![Figuur 7.2 — Windows dwingt een herstart af na enkele foute PINs]({{ '/assets/img/image2.png' | relative_url }})

*Figuur 7.2 — Windows dwingt een herstart af na enkele foute PINs*

![Figuur 7.3 — TPM vergrendeld na 31 pogingen]({{ '/assets/img/image5.png' | relative_url }})

*Figuur 7.3 — TPM vergrendeld na 31 pogingen*

![Figuur 7.4 — PIN-aanmelding uitgeschakeld op het aanmeldscherm]({{ '/assets/img/image4.png' | relative_url }})

*Figuur 7.4 — PIN-aanmelding uitgeschakeld op het aanmeldscherm*

![Figuur 7.5 — Na 10 minuten daalt de teller naar 30 en is de TPM ontgrendeld]({{ '/assets/img/heal.png' | relative_url }})

*Figuur 7.5 — Na 10 minuten daalt de teller naar 30 en is de TPM ontgrendeld*

**Bevindingen.**

1. **Er zijn twee remmen.** Na enkele foute PINs dwingt Windows een herstart af terwijl de TPM nog niet vergrendeld is. De eigenlijke TPM-lockout volgt bij 31 pogingen.
2. **De lockout herstelt traag.** De teller daalt met één per 10 minuten. Na de lockout kan een aanvaller dus één PIN per 10 minuten proberen, ongeveer 144 per dag.
3. **De PIN werkt alleen op dit toestel.** De teller zit in de TPM; de PIN kan niet vanop afstand geraden worden.
4. **Het wachtwoord blijft werken tijdens de lockout.** We meldden opnieuw aan met het wachtwoord terwijl `LockedOut` nog True was.

Eén punt is niet verklaard: na de vijf genoteerde foute PINs stond de teller op 6. Het aantal pogingen is op dat moment niet per poging bijgehouden.
