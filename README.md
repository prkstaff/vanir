# Vanir

Vanir is a database masking tool.

    $ cat example.sql
    INSERT INTO `users` (name, email) VALUES ("Fumiaki MATSUSHIMA", "mtsmfm@gmail.com");
    $ cat example.yml
    users: # Table name
      name: "AAA" # Simply replace all name with "AAA"
      email: "{{.First 6}}.{{.Hashed}}.{{.Last 3}}@example.com" # You can use go template here
    $ cat example.sql | vanir --config example.yml > masked.sql
    Masking `users`...
    $ cat masked.sql
    insert into users(name, email) values ('AAA', 'mtsmfm.hr9FYQ5ZbHCEonO9ucjozR6DhBzyPwW.com@example.com');

## Requirement for local development/build
    ```
    go get github.com/xwb1989/sqlparser@a9cdf22bd561e715bba34ee228cb1c06bfa2719c
    ```
## Usage

1. Write config file:

  ```yaml
  users: # Table name
    name: "AAA" # Simply replace all name with "AAA"
    email: "{{.Raw}}+{{.Hashed}}@example.com" # You can use go template here
  ```

2. Pipe the dumped SQL:

At this time, vanir can mask SQL dumped with `--complete-insert` option only.

        mysqldump --complete-insert | vanir -c path/to/config.yml

### Formatting

You can use [Go templates](https://golang.org/pkg/text/template/) to format output.

| Function     | Configuration                   | Input                 | Output                                      |
|--------------|---------------------------------|-----------------------|---------------------------------------------|
| Hashed       | `{{.Hashed}}@example.com`       | mtsmfm@gmail.com      | hr9FYQ5ZbHCEonO9ucjozR6DhBzyPwW@example.com |
| First(n)     | `{{.First 3}}@example.com`      | mtsmfm@gmail.com      | mts@example.com                             |
| Last(n)      | `{{.Last 3}}@example.com`       | mtsmfm@gmail.com      | com@example.com                             |
| Raw          | `{{.Raw}}@example.com`          | mtsmfm@gmail.com      | mtsmfm@gmail.com@example.com                |
| Salt         | `{{.Salt}}@example.com`         | mtsmfm@gmail.com      | RzXBbxLFGNUzuy1ppryBQu@example.com          |
| RandInt(n)   | `{{.RandInt 3}}@example.com`    | mtsmfm@gmail.com      | 012@example.com                             |
| RandString(n)| `{{.RandString 3}}@example.com` | mtsmfm@gmail.com      | abx@example.com                             |

## Installation

Donwload an executable binary for your platform:

https://github.com/mtsmfm/vanir/releases

## Development

### Requirements

- docker
- docker-compose

### Setup

    git clone https://github.com/mtsmfm/vanir
    cd vanir
    docker-compose run app go-wrapper download

### Compile

    docker-compose run app go-wrapper install

### Run

    docker-compose run app go-wrapper run
