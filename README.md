# 2019-osc-talk-a-rails-overview
A talk for Open South of Code 2019:  a ruby on rails overview for newbies

# requisites

```bash
sudo systemctl start docker
source .xmodmap-apple
```gem







## terminal show 1: ruby versions showtime

- using `rbenv`

```bash
# first step into new installation is check
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash

rbenv

rbenv versions      #  preguntar por las versiones instaladas
rbenv install -l    #  listar ls posible que podrias instalar, las diferentes implementaciones

rbenv global
rbenv global 2.6.3
ruby --version
rbenv global 2.5.3
ruby --version

rbenv local
cd tech-talent-show/rs-webapp/
rbenv local
cat .ruby-version


rbenv which irb
rbenv whence irb
rbenv whence rails  # mostrar en que versiones de ruby esta instalado una gema 

rbenv shell

rbenv rehash        # a veces tras instalar una gema tienes que ejecutar esto
```



```bash
# Why rbenv is sooo cool!! because it has been fork in every language
phpenv
nodenv
pyenv
goenv
```


BACK TO THE SLIDES















## terminal show 2: libraries in ruby gems

- para gestionar las gemas ruby provee de `gem` cli tool

```bash
gem

gem list                 # nos permite listar gemas instaladas
gem server               # abrir un servidor web con documentacion

gem search --remote --all rails      # gem nos permite buscar 
gem search
gem search rails --remote --all | grep '^rails '


gem install rails                     # gem nos permite instalar
gem install --no-ri --no-rdoc
gem install rails --version=5.2.1
gem install rails -v 5.2.1

gem uninstall rails                   # gem nos permite desistalar
gem uninstall rails -v 5.2.1

gem update rails                      # gem nos permite actualizar



# PERO GEM NO NOS MANEJA LAS DEPENDENCIAS....

```






BACK TO SLIDES


















## terminal show 3: Dependences Fortunely exist bundler



```bash
# bundler debe ser instalado en primer lugar

docker run -it --rm ruby:2.5.3
docker ps -a
docker exec -it c43f5681053a /bin/bash

apt-get install vim

gem install bundler
rbenv rehash

bundle       # bundle needs a Gemfile
cd ; cd osc ; mkdir deleteme ; cd deleteme
bundle init

bundle add bundle-audit           # vamos a añadir una gema a nuestras dependencias
bundle check                      # 
bundle install                    # instalamos todas nuestras dependencias
bundle show                       # visualizamos nuestras dependencias

bundle outdate                    # visualizamos las dependencias que podriamos actualizar
bundle update                     # las actualizamos


bundle exec                       # EL MAS IMPORTANTE, ejecutamos algo en ruby permitiendo
                                  # tan solo usar las gemas que aparecen en el Gemfile
```



Most of the version specifiers,
    like >= 1.0, are self-explanatory.
    The specifier ~> has a special meaning, best shown by example. ~> 2.0.3 is identical to >= 2.0.3 and < 2.1. ~> 2.1 is identical to >= 2.1 and < 3.0. ~> 2.2.beta will match prerelease versions like 2.2.beta.12. ~> 0 is identical to >= 0.0 and < 1.0.

```ruby
ruby '1.9.3', :engine => 'jruby', :engine_version => '1.6.7'
```







BACK TO THE SLIDES








## terminal showtime 4: a first generator (rails new)

Vamos a crear nuestro proyecto, lo voy a hacer sin docker para ir mas rapido en la parte de
rails para ir mas rapido y que se vea mas claro.

```bash
rails new --help

rails new osc --database=postgresql --skip-coffee  #--webpack=vue

# ahora necesitamos una base de datos en postgres
docker pull postgres:11.1

# the rails new command use the convention for your databasename
# project name `osc` => database  `osc_production`
# project name `osc` => database  `osc_development`
# project name `osc` => database  `osc_test`

# luchar contra las convenciones te va a costar tiempo...

docker run -it -p 5432:5432 --name oscpgcontainer postgres:11.1

psql -h localhost -U postgres
```

```sql
# \?
# \l  list databases

CREATE DATABASE osc_production;
CREATE DATABASE osc_development;
CREATE DATABASE os_test;

# \l  list databases
# \d
# \c osc_development
# \dt list tables
```



```bash
cd osc
atom .
```







```yml
# config database.yml
default: &default
  host: localhost
  username: postgres
```

FIJATE en como aprovechamos las convenciones de rails para no ir a contracorriente... como se
llaman las bases de datos osc_development osc_production osc_test

ahora vamos a probar a arrancar rails

este es nuestro primer checkpoint. asi que vamos a usar la magia de git: git time

```bash
git status
git add .
git commit -m "rails new osc --version 5.2.3"
```

pulsado el boton se salvado, vamos a usar otro generador que viene con rails

### PRIMER SCAFFOLD

```bash
bin/rails generate scaffold --help
bin/rails generate scaffold author name:string surname:string slug:string
bin/rails db:migrate

# GO TO THE BROWSER!!!!
# http://localhost:3000/authors
# http://localhost:3000/authors.json
# http://localhost:3000/author/1.json

bin/rails test     # YA TENEMOS TEST


# VAMOS A VER POR ENCIMA LAS MIGRACIONES
bin/rails db:migrate:status
bin/rails db:migrate:down VERSION=20190519224055


# NO VAMOS A DESTRUIRLO PERO PODRIAMOS...
bin/rails destroy scaffold author
git status
git clean -df


# git time
git add .
git commit -m "authors scaffold"
```



### SEGUNDO SCAFFOL

```bash
bin/rails generate scaffold book title:string description:text slug:string: price:decimal author:references
bin/rails db:migrate

# momento traumatico, rails tiene grabado a fuego en su adn los test
bin/rails test
```





```bash

```

paaaaa error!!

```ruby
class Author < ApplicationRecord
  has_many :book, dependent: :destroy
end
```

new checkpoint: git time

```bash
git add .
git commit -m "scaffold books"
# se acabo la demo
```

BACK TO THE SLIDESS









## DEPLOY TIME!!

Para poder desplegar vamos tocar un poco de la configuracion para el entorno de produccion

```ruby
# config/environments/production.rb
config.public_file_server.enabled = ENV['RAILS_SERVE_STATIC_FILES'].present?

if ENV["RAILS_LOG_TO_STDOUT"].present?
  logger           = ActiveSupport::Logger.new(STDOUT)
  logger.formatter = config.log_formatter
  config.logger = ActiveSupport::TaggedLogging.new(logger)
end
```

modificamos el Gemfile con la version de ruby hemos usado

```ruby
# Gemfile
ruby "2.5.3"
```

```bash
heroku create
git config --list | grep heroku
git push heroku master

heroku run rake db:migrate
heroku open
```

```
web bin/rails server -p $PORT -e $RAILS_ENV

```


BACK TO THE SLIDES




## build API with rails


CUT HERE, 45 MINUTES ONLY








