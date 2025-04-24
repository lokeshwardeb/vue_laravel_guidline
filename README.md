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




