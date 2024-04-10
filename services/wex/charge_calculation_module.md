
# Charge Calculation Module

This calculation module is based on Data Model defined in https://eaflood.atlassian.net/browse/RUBY-2979?focusedCommentId=584253

Overall criteria and the presence of custom Farming charging requirements (and possibly custom Charity charging requirements) make it a perfect scenario for the use of the Strategy design pattern

#### Charging Strategy Interface

```ruby
# The ChargingStrategy interface declares operations common to all supported versions of
# some algorithm.
#
# The OrderCalculator uses this interface to call the algorithm defined by Concrete
# Strategies.
class ChargingStrategy
  # @abstract
  #
  def calculate_registration_fee(_order)
    raise NotImplementedError, "#{self.class} has not implemented method '#{__method__}'"
  end

  # @abstract
  #
  def calculate_initial_charge(_order)
    raise NotImplementedError, "#{self.class} has not implemented method '#{__method__}'"
  end

  # @abstract
  #
  def calculate_additional_charge(_order)
    raise NotImplementedError, "#{self.class} has not implemented method '#{__method__}'"
  end
end
```


#### Regular Charging Strategy

Concrete Regular Charging Strategy implementation

```ruby
class RegularChargingStrategy < ChargingStrategy
  def calculate_registration_fee(order)
    # Each individual registration will be subject to a registration charge of £41
    # (stored in registration_fee field of the highest band)
    order.highest_band.registration_fee
  end

  def calculate_initial_charge(order)
     # The user shall only ever pay one 'full' compliance charge of whichever is the highest band charge
     # ( stored in initial_compliance_charge field of he highest band)
    order.highest_band.initial_compliance_charge
  end

  def calculate_additional_charge(order)
     # The user shall pay the discounted or Band 3 standard compliance charge/s for all additional exemptions
     # (stored in additional_compliance_charge field of band)
     total = 0
     excluded_exemption = nil
     order.exemption_order_items.each as |exemption_order_item|
       # exclude first exemption of the highest band
        if exemption_order_item.band == order.highest_band && excluded_exemption.nil?
          excluded_exemption = exemption_order_item.exemption
          next 
        end
        total += exemption_order_item.band.additional_compliance_charge
      end
      total
  end
end
```


#### Farmer Charging Strategy

Concrete Farmer Charging Strategy implementation

```ruby
class FarmerChargingStrategy < ChargingStrategy
  def calculate_registration_fee(order)
    # Each individual registration will be subject to a registration charge of £41
    # (stored in registration_fee field of the highest band)
   order.bucket.highest_band.registration_fee
  end

  def calculate_initial_charge(order)
    # There is a £90 compliance charge for any or all of the common on farm exemptions
    # (stored in charge_amount field of bucket)
    order.bucket.charge_amount
  end
 
  def calculate_additional_charge(order)
    # Farmers do not pay additional charge for any 'on farm' exemptions but are charged as normal for all others
    # (stored in additional_compliance_charge field of band)

    # order.bucket_order_items are excluded
    # only charging exemptions out of the bucket
    total = 0
    order.exemption_order_items.each as |exemption_order_item|
       total += exemption_order_item.band.additional_compliance_charge
     end
     total
  end
end
```


#### Charity Charging Strategy

Concrete Charity Charging Strategy implementation

```ruby
class CharityChargingStrategy < Strategy
  # currently not implemented
end
```


#### Order Calculator 

The main class responsinble for performing charge calculations

```ruby
class OrderCalculator
  # The OrderCalculator maintains a reference to one of the Strategy objects. It # does not know the concrete class of a strategy. It should work with
  # all strategies via the Strategy interface.
  attr_writer :strategy

  def initialize(order:, strategy:)
    @order = order
    @strategy = strategy
  end

  # OrderCalculator accepts a strategy through the constructor, but also
  # provides a setter to change it at runtime.
  def strategy=(strategy)
    @strategy = strategy
  end

  def calculate
    calculate_registration_fee + calculate_compliance_charge
  end

  # The OrderCalculator delegates some work to the Strategy object instead of
  # implementing multiple versions of the algorithm on its own.
  def calculate_registration_fee
    @strategy.calculate_registration_fee(@order)
  end

  # The OrderCalculator delegates some work to the Strategy object instead of
  # implementing multiple versions of the algorithm on its own.
  def calculate_compliance_charge
    @strategy.calculate_initial_charge(@order) + @strategy.calculate_additional_charge(@order)
  end
end
```


#### Order Calculator Service

Handy service object that defines the strategy and calls the OrderCalculator

```ruby
class OrderCalculatorService
  def initialize(order)
    @order = order
    @strategy = @order.has_bucket? ? FarmerChargingStrategy.new : RegularChargingStrategy.new
  end
  
  def perform
    OrderCalculator.new(order: @order, strategy: @strategy).calculate
  end
end
```


#### Order

```ruby
class Order
  def bucket
    bucket_order_items.first.bucket
  end

  def has_bucket?
    bucket_order_items.count.positive?
  end
  
  def highest_band
    exemption_order_items.max_by{|k,v| v.band.sequence.to_i}.band
  end
end
```


#### Bucket

```ruby
class Bucket
  def highest_band
    exemption_order_items.max_by{|k,v| v.band.sequence.to_i}.band
  end
end 
```


### Use examples:


1. Invoking OrderCalculator directly

```ruby
order = Order.new
strategy = RegularChargingStrategy.new
calculator = OrderCalculator.new(order:, strategy:)
calculator.calculate
```


2. Via the service

```ruby
order = Order.new
OrderCalculatorService.new(order:).perform
```

