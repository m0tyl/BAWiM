# BAWiM
Fuzzing, Zero-day attack, Authentication Failure

## Wymagania
* Wirtualnie bądź natywnie zainstalowana dystrybucja linux

## Zadanie 1
Wchodzimy na stronę: https://tryhackme.com/room/ffuf
Należy się zalogować a jeśli nie posiada się jeszcze konta to stworzyć darmowe konto. 
Następnie postępujemy zgodnie z intrukcją tam zawartą.

Instalacja podstawowych narzędzi
sudo apt -y install ffuf (jeśli nie działa to https://github.com/ffuf/ffuf)
sudo apt -y install seclists (jeśli nie działa to https://github.com/danielmiessler/SecLists#install
sudo apt install openvpn

Po uruchomieniu Maszyny na TryHackMe klikamy w nasz profil i z ctr klikamy w Access.
Natepnie pobieramy My configuration File.
sudo openvpn path/to/file.ovpn  - pozwala nam połączyć się z maszynką

Nastepnie wchodzimy w przeglądarce na IP Adress, który znajduje się wyżej i jeśli wyświetli nam się strona z napisem "DVWA" to znaczy, że udało nam się połączyć.
Postepujemy już dalej z instrukcją na stronie.
