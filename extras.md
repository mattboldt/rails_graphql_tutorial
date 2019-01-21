
# API

```
rails new graphql_api --no-test --api
rails g model User email:string name:string
rails g model Book user:belongs_to title:string
rails db:migrate

gem 'graphql'
gem 'faker'
gem 'rack-cors'
bundle
rails generate graphql:install
bundle
rails generate graphql:object user
rails generate graphql:object book
```

```ruby
# config/application.rb
# CORS config to allow ajax
config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'
    resource '*', headers: :any, methods: [:get, :post, :options]
  end
end

# user.rb
class User < ApplicationRecord
  has_many :books
end

# book.rb
class Book < ApplicationRecord
  belongs_to :user
end

# seeds.rb
5.times do
  user = User.create(name: Faker::Name.name, email: Faker::Internet.email)
  5.times do
    user.books.create(title: Faker::Book.title)
  end
end

# rake db:seed

# app/graphql/types/user_type.rb
module Types
  class UserType < Types::BaseObject
    field :id, ID, null: false
    field :name, String, null: true
    field :email, String, null: true
    field :books, [Types::BookType], null: true
    field :books_count, Integer, null: true

    def books_count
      books.size
    end
  end
end


# app/graphql/types/book_type.rb
module Types
  class BookType < Types::BaseObject
    field :title, String, null: false
  end
end

# app/graphql/types/query_type.rb
module Types
  class QueryType < Types::BaseObject

    field :users, [Types::UserType], null: false

    def users
      User.all
    end

    field :user, Types::UserType, null: false do
      argument :id, ID, required: true
    end

    def user(id:)
      User.find(id)
    end
  end
end

```


### graphiql

```ruby
gem "graphiql-rails"

# routes.rb
if Rails.env.development?
  mount GraphiQL::Rails::Engine, at: "/graphiql", graphql_path: "graphql#execute"
end

# application.rb
require "sprockets/railtie"
```


# Frontend

```
create-react-app frontend
cd frontend
npm install apollo-boost react-apollo graphql --save
yarn start
```
