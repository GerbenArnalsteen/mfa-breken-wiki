---
title: "Attacker-VM's"
nav_order: 4
parent: "Lab"
---

# Attacker-VM's

## Attacker Windows

| Tool | Installatie | Aandachtspunt |
|---|---|---|
| roadtx (ROADtools) | `py -3.12 -m pip install roadtx` | Python 3.12 gebruiken: nieuwere versies missen een kant-en-klaar pakket voor de `zstandard`-afhankelijkheid. De `Scripts`-map handmatig aan PATH toevoegen. |
| AADInternals en AADInternals-Endpoints | `Install-Module ... -Scope AllUsers` in PowerShell 7 | PowerShell 5.1 en 7 hebben gescheiden modulemappen |
| TokenTacticsV2 | ZIP van GitHub, daarna `Get-ChildItem -Recurse \| Unblock-File` | Dubbele mapnesteling in de ZIP; Mark of the Web blokkeert het laden |

{: .todo }
> De installatie en configuratie van Evilginx3 met de gebruikte phishlet, DNS en certificaat (Pad A), de opstelling met YubiKey en smartphone (Pad C) en de opbouw van de DC met AD CS en de kwetsbare template (Pad D).

## Installatiecommando's

```
py install 3.12
py -3.12 -m pip install roadtx

[Environment]::SetEnvironmentVariable("PATH",
  $env:PATH + ";C:\Users\attacker\AppData\Local\Python\pythoncore-3.12-64\Scripts",
  [EnvironmentVariableTarget]::User)

Install-Module AADInternals -Scope AllUsers
Install-Module AADInternals-Endpoints -Scope AllUsers

Get-ChildItem -Recurse | Unblock-File
Import-Module "C:\Users\attacker\Downloads\TokenTacticsV2-main\TokenTacticsV2-main\TokenTacticsV2.psd1"
```
