# Environment Setup

> You can view Refractor repositories on our [GitHub](https://github.com/RefractorGSCM/).

### Overview

Refractor has two main components: the backend and the frontend. In the future we may have multiple frontends available, but for now the only available frontend application is the Svelte based Refractor-Svelte single page application (SPA).

The Refractor backend is written entirely in [Go](https://go.dev/) and currently uses [PostgreSQL](https://www.postgresql.org/) as it's database. It provides a RESTful API which is consumed by authenticated the frontend.

Authentication is handled by a wonderful identity management service called [ORY Kratos](https://www.ory.sh/kratos/). Kratos allows us access to secure authentication without the risks associated with writing a custom identity management solution for Refractor.

### 1. Obtaining the Source Code

You can clone the repositories using Git. The following commands can be used to clone both the backend and frontend applications:

```bash
git clone git@github.com:RefractorGSCM/Refractor
```

```bash
git clone git@github.com:RefractorGSCM/Refractor-Svelte
```

### 2. Backend Setup

You must have Go, docker and docker-compose installed to use the built in dev environment.

To launch the backend services, `cd` into the backend source code you cloned in step 1 and run the following command:

```bash
make up
```

This is a make command which will automatically spin up the required services. For more info, see `docker-compose.dev.yml` and the `Makefile`.

The following services will be created:

-   A postgres database (named `postgresd`)
-   Kratos (named `refractor_kratos`)
-   Mailhog (named `refractor_mailhog`)

#### Setting up app.env development variables

You must create an `app.env` file in the root directory and populate it with the environment variables listed below.

```env
MODE=dev
DB_DRIVER=postgres
DB_SOURCE=postgresql://postgres:password@127.0.0.1:5432/refractor?sslmode=disable
KRATOS_PUBLIC_ROOT=http://127.0.0.1:4433
KRATOS_ADMIN_ROOT=http://127.0.0.1:4434
FRONTEND_ROOT=http://127.0.0.1:3000
SMTP_CONNECTION_URI=smtp://@127.0.0.1:1025
ENCRYPTION_KEY=notsecure
INITIAL_USER_EMAIL=dev@refractor.dmas.dev
INITIAL_USER_USERNAME=dev
```

You can customize `ENCRYPTION_KEY`, `INITIAL_USER_EMAIL` and `INITIAL_USER_USERNAME` but you should leave the rest as is.

### 3. Launching the Backend

You can run the backend with the following command:

```bash
go run src/main.go
```

If you were not presented with any errors, proceed to the next step.

### 4. Refractor-Svelte Setup

Refractor-Svelte uses npm to manage dependencies and run scripts. `cd` into the frontend source code you cloned in step 1 and run `npm install` to install the dependencies.

Once done, you can run `npm run dev` and navigate your browser to `http://localhost:3000` to access the frontend application.

Refractor-Svelte uses Vite. Vite allows for blazing fast hot-reloading which greatly speeds up frontend development.

### 5. Logging In

With the backend and frontend services both running, you should be presented with a login screen upon visiting `http://localhost:3000`.

When the backend first started, it sent out an invitation email which you need to get access to. Before you go checking your email, the development environment is not equipped with a real SMTP server so no emails were actually sent.

Instead, we use Mailhog to intercept emails and allow us to view them without having to work with any live SMTP servers.

You can access Mailhog by navigating to the following URL: `http://http://127.0.0.1:8025/`.

Once there, you should see your welcome email. Open it and click the link. You should then be redirected to the Kratos-powered settings page to set your password.

After selecting a password, you should be redirected to Refractor-Svelte and presented with an email verification page. Go into Mailhog once again and this time open the verification email and click the link. You should then be redirected to the Refractor dashboard.

### Setup Complete

If all of the above steps were completed successfully, your dev environment should now be up ready to go.

Happy coding!
