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

### Presenters, helpers and decorators

We use presenters, helpers and decorators to remove complexity from views and models. Which one you use is on a case-by-case basis, but we have some loose rules.

Use **presenters** for a single view that requires a lot of logic. Don't bulk up models with methods that are only needed for display purposes - put them in a presenter instead.

Things that should be in a presenter:

- methods that you want to call on a model but are only required for use in views
- methods that are only required for a single view
- methods that determine which text should be displayed (like a long `if/else` block that determines which version of a heading to show)
- methods that return a `true` or `false` to determine the structure of a page (like a `show_actions_panel?` method)

Things that should not be in a presenter:

- methods that return HTML
- ability checks involving user roles and CanCanCan
- methods that are used across multiple views - put these in a decorator or helper instead

Use **decorators** in a similar way to presenters, but for logic which you use across multiple presenters.

Use **helpers** for more generic logic that you intend to be reused in lots of contexts – like a method that takes a first and last name, and outputs them as a single string.

It is OK to use ability checks in helpers.

### Conditional view partials

If you have a partial view which is displayed conditionally, then put the conditional around the `render` tag, not in the partial itself.

For example, do this:

```ruby
# View
if condition_is_true?
  render "shared/my_partial"
end

# Partial
<p>Wow, a partial!</p>
```

And not this:

```ruby
# View
render "shared/my_partial"

# Partial
if condition_is_true?
  <p>Wow, a partial!</p>
end
```

This makes the partial more reusable. It's also easier to get an overview of the whole page from the view that renders it.

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

### One-off rake tasks

We use rake tasks to run certain processes on command. This includes tasks like generating the changelog, or sending a test email to check configuration.

Sometimes we need to use a task only once – for example, if we are checking the validity of a database migration.

One-off tasks should be stored in `lib/tasks/one_off`.

When you write a one-off task, you should create an issue to remind you to delete the task after it's served its purpose, so it doesn't clutter up the repo. The task will still be recorded in the GitHub history if we ever need to refer back to it.
