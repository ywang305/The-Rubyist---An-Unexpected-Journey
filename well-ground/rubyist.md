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

## Part 2 Built-in

### ==, equl?, equal?

String redefines == and eql? to return whether the value of the strings being compared are identical.

```sh
>> string1 = "text"
=> "text"
>> string2 = "text"
=> "text"
>> string1 == string2
=> true
>> string1.eql?(string2)
=> true
>> string1.equal?(string2)
=> false
```

As you can see, the strings are == and eql?, but not equal?.

Why do we have == and eql? Because it gives us more flexibility as we subclass Object.

```sh
>> 5 == 5.0
=> true
>> 5.eql? 5.0
=> false
```

### spaceship operator

```rb
class Bid
  include Comparable # come with Ruby
  attr_accessor :estimate
  def <=>(other_bid)
    self.estimate <=> other_bid.estimate
  end
end
```

### string

- string[-12]

```sh
>> string = "Ruby is a cool language."
=> "Ruby is a cool language."
>> string[5]
=> "i"
>> string[-12]  # -12
=> "o"
```

- string[5, 10] : substring start + length

```sh
 >> string[5,10] # 10 is sub length
 => "is a cool "
```

- string[7...14] : substring with range

```sh
>> string[7...14]
=> " a cool"
>> string[-12..-3]
=> "ol languag"
>> string[-12..20]
=> "ol langua"
>> string[15...-1]
=> "language"
```

- string[regexp]

```sh
>> string[/c[ol ]+/]
=> "cool l"
```

- string[substring] : substring by substring. what's the purpose? see next part

```sh
>> string["cool lang"]
=> "cool lang"
>> string["very cool lang"]
=> nil
```

- substring assignment

```sh
>> string = "Ruby is a cool language."
=> "Ruby is a cool language."
>> string["cool"] = "great" # cool, replace once first meet
=> "great"
>> string
=> "Ruby is a great language."
>> string[-1] = "!"
=> "!"
>> string
=> "Ruby is a great language!"
>> string[-9..-1] = "thing to learn!" # cool
=> "thing to learn!"
>> string
=> "Ruby is a great thing to learn!"
```

- "+ or <<"

```sh
>> str = "Hi "
=> "Hi "
>> str << "there." # append, mutating origin str
=> "Hi there."
>> str
=> "Hi there."
```

- string.include?
- string.start_with?
- string.end_with?
- string.empty? : "", not " "
- string.index
- string.rindex
- string.count : frequency

```sh
# Still using the string "Ruby is a cool language.", there are three occurrences of "a":
>> string.count("a")
=> 3

# To count how many of a range of letters there are, you can use a hyphen-separated range:
>> string.count("g-m")
=> 5

# You can also provide a written-out set of characters you want to count:
>> string.count("aey. ")
=> 10

# To negate the search, put a ^ (caret) at the beginning of your specification:
>> string.count("^aey. ")
=> 14
>> string.count("^g-m")
=> 19
>> string.count("^g-m") + string.count("g-m") == string.size
=> true

# You can combine the specification syntaxes and even provide more than one argument:
>> string.count("ag-m")
=> 8
>> string.count("ag-m", "^l")
=> 6 # Counts "a" and "g-m", except for "l"
```

- string.ord / string.chr: char <-> int

```sh
>> "a".ord
=> 97
>> 97.chr
=> "a"
```

- ==, equal?

```sh
>> "a" == "a"
=> true
>> "a".equal?("a")
=> false
```

- ljust, rjust, center

```sh
>> string = "David A. Black"
=> "David A. Black"
>> string.rjust(25)
=> "           David A. Black"
>> string.ljust(25)
=> "David A. Black           "
>> string.rjust(25,'.')
=> "...........David A. Black"
>> string.rjust(25,'><')
=> "><><><><><>David A. Black"
>> "The middle".center(20, "*")
=> "*****The middle*****"
```

- chop, chomp

```sh
>> "David A. Black".chop
=> "David A. Blac"
>> "David A. Black\n".chomp
=> "David A. Black"
>> "David A. Black".chomp('ck')
=> "David A. Bla"
```

- clear, replace, delete: mutating as names suggest, without exclamtion mark

```sh
>> string = "David A. Black"
=> "David A. Black"
>> string.clear
=> ""
>> string
=> ""
>> string = "(to be named later)"
>> string.replace("David A. Black")
=> "David A. Black"
>> "David A. Black".delete("abc")
=> "Dvid A. Blk"
>> "David A. Black".delete("^abc") # delete all except a, b, c
=> "aac"
>> "David A. Black".delete("a-e","^c") # delete a-e except c
=> "Dvi A. Blck"
```

- succ, next

```sh
>> "a".succ
=> "b"
>> "abc".succ
=> "abd"
>> "azz".succ
=> "baa"
```

- upto, downto

```rb
"a8".upto("b6") {|s| print s, ' ' }
for s in "a8".."b6"
  print s, ' '
end
"9".upto("11").to_a   #=> ["9", "10", "11"]
"25".upto("5").to_a   #=> []
"07".upto("11").to_a  #=> ["07", "08", "09", "10", "11"]
```

### symbol

```sh
>> sym = :david
=> :david
>> sym.upcase
=> :DAVID
>> sym.succ
=> :davie
>> sym[2]
=> "v" # Note that indexing into a symbol returns a substring, not a symbol.
>> sym.casecmp(:david)
=> 0
```

### number

```rb
n = 99.6
m = n.round # round to nearest integer
x = 12
if x.zero? # if x is zero
  puts "x is zero"
else
  puts "x is not zero"
end
puts "The ASCII character equivalent of 97 is #{97.chr}"
```

### Times and Dates

## Part 3 Dynamics
