### Kezdd itt, mielőtt a neten keresnél...

### Adminisztráció

#### Alapértelmezett Java módosítása linkek segítségével.
Valami hasonló helyről le kell tölteni a Java csomagot, és kicsomagolni:
https://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html
`tar -xzvf jdkXXX.tar.gz`
És ha már volt Java telepítve, akkor az `/etc/alternatives` mappában kell legyenek linkek, ezeket kell megváltoztatni:
`for i in \`ls -al | grep jdk1.8.0 | awk '{ print $9 }'\`;do ln -sf kicsomagolt-jdk/bin/$i $i;done`

#### Több fájlt kelll egyszerre összehasonlítani
Erre jó grafikus felület a `Meld` program, de még jobb a konzolról is indítható `diffuse`
`apt-get install diffuse`
És utána `diffuse <egyik> <másik> <n.dik>

#### PIN kód megadása Modem Manager számára
A GUI felületen nem jelenik meg mindig a PIN bekérő felület, de parancssorban is meg lehet adni:
`mmcli -i 0 --pin=CODE`

#### Keresni kell egy fájlt dátum szerint
Fájlok, módosítva adott nap on vagy utána
`find . -type f -newermt 2019-09-19`
Fájlok hozzáférési dátum szerint
`find . -type f -newerat 2019-09-19`
Fájlok jogosultsás módosítási ideje szerint (ha nem lett jog módosítva rajtuk, akkor az tekinthető a létrehozás dátumának)
`find . -type f -newerct 2019-09-19`

#### Le kell ellenőrizni, hogy nyitva van-e egy adott port a rendszeren
`netstat -aon | grep "PORT_NBR"`
`netstat -ntlp`

#### Ki kell nyitni egy portot Debian alatt Apache2-ben
Az /etc/apache2/ports.conf fájlban:
```
NameVirtualHost *:8080
```
Az /etc/apache2/sites-available/default-ssl.conf fájlban:
```
<VirtualHost *:8080></VirtualHost>
```

#### OpenVPN nem tud kapcsolódni
1. Hibakereséshez a legjobb az OpenVPN szerver logja, de a kliens is megteszi. Pl. `tail -f /var/log/syslog` vagy `journalctl -b -u NetworkManager`.
2. Hiba: __SIGUSR1[soft,private-key-password-failure] received, process restarting__. Megoldás: -látszólagos- az `/etc/NetworkManager/system-connections/` helyen megkerestem az adott kapcsolathoz tartozó konfig fájlt, majd abban a `cert-pass-flags` értékét először nullára, és vissza 1-re írtam át. Minden egyes módosítás után újraindítottam a szervizt: `systemctl restart network-manager`. A második módosítás után ismét bekérte a VPN jelszavamat a kapcsolódás előtt (merthogy a jelszó elvileg a kulcstartóban van/volt), és utána kapcsolódott rendesen. Semmit nem módosítottam, nem telepítettem a hiba előtt. A `key-size` és a `cipher` beállítások beleírása nem segített a hibán, de a warningokat eltűntette.

#### Egy felhasználó milyen csoportnak vagy csoportoknak a tagja?
`less /etc/group | grep -e "USERNAME"`

#### Adjuk hozzá az adott felhasználót egy csoporthoz, például a sudo csoporthoz ott, ahol a sudo parancs elérhető, és szeretnénk, ha a felhasználó tudna admin jogokkal bírni
`usermod -a -G sudo USERNAME`

#### Apache2 szerver van, és kódból (PHP) szeretnék a rendszer /tmp mappába írni, de Debian 9 alatt alapból nem engedett a sima /tmp-be írás biztonsági okokból. Ezt a követezőképpen lehet felülírni.
```
# hogy lássuk, hol van engedélyezve:
grep -R PrivateTmp /etc/systemd/
# állítsuk meg a szervert
systemctl stop apache2
# módosítsuk az apache2 service konfigot
/etc/systemd/system/multi-user.target.wants/apache2.service
  _PrivateTmp=false_
systemctl daemon-reload
systemctl start apache2
```

#### Adobe Reader telepítése Debian 9-re. (utils/AdbeRdr9.5.5-1_i386linux_enu.deb)
```
wget ftp://ftp.adobe.com/pub/adobe/reader/unix/9.x/9.5.5/enu/AdbeRdr9.5.5-1_i386linux_enu.deb
gdebi AdbeRdr9.5.5-1_i386linux_enu.deb
```

#### A Network Manager-el szeretnék hozzáadni egy openvpn konfig fájlt.
```
apt-get install network-manager-openvpn-gnome openvpn-systemd-resolved
nmcli connection import type openvpn file *.ovpn
```

#### Adott egy fájl, benne fájlnevekkel. Ezek helyfoglalását, méretét kell kiszámolni egyesével.
`for f in `cat files.log`; do s=`stat -c%s $f`; let "s=$s/1024"; echo $s done`

#### Szeretném becsomagolni a mappa tartalmát, amiben benne állok ZIP-be.
`zip -r ~/csomag.zip *`

#### Nincsen zip telepítve, de be kell csomagolnon egy komplett mappát.
`apt-get install xz-utils`, ha kell.
`xz [becsomagolni]`
`unxz [kicsomagolni]`

#### Csomagolás TAR-ral.
```
tar -czvf MyArchive Source_file 
vagy
tar --create --gzip --verbose --file=MyArchive.gz Source_file
```

#### Kicsomagolás TAR-ral.
```
tar -xzvf MyArchive Destination_file 
vagy
tar --extract --gunzip --verbose --file=MyArchive.gz Destination_file
```


#### Szeretnék felmásolni SSH-n keresztül egy fájlt a távoli gépre. A -r recurzíven másol, a -P megmondja a portot, ha nem az alap 22-es az.
`scp -r -P 52347 valami.zip  USER@HOST:FULLPATH`

#### Kell egy mappa, de ne legyen hiba, ha már létezik.
`mkdir -p ujmappa`

#### Debian alatt kell használnom a Novell mappákat
Találtam egy csomagot, amit lehet telepíteni https://launchpadlibrarian.net/122879995/ncpfs_2.2.6.orig.tar.gz,
de @jmarton által összeállított csomagban benne van minden.
Függőségei: libncp_2.2.6-9, és libpam-ncp_2.2.6.-9
Telepítés után így lehet mountolni (mindegyik meghajtó egy-egy mappaként fog megjelenni):
`ncpmount -S ORCA -A [IP] -U [novell-user] -u [local-user] [mount-point]`

### Szövegfeldolgozás
#### Új sor karakter van a sztring végén, de nem kellene, hogy ott legyen.
`permission=${permission//[$'\t\r\n']}`

#### Szeretném egy tömbbe tölteni egy szövegfájl egyes sorait. (BASH >4)
```
readarray arr < ../DSpace/dspace_groups.lst
echo ${arr[0]} # a 0. eleme a tömbnek
```

#### Egy sztringnek kell az első négy karaktere.
`ls -1 . | cut -c1-4`

#### Adott egy mappa, tele fájlokkal. A fájlok nevei számok és jpg a végük. Ezeket szeretném másik mappákban mozgatni úgy, hogy a mappa neve a fájl nevének az első négy karaktere legyen.
`find . -type f -name "*.jpg" | while read i; do d="$(echo `basename $i` | cut -c1-4)"; mkdir -p "../$d/"; mv $i "../$d/"; done`

### Keresés
#### Keresek egy fájlt, aminek nem ismerem a teljes nevét. A keresés kiindulópontja az aktuális mappa.
`find . -type f -name "*_url.txt"`

#### Keresek egy szövegrészt egy adott fájlban, és szeretném azt is tudni, melyik sorban van.
`grep -Rn "https://" --include "*.php" . | while read x; do echo $x; done`
`find . -type f -name "*.php" -exec grep -rne 'https' {} \;`
`find . -type f -print0 | xargs -0 grep "some string"`

#### Keresek egy szövegrészt egy adott fájlban, és szeretném tudni, melyik sorban van, de csak egy részét szeretném látni.
`grep -Rn "https://" --include "*.php" ./subfolder | while read x; do echo ${x:0:230}; done`


### Programozás
#### Van egy fomrázatlan JSON, amit a VIM-ben ezzela négy sorral viszonylag gyorsan olvashatóra lehet alakjtani.
```
%s/},/},\r/g

%s/\",\"/\",\r\t\t\t\"/g

%s/[0-9],/&\r\t\t\t/g

%s/{\"/{\r\t\t\t\"/g
```

#### Szeretnék összehasonlítani két fájlt.
`vimdiff 20190222190840_341733007_final_array.txt 20190222204937_17047325_final_array.txt`

#### Futtatnék PHP szkriptet, de ne kelljen hozzá forrásfájlt készíteni.
`php -r 'echo phpinfo();'`

#### Szeretnék csak belenézni egy gzip-be, de nincsen MC.
`vim file.gz`

#### JavaScriptben van egy nagyon hosszú sztring, és ezt szeretném átnézni, de a `console.log` metódus csak egy részét mutatja meg.
```
  var link = document.createElement('a');
  link.setAttribute('href', 'data:text/plain,'+jsonString);
  link.setAttribute('download', 'jsonString.txt');
  document.getElementsByTagName("body")[0].appendChild(link).click();
```

#### Szeretnék látni egy fájlt hexa formában, mint ahogy például az MC hexa nézete, csak mindezt Vim-ben.
```
:e <a fájl>
:%!xxd
```

#### Fájl megnyitása, szerkesztése távoli gépről vim-mel
`vim scp://user@myserver[:port]//path/to/file.txt`

### Git
#### LFS telepítése, beállítása
```
git lfs install # minden repo-ra külön kell meghívni
git lfs track "*.EXT" # többféle minta is illeszthető ide, de lehet szerkeszteni a .gitignore fájlt is kézzel
git add .gitattributes
```
Ha már a history-ban lévő állományokra is érvényesíteni kell, hogy LFS-ben legyenek tárolva, akkor vagy szerkeszteni kell azokat, vagy `rebase` paranccsal újraírni a history-t.

### ImageMagick
#### Kellene n darab kép adott mérettel, amik mond másként néznek ki.
```
#!/bin/bash

convert -size 80x80 xc:green -random-threshold 1x99% background.ppm;

for i in `seq -w 01 40`
do
convert background.ppm -fill white -draw "scale 4,4 gravity center text 0,0 '$i'" -quality 98 image_$i.jpg
done
exit;
```

#### El kell vágni egy képet két részre vízszintesen úgy, hogy a két részletet mentsük el külön fájlba.
`convert [IMAGE_TO_BE_SPLIT] -crop 50%x100% out%d_image.XXX`

#### Információk képekről a konzolon
`identify [IMAGE_NAME]`

#### Kép megnyitása IM megjelenítőben konzolról
`display [IMAGE_NAME]`

### Vagrant

#### Vagrant-DSpace telepítésnél jött elő, hogy a Github-ról nem tudott letöltődni a DSpace repo.
Hogy ez megoldódjon, a hoszt gépen kellett a Github felhasználómnak az SSH publikus kulcsát hozzáadni az `ssh-add` paranccsal az ssh ügynökhöz. Így már le tudta tölteni a Gitről a DSpace cuccokat.

### Raspberry Pi

#### Képfájlt szeretnék sima `dd` paranccsal másolni a kártyára
(Installing OS images on Linux)[https://www.raspberrypi.org/documentation/installation/installing-images/linux.md]
`lsblk` vagy hasonló paranccsal kell megnézni a diszk/sd kártya nevét, és azt kell beírni az `of=`-hoz.

```
unzip -p 2018-11-13-raspbian-stretch.zip | sudo dd of=/dev/sdX bs=4M conv=fsync status=progress
```
A `bs=4M` helyett lehet, hogy `1M` kellhet.

### Adatbázis

#### Adott egy xz-ben tömörített postgresql dump, amit be kell importálni
```
unxz valami.xz
createdb [adatbazis_neve] -U [postgres_user_with_create_privilege]
psql [adatbazis_neve] -U [postgres_user] -h [localhost_peldaul] < valami.sql
```

#### Oracle adatbázis verzió lekérdezése SQLDeveloperből
```
begin
  dbms_output.put_line(dbms_db_version.version || '.' || dbms_db_version.release);
end;
```

### Multimédia

#### DVD rippelés ffmpeg-gel
```
cat VALAMI_01.VOB VALAMI_02.VOB VALAMIN_NN.VOB > VOB01.VOB
ffmpeg -i VOB01.VOB -map 0:1 -map 0:2 -map 0:3 -codec:v libx264 -crf 21 -codec:a libmp3lame -qscale:a 2 -threads 2 /path/to/eg/test.mkv
```
