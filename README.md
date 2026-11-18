# Wiki — MFA breken (groep MFA4)

Deze map is een kant-en-klare site voor GitHub Pages. De pagina's zijn Markdown; het thema (just-the-docs) zorgt voor de zijbalk, de zoekfunctie en de diagrammen. Er hoeft niets lokaal gebouwd te worden.

## Publiceren

1. Maak op GitHub een nieuwe repository aan (bijvoorbeeld `mfa-breken-wiki`), zonder README.
2. Open `_config.yml` en vul `url` en `baseurl` in (haal de `#` weg).
3. Zet de inhoud van deze map in de repository:

       git init
       git add .
       git commit -m "Eerste versie van de wiki"
       git branch -M main
       git remote add origin https://github.com/<gebruikersnaam>/<naam-van-de-repo>.git
       git push -u origin main

4. Ga op GitHub naar Settings → Pages, kies "Deploy from a branch", branch `main`, map `/ (root)`.
5. Na een minuut staat de site op `https://<gebruikersnaam>.github.io/<naam-van-de-repo>/`.

## Let op: een GitHub Pages-site is openbaar

Deze wiki bevat de naam van de schooltenant, namen van testaccounts en werkende aanvalsstappen. Overleg met de lector voordat je publiceert.

## Een pagina toevoegen

Elke pagina begint met een blokje bovenaan:

    ---
    title: "Titel"
    nav_order: 3
    parent: "Playbooks"
    ---

`parent` is de titel van de sectie waaronder de pagina in de zijbalk komt. Voor een nieuw playbook: kopieer `playbooks/sjabloon.md`.

Een geel "Aan te vullen"-blok:

    {: .todo }
    > Tekst.
