# How to create a Laravel + Vue + Inertia Project ?
#### To create a laravel, vue and inertia project please follow these steps :-

## For Server-Side

### Install dependencies

First, install the Inertia server-side adapter using the Composer package manage

```bash
composer require inertiajs/inertia-laravel
```

### Root template

Next, setup the root template that will be loaded on the first page visit to your application. This will be used to load your site assets (CSS and JavaScript), and will also contain a root `<div>` in which to boot your JavaScript application.

```blade
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
    @vite('resources/js/app.js')
    @inertiaHead
  </head>
  <body>
    @inertia
  </body>
</html>

```

This template should include your assets, as well as the `@inertia` and `@inertiaHead` directives.

By default, Inertia's Laravel adapter will assume your root template is named `app.blade.php`. If you would like to use a different root view, you can change it using the `Inertia::setRootView()` method.


### Middleware

Next we need to setup the Inertia middleware. You can accomplish this by publishing the `HandleInertiaRequests` middleware to your application, which can be done using the following Artisan command.

```bash

php artisan inertia:middleware


```

Once the middleware has been published, append the `HandleInertiaRequests` middleware to the web middleware group in your application's `bootstrap/app.php` file.


```php
use App\Http\Middleware\HandleInertiaRequests;

->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        HandleInertiaRequests::class,
    ]);
})

```

This middleware provides a `version()` method for setting your asset version, as well as a `share()` method for defining shared data.


### Creating responses

That's it, you're all ready to go server-side! Now you're ready to start creating Inertia pages and rendering them via responses.

```php

use Inertia\Inertia;

class EventsController extends Controller
{
    public function show(Event $event)
    {
        return Inertia::render('Event/Show', [
            'event' => $event->only(
                'id',
                'title',
                'start_date',
                'description'
            ),
        ]);
    }
}

```

## Also you have to do some additional changes 

### ViteJs configuration setup
You have to set up the vite js and configure it to work perfectly with the laravel and vue js architecture

```js

import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import  vue  from "@vitejs/plugin-vue";

export default defineConfig({
    plugins: [
        
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
        vue(),
    ],
});


```

### TailwindJs configuration setup
If you want to work with the tailwindcss (maybe you will) you have to configure it with the laravel and vuejs architecture

```js

import defaultTheme from 'tailwindcss/defaultTheme';

/** @type {import('tailwindcss').Config} */
export default {
    content: [
        './vendor/laravel/framework/src/Illuminate/Pagination/resources/views/*.blade.php',
        './storage/framework/views/*.php',
        './resources/**/*.blade.php',
        './resources/**/*.js',
        './resources/**/*.vue',
    ],
    theme: {
        extend: {
            fontFamily: {
                sans: ['Figtree', ...defaultTheme.fontFamily.sans],
            },
        },
    },
    plugins: [],
};



```



## For Client-Side

First of all install the vuejs 

```bash

npm install vue


```

### Install dependencies

First, install the Inertia client-side adapter corresponding to your framework of choice.

```bash

npm install @inertiajs/vue3


```

### Install @@vitejs/plugin-vue

```bash

npm install @vitejs/plugin-vue


```


### Initialize the Inertia app

Next, update your main JavaScript file to boot your Inertia app. To accomplish this, we'll initialize the client-side framework with the base Inertia component.

```bash

import { createApp, h } from 'vue'
import { createInertiaApp } from '@inertiajs/vue3'

createInertiaApp({
  resolve: name => {
    const pages = import.meta.glob('./Pages/**/*.vue', { eager: true })
    return pages[`./Pages/${name}.vue`]
  },
  setup({ el, App, props, plugin }) {
    createApp({ render: () => h(App, props) })
      .use(plugin)
      .mount(el)
  },
})


```

The `setup` callback receives everything necessary to initialize the client-side framework, including the root Inertia `App` component.



### Setup into the App.js to set up the entry point of the vue app into laravel

You have to set up the entry point into the laravel app. Please `copy` and `past` the code into the `App.js` in under you `resources/js/App.js` file


```js

import { createApp, h } from 'vue'
import { createInertiaApp } from '@inertiajs/vue3'

createInertiaApp({
  resolve: name => {
    const pages = import.meta.glob('./Pages/**/*.vue', { eager: true })
    return pages[`./Pages/${name}.vue`]
  },
  setup({ el, App, props, plugin }) {
    createApp({ render: () => h(App, props) })
      .use(plugin)
      .mount(el)
  },
})



```



## Production setup (For shared hosting)

When you are on shared hosting you have to set up the software so that it can work on the hosting efficiently. 


### Setup the `app.blade.php` for the production

For production update the `app.blade.php` code :-

```blade

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
    <title>{{ config('app.name') }}</title>

    
    <!-- Change these scripts and css paths with the actual build in css and js files -->
    <link rel="stylesheet" href="{{ asset('build/assets/app-BtW0pRbl.css') }}">
    <script  src="{{ asset('build/assets/app-BOO1F6JN.js') }}" defer></script>
    
    <!-- Manually include dynamic head -->
    <script>
        document.title = 'Inertia Example'; // You can change this dynamically
    </script>
  </head>
  <body>
    <div id="app" data-page="{{ json_encode($page) }}"></div>

    <!-- Mounting Inertia -->
    <script>
        import { createApp, h } from 'vue';
        import { createInertiaApp } from '@inertiajs/vue3';

        createInertiaApp({
            resolve: name => import(`./Pages/${name}.vue`),
            setup({ el, App, props }) {
                createApp({ render: () => h(App, props) })
                    .mount(el);
            },
        });
    </script>
  </body>
</html>




```

## Basic starter code 

### Frontend

```vue
<script setup>
import Layout from './Layout'
import { Head } from '@inertiajs/vue3'

defineProps({ user: Object })
</script>

<template>
  <Layout>
    <Head title="Welcome" />
    <h1>Welcome</h1>
    <p>Hello {{ user.name }}, welcome to your first Inertia app!</p>
  </Layout>
</template>


```



### Backend (Laravel)

```php

use Inertia\Inertia;

class UserController extends Controller
{
    public function show(User $user)
    {
        return Inertia::render('User/Show', [
          'user' => $user
        ]);
    }
}


```

### Web.php (Router)

```php

Route::get('/test', function(){
    return  Inertia::render('Test', [
        'user' => 'protik'
      ]);
});



```


### Build the frontend with the npm before uploading into the production server

You have to build the frontend with this command to genereate the production ready compressed assets and css, js and manifest.json files

```bash

npm run build


```

After this you have to upload the files on the production server

And that's how you can create and develop a laravel + vue + inertia project








