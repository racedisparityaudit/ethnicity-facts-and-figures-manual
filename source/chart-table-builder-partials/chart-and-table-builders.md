## Chartbuilder and Tablebuilder

Chartbuilder and Tablebuilder function in roughly the same way. Chartbuilder will be detailed here and anything 
particular to Tablebuilder should be minor.

### Charts in the Publisher

Charts in the Ethnicity Facts and Figures ecosystem work on three files:

- `templates/cms/create_chart.html` is the template that contains the chartbuilder form.
- `rd-chart-objects.js` is a factory for building rd-chart objects.
- `rd-graph.js` is a renderer for rendering rd-chart objects.

Some common functions between the table builder and chart builder are also stored in `rd-builder.js`

On **Save** the chartbuilder builds a rd-chart object which it sends to be stored on the dimension for rendering by 
rd-graph. It also sends a json dump of all the current builder settings so it can recall them next time this chart is 
opened up.

#### Chartbuilder

On the back-end it is supported by EthnicityClassificationFinder.

#### How it works

Chartbuilder has the handleNewData(success) function at its heart

- User pastes excel content as text into the data box and clicks `Next`.
- `handleNewData(success)` is triggered and makes an AJAX call to the EthnicityClassificationFinder endpoint. A list of 
  EthnicityClassificationFinder classifications with processed data is returned.
- The success function stores all the valid EthnicityClassificationFinder classifications (and their converted data) 
  client side. It picks the first classification for preference then sets up the rest of the builder form using data 
  from that classification.
- When changes are made to any setting chartbuilder builds a chart object and renders in the chart area using the 
  standard values
- Save posts an AJAX call to the Publisher's chartbuilder endpoint with a chart object and the current builder settings,
  both as lumps of json. These are stored as json objects in the database.
- If the builder is reopened it will fill the data text box from the settings. It then calls `handleNewData(success)`
  which builds classifications. At the end of the regular success function it uses the rest of the settings object to 
  set up the existing chart.
