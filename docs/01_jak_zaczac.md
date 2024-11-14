# Jak zacząć z Meshtastic?

## Najlepiej skocz na główkę :)

Kup 3 urządzenia i zacznij ich używać. Zamontuj jeden za oknem (to będzie Twój węzeł "domowy"), drugi daj znajomemu albo
rodzinie. Z trzecim pojeździj dookoła swojego domu i sprawdź jak daleko możesz odjechać wciąż utrzymując łączność ze
swoim węzłem stacjonarnym. Do wymuszenia transmisji, jako zamiennik "pinga" możesz używać opcji "Request node info",
albo po prostu otworzyć czat ze swoim węzłem domowym i wysyłać wiadomości o tym w jakim miejscu aktualnie się
znajdujesz. Po powrocie do domu zobaczysz które wiadomości dotarły i zostały zachowane - w ten sposób poznasz zasięg
swojego węzła domowego. Gdy podłączysz się do większej sieci mesh opcja "Traceroute" pomoże ci zrozumieć jakimi
ścieżkami przesyłane są wiadomości w sieci i który węzeł ma lepszy sygnał. Urządzenia Meshtastic są tak zaprojektowane,
że nie musisz konfigurować prawie niczego by zacząć ich używać - wystarczy, że wgrasz firmware Meshtastic i gotowe.
Możesz zostawić limit "hopów" na domyślnej wartości (3), poza szczególnymi przypadkami jest to i tak więcej niż
potrzeba, bo w praktyce oznacza to, że wiadomości wysyłane przez twoje urządzenie będą mogły zostać retransmitowane
przez trzy inne węzły a następnie zostać odebrane przez czwarty (po więcej informacji zajrzyj do sekcji FAQ: "Jak działa
limit hopów?"). Żeby potrzebować wyższej wartości limitu hopów musiałbyś chcieć łączyć się pomiędzy trzema dolinami
przedzielonymi grzbietami górskimi w odległości >20 km.

## Jak to działa?

Tu jest oficjalna dokumentacja projektu: https://meshtastic.org/docs/introduction

Przydatne filmy: https://youtube.com/playlist?list=PLshzThxhw4O5JTOACGHzYSSd3soDhoXKK

LoRa (Long Range Radio) działa nawet dużo poniżej poziomu szumu dzięki opatentowanej technice modulacji sygnału. Dzięki
temu przesyłanie danych jest możliwe na duże odległości przy użyciu małej mocy. Wady to ograniczona długość wiadomości i
niska przepustowość transmisji.

## Kilka pojęć na początek

Będę używał takich samych określeń jakie pojawiają się w aplikacji na telefon, żeby łatwiej było odnaleźć odpowiednie
ustawienie.

### Firmware

To oprogramowanie, które instalujesz bezpośrednio na urządzeniu Meshtastic i które steruje działaniem sprzętu. Firmware
decyduje o tym jakie dane są emitowane przez antenę urządzenia w trakcie wysyłania wiadomości oraz dekoduje i
interpretuje odebrane sygnały. W wypadku korzystania z aplikacji na telefon, firmware jest odpowiedzialny za komunikację
z aplikacją przez Bluetooth i wysyłanie/odbieranie informacji i komend od użytkownika. Instalacja firmware potocznie
nazywa się "flashowaniem" (ang. "flashing" - od nazwy [pamięci](https://pl.wikipedia.org/wiki/Pami%C4%99%C4%87_flash) w
której firmware zazwyczaj był zapisywany).

### LoRa Modem Preset

Urządzenie Meshtastic może zostać ustawione na jeden z kilku trybów (ang. "preset") LoRa. W obecnych wersjach firmware
domyślnie ustawiony jest tryb `LongFast` (Long = duży zasięg, Fast = szybka transmisja). Urządzenia ustawione na ten sam
tryb przesyłają do siebie wiadomości, nawet jeśli nie mają skonfigurowanego tego samego kanału (channel - patrz
poniżej). Z powodów praktycznych niektóre sieci Meshtastic (np. czeska) przełączyły się na tryb `MediumFast` (Medium =
średni zasięg, Fast = szybka transmisja). Tryb LF (`LongFast`) staje się nieużywalnie wolny już przy sieciach rozmiaru
ok. 10 węzłów i pojawiają się problemy z przesyłaniem wiadomości. Gdy w ustawieniach sieci LoRa wybierzesz tryb
`MediumFast` nazwa podstawowego kanału zostanie automatycznie zmieniona.

### Channel

W aplikacji można skonfigurować wiele kanałów ("channels"), każdy z nich będzie miał swój własny klucz szyfrujący.
Domyślnie zawsze zostanie utworzony kanał o takiej samej nazwie jak LoRa Modem Preset (czyli na przykład `LongFast`),
zawsze z kluczem `AQ==` (jest to bajt `0x01` zapisany przy użyciu kodowania
[Base64](https://en.wikipedia.org/wiki/Base64)). Dzięki temu, że domyślny klucz jest znany i wszędzie taki sam, każdy
może się wymieniać wiadomościami na domyślnym kanale bez dodatkowej konfiguracji, od razu po włączeniu urządzenia.

### ACK

ACK, z angielskiego "Acknowledgement" (potwierdzenie) to mechanizm, który poinformuje Cię czy wysłana wiadomość została
odebrana przez inne węzły. W wypadku każdej wiadomości, ikona chmurki z "ptaszkiem" ( :white_check_mark: ) oznacza, że
Twoja wiadomość dotarła do innego węzła w sieci i dostałeś ją z powrotem w ramach retransmisji. W wypadku wiadomości
bezpośrednich, które wysłałeś na adres konkretnego węzła (DM - ang. Direct Message) dodatkowo może pojawić się ikona
człowieka - to oznacza, że docelowy odbiorca dostał wiadomość i odesłał do Twojego węzła potwierdzenie.

Uwaga: brak ikony potwierdzenia nie musi koniecznie oznaczać, że Twoja wiadomość nie dotarła. W wypadku kiedy Twoja moc
nadawania jest dużo większa od sąsiedniego węzła i znajdujesz się od niego w większej odległości, może się zdarzyć, że
Twoje pakiety będą doskonale odczytywane, jednak potwierdzenia wysyłane przez sąsiada nigdy do Ciebie nie dotrą. Dlatego
w trakcie testów zasięgu zawsze powinieneś sprawdzić w drugim węźle które wiadomości dotarły, a nie opierać się tylko na
statusach potwierdzeń pokazywanych przez aplikację.

## Co trzeba wiedzieć o urządzeniach Meshtastic

Zasadniczo powinny interesować Cię 2/3 najważniejsze parametry:

- platforma zastosowanego mikrokontrolera (ESP32 albo nRF)
- chipset LoRa do obsługi radia (SX1276 albo SX1262)
- (opcjonalnie) chipset GPS (NEO 6M albo M8N)

### Mikrokontroler

Kontrolery z rodziny ESP32 zużywają więcej prądu (na dobrej jakości akumulatorze 18650 wytrzymają do dwóch dni),
obsługują WiFi, a firmware instaluje się po podłączeniu do komputera (przez kabel USB). WiFi obsługiwane przez ESP32
przydaje się na przykład wtedy gdy chcesz podłączyć się do serwera MQTT i przesyłać wiadomości do innych węzłów przez
internet.

Chipy nRF z kolei zużywają nawet 10 razy mniej energii, nie mają WiFi, a firmware może być aktualizowany z telefonu
przez Bluetooth. Są za to droższe od ESP32.

### Chipset LoRa

Wersja SX1262 lepiej odbiera i silniej nadaje sygnał, najlepiej jeśli będziesz miał urządzenie właśnie z takim chipem.

### GPS

Układ M8N jest czulszy niż NEO6M i w trudnych warunkach typu las będzie miał taką samą lub większą dokładność niż
smartfon.

## Co kupić na początek

Jako węzła bazowego (stacjonarnego) możesz użyć
[Heltec Wireless Stick Lite V3](https://heltec.org/project/wireless-stick-lite-v2/) - ma ESP32 (tańszy mikroprocesor) i
SX1262 (lepsze radio), nie ma GPS, koztuje około 75zł
[na Aliexpress](https://pl.aliexpress.com/item/1005005443311259.html) (listopad 2024).

Do noszenia (mobile) możesz kupić urzadzenie [T-Beam](https://lilygo.cc/products/t-beam-v1-1-esp32-lora-module) - ma
również ESP32 i SX1262, a dodatkowo lepszy układ GPS. Ma też miejsce na akumulator 18650 (sensowny egzemplarz Panasonica
możesz kupić w vape shopie za 20-30zł) oraz zabezpieczenie przeciw głębokim rozładowaniem. Antena zamontowana na
pigtailu (krótki przewód) ułatwia noszenie w kieszeni (nie wystaje na bok ani nie trzeba montować składanej tak jak w
wersji z gniazdem SMA na dłuższej krawędzi). Warto dopłacić trochę ponad cenę standardowego T-Beam.
[Aliexpress](https://www.aliexpress.com/item/4001287221970.html).

## Co można poprawiać dla lepszej łączności

### Lokalizacja węzła

Pasmo 868MHz działa najlepiej przy bezpośredniej widoczności między węzłami (ang. "LOS" - "line of sight"), więc im
wyżej zamontujesz swój węzeł/antenę tym lepiej. W wypadku miejscowości w dolinach najlepiej sprawdzi się węzeł
zainstalowany na zboczu wzgórza, z dobrą widocznością całej okolicy. Rzeźbę terenu pomiędzy planowanymi lokalizacjami
węzłów możesz sprawdzić narzędziem [HeyWhatsThat](https://www.heywhatsthat.com/profiler.html). W ustawieniach wpisz
częstotliwość (868 albo 433MHz) i zaznacz uwzględnienie krzywizny Ziemi ("curved Earth"). Teren nie powinien przesłaniać
na wykresie pomarańczowego kształtu przypominającego cygaro (wyszukaj w internecie
"[strefa Fresnela](https://pl.wikipedia.org/wiki/Strefa_Fresnela)" jeśli chcesz dowiedzieć się więcej).

### Antena LoRa

Anteny to temat na długą opowieść, tutaj wkleję tylko linki do dwóch sprawdzonych produktów:

- do węzła przenośnego (wariant SMA, **nie RP-SMA**): https://www.aliexpress.com/item/1005004607615001.html
- do węzła stacjonarnego https://www.aliexpress.com/item/1005001411046022.html

Zazwyczaj 9 na 10 egzemplarzy jest w porządku ;)

### Antena GPS

Większa antena zazwyczaj daje większą dokładność lokalizacji, po wymianie fabrycznej antany na taką 20mm da się zauważyć
różnicę: https://www.aliexpress.com/item/1005003123199379.html Uwaga: prawidłowe ustawienie anteny to metalową srebrną
"kropką" w stronę nieba.

## Nie masz w pobliżu żadnych innych węzłów?

Możesz podłączyć się do serwera MQTT [loranet.pl](https://loranet.pl/mqtt.html) i wymieniać się wiadomościami przez
internet, dopóki nie znajdziesz sąsiadów :)

## FAQ - często zadawane pytania

*Czy jest gdzieś dostępna mapa zasięgu Meshtastic?*

Zasadniczo nie ma mapy, która pokazywałaby dokładny zasięg, ponieważ sieć Meshtastic nie wymaga podłączenia węzłów do
internetu, ani podawania lokalizacji (tym bardziej prawdziwej i zawsze aktualnej lokalizacji). Można jednak sprawdzić
czy w okolicy zgłaszają się jakieś węzły podłączone do serwera MQTT. Dla globalnego publicznego serwera MQTT mapa jest
na https://meshmap.net/, dla polskiego serwera loranet: https://loranet.pl/

*Czy węzły muszą mieć bezpośrednią widoczność? To mocno problematyczne gdy po drodze są wzgórza albo budynki*

Bezpośrednia linia wzroku to idealne ustawienie, ale łączność może zadziałać także przez przeszkody, to zależy od
rozmiaru i materiału. Zdarza się, że komunikacja jest możliwa na 500 metrów przez trzy bloki mieszkalne pomiędzy, ale
przez wzgórze się nie przebijesz, choćby nie wiem co.

*Czy da się przesyłać przez Meshtastic zwykły ruch internetowy?*

Nie jest to możliwe, komunikacja ogranicza się tylko do tego co umożliwia aplikacja Meshtastic (wiadomości tekstowe,
lokalizacja GPS, dane telemetryczne itp.). Główne ograniczenie wynika z przepustowości: w trybie `LongFast` jest to
maksymalnie 800 bitów/s, na `MediumFast` 3 kilobity/s, a mówimy tutaj o komunikacji simplex (w jednym kierunku) i z
uwzględnieniem narzutu protokołu (nagłówki itp.). Poza wiadomościami tekstowymi przez mesh rozsyłane są też informacje o
statusie węzłów, współrzędne GPS itd., więc nietrudno o zatkanie sieci.

*Czy Meshtastic jest odporne na przeciążenie? Czy mogę zapchać pośrednie węzły jeśli zacznę wysyłać dużo danych?*

Zasadniczo nie, sieć jest podatna na przeciążenia i nie ma na to stuprocentowych zabezpieczeń. Natomiast wewnątrz Unii
Europejskiej obowiązuje limit cyklu pracy wynoszący 10%, to znaczy, że radio może nadawać przez 10% czasu (np. 6 minut w
ciągu każdej godziny). Jest to wymuszane przez firmware po wybraniu regionu `EU_`. Dodatkowo firmware ignoruje mniej
istotne pakiety takie jak wielokrotnie powtarzane `NodeInfo` oraz priorytetyzuje wiadomości tekstowe w wypadku większych
obciążeń.

*Czy komunikacja jest szyfrowana end-to-end, czy raczej każdy może czytać przesyłane wiadomości?*

Od wersji firmware 2.5 wiadomości prywatne (bezpośrednie, DM) są szyfrowane kluczami publicznymi odbiorców, czyli
end-to-end. Wiadomości wysyłane na kanałach wspólnych są szyfrowane kluczem danego kanału (tzw. klucz współdzielony,
ang. PSK - pre-shared key). W związku z tym każdy kto zna klucz kanału może odczytać wiadomości. Dla kanałów domyślnych
klucz jest powszechnie znany (`AQ==`), więc są to de facto kanały nieszyfrowane.

*Jak działa ograniczenie na liczbę węzłów pośredniczących ("hopów")?*

Limit hopów (od ang. "hop" - przeskok) to licznik ustawiany w każdej wiadomości wysyłanej przez urządzenie Meshtastic.
Każdy węzeł, który przekazuje wiadomość dalej zmniejsza wartość tego licznika o 1. Jeśli węzeł odbierze wiadomość z
limitem równym zero, to nie przekazuje jej dalej. Jeśli węzeł jest podłączony do MQTT, wysyłane tam pakiety są kopiowane
(wartość limitu nie jest zmieniana), natomiast jeśli węzeł odbierze pakiet z MQTT to przed wysłaniem go w eter powinien
zmniejszyć licznik o 1.

Dla przykładu: limit hopów ustawiony przez nadawcę na 3 oznacza, że wiadomość może dotrzeć najdalej do czwartego węzła
od nadawcy (max 3 pośredników):

```
         limit = 3          limit = 2          limit = 1          limit = 0
Nadawca -----------> Hop 1 -----------> Hop 2 -----------> Hop 3 -----------> Odbiorca
```

## Źródła

Pierwsza wersja tego dokumentu powstała jako adaptacja poradników czeskiej społeczności Meshtastic ze strony
https://czmesh.cz/.

First version of this document was based on translated guides from the Czech Meshtastic community, courtesy of the owner
of https://czmesh.cz/.
