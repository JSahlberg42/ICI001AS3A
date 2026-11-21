# h5 gitar hero

## x) Lue ja tiivistä
- Kirjoitus selittää: 
    - Miten Git eroaa muista versionhallintajärjestelmistä (VCS).
    - Gitin työnkulkua (workflow)
    - Kehuu hieman Gitin paremmuutta ;-)
- 'git add --all && git commit; git pull && git push'
    - Add: lisätään tiedosto(t), joita on muutettu
    - Commit: Lisätään selite muutokseen
    - Pull: Lähetetään muutokset repositoryyn
    - Push: Haetaan muutokset repositorystä


## a) Online

Minulla on jo GitHub-käyttäjätunnus, joten käytin sitä tässä harjoituksessa: https://github.com/JSahlberg42/. Web-käyttöliittymään kirjautumisen jälkeen Repositories-välilehdeltä klikkasin `New`-nappulaa, josta uuden repositoryn (tai suomeksi varasto) luominen alkaa. Lomakkeelle piti ensin valita omistaja-organisaatio, koska Github-tunnukseni on liitetty useaan oraganisaatioon. Valitsin henkilökohtaisen organisaation. Lisäksi piti keksiä repositorylle nimi. Nimessä piti esiintyä sana 'sunshine'. Nimeksi valitsin 'sunshine-is-my-pleasure'.

Description-kohdan jätin tyhjäksi, koska se ei ole pakollinen tieto.

Määritin repositoryn näkyvyyden olevan yksityinen, koska turha kuormittaa Internetin hakukoneita, eli oltiin tosi vastuullisia. Lisäksi käänsin päälle vivun, jolla lisätään README tiedosto repositoryyn. Jos repositoryyn ei lisää luomisvaiheessa tiedostoa, saattaa ongelmia olla myöhemmin. 

Tämän jälkeen `Create repository` napilla käynnistin repositoryn luomisen, joka oli oli nopea toimenpide.

Unohdin lisätä lisenssityypin, mutta lisäsin sen myöhemmin käsin. Valitsin lisenssityypiksi GNU General Public License v3.0.


## b) Dolly

Seuraavaksi kloonasin repositoryn paikalliselle koneelleni. Olin jo aiemmin antanut julkisen SSH-avaimen Githubiin, joten sitä ei tässä kohtaa tarvinnut tehdä. Valitsin selaimessa repositoryn kotinäkymässä `Code`-napin alta Clone-osiosta SSH:n ja kopioin leikepöydälle tarvittavan osoitteen.

Paikallisella koneella annoin `git clone <osoite>` koemnnon:

    ➜  haaga-helia git clone git@github.com:JSahlberg42/sunshine-is-my-pleasure.git
    Cloning into 'sunshine-is-my-pleasure'...
    remote: Enumerating objects: 6, done.
    remote: Counting objects: 100% (6/6), done.
    remote: Compressing objects: 100% (4/4), done.
    remote: Total 6 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
    Receiving objects: 100% (6/6), 13.64 KiB | 170.00 KiB/s, done.
    ➜  haaga-helia

Komentoi ajautui odotusten mukaisesti. Tämän jälkeen siirryin kohdehakemistoon ja tutkin, mitä tiedostoja siellä on:

    ➜  haaga-helia cd sunshine-is-my-pleasure
    ➜  sunshine-is-my-pleasure git:(main) tree .
    .
    ├── LICENSE
    └── README.md

    1 directory, 2 files
    ➜  sunshine-is-my-pleasure git:(main)

Seuraavaksi piti tehdä muutoksia paikallisesti projektiin ja puskea muutokset repositoryyn:

    ➜  sunshine-is-my-pleasure git:(main) echo "Maailmalle terve" > hello.txt
    ➜  sunshine-is-my-pleasure git:(main) ✗ git add hello.txt
    ➜  sunshine-is-my-pleasure git:(main) ✗ git commit -m "paikallinen lisäys"
    [main 0f20faf] paikallinen lisäys
    1 file changed, 1 insertion(+)
    create mode 100644 hello.txt
    ➜  sunshine-is-my-pleasure git:(main) git push
    Enumerating objects: 4, done.
    Counting objects: 100% (4/4), done.
    Delta compression using up to 10 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 343 bytes | 343.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
    To github.com:JSahlberg42/sunshine-is-my-pleasure.git
    ca400f8..0f20faf  main -> main
    ➜  sunshine-is-my-pleasure git:(main) git pull
    Already up to date.
    ➜  sunshine-is-my-pleasure git:(main)

Tämän jälkeen tarkistin muutosten näkyvän myös weppiliittymässä:

![Screenshot 2026-11-21 at 11.51.38](Screenshot%202026-11-21%20at%2011.51.38.png)

Huomion commit hashin olevan '0f20faf', joka vastaa paikallista ja weppiliittymässä näkyvää.

## c) Doh! 

Seuraavaksi tein tyhmän muutoksen lisenssitiedostoon, jonka peruin `git reset --hard` komennolla:

    ➜  sunshine-is-my-pleasure git:(main) echo "foobar" > LICENSE
    ➜  sunshine-is-my-pleasure git:(main) ✗ git status
    On branch main
    Your branch is up to date with 'origin/main'.

    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
        modified:   LICENSE

    no changes added to commit (use "git add" and/or "git commit -a")
    ➜  sunshine-is-my-pleasure git:(main) ✗ git reset --hard
    HEAD is now at 0f20faf paikallinen lisäys
    ➜  sunshine-is-my-pleasure git:(main) git status
    On branch main
    Your branch is up to date with 'origin/main'.

    nothing to commit, working tree clean
    ➜  sunshine-is-my-pleasure git:(main)

## d) Tukki

Seuraavaksi piti tutkia, mitä muutoksia repositoryssä oli tehty `git log` komennolla:

    ➜  sunshine-is-my-pleasure git:(main) git log | more
    commit 0f20fafa382dccf7184653133f34db02d7e8516d
    Author: Jussi Sahlberg <jussi.sahlberg@elisa.fi>
    Date:   Sat Nov 21 11:49:43 2026 +0200

        paikallinen lisäys

    commit ca400f8a1b9b2bfa26a43cbdcc62f4decf9851ce
    Author: Jussi Sahlberg <59578056+JSahlberg42@users.noreply.github.com>
    Date:   Sat Nov 21 11:43:37 2026 +0200

        Add GNU GPL v3 license

        Added the GNU General Public License version 3 to the project.

    commit 8d26cfffa43f4da46da60a4800f4fea615267b72
    Author: Jussi Sahlberg <59578056+JSahlberg42@users.noreply.github.com>
    Date:   Sat Nov 21 11:40:50 2026 +0200

        Initial commit
    ➜  sunshine-is-my-pleasure git:(main)

Ylimpänä on tuorein muutos. Huomasin samalla, että näköjään paikallinen muutos oli mennyt työidentiteetillä sisään. Laitetaan korjauslistalle. Tutkitaan tarkemmin, mikä on muuttunut viimeisimmässä muutoksessa käyttäen `git diff <commitA <commitB>>`:

    ➜  sunshine-is-my-pleasure git:(main) git diff 0f20fafa382dccf7184653133f34db02d7e8516d ca400f8a1b9b2bfa26a43cbdcc62f4decf9851ce | more
    diff --git a/hello.txt b/hello.txt
    deleted file mode 100644
    index 24eeecf..0000000
    --- a/hello.txt
    +++ /dev/null
    @@ -1 +0,0 @@
    -Maailmalle terve
    ➜  sunshine-is-my-pleasure git:(main)

Ja sitten korjaamaan identiteettiä komennolla `git config user.email <osoite>`:

    ➜  sunshine-is-my-pleasure git:(main) git config user.email "59578056+JSahlberg42@users.noreply.github.com"
    ➜  sunshine-is-my-pleasure git:(main) echo "Moar sunshine" >> hello.txt
    ➜  sunshine-is-my-pleasure git:(main) ✗ git add hello.txt
    ➜  sunshine-is-my-pleasure git:(main) ✗ git commit -m "moar"
    [main 22f9d27] moar
    1 file changed, 1 insertion(+)
    ➜  sunshine-is-my-pleasure git:(main) git push
    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Delta compression using up to 10 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 298 bytes | 298.00 KiB/s, done.
    Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
    remote: Resolving deltas: 100% (1/1), completed with 1 local object.
    To github.com:JSahlberg42/sunshine-is-my-pleasure.git
    0f20faf..22f9d27  main -> main
    ➜  sunshine-is-my-pleasure git:(main) git log | more
    commit 22f9d27a1ebacc13a5eed8722f400cbed8c02d40
    Author: Jussi Sahlberg <59578056+JSahlberg42@users.noreply.github.com>
    Date:   Sat Nov 21 12:09:51 2026 +0200

        moar

    commit 0f20fafa382dccf7184653133f34db02d7e8516d
    Author: Jussi Sahlberg <jussi.sahlberg@elisa.fi>
    Date:   Sat Nov 21 11:49:43 2026 +0200

        paikallinen lisäys

    commit ca400f8a1b9b2bfa26a43cbdcc62f4decf9851ce
    Author: Jussi Sahlberg <59578056+JSahlberg42@users.noreply.github.com>
    Date:   Sat Nov 21 11:43:37 2026 +0200

        Add GNU GPL v3 license

        Added the GNU General Public License version 3 to the project.

    commit 8d26cfffa43f4da46da60a4800f4fea615267b72
    Author: Jussi Sahlberg <59578056+JSahlberg42@users.noreply.github.com>
    Date:   Sat Nov 21 11:40:50 2026 +0200

        Initial commit
    ➜  sunshine-is-my-pleasure git:(main)

Nyt commitit lähtevät oikealla identiteetillä. 

## e) Gitanbile

Tehtävänannossa pyydittiin laittamaan Ansible-kansio versionhallintaan ja tekemään muutos, ja puskemaan muutos repositoryyn. Olen opintojakson Ansible-kansion vienyt toiseen repositoryyn jo aiemmin. Linkistä https://github.com/JSahlberg42/ICI001AS3A/commits/main/ansible löytyy versiohistoriaa, joka riittänee tähän tehtävään.

## f) Hae pari projektiin Moodlen keskustelusta

Pari löydetty. Hei Klaudija.

## g) Vapaaehtoinen: Se toinen järjestelmä

Opintojakson aika olen sekakäyttänyt Gitiä niin MacOS kuin Debian Linux-järjestelmässä. Näissä git toimii hyvin pitkälti samalla tavalla. Debianin paketinhallintajärjestelmän git-paketti on versioltaan moderni, kuin MacOS:n git nojaa XCodeen, jonka git versio on hiemna vanha. Käytännössä en ole huomannut tässä ongelmaa. Jos ongelmia tulisi, voisi sen ratkaista käyttämällä vaihtoehtoisen pakettijärjestelmän Homebrew:n git-pakettia.

## Lähteet

[Chacon and Straub 2014: Pro Git, 2ed: 1.3 Getting Started - What is Git?](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F)
