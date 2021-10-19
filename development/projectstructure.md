# Project Structure

## Project Layout

All Go source code is located in the `src` directory.

The entrypoint of Refractor is within `main.go`. The main function facilitates setup of various components such as config loading, logging, database connection, as well as application components. Dependency injection is heavily used to provide the various application components where they are needed.

Models, interfaces and other domain components can be found in the `domain` folder.

Implementations of core application components (repositories, services, etc) can be found within the `internal` folder. Most files in the `domain` folder will have a corresponding package within `internal`. These corresponding packages will often contain child packages which handle storage logic (repositories), business logic (services) and delivery (handlers, e.g HTTP API).

The `pkg` folder contains more generic packages not directly tied to the application domain, but important nonetheless. In here, things such as helper functions, query builders, internal permission libraries and more can be found.

The `params` folder contains our data transfer objects.

The `migrations` folder contains database migrations.

The `platforms` folder contains platform definitions.

The `games` folder contains game definitions via child packages including game configs.

The `auth` folder contains the templates and logic for Kratos functionality.

## Repositories, Services and Handlers

Because Refractor is built with support for many games in mind, it is important to have modularity to ensure that the code is flexible and modular.

To achieve this, the bulk of the code is divided into three types:

-   Repositories (storage, persistence)
-   Services (business logic)
-   Handlers (delivery, REST API, etc)

Consider the following example.

### Example

We have a domain entity called Player:

```go
type Player struct {
    PlayerID string
    Name     string
    Kills    int
    Deaths   int
}
```

Player needs to be stored in a database, so we create a repository for it. This repository must implement the `PlayerRepo` interface seen below:

```go
type PlayerRepo interface {
    Store(player Player) error
    GetByID(id string) (Player, error)
    Delete(id string) error
}
```

Assume that PostgreSQL is our chosen database. What we can do is create a package in `internal/player/repos` called `postgres` and inside this package we create a `player_repo.go` file.

Inside this file, we create a `playerRepo` struct and attach the required methods to implement the PlayerRepo:

```go
type playerRepo struct {}

func (r *playerRepo) Store(player Player) error {
    // Run insert query, check for errors, etc
    return nil
}

func (r *playerRepo) GetByID(id string) (Player, error) { ... }
func (r *playerRepo) Delete(id string) error { ... }
```

The `playerRepo` struct can now be used as a player repository!

But wait, our player entity has business logic associated with it! We want to validate the player's name before storing them. For this, we create a **service**.

We come up with the following interface for our service:

```go
type PlayerService interface {
    Store(player Player) error
    GetByID(id string) (Player, error)
    Delete(id string) error
}
```

> Notice how similar this is to the repo. Typically, a service will have quite a bit of overlap with the repository. However, services can also have many more methods which the repository doesn't have. Services are where our business logic lives.

Just like our implementation for the repo, we create a struct with the necessary functions attached. Notice how the repository gets injected into the service struct.

```go
type playerService struct {
    repo PlayerRepo
}

func (s *playerService) Store(player Player) error {
    // Make sure name isn't equal to "admin"
    if (player.Name === "admin") {
        return error("name cannot be admin")
    }

    // If name is valid, store the player
    return s.repo.Store(player)
}

func (s *playerService) GetByID(id string) (Player, error) {
    // Since there is no extra business logic to run here, we just
    // call the repo GetByID method straight away.
    //
    // It's still important to have this method to make it easier
    // to add business logic in the future should the need arise!

    return s.repo.GetByID(id)
}

func (s *playerService) Delete(id string) error { ... }
```

This service can now be consumed (via dependency injection) by handlers (e.g a REST API router) or other services. For the sake of brevity, I will not show an example of a handler.

**But wait**

Uh oh, someone requested support for a MySQL database because they can't use Postgres. What will we do? Supporting MySQL could take a very large refactor which can be very time consuming...

Fortunately for us, we decoupled our application layers! Because our repositories are separate from our business logic and delivery methods we don't actually need to Refactor anything! All we have to do is create an alternate player repository which uses MySQL instead of Postgres. Then the repos can be swapped simply by injecting the MySQL player repository into the service in place of the Postgres repo.

### Why should I care?

You should care because writing decoupled code is very important to save lots of work and headaches in the future. It can seem like it takes a lot of boilerplate up front, and you'd be correct, but it is well worth it in the long run. Throughout the process of building Refractor, the time it took to write the extra boilerplate code has paid off by speeding up testing and by limiting refactors needed to support new features. I find that decoupling the code this much actually **speeds up development time** in most cases.

If you plan on contributing to Refractor, please take care to follow this approach and avoid crossing domain boundaries unnecessarily.
