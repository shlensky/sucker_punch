# Sucker Punch

[THIS IS A WORK IN PROGRESS AND SHOULD NOT BE USED IN PRODUCTION]

Sucker Punch is a Ruby asynchronous processing using Celluloid, heavily influenced by Sidekiq and girl_friday. With Celluloid's actor pattern, we use do asynchronous processing within a single process. This reduces costs of hosting on a service like Heroku along with the memory footprint of having to maintain additional workers if hosting on a dedicated server.

## Installation

Add this line to your application's Gemfile:

    gem 'sucker_punch'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install sucker_punch

## Usage

Configuration:

`app/config/sucker_punch.rb`

```Ruby
  SuckerPunch.config do
    queue worker: LogWorker, size: 10
    queue worker: AwesomeWorker, size: 2
  end
```

Workers:

`app/workers/log_worker.rb`

```Ruby
class LogWorker
  include SuckerPunch::Worker

  def perform(event)
    Log.new(event).track
  end
end
```

All workers should define an instance method `perform`, of which the job being queued will adhere to.

Workers interacting with `ActiveRecord` should take special precaution not to exhause connections in the pool. This can be done with `ActiveRecord::Base.connection_pool.with_connection`, which ensures the connection is returned back to the pool when completed.

`app/workers/awesome_worker.rb`

```Ruby
class AwesomeWorker
  include SuckerPunch::Worker

  def perform(user_id)
    ActiveRecord::Base.connection_pool.with_connection do
      user = User.find(user_id)
      user.update_attributes(is_awesome: true)
    end
  end
end
```

## Gem Name

With all due respect, [@jmazzi](https://twitter.com/jmazzi) is completely responsible for the name, which is totally awesome. If you're looking for a name for something, he is the one to go to.

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

