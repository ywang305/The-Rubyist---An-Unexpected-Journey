## Part 1 Foundation

### respond_to, send

> object dynmaic method invoking

```rb
if ticket.respond_to?(request)
  puts ticket.send(request)
else
  puts "No such information available"
end
```

### Class::Constant

It’s also possible to refer to a constant from outside the class definition entirely, using a special constant lookup notation: a double colon (::). Here’s an example of setting a constant inside a class and then referring to that constant from outside the class:

```rb
class Ticket
  VENUES = ["Converntion Center", "Fairgrounds"]
end

puts "The venues are:"
puts Ticket::VENUES
```

### super is just super only

```rb
module M
  def report
    puts "'report' method in module M"
  end
end

class C
  include M
  def report
    puts "'report' method in class C"
    puts "About to trigger the next higher-up report method..."
    super
    puts "Back from the 'super' call."
  end
end
c = C.new
c.report

# 'report' method in class C
# About to trigger the next higher-up report method...
# 'report' method in module M
# Back from the 'super' call.
```

### Special Rule in super arguments

When we call super, we don’t explicitly forward the gears argument that’s passed to initialize. Yet when the original initialize method in Bicycle is called, any arguments provided to the Tandem version are visible. **This is a special behavior of super**. The way super handles arguments is as follows:

```rb
class Bicycle
  attr_reader :gears, :wheels, :seats
  def initialize(gears = 1)
    @wheels = 2
    @seats = 1
    @gears = gears
  end
  def report(by_name)
    puts "report form Bicycle by #{by_name}"
  end
end
class Tandem < Bicycle
  def initialize(gears=9)
    super # here gears is passed as argument implicitly
    @seats = 2
  end
  def report(by_name)
    puts "report form Tandem by #{by_name}"
    super
  end
end

t = Tandem.new
p t.gears # => 9
t.report("Alex")
# report form Tandem by Alex
# report form Bicycle by Alex
```

- Called with no argument list (empty or otherwise), super automatically for- wards the arguments that were passed to the method from which it’s called.
- Called with an empty argument list—super()—super sends no arguments to the higher-up method, even if arguments were passed to the current method.
- Called with specific arguments—super(a,b,c)—super sends exactly those
  arguments.

arguments.

### self context

![image](https://user-images.githubusercontent.com/24782000/144925556-638782e9-7603-4e94-94da-be8a2218201c.png)

### self inner setter pitfall

a setter method, in other words. You have to do self.venue = "Town Hall" rather than venue = "Town Hall"

```rb
class CC
  attr_accessor :name
  def nick
    name = "_nicked"  # this is not self.name
    puts "self.name: #{self.name}"  # nil
    puts "just local name: #{name}" # _nicked to local
  end
end
```

### code block and yield

We’re ready to implement times—or, rather, my_times.

```rb
class Integer
  def my_times
    c = 0
    puts "c = 0"
    puts "until c == #{self}..." # self is the receiver
    until c == self
      yield c
      c+=1
    end
    self # self as the return value just as Integer# times does
  end
end

5.my_times { |i| puts "I'm on iter #{i}"}
=begin
c = 0
until c == 5...
I'm on iter 0
I'm on iter 1
I'm on iter 2
I'm on iter 3
I'm on iter 4
5
=end
```

my_each

```rb
class Array
  def my_each
    c=0
    until c == size
      yield self[c]
      c += 1
    end
    self
  end
end
```

my_map

```rb
class Array
  def my_map
    c=0
    new_arr = []
    until c == size
      new_arr << yield(self[c])
      c += 1
    end
    new_arr
  end
end
```

### block can capture outter

> block can capture outter variable, \
> yet function cannot, reference to scopes

```rb
def block_scope_demo_2
  x = 100
  1.times do
    x = 200
  end
  puts x # => 200
end
block_scope_demo_2
```

### block-local variable

If you wish to preserve the value of fahrenheit defined before the block, Ruby provides block-local variables.

```rb
fahrenheit = 100
celsius.each do |c;fahrenheit|
  fahrenheit = Temperature.c2f(2)
  puts "#{c}\t#{fahrenheit}"
end
```
