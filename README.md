Getting Started

This guide offers a comprehensive tutorial on how to utilize the Mapbox Isochrone API to create a web application that allows users to estimate travel distances by foot, bicycle, or car within predefined time limits. Follow these steps to develop your app:

Access Token:
![image](https://github.com/aqazi2/map/assets/129705579/accc2e9d-df80-4099-9af5-80d7260d0e41)

_config.yml<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Demo: Get started with the Isochrone API</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <!-- Mapbox GL JS -->
    <script src="https://api.tiles.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.js"></script>
    <link
      href="https://api.tiles.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.css"
      rel="stylesheet"
    />
    <!-- Mapbox Assembly -->
    <link
      href="https://api.mapbox.com/mapbox-assembly/v1.3.0/assembly.min.css"
      rel="stylesheet"
    />
    <script src="https://api.mapbox.com/mapbox-assembly/v1.3.0/assembly.js"></script>
    <style>
      body {
        margin: 0;
        padding: 0;
      }

      #map {
        position: absolute;
        top: 0;
        bottom: 0;
        width: 100%;
      }
    </style>
  </head>

  <body>
    <div id="map"></div>

    <div class="absolute fl my24 mx24 py24 px24 bg-gray-faint round">
      <form id="params">
        <h4 class="txt-m txt-bold mb6">Choose a travel mode:</h4>
        <div class="mb12 mr12 toggle-group align-center">
          <label class="toggle-container">
            <input name="profile" type="radio" value="walking" />
            <div class="toggle toggle--active-null toggle--null">Walking</div>
          </label>
		  <label class="toggle-container">
            <input name="profile" type="radio" value="Bus" />
            <div class="toggle toggle--active-null toggle--null">Bus</div>
          </label>
          <label class="toggle-container">
            <input name="profile" type="radio" value="cycling" checked />
            <div class="toggle toggle--active-null toggle--null">Cycling</div>
          </label>
          <label class="toggle-container">
            <input name="profile" type="radio" value="driving" />
            <div class="toggle toggle--active-null toggle--null">Driving</div>
          </label>
        </div>
        <h4 class="txt-m txt-bold mb6">Choose a maximum duration:</h4>
        <div class="mb12 mr12 toggle-group align-center">
          <label class="toggle-container">
            <input name="duration" type="radio" value="15" checked />
            <div class="toggle toggle--active-null toggle--null">15 min</div>
          </label>
          <label class="toggle-container">
            <input name="duration" type="radio" value="25" />
            <div class="toggle toggle--active-null toggle--null">25 min</div>
          </label>
		  </label>
          <label class="toggle-container">
            <input name="duration" type="radio" value="40" />
            <div class="toggle toggle--active-null toggle--null">40 min</div>
          </label>
          <label class="toggle-container">
            <input name="duration" type="radio" value="35" />
            <div class="toggle toggle--active-null toggle--null">35 min</div>
          </label>
        </div>
      </form>
    </div>

    <script>
      mapboxgl.accessToken = "pk.eyJ1IjoiYXFhemkiLCJhIjoiY2xvaHc4dWkzMGZqajJrcHF5MDlxY2FnNCJ9.w2rhdjTBwODsGV8thEVZmw";

      const map = new mapboxgl.Map({
        container: 'map', // container id
        style: 'mapbox://styles/mapbox/streets-v12', // stylesheet
        center: [-87.654651, 41.903511], // starting position [lng, lat]
        zoom: 11.5 // starting zoom
      });

      // Target the params form in the HTML
      const params = document.getElementById('params');

      // Create variables to use in getIso()
      const urlBase = 'https://api.mapbox.com/isochrone/v1/mapbox/';
      const lon = -87.654651; 
      const lat = 41.903511;
      let profile = 'cycling';
      let minutes = 12;

      // Set up a marker that you can use to show the query's coordinates
      const marker = new mapboxgl.Marker({
        'color': '#314ccd'
      });

      // Create a LngLat object to use in the marker initialization
      // https://docs.mapbox.com/mapbox-gl-js/api/#lnglat
      const lngLat = {
        lon: lon,
        lat: lat
      };

      // Create a function that sets up the Isochrone API query then makes a fetch call
      async function getIso() {
        const query = await fetch(
          `${urlBase}${profile}/${lon},${lat}?contours_minutes=${minutes}&polygons=true&access_token=${mapboxgl.accessToken}`,
          { method: 'GET' }
        );
        const data = await query.json();
        // Set the 'iso' source's data to what's returned by the API query
        map.getSource('iso').setData(data);
      }

      // When a user changes the value of profile or duration by clicking a button, change the parameter's value and make the API query again
      params.addEventListener('change', (event) => {
        if (event.target.name === 'profile') {
          profile = event.target.value;
        } else if (event.target.name === 'duration') {
          minutes = event.target.value;
        }
        getIso();
      });

      map.on('load', () => {
        // When the map loads, add the source and layer
        map.addSource('iso', {
          type: 'geojson',
          data: {
            'type': 'FeatureCollection',
            'features': []
          }
        });

        map.addLayer(
          {
            'id': 'isoLayer',
            'type': 'fill',
            'source': 'iso',
            'layout': {},
            'paint': {
              'fill-color': 'green',
              'fill-opacity': 0.5
            }
          },
          'poi-label'
        );

        // Initialize the marker at the query coordinates
        marker.setLngLat(lngLat).addTo(map);

        // Make the API call
        getIso();
      });
    </script>
  </body>
</html>


An access token serves as an authentication key for accessing Mapbox services.
Obtain your access token from your Mapbox account. If you don't have one, you must sign up for an account here.
Mapbox GL JS:

Mapbox GL JS is an essential tool for building interactive web maps in this tutorial.
This JavaScript API by Mapbox is designed for creating maps in web applications. It will help you incorporate maps into your app, allowing users to visualize and interact with isochrone data. Get Mapbox GL JS here.
Mapbox Isochrone API:

The Mapbox Isochrone API is the core of this tutorial, responsible for calculating areas reachable within specific time constraints from a given location.
It returns these areas as contours, either in the form of polygons or lines. You can seamlessly integrate these contours into your map to visually depict areas that users can reach within specified time limits. Learn more about the Isochrone API here.
Mapbox Assembly:

To style the user interface (UI) of your web application, we recommend using Mapbox Assembly, an open-source CSS framework.
Applying Assembly's styles enables you to create an aesthetically pleasing and user-friendly UI for your travel-time estimation app, enhancing the user experience. Explore Mapbox Assembly here.
Text Editor:

You'll need a text editor to code and develop your app. You can use your preferred text editor to write HTML, CSS, and JavaScript.
Your text editor is where you'll create and structure the code for your web application, so it's an essential tool. If you don't have a preferred text editor, you can download one, like Notepad++, from here.
Using the Isochrone API

To make your travel-time estimation app work seamlessly, it's crucial to understand how to harness the Isochrone API. This API empowers you to calculate and display areas reachable within specific time constraints. Here's a step-by-step explanation of how to use it:

Three Essential Parameters:

Profile: The profile parameter determines the Mapbox routing profile your query should use, depending on the type of travel you want to analyze (e.g., "walking," "cycling," or "driving").
Coordinates: You'll need to provide a set of coordinates, which are essentially a {longitude, latitude} pair. These coordinates act as the center point for drawing isochrone lines, serving as the origin of your travel-time calculations.
Contours Minutes: This parameter defines the duration of trips in minutes. You can specify one or more times, separated by commas, to indicate different time limits, but note that the maximum duration for each isochrone you can request is 60 minutes.
Optional Parameters:
The Isochrone API offers optional parameters. For your app's specific purpose, you'll focus on one optional parameter:

Polygons: The "polygons" parameter lets you choose whether the isochrone contours should be returned as GeoJSON polygons or linestrings. Setting "polygons" to "true" returns any contour that forms a ring as a polygon, making it easier to visualize the areas users can reach.
Constructing an API Request:

To build an API request, combine these parameters in a URL, as follows:
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mapbox Isochrone API Tutorial</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #f1f1f1;
            font-family: Arial, sans-serif;
        }

        .container {
            width: 80%;
            max-width: 800px;
            margin: 0 auto;
            background-color: #fff;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            padding: 20px;
        }

        header {
            text-align: center;
        }

        nav ul {
            list-style: none;
            padding: 0;
        }

        nav li {
            display: inline;
            margin-right: 20px;
        }

        main {
            text-align: left;
            padding: 20px;
        }

        .map-container {
            width: 100%;
            max-width: 400px;
            height: 300px;
            margin: 0 auto;
            background-color: #fff;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>Mapbox Isochrone API Tutorial</h1>
        </header>

        <nav>
            <ul>
                <li><a href="#getting-started">Getting Started</a></li>
                <li><a href="#using-isochrone-api">Using the Isochrone API</a></li>
                <li><a href="#create-a-map">Create a map</a></li>
                <li><a href="#add-the-sidebar">Add the sidebar</a></li>
                <li><a href="#add-isochrone-api">Add the Isochrone API</a></li>
                <li><a href="#draw-isochrone-contour">Draw the isochrone contour</a></li>
                <li><a href="#make-app-interactive">Make the app interactive</a></li>
                <li><a href="#add-a-marker">Add a marker</a></li>
                <li><a href="#final-product">Final product</a></li>
                <li><a href="#next-steps">Next steps</a></li>
            </ul>
        </nav>

        <main>
            <section id="getting-started">
                <h2>Getting Started</h2>
                <p>This tutorial provides a step-by-step guide on harnessing the Mapbox Isochrone API, leveraging Mapbox routing profiles and travel times. By following this tutorial, you will learn how to develop a web application that empowers users to gauge their potential travel distances by foot, bicycle, or car based on predefined time limits.</p>
                <!-- Add more content for each of your subsections -->
            </section>

            <!-- Add content for other sections here -->
        </main>
    </div>

    <!-- Embed the map using an iframe -->
    <div class="map-container">
        <iframe src="https://aqazi2.github.io/home/" width="100%" height="100%"></iframe>
    </div>
</body>
</html>

ruby
Copy code
https://api.mapbox.com/isochrone/v1/mapbox/{profile}/{coordinates}.json?{contours_minutes}&access_token=YOUR_ACCESS_TOKEN
Replace the placeholders like {profile}, {coordinates}, {contours_minutes}, and YOUR_ACCESS_TOKEN with your specific values. For example:

ruby
Copy code
https://api.mapbox.com/isochrone/v1/mapbox/driving/-117.17282,32.71204?contours_minutes=15&polygons=true&access_token=YOUR_ACCESS_TOKEN
Inspecting the JSON Response:

After making your API request, paste it into your web browser's address bar to receive a JSON response. This response contains critical information for your app's functionality:

Geometry Object: This part of the response includes an array of coordinates defining the outlines of the isochrone contour, forming the shape of the reachable area on your map.
Features Object: In this section, you'll find details on how the isochrone should be visually represented, including fill color and opacity. This data allows you to style and draw the isochrone contours on your app's map.
Creating a Map:

To get your app started, the first step is to build a map using Mapbox GL JS. Here's how to set up your HTML file for this purpose:

Open Your Text Editor:

Begin by opening your preferred text editor since you'll be writing HTML, CSS, and JavaScript. A good text editor is essential for this process.
Create a New HTML File:

In your text editor, create a new file and save it as "index.html." This will serve as the main HTML file for your app.
HTML Setup:

Now, set up your HTML file by copying and pasting the following HTML code into your "index.html" file. This code will lay the foundation for your app:
html
Copy code
<!DOCTYPE html>
<html>
  <head>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.tiles.mapbox.com/mapbox-gl-js/v2.6.1/mapbox-gl.js'></script>
    <link href='https://api.tiles.mapbox.com/mapbox-gl-js/v2.6.1/mapbox-gl.css' rel='stylesheet' />
    <style>
      body {
        margin: 0;
        padding: 0;
      }
      #map {
        position: absolute;
        top: 0;
        bottom: 0;
        width: 100%;
      }
    </style>
  </head>
  <body>
    <div id='map'></div>
    <script>
      mapboxgl.accessToken = 'YOUR_ACCESS_TOKEN';
      var map = new mapboxgl.Map({
        container: 'map',
        style: 'mapbox://styles/mapbox/streets-v11',
        center: [-74.50, 40],
        zoom: 9
      });
    </script>
  </body>
</html>
Let's break down the HTML code you've inserted into your "index.html" file:

Document Structure:

The <!DOCTYPE html> declaration specifies the document type as HTML5.
<html> encloses the entire HTML content, serving as the root element of your HTML document.
Head Section:

<head> contains meta-information about the document, external resource links, and styling information.
<meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' /> sets the viewport properties to control the initial scale, maximum scale, and user scalability.
<script> and <link> tags import the Mapbox GL JS resources. The JavaScript file (mapbox-gl.js) and CSS file (mapbox-gl.css) are essential for Mapbox functionality and map style.
The <style> section includes CSS rules that style the webpage. In this case, it sets the body's margin and padding to zero, ensuring the map spans the entire screen. The #map selector defines the styling for the map container, making it absolute-positioned, covering the full vertical height, and having 100% width.
Body Section:

<body> contains the visible content of the web page.
<div id='map'></div> creates a <div> element with the ID "map." This is the container where your map will be displayed on the page.
The <script> tag includes JavaScript code. First, it sets your Mapbox access token by assigning it to mapboxgl.accessToken. Replace 'YOUR_ACCESS_TOKEN' with your actual access token.
Inside the JavaScript code block, mapboxgl.Map creates your map. It specifies that the map should be displayed in the 'map' container, uses the 'mapbox://styles/mapbox/streets-v11' style, centers the map at coordinates [-74.50, 40], and sets an initial zoom level of 9.
Viewing the Rendered Map:

Save your changes in the text editor, and then open the HTML file in your web browser. You will see a rendered map, centered on the Mapbox headquarters in Washington D.C.
This HTML structure and setup serve as the foundation for building your map-based web app using Mapbox GL JS. It's the starting point for adding more functionality and customizing your map to suit your needs.
