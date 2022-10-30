# eloverblikugeforbrug

## Den hurtige løsning
Et diagram som viser ugeforbrug. Bemærk at hvis du skal have mere end 10 dages historik, så skal du bruge løsning med sætte purge_keep_days, https://www.home-assistant.io/integrations/recorder/. Standard i HA er 10 dages historik. 

"Tilføj kort" til din brugergrænseflade/dashboard, og vælg apexcharts-card. Så kopier du koden ind vinduet til venstre, så skulle diagrammet komme til syne i højre vindue. Diagrammet bruger grøn, gul og rød farve for at markere forskelle i forbrugt kWh, hvor grøn er lavest og rød højest.

Hvis der står "Loading" i diagram, så mangler der data. Enten er koden formateret forkert eller så mangler der kode. På github ville jeg bruge knappen "Copy raw contents", og i apexchart Ctrl+a i venstre vindue (for vælg al kode) og Ctrl+v for at indsætte/erstatte med ny kode.

For mere historik på diagrammet ændres "graph_span", fx 10d. Af ukendte årsager mister man til tider yderste højre dato, dog kan det løses ved lægge tid til, fx et sekund -> 10d1s

![image](https://user-images.githubusercontent.com/103023823/187018251-da6fd6f2-322e-4ede-8aa0-4568d53544d7.png)

## Den korrekte løsning
Datoen passer ikke, og det skyldes at HA bruger last_updated frem for den korrekte metering_date på sensor.eloverblik_energy_total. Får at få fandt på den korrekt dato(metering_date), så er en løsning på dette:
1) at lave en SQL integration
2) at lave en data_generator på apexcharts-card
<p>Ad 1<br>
  Der skal tilføjes en SQL-integration - Indstillinger > Enheder og tjenester > Tilføj integration (nedre højre hjørne) og søg på SQL. Jeg har valgt navnet "eloverblik historik", hvilket betyder jeg får en sensor som hedder: sensor.eloverblik_historik. Hvis du ikke ved hvilken database du bruger, så er HA født med sqlite.</p>
  Query skal være følgende:<br>
  Sqlite kender ikke nøgleordet: "separator", så det skal ersattes med et komma.<br>
<h4>Sqlite</h4>
select group_concat(Dato, ': ', state, ',') AS Data, state from (SELECT SUBSTRING_INDEX(SUBSTRING_INDEX(shared_attrs, '","', 1), '":"', -1) AS Dato, state FROM states s, state_attributes a WHERE entity_id = 'sensor.eloverblik_energy_total' AND state <> 'unknown' AND s.attributes_id = a.attributes_id GROUP BY s.attributes_id ORDER BY state_id ASC) b;<br>
<h4>MariaDB/MySQL</h4>
select group_concat(Dato, ': ', state separator ',') AS Data, state from (SELECT SUBSTRING_INDEX(SUBSTRING_INDEX(shared_attrs, '","', 1), '":"', -1) AS Dato, state FROM states s, state_attributes a WHERE entity_id = 'sensor.eloverblik_energy_total' AND state <> 'unknown' AND s.attributes_id = a.attributes_id GROUP BY s.attributes_id ORDER BY state_id ASC) b;<br>
<br><b>og husk at angive "state" for "Column"</b><br>
Hvis du har valgt at bruge anden database end sqlite, så <b>skal</b> Database URL udfyldes selvom der står noget andet.</p>
<p>Når det lykkes vil sensoren hedde det samme som det navn du gav den ifm. SQL-integrationen. Egenskaben "Data:" er historikken.</p>

![image](https://user-images.githubusercontent.com/103023823/198866640-c0d61ea5-4296-47ab-8e72-4b83b86ebb3d.png)

<p>Ad 2<br>
  På kortet skal data formateres korrekt og således:<br>
    <p style="font-family: Times New Roman">data_generator: |<br>
      var new_data = entity.attributes.Data.split(',');<br>
        if (isNaN(new_data[new_data.length - 1][0]))<br>
	        new_data.pop();<br>
        var hist = new_data.map((start) => {<br>
        var element = start.split(': ');<br>
        return [new Date(element[0]).getTime(), parseFloat(element[1])];<br>
      });<br>
      return hist;</p>
      
![image](https://user-images.githubusercontent.com/103023823/198835653-2afb1fb8-9cbd-4933-92ff-0bb53e05cae4.png)
<p>Et komplet eksempel og formateret korrekt findes her: https://github.com/MaximusClavius/eloverblikugeforbrug/blob/main/Ugeoversigt%20med%20korrekte%20datoer</p>
