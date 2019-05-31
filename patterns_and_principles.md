# Patterns & principles

There are some things we can agree and check for using [Defra ruby style](https://github.com/DEFRA/defra-ruby-style). There are others though that require more context or judgement.

For those things we have this document.

## Aspirations vs current state

New readers note that you are likely to find examples in the code where we have ***not*** followed an agreed pattern or principle.

Most of the time our decisions form after working with the code base for sometime, and as new members join the team.

So when we document something here it is as a statement of our intent to refactor previous code as per the [boy scout rule](https://www.oreilly.com/library/view/97-things-every/9780596809515/ch08.html), and abide by it for anything new we add.

## Code

### Service objects

To ensure our services don’t become a kitchen draw of things we are not sure where to put, we have agreed the following.

- An object can be defined as a service if it does only one thing
- The class name should end with `service`, eg `CompletionService`
- They should expose only a single public method called `run()`
- `run()` should accept whatever parameters are needed to complete the action

To support this we define the following base class that all services should inherit.

```ruby
module WasteEngine
  class BaseService
    def self.run(attrs)
      new.run(attrs)
    end
  end
end

module WasteEngine
  class CompletionService < BaseService
    def run(registration)
      create_submitted_registration(registration)
      send_confirmation_email(registration)
      delete_transient_registration(registration)
    end
  end
end
```

### Object interaction

Where an object needs to interact with another object, that interaction should happen within a private method.

Say we have the following classes

```ruby
class TokenGenerator
  def generate
    ('a'..'z').to_a.shuffle[0,8].join
  end
end

class EntryLog < BaseService
  def log(note)
    token_generator = TokenGenerator.new
    save_to_log(note, token_generator.generate)
  end
  # ...
end
```

If the API for `TokenGenerator` were to change, we'd need to open up the code in `EntryLog.log` and make changes. And if `EntryLog` used it in a number of places that would cause multiple changes.

So we have agreed that when one objects needs to use another, its does so through a private method.

```ruby
class TokenGenerator
  def generate
    ('a'..'z').to_a.shuffle[0,8].join
  end
end

class EntryLog < BaseService
  def log(note)
    save_to_log(note, token)
  end

  private

  def token
    token_generator = TokenGenerator.new
    token_generator.generate
  end
  # ...
end
```
