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
