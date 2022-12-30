# Local Development

Each repository has its own README, and there will be instructions about how to run them on your machine. But let's get familiar with them first!

## App

I started the project with TypeScript out of habit but I didn't use any TypeScript code other than in the authentication context provider. Although if you want to go full bland, I can remove those for you.

But I feel insecure when dealing with a typeless codebase. Meet JSDoc: "JSDoc is a markup language used to annotate JavaScript source code files" - wikipedia. VSCode supports them too. They're just comments, nothing fancy. But it was a huge pain in the a** to use JSDoc. And you'll see lots of eslint warnings about imports not being used, while they're used in the JSDoc comment/type! You might take a look at the file `src/components/Logo.jsx` for what I'm talking about.

### So files

Every global component is in the `src/components` folder. Large components can have their own local components folder. All pages are in the pages folder with names that match the route they will render to with `react-router`.

### React Router

is used extensively to render parts and pages including nestedu Layouts, which is a pretty under-rated feature

Coupled with React Suspense to take the game to the next level, though react-router had minor issue with suspense and streaming, which is why you'll see even more `<Suspense>` hanging around routers `<Outlet />`. Wait, that's going a bit deep :)

### SWR = Stale While Revalidate

Used almost everywhere for fetching, prefetching, caching, revalidating and error handing, of data. Also paired with Suspense, that's why you might never see any loading guard (condition) when fetching data.

### Other

The brand font Epilogue is bundled in the app with `fontsource`, in other words there is no font CDN, which means a (or more) network roundtrip for fonts is eliminated! ✌️

`react-app-rewired` is used for customizing `create-react-app` to use a babel plugin which helps the local dev server to not get bogged down by MUI, specifically `icons-material`.

The minimum node version is 16 LTS. Please make sure yours is up to date before proceeding with local setup.

## Server

Laravel with default packages and nicely spaced comments to get a outline of what the code is doing, because simply using a good variable name is never enough!

Anyway, you should be looking at the controllers at `app/Http/Controllers`, Models, `database/migrations.php` and `routes/api.php`

Right now the authentication is not that secure, and it's not persisted by/to the client either. And the tokens will be stored in the database, which is also not good IMHO.

We'll implement a secure (`httpOnly`) cookie auth (token in a cookie) to make the authentication both secure and persistant.

A httpOnly cookie is not accessible by the client. It will be the browser itself that will send the cookie on every request the client make. The server should check the cookie for a token it's aware of.

And possibly we don't have to store the token in the database by using a solution like JSON Web Token!

Now you can follow the README in the backend repository.

## Database

Create a new database from your PlanetScale Dashboard, give it a short name, and possibly select a region close to you, or your audience! Press the Connect -> Connect with -> Laravel -> Copy .env button. And keep it around.

At this point I assume you have cloned both repositories locally and followed the README. Then you should have a file called .env in your backend folder. Paste the PlanetScale env's in the place where other variables started with `DB_`. Meaning replace DB variables with the new ones.

This is your Cloud Serverless MySQL. You can use your phpmyadmin too, which I haven't tested. However, I was able to connect to MySQL Workbench with these connection strings and run queries there.

There are two separate branches for production and development in PlanetScale. By default it's a development branch. Each branch will have different connection strings (env).

# Deployment

Deploy the backend first. In your Railway dashboard, create a new project from Github repo, install the Railway Github App in this organization, Select the backend repo, click Add variables -> Raw Editor -> paste the env variables you copied from PlanetScale earlier. Then put these at the bottom:

```env
APP_NAME=Laravel
APP_ENV=production
APP_KEY=base64:wFec06GZaNYilrjnfQDkC3GQYGhej5E/py7e0YbLeJs=
APP_DEBUG=false
APP_URL=https://jobsearch-api-production.up.railway.app
SESSION_DOMAIN="${APP_URL}"
SANCTUM_STATEFUL_DOMAINS="${APP_URL}"
LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug
BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120
NIXPACKS_BUILD_CMD=composer install --optimize-autoloader --no-dev && php artisan config:cache && php artisan route:cache && php artisan view:cache && php artisan migrate
```

You should change the `APP_URL` to the URL from the front-end deployment below later. The deployment should be successful at this point, then go to the settings tab and Generate a domain, that will be the backend URL.

Now deploy the frontend the same way, this time you'll see a Suggested variable `REACT_APP_API`. Change the URL to the backend URL you just deployed. Generate a domain and you're ready to go ahead and create a jobenv

# App Flow

When you register you just need to provide your names, email and password. Next, you can apply or create a job. There is no role, so you can do both. Completing the profile is not required to do anything.

When you create a job, your company name and avatar will be propagated to your profile.

When applying, you're required to give your location, phone number, resume and other details. Some of them will reflect or replace (if already there) the ones on your profile.

There are two search boxes, but the one in home is stateless, it's just a placeholder, all the searching and filtering will happen on the find jobs page. In addition, the job URL is readable and shareable. You can even share the application URL directly!

And finally when you refresh the browser everything will get vanished.
