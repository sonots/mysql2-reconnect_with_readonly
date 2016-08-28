# Mysql2::ReconnectWithReadonly

Reconnect your mysql2 connection if `Mysql2::Error: The MySQL server is running with the --read-only option so it cannot execute this statement` occurs because of failover.

When mysql2 cluster failovers, the mysql2 master is depromoted to slave and will be READONLY. Such case we have to reconnect mysql2 connection so that new connection goes to new master which is writable.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'mysql2-reconnect_with_readonly'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install mysql2-reconnect_with_readonly

## Usage

```ruby
require 'mysql2/reconnect_with_readonly'
```

## Configuration

This gem tries to reconnect `reconnect_attempts` times.
It will wait `initial_retry_wait * number of retries` seconds on each retry.
The waiting interval can be suppressed up to `max_retry_wait` seconds.

```
Mysql2::ReconnectWithReadonly.reconnect_attempts = 10     # default: 3 (times)
Mysql2::ReconnectWithReadonly.initial_retry_wait = 1.0    # default: 0.5 (sec)
Mysql2::ReconnectWithReadonly.max_retry_wait     = 5.0    # default: nil which means no max (sec)
Mysql2::ReconnectWithReadonly.logger = Logger.new(STDOUT) # default: nil
```

## Implementation

This gem monkey patches `Mysql2::Client`.

## Development

To create a read-only mysql server:

```
$ mysql -uroot
root> GRANT  CREATE, ALTER, DELETE, INSERT, UPDATE, SELECT ON *.* TO 'test'@'localhost' identified by 'test'
root> SET GLOBAL read_only = ON;
```

Connect with `test` user (`root` has write privilege even in read_only mode):

```
$ bin/console
> mysql2 = Mysql2::Client.new(username: 'test', password: 'test')
> mysql2.query(%Q[create database 'test'])
```

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/sonots/mysql2-reconnect_with_readonly. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

## ChangeLog

[CHANGELOG.md](./CHANGELOG.md)
