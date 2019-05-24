# 2019-osc-talk-a-rails-overview
A talk for Open South of Code 2019:  a ruby on rails overview for newbies

# requisites

```bash
sudo systemctl start docker
source .xmodmap-apple
```







## terminal show 1: ruby versions showtime

- using `rbenv`

```bash
# first step into new installation is check
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash

rbenv

rbenv versions
rbenv install -l

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
rbenv whence rails

rbenv shell

rbenv rehash
```


Why rbenv is sooo cool!! because it has been fork in every language

```bash
phpenv
nodenv
pyenv
goenv
```



Until now we use a ruby installation into our machine, from now docker a tope


- using `docker run`

now ruby versions using docker


[https://hub.docker.com/_/ruby](https://hub.docker.com/_/ruby)

```bash
docker pull ruby:2.6.3
# while download, why is so important learn docker
# let me overdone
# docker knowledge is a transversal independenly of your stack...
docker run -i -t --rm ruby:2.6.3
```

```ruby
puts RUBY_VERSION
Random.bytes(1)
[1,2,3].difference([3,4])
[1,2,3].union([3,4])
```



BACK TO THE SLIDES











## terminal show 2: libraries in ruby gems

- gems are managed using `gem` cli tool

```bash
gem

gem server

gem search --remote --all rails
gem search
gem search rails --remote --all | grep '^rails '

gem list

gem install rails
gem install --no-ri --no-rdoc
gem install rails --version=5.2.1
gem install rails -v 5.2.1

gem uninstall rails
gem uninstall rails -v 5.2.1

gem update rails
```



but we want to write dependences names and versions into one file the `Gemfile`


BACK TO SLIDES...


















## terminal show 3: Dependences Fortunely exist bundler

bundler is a gem need install

```bash
docker run -it --rm ruby:2.5.3
docker ps -a
docker exec -it c43f5681053a /bin/bash

apt-get install vim

gem install bundler
rbenv rehash

bundle
# bundle needs a Gemfile
cd ; cd osc ; mkdir deleteme ; cd deleteme
bundle init
```

```ruby
source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

gem "rails"
gem 'rails', '>=5.2.2.2'
gem 'rails', '~>5.2.2.2' # '>=5.2.2.2' and '<2.1' # ~> pesimist operator

gem 'rails', '~>6.0.0.rc1

group :development, :test do
  gem 'rspec-rails'
end

gem 'byebug', :group => :development
gem 'byebug', :group => [:development, :test]

gem 'nokogiri', :git => 'https://github.com/tenderlove/nokogiri.git', :branch => '1.4'
```


Most of the version specifiers,
    like >= 1.0, are self-explanatory.
    The specifier ~> has a special meaning, best shown by example. ~> 2.0.3 is identical to >= 2.0.3 and < 2.1. ~> 2.1 is identical to >= 2.1 and < 3.0. ~> 2.2.beta will match prerelease versions like 2.2.beta.12. ~> 0 is identical to >= 0.0 and < 1.0.

```ruby
ruby '1.9.3', :engine => 'jruby', :engine_version => '1.6.7'
```


```bash
cd rails-001
bundle --help

bundle init

bundle
bundle install

bundle check
bundle show

bundle outdated
bundle update
bundle dependency
bundle viz

bundle package

bundle doctor

bundle add
bundle remove
bundle clean

bundle console
bundle exec # execute script into current bundle
```











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
npm i -g yarn
bin/rail webpacker:install
```

```yml
# config database.yml
default: &default
  host: localhost
  username: postgres
```
FIJATE en como aprovechamos las convenciones de rails para no ir a contracorriente... como se
llaman las bases de datos osc_developement osc_production osc_test

ahora vamos a probar a arrancar rails

este es nuestro primer checkpoint. asi que vamos a usar la magia de git: git time

```bash
git status
git add .
git commit -m "rails new osc --version 5.2.3"
```

pulsado el boton se salvado, vamos a usar otro generador que viene con rails


```bash
bin/rails generate scaffold --help
bin/rails generate scaffold author name:string surname:string slug:string
bin/rails db:migrate

# select tablename, indexname from pg_indexes where tablename='authors';


# go to http://localhost:3000/authors
bin/rails db:migrate:status
# check timestamp
bin/rails db:migrate:down VERSION=20190519224055
bin/rails destroy scaffold author

git status
git clean -df

# de nuevo desde el principio
bin/rails generate scaffold author name:string surname:string slug:string
bin/rails db:migrate

bin/rails test

# git time
git add .
git commit -m "authors scaffold"

bin/rails generate scaffold book title:string description:text slug:string: price:decimal author:references
bin/rails db:migrate
```


Antes dije que nos faltaba algo y ese algo es el router que nos permite diferenciar
que controller servira que rutas

http://localhost:3000/authors
http://localhost:3000/books


insisto de nuevo una de las mayores bondades de rails es que tiene grabado a fuego en su adn el testing, vamos a hacer los test

```bash
bin/rails test
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
```



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



##  añadimos la logica de negocio de author

- app/models/author.rb
```ruby
class Author < ApplicationRecord
  validates :name, :surname, :slug, presence: true
  validates :slug, uniqueness: true

end
```

- test/fixtures/authors.yml
```yml
sandi_metz:
  name: Sandi
  surname: Metz
  slug: sandi-metz

katrina_owen:
  name: Katrina
  surname: Owen
  slug: katrina-owen

sam_ruby:
  name: Sam
  surname: Ruby
  slug: sam-ruby

rob_isenberg:
  name: Rob
  surname: Isenberg
  slug: rob-isenberg
```

- db/seeds.rb
```ruby
sandi_metz = Author.create([name: 'Sandi', surname: 'Metz', slug: 'sandi-metz'])
katrina_owen = Author.create([name: 'Katrina', surname: 'Owen', slug: 'katrina-owen'])
sam_ruby = Author.create([name: 'Sam', surname: 'Ruby', slug: 'sam-ruby'])
rob_isenberg = Author.create([name: 'Rob', surname: 'Isenberg',slug: 'rob-isenberg'])
```


- models/author_test.rb

```ruby
require 'test_helper'

class AuthorTest < ActiveSupport::TestCase
  def test_valid_author
    assert Author.new(name: 'valid', surname: 'valid', slug: 'valid-valid').valid?
  end
  def test_invalid_author_name_empty
    refute Author.new(name: nil, surname: 'partial_valid', slug: '-partial_valid').valid?
  end
  def test_invalid_author_surname_empty
    refute Author.new(name: 'partial_valid', surname: nil, slug: 'partial_valid-').valid?
  end
  def test_invalid_author_uniqueness_slug
    assert Author.new(name: 'valid1', surname: 'valid1', slug: 'invalid-slug').save
    refute Author.new(name: 'valid2', surname: 'valid2', slug: 'invalid-slug').valid?
  end
end
```



## añadimos la logica de negocio de books

- app/models/book.rb

```ruby
class Book < ApplicationRecord
  validates :title, :slug, presence: true , uniqueness: true
end
```

- db/seeds.rb
```ruby
agile_web_development_with_rails51 = Book.create(
  [
    title: 'Agile web development with rails 5.1',
    brief: 'a classic book to start with rails, published for every major rails version...',
    slug: 'agile-web-development-with-rails-5-1:'
  ]
)

bottles_of_OOP = Book.create(
  [
    title: '99 Bottles of OOP',
    brief: 'a book for improve your ruby skill, of brilliant Katrina owen and Sandi Metz',
    slug: '99-bottles-of-oop',
  ]
)

practical_object_oriented_design_in_ruby = Book.create(
  [
    title: 'Practical object-oriented design in ruby',
    brief: 'Meticulously pragmatic and exquisitely articulate, POODiR make otherwise elusice knowledge available',
    slug: 'practical-object-oriented-design-in-ruby',
  ]
)

docker_for_rails_developer = Book.create(
  [
    title: 'Docker for rails developer',
    brief: 'build, ship and run your application everywhere',
    slug: 'docker-for-rails-developers'
  ]
)
```

- test/fixtures/books.yml
```yml
agile-web-development-with-rails-5-1:
  title: 'Agile web development with rails 5.1'
  brief: 'a classic book to start with rails, published for every major rails version...'
  slug: 'agile-web-development-with-rails-5-1:'

99bottlesOOP:
  title: '99 Bottles of OOP'
  brief: 'a book for improve your ruby skill, of brilliant Katrina owen and Sandi Metz'
  slug: 99-bottles-of-oop

practical-object-oriented-design-in-ruby:
  title: 'Practical object-oriented design in ruby'
  brief: 'Meticulously pragmatic and exquisitely articulate, POODiR make otherwise elusice knowledge available'
  slug: 'practical-object-oriented-design-in-ruby'

docker-for-rails-developer:
  title: 'Docker for rails developer'
  brief: 'build, ship and run your application everywhere'
  slug: 'docker-for-rails-developers'
```

- test/models/book_test.rb

```ruby
require 'test_helper'

class BookTest < ActiveSupport::TestCase
  def test_valid_book
    assert Book.new(title: 'valid title', brief: 'a valid brief of book', slug: 'valid-title').valid?
  end
  def test_invalid_book_title_nil
    refute Book.new(title: nil, brief: 'a valid brief of book', slug: 'valid-title').valid?
  end
  def test_invalid_book_title_uniqueness
    assert Book.new(title: 'book valid 1', brief: 'a valid brief of book', slug: 'book-valid-1').save
    refute Book.new(title: 'book valid 1', brief: 'a valid brief of book', slug: 'book-valid-2').valid?
  end
  def test_invalid_book_title_empty
    refute Book.new(title: '', brief: 'a valid brief of book', slug: 'valid-title').valid?
  end
  def test_invalid_book_slug_nil
    refute Book.new(title: nil, brief: 'a valid brief of book', slug: nil).valid?
  end
  def test_invalid_book_slug_empty
    refute Book.new(title: '', brief: 'a valid brief of book', slug: '').valid?
  end
  def test_invalid_book_slug_uniqueness
    assert Book.new(title: 'book valid 1', brief: 'a valid brief of book', slug: 'book-valid-1').save
    refute Book.new(title: 'book valid 2', brief: 'a valid brief of book', slug: 'book-valid-1').valid?
  end
end
```



## ejecutamos los test




## build API




## añadimos test de la API


## volvemos a las transparencias HEROKU







