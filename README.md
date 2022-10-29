# eloverblikugeforbrug

Et diagram som viser ugeforbrug. Bemærk at hvis du skal have mere end 10 dages historik, så skal du bruge løsning med sætte purge_keep_days, https://www.home-assistant.io/integrations/recorder/. Standard i HA er 10 dages historik. 

"Tilføj kort" til din brugergrænseflade/dashboard, og vælg apexcharts-card. Så kopier du koden ind vinduet til venstre, så skulle diagrammet komme til syne i højre vindue. Diagramet bruger grøn, gul og rød farve for at markere forskelle i forbrugt kWh, hvor grøn er lavest og rød højest.

Hvis der står "Loading" i diagram, så mangler der data. Enten er koden formatteret forkert eller så mangler der kode. På github ville jeg bruge knappen "Copy raw contents", og i apexchart Ctrl+a i venstre vindue (for vælg al kode) og Ctrl+v for at indsætte/erstatte med ny kode.

For mere historik på diagrammet ændres "graph_span", fx 10d. Af ukendte årsager mister man til tider yderste højre dato, dog kan det løses ved lægge tid til, fx et sekund -> 10d1s

![image](https://user-images.githubusercontent.com/103023823/187018251-da6fd6f2-322e-4ede-8aa0-4568d53544d7.png)

Bemærk!
Til tider passer datoen ikke, og det sker når sensor ikke er blevet opdateret. Umiddelbart skyldes det integrationens opdatering af sensor.eloverblik_energy_total.
En løsning på dette er:
1) at lave en SQL integration
2) at lave en data_generator på apexcharts-card

Ad 1)<br>
select group_concat(Dato, ': ', state separator ',') AS Data, state from (SELECT SUBSTRING_INDEX(SUBSTRING_INDEX(shared_attrs, '","', 1), '":"', -1) AS Dato, state FROM states s, state_attributes a WHERE entity_id = 'sensor.eloverblik_energy_total' AND state <> 'unknown' AND s.attributes_id = a.attributes_id GROUP BY s.attributes_id) b;
og husk at angive "state" for "Column"
