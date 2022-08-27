# eloverblikugeforbrug

Et diagram som viser ugeforbrug. Bemærk at hvis du skal have mere end 10 dages historik, så skal du bruge løsning med sætte purge_keep_days, https://www.home-assistant.io/integrations/recorder/. Standard i HA er 10 dages historik. 
For mere historik på diagrammet ændres "graph_span", fx 10d. Af ukendte årsager mister man til tider yderste højre dato, dog kan det løses ved lægge tid til, fx et sekund -> 10d1s

![image](https://user-images.githubusercontent.com/103023823/187018251-da6fd6f2-322e-4ede-8aa0-4568d53544d7.png)

PS
Til tider passer datoen ikke, og det sker når sensor ikke er blevet opdateret. Umiddelbart skyldes det integrationens opdatering af sensor.eloverblik_energy_total.
