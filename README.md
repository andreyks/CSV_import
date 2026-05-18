# CSV → CREATE TABLE

A minimal Symfony Console CLI tool that reads a CSV file and generates a PostgreSQL-compatible `CREATE TABLE` statement. 

Requires PHP ≥ 8.5 and Composer.

## Install steps

    $ git clone ...
    $ cd folder
    $ composer install

## Usage

    $ php app.php create-table data/employees.csv  

OR

    $ chmod +x app.php 
    $ ./app.php create-table data/employees.csv 


## Notes

The implementation assumes a maximum field length of 255 characters.
Data folder is added to git as example. Avoid to push real customer data into git.

## Roadmap

- Validate if table exists
    CREATE TABLE IF NOT EXISTS table_name
- Improve type detection, add more types
- Detect column length from CSV file values

## Seach integration

## Coding Standards

To automatically fix your project, or only check against the need of changes, run:

    $ ./vendor/bin/php-cs-fixer fix  
    $ ./vendor/bin/php-cs-fixer check --diff  

See https://cs.symfony.com/ for more information.