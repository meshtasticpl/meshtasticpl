## Preferowana maksymalna liczba skoków (hop limit) w sieci Meshtastic zależy od konkretnego scenariusza użytkowania i konfiguracji sieci. Ogólnie rzecz biorąc:

**1. Domyślnie liczba hopów w Meshtastic wynosi 3:**
- Oznacza to, że wiadomość może być przekazana maksymalnie przez trzy urządzenia, zanim dotrze do miejsca docelowego.
- Ta liczba została ustalona, aby znaleźć balans między zasięgiem a opóźnieniem oraz obciążeniem sieci.
 
**2. Dlaczego nie za dużo?**
- Większa liczba hopów zwiększa zasięg sieci, ale może również wprowadzać opóźnienia w przesyłaniu wiadomości.
-  Każdy dodatkowy "hop" oznacza większe obciążenie urządzeń i więcej energii zużywanej przez baterie.
  
**3. Kiedy zwiększyć liczbę hopów?**
- Jeśli potrzebujesz większego zasięgu w sieci, np. w przypadku bardzo rozległego terenu, gdzie węzły są rozmieszczone daleko od siebie.
- Zwiększenie liczby hopów wymaga modyfikacji ustawień w aplikacji Meshtastic lub odpowiednich komend w CLI.
  
**4. Kiedy zmniejszyć liczbę hopów?**
- W małych sieciach, gdzie urządzenia są blisko siebie, aby zredukować opóźnienia i obciążenie.
