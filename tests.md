# Tests

All information and guidance related to testing and how we do it are recorded here. In general

- we use [RSpec](https://rspec.info/)
- we use unit tests, some integration tests, but don't include feature tests directly in our projects
- we aim for a minumum of 90% test coverage, and use [SimpleCov](https://github.com/colszowka/simplecov) to track it

## Traits over setup

We use [Factory Bot](https://github.com/thoughtbot/factory_bot) to create our test objects in Rspec. With factory_bot comes [Traits](https://www.rubydoc.info/gems/factory_bot/file/GETTING_STARTED.md#Traits) which

> [..] allow you to group attributes together and then apply them to any factory.

For example

```ruby
factory :story do
  title { "My awesome story" }

  trait :published do
    published { true }
  end

  trait :unpublished do
    published { false }
  end
end
```

When writing a test and creating an object you can specify which trait to use. Often in new tests there isn't an existing trait, or there is one but you need something else.

```ruby
test_story = build(:story, :published, in_print: false)
```

The issue we have found is that as time goes on, the required conditions of the object may change, and then there is contention as to whether the existing trait gets updated or the test code.

To avoid this our agreed pattern is that *all object setup should be in a trait*.

```ruby
factory :story do
  title { "My awesome story" }

  trait :published do
    published { true }
  end

  trait :unpublished do
    published { false }
  end

  trait :not_in_print do
    in_print { false }
  end

  trait :published_but_not_in_print do
    unpublished
    not_in_print
  end
end

###

test_story = build(:story, :published_but_not_in_print)
```

## Unit Vs Integration

### Context

We agree that unit tests will require one `it` definition for each expected behaviour.
So that a `context` is allowed multiple scenarios.

We agree that we will keep all expectations for a given request or integration test which touches the database context to a maximum of one scenario, with multiple expectations on it.
This will allow us to test more scenarios (like the forms in the persistent object to being updated and the request status return) in one go and save test suite time.

### Factories

We have agreed that we will not use Factoris in our unit tests, this will make sure that no r/w to the DB is executed giving best performance.
We can also make sure we control any signal that the code exchange with the integrated object.

### Forms

We have agreed that Forms functionality should be covered by pure unit tests as forms specs (`spec/forms`).
We have agreed that Forms integrated functionality is tested through the `request`'s scenarios.

### Requests

We agree to use factories with traits in all our request specs.
Our request specs will mainly check:
  - Response status (redirect, success, failure)
  - Persistence of the data after `POST`
  - Errors raised by invalid parameters

(? Share scenarios ?)

### Services

We have agreed that some services requires integration level testing, while some others do not.
Is up to the wise developer to chose the road to follow.

Usually we might decide to write integration testes on services if:
  - The service is the main entry point of a _background job_;
  - The service contains very complex logic that cannot be split in multiple object and hence the unit test will be too tedious to construct;
We might decide to not use an integration approach if:
  - The service is already integration-tested through request tests;
  - The service performs very simple operations

### Background jobs

We have agreed that we will not test rake tasks.
We do test scheduled jobs for correctness of informations in a unit-style, but no unit nor integration test is added for them.
This is becasue we want every one of our Rake task to just contain a call to a `Service` class.
We can then test our code through an integration test in the service itself.

#### One Off tasks

We can choose to keep code in the rake tasks rather than create a service, and hence, if this is the quickes choise, we will create tasks tests.

## Assemble - Assert - Act

We agree that tests should divide their Assert, Assemble and Act logic using empty lines. For example

```ruby
RSpec.describe SimpleService do
  describe ".run" do
    it "Does A, B and C" do
      required_object = build(:required_object) # Assemble

      result = described_class.run(required_object: required_object) # Act

      expect(result).to have_done(:a) # Assert
      expect(result).to have_done(:b) # Assert
      expect(result).to have_done(:c) # Assert
    end
  end
end
```
