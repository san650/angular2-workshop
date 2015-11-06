# Angular 2.0 with angular-cli

Disclaimer: Angular 2.0 and `angular-cli` are in alpha releases. So things will
break!

## 0. Pre-requisites

* [Node](https://nodejs.org/download/release/v4.1.1/) >= v4.1.0

You can test which version of node you have on a terminal

```
$ node -v
v4.1.1
```

## 1. angular-cli

`angular-cli` provides a tooling for Angular 2.0 projects. Some of the highlights:

* Defines a project structure based on conventions using best practices
* Development server
* Test runner
* Blueprints (generators) for components, services, pipes and tests
* Addons

### 1.1 Install angular-cli tool

Install `angular-cli` tool

```
$ npm install -g angular-cli
$ ng --version
version: 1.13.8
node: 4.1.1
npm: 2.14.9
os: darwin x64
```

### 1.2 Generate new Angular 2.0 project

Generate the Angular 2.0 application using the `ng` command and start the
development server.

```
$ ng new music
$ cd music/
$ ng serve
```

Note that on Windows you need to start the development server using Admin
permissions [1](https://github.com/angular/angular-cli#known-issues).

## 2. Creating base structure

### 2.1 Copy assets to src/ folder

Copy the assets to the project

```
$ curl VENDOR_CSS_URL src/vendor.css
$ curl MUSIC_CSS_URL src/app/music.css
$ curl MUSIC_HTML_URL src/app/music.html
$ copy IMAGES/ src/images/
```

### 2.2 Link vendor.css

Include `vendor.css` in `src/index.html` file

```html
<link rel="stylesheet" type="text/css" href="vendor.css">
```

### 2.3 Reference to component's CSS file

Tell `music` component to load it's CSS file. This is done by passing the
`styleUrls` parameter to the component definition.

```js
@Component({
...
styleUrls: ['app/music.css'],
...
})
```

## 3. Fetch albums from an external service

We're going to simulate an external HTTP API so we're going to fetch albums.

### 3.1 Copy dummy json file

Copy the `albums.json` to `src/` folder.

```
$ cp sources/albums.json music/src/
```

### 3.2 Register `http` dependency

We need to do a couple of steps here

1. Import angular2/http js file
2. Register the HTTP module so it can be injected in our app

For the first step we need to register the file on `index.html`

```html
<script src="vendor/angular2/bundles/http.dev.js"></script>
```

For the second step we import `HTTP_PROVIDERS` from `angular2/http` file and
then we add it as a dependency to the bootstrap.

```js
import {bootstrap} from 'angular2/angular2';
import {HTTP_PROVIDERS} from 'angular2/http';
import {MusicApp} from './app/music';

bootstrap(MusicApp, [HTTP_PROVIDERS]);
```

### 3.3 Load the list of albums

New template syntax

* Using `*` to prefix directives e.g. `*ng-for`
* Using `[...]` to bind attributes
* Using `(...)` to bind events
* Using `[(...)]` to do two-way bindings
* Using `#` to create local template variables

Note that in the case of events, you can use `^` sign as prefix to tell
Angular to bubble the event up the component tree.

---

Let's read the list of albums from the context and iterate using the `ngFor`
directive. Let's create it in two steps, first load the list of albums from an
array and then from a json file using the `http` service.

Change the template to generate the list of album covers

src/app/music.html

```html
<div *ng-for="#album of albums">
  <div class="col-sm-3 col-md-3 col-lg-2 media default image-grid genre">
    <a>
      <div class="genreImage" [style.background-image]="'url('+album.image+')'"></div>
    </a>
  </div>
</div>
```

And on the JavaScript side return the list of albums

src/app/music.ts

```js
import {Http} from 'angular2/http';

...

constructor(private http:Http) {
  http
    .get("/albums.json")
    .map(response => response.json()["albums"])
    .subscribe(albums => this.albums = albums)
}

...
```

More info about [`http`](https://angular.io/docs/js/latest/api/http/Http-class.html).
