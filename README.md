# Twitter Follows

## Objectives

1. Create a complex Domain Model
2. Implement an active follow relationship (following )
3. Implement the passive relationship (i.e. followers )  

## Instructions

In Twitter, a user follows many other users. That person can be followed by many people. For example, I follow @coffeedad and @cher. @coffeedad has many followers and so does @cher.

### How you would you domain model this out?

A user can either follow or be followed by another user. Since this relationship can be true of many users, we have a many-to-many relationship between users. Consequently, we need at least two models: the User model and a join model. Creating another model for users (like a followers model) would be redundant, as it would replicate the information present in the User model. A user has many other users through relationships, for a total of two models.

### What tables/foreign keys would you need?

We determined previously that we need two models. In Active Record, each model corresponds to a table. We will have a Users table and a Relationships table.

```ruby
class User < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :username
      t.timestamps
    end
  end
end
```

For the sake of this exercise, we will keep the attributes of the User table simple, with just a username.

```ruby
class CreateRelationships < ActiveRecord::Migration[5.2]
  def change
    create_table :relationships do |t|
      t.integer :follower_id
      t.integer :followed_id
      t.timestamps
    end
  end
end
```

The Relationships table introduces more interesting elements, with us specifying a columns for the follower and followew user ids.

### How would you domain model this out?

```ruby
class Relationship < ActiveRecord::Base
  belongs_to :followed, class_name: "User", foreign_key: :followed_id
  belongs_to :follower, class_name: "User", foreign_key: :follower_id
end
```

In defining our ActiveRecord class, we begin implementing a [self join](https://guides.rubyonrails.org/association_basics.html#self-joins). We must specify the name of the class/model to which the relationship belongs, as well as the foreign key.

```ruby
class User < ActiveRecord::Base
  has_many :active_relationships, class_name: "Relationship", foreign_key: :follower_id, dependent: :destroy
  has_many :followeds, through: :active_relationships, source: :followed
  has_many :passive_relationships, class_name: "Relationship", foreign_key: :followed_id, dependent: :destroy
  has_many :followers, through: :passive_relationships, source: :follower
end
```

We close out the definition of this relationship by implementing our many-to-many self join of users. Like with the Relationship model, we create an alias/method for the relationship between users.

## Resources

* [Rails Tutorial - Following Users](https://rails-4-0.railstutorial.org/book/following_users)

<a href='https://learn.co/lessons/twitter-follows' data-visibility='hidden'>View this lesson on Learn.co</a>
