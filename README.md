# Angular with GoogleMaps integration.

https://github.com/luiscoco/AngularWithGooglemaps_Sample3-Geolocation

Here's an Angular application that integrates the provided TypeScript code to show a simple Google Maps:

1.Create a new Angular blank application.
```
ng new googlemaps_simplemap
```

2.Install the required dependencies:
```
npm install @types/googlemaps
```

3.Navigate inside the project folder 
```
cd project-folder
```
and create a new Angular component. Let's call it MapComponent:
```
ng generate component Map
```

4.Open the generated map.component.ts file and replace the content with the following code:
```typescript
import { Component, OnInit } from '@angular/core';

declare const google: any;

@Component({
  selector: 'app-map',
  template: `
    <div id="map" style="width: 100%; height: 400px;"></div>
  `,
  styles: [`
    #map {
      height: 100%;
    }
  `]
})
export class MapComponent implements OnInit {
  ngOnInit() {
    this.initMap();
  }

  async initMap(): Promise<void> {
    let map: google.maps.Map, infoWindow: google.maps.InfoWindow;

    map = new google.maps.Map(document.getElementById("map") as HTMLElement, {
      center: { lat: -34.397, lng: 150.644 },
      zoom: 6,
    });
    infoWindow = new google.maps.InfoWindow();

    const locationButton = document.createElement("button");

    locationButton.textContent = "Pan to Current Location";
    locationButton.classList.add("custom-map-control-button");

    map.controls[google.maps.ControlPosition.TOP_CENTER].push(locationButton);

    locationButton.addEventListener("click", () => {
      // Try HTML5 geolocation.
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position: GeolocationPosition) => {
            const pos = {
              lat: position.coords.latitude,
              lng: position.coords.longitude,
            };

            infoWindow.setPosition(pos);
            infoWindow.setContent("Location found.");
            infoWindow.open(map);
            map.setCenter(pos);
          },
          () => {
            this.handleLocationError(true, infoWindow, map.getCenter()!);
          }
        );
      } else {
        // Browser doesn't support Geolocation
        this.handleLocationError(false, infoWindow, map.getCenter()!);
      }
    });
  }

  handleLocationError(
    browserHasGeolocation: boolean,
    infoWindow: google.maps.InfoWindow,
    pos: google.maps.LatLng
  ) {
    infoWindow.setPosition(pos);
    infoWindow.setContent(
      browserHasGeolocation
        ? "Error: The Geolocation service failed."
        : "Error: Your browser doesn't support geolocation."
    );
  }
  }
```

5.Open the app.module.ts file and import the MapComponent:
```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';
import { MapComponent } from './map/map.component';

@NgModule({
  declarations: [
    AppComponent,
    MapComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

6.Open the app.component.html file and add the <app-map></app-map> tag:
```html
<h1>My Angular App</h1>
<app-map></app-map>
```

7.Open the tsconfig.app.json file in the root directory of your Angular project.
Add "node_modules/@types" to the "types" array. Your tsconfig.app.json file should look like this:
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/app",
    "types": [
      "node",
      "googlemaps"
    ]
  },
  "files": [
    "src/main.ts",
    "src/polyfills.ts"
  ],
  "include": [
    "src/**/*.d.ts"
  ]
}
```

8.In the root directory of your Angular project, open the angular.json file.
Inside the "build" target, ensure that the "options" property has a "polyfills" property with the value set to "src/polyfills.ts". It should look like this:

```
"options": {
  "polyfills": "src/polyfills.ts",
  ...
},
```

9.Now, create a new file called polyfills.ts in the src folder of your project. Add the following content to the polyfills.ts file:
```typescript
import 'zone.js/dist/zone';  // Included by default in Angular CLI projects
```


10.Confirm that you have added the Google Maps API script correctly in the index.html file. It should be placed just before the closing </body> tag, like this:
```html
<script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places"></script>
```

11.Start the application with the command:
```
ng serve -o
```
