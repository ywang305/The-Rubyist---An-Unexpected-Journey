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

```sh
>> puts DateTime.new(2009, 1, 2, 3, 4, 5)
2009-01-02T03:04:05+00:00
=> nil
>> puts DateTime.now
2018-12-15T19:02:29-05:00
=> nil
>> puts DateTime.parse("October 23, 1973, 10:34 AM")
1973-10-23T10:34:00+00:00
```

### Date/Time arithmetic operators:

- Time on seconds

```sh
>> t = Time.now
=> 2018-12-15 19:19:39 -0500
>> t - 20 # subtract 20 seconds
=> 2018-12-15 19:19:19 -0500
>> t + 20
=> 2018-12-15 19:19:59 -0500
```

- DateTime on days / months

```rb
dt = DateTime.now
<DateTime: 2021-12-13T15:36:19+00:00 ((2459562j,56179s,881691230n),+0s,2299161j)>
puts dt + 100 # add 100 days
<DateTime: 2022-03-23T15:36:19+00:00 ((2459662j,56179s,881691230n),+0s,2299161j)>
puts dt >> 3 # shift 3 months
<DateTime: 2021-09-13T15:36:19+00:00 ((2459471j,56179s,881691230n),+0s,2299161j)>
puts dt << 3 # shift 3 months back
<DateTime: 2022-03-13T15:36:19+00:00 ((2459652j,56179s,881691230n),+0s,2299161j)>
```

- Date next next\_\* prev\_\*

```sh
>> d = Date.today
<Date: 2018-12-15 ((2458134j,0s,0n),+0s,2299161j)>
>> puts d.next # next day
2018-12-16
>> puts d.next_year
2019-12-16
>> puts d.next_month(3)
2018-12-15
>> puts d.prev_day(10)
2018-12-05
```

- Date/Time upto downto

```sh
>> d = Date.today
=> #<Date: 2018-12-15 ((2458134j,0s,0n),+0s,2299161j)>
>> next_week = d + 7
=> #<Date: 2018-12-22 ((2458141j,0s,0n),+0s,2299161j)>
>> d.upto(next_week) {|date| puts "#{date} is a #{date.strftime("%A")}" }
2018-12-15 is a Saturday
2018-12-16 is a Sunday
2018-12-17 is a Monday
2018-12-18 is a Tuesday
2018-12-19 is a Wednesday
2018-12-20 is a Thursday
2018-12-21 is a Friday
2018-12-22 is a Saturday
```

## Part 2: Ruby Collections

### Array

- new array

  ```rb
  a = Array.new
  a = Array.new(3) # [nil, nil, nil]
  a = Array.new(3, "apple") # ["apple", "apple", "apple"]
  a = Array.new(3) {|i| i * i} # [0, 1, 4]

  a = [1, 2, 'three', [] ] # literal array


  # Array will look up for object's to_ary, then to_a, then wrap the object in an array.
  string = "A string"
  Array(string) # ["A string"]
  # so let's def the to_ary
  def string.to_a
    split(//)
  end
  Array(string) # ["A", " ", "s", "t", "r", "i", "n", "g"]

  # %w and %W, create array of strings
  %w(Joe Leo III) # ["Joe", "Leo", "III"]
  # % takes any valid delimiter, but most often curly braces or brackets:
  %w{ Joe Leo III } # ["Joe", "Leo", "III"]
  # But if you need double- quoted strings, you can use %W instead of %w:
  %W(Joe is #{2018 - 1981} years old.) # ["David", "is", "37", "years", "old."]

  # %i and %I, create array of symbols
  %i(a b c) # [:a, :b, :c]
  ```

- array methods

  - range

    ```rb
    a = %w(red orange yellow purple gray indigo violet)
    a[3,2] # ["purple", "gray"]
    a[3,2] = "green", "blue" # ["red", "orange", "yellow", "green", "blue", "indigo", "violet"]

    a = %w(red orange yellow purple gray indigo violet)
    a[3..5] # ["purple", "gray", "indigo"]
    a[1..2] = "green", "blue" # ["red", "green", "blue", ...]
    ```

  - dig nested element

    ```rb
    [["Joe", %w(loves Lucy,), "his"],"adorable", ["daughter"]].dig(0,1,1) # "Lucy,"
    ```

  - unshift

    ```rb
    a = [1, 2, 3]
    a.unshift(0) # [0, 1, 2, 3]
    ```

  - push <<

    ```rb
    a = [1, 2, 3]
    a.push(4) # [1, 2, 3, 4]
    a << 5 # [1, 2, 3, 4, 5]

    # The methods << and push differ in that push can take more than one argument
    a.push(6, 7, 8) # [1, 2, 3, 4, 5, 6, 7, 8]
    a << 9 << 10 # [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    ```

  - shiift pop

    ```rb
    a = [1, 2, 3, 4, 5]
    a.pop # return 5
    a.shift # return 1

    # shift and pop can remove more than one element at a time.
    a = %w{ one two three four five }
    a.pop(2) # ["four", "five"]
    a.shift(2) # ["one", "two"]
    ```

  - concat, replace, +

    ```rb
    a = [1, 2, 3]
    a.concat([4, 5, 6]) # a is mutated as [1, 2, 3, 4, 5, 6]
    a.replace([7, 8, 9]) # a is mutated as [7, 8, 9]

    b = a + [4, 5, 6] # a is not mutated, b is new [1, 2, 3, 4, 5, 6]
    ```

  - flatten

    ```rb
    a = [1,2,[3,4,[5],[6,[7,8]]]]
    a.flatten # [1,2,3,4,5,6,7,8] fully flatten
    a.flatten(1) # [1, 2, 3, 4, [5], [6, [7, 8]]] one level
    a.flatten(2) # [1,2,3,4,5,6,[7,8]] two level
    ```

  - reverse

    ```rb
    a = [1, 2, 3]
    a.reverse # [3, 2, 1]
    a.reverse! # a is mutated as [3, 2, 1]
    ```

  - join , \*

    ```rb
    ["abc", "def", 123].join # "abcdef123"
    ["abc", "def", 123].join("") # "abcdef123"
    ["abc", "def", 123].join(",") # "abc,def,123"

    # In a great example of Ruby’s design style, there’s another way to join an array: the * method. It looks like you’re multiplying the array by a string, but you’re actually per- forming a join operation
    a = %w(one two three)
    a * "-" # "one-two-three"
    ```

  - uniq

    ```rb
    [1,2,3,1,4,3,5,1].uniq # [1,2,3,4,5]
    ```

  - compact \
    This method returns a new array identi- cal to the original array, except that all occurrences of nil have been removed:

    ```rb
    zip_codes = ["06511", "08902", "08902", nil, "10027", "08902", nil, "06511"]
    zip_codes.compact # ["06511", "08902", "08902", "10027", "08902", "06511"]
    ```

  - a.size, a.length, a.count
  - a.emtpy?
  - a.include?(item)
  - a.count(item)
  - a.first(n=1)
  - a.last(n=1)
  - a.sample(n-1) # n random elements

### Hash

- new

  ```rb
  h = {"a"=> 100, b: 200} # key can be hybrid
  Hash.new # {}
  Hash["Connecticut", "CT", "Delaware", "DE"] # {"Connecticut"=>"CT", "Delaware"=>"DE"}
  ```

- retrieve

  ```rb
  state_hash.fetch("Nebraska", "Unknown state")
  state_hash = { "New Jersey" => "NJ",
               "Connecticut" => "CT",
               "Delaware" => "DE" }
  two_states = state_hash.values_at("New Jersey","Delaware") # ["NJ", "DE"]
  state_hash.fetch_values("New Jersey", "Wyoming") # raises KeyError(key not found: "Wyoming")

  # To create a default behavior, pass a block to fetch or fetch_values. Rather than rais- ing an error, the unknown key will be appended to the resulting array:
  state_hash.fetch_values("New Jersey", "WYOMING") do |key|
    key.capitalize
  end # ["NJ", "Wyoming"]
  ```

- default value/behavior

  ```rb
  h = Hash.new(0) # {} # default value is 0 if any nonexistent key is accessed
  h["no such key!"]  # 0 , note the key "no such key!" is not in the hash yet
  # In other words, saying h["blah"] doesn’t mean that h now has a "blah" key

  # If you want references to nonexistent keys to cause the keys to come into existence,
  h = Hash.new {|hash,key| hash[key] = 0 }
  h["new key!"] # 0 , now h has a "new key!" key because of the block behavior
  h # {"new key!"=>0}
  ```

- update , merge

  ```rb
  h1 = {  first:  "Joe",
          last:   "Leo",
          suffix: "III" }
  h2 = { suffix: "Jr." }
  h1.update(h2) # mutate
  puts h1[:suffix] # "Jr." , override

  h1 = {  first:  "Joe",
          last:   "Leo",
          suffix: "III" }
  h2 = { suffix: "Jr." }
  h3 = h1.merge(h2)
  p h1[:suffix] # "III" , no override
  ```

- select , reject

  ```rb
  h = hash[1,2,3,4,5,6]
  h.select {|k,v| k>1} # {3=>4, 5=>6}
  h.reject {{|k,v| k>1}} # {1=>2}
  ```

- keep_if , delete_if

  > use thme, dont use select! or reject!

- compact
  > The compact method works similarly for hashes as it does for arrays, eliminating any keys containing nil values:
  ```rb
  { street: "127th Street", apt: nil, borough: "Manhattan" }.compact # { street: "127th Street", borough: "Manhattan" }
  ```
- invert
- clear, replace

  > in-place method

- querying

  ```rb
  h = {"a"=>100, "b"=>200}
  h.keys # ["a", "b"]
  h.values # [100, 200]
  h.has_key?("a") # true
  h.has_value?(100) # true
  h.empty? # false
  h.size # 2
  h.length # 2
  h.count # 2
  h.merge({"c" => 300}) # {"a"=>100, "b"=>200, "c"=>300}
  h.merge!({"c" => 300}) # {"a"=>100, "b"=>200, "c"=>300}
  h.delete("a") # 100
  h.delete_if {|k,v| v < 200} # {"b"=>200}
  h.reject {|k,v| v < 200} # {"b"=>200}
  h.select {|k,v| v < 200} # {"a"=>100}
  h.invert # {100=>"a", 200=>"b"}
  h.shift # ["a", 100]
  h.clear # {}
  h.to_a # [["a", 100], ["b", 200]]
  h.to_s # "a=>100, b=>200"
  h.inspect # "{"a"=>100, "b"=>200}"
  h.to_h # {"a"=>100, "b"=>200}
  ```

- Hashes as final method arguments

  > If you call a method in such a way that the last argument in the argument list is a hash, Ruby allows you to write the hash without curly braces.

  ```rb
  def add_to_city(id, info)
    p info # {:city=>"flushing", :state=>"ny", :population=>20122}
    puts "population: ", info[:population] # population:  20122
  end
  add_to_city(10001, city: "flushing", state: "ny", population: 20122)
  ```

- A detour back to argument syntax: named (keyword) arguments

  > Using named arguments saves you the trouble of “unwrapping” hashes in your methods.

  ```rb
  def m(a:, b:)
    p a,b
  end

  m(b:2, a:1) # note: sequence not matters
  ```

  > You can make keyword arguments optional by supplying default values

  ```rb
  def m(a:, b: 20)
    p a,b
  end

  m(a:1)
  ```

  > If the method’s parameter list includes a double- starred name, the variable of that name will sponge up all unknown keyword argu- ments into a hash

  ```rb
  def m(a: 1, b: 2, **c)
   p a,b,c # 1, 2, {...}
  end

  m(x: 1, y: 2)  # 1, 2, {:x=>1, :y=>2}
  ```

### Set

it is not core class,

```rb
require 'set'
```

- new

  ```rb
  s = Set.new
  s = Set.new(["a", "b"])
  s = Set.new(1..3)
  s = Set.new([1,2,3])

  s = Set.new(["a", "b"]) {|name| name.upcase } # Set<{ "A", "B" }>
  ```

- adding

  ```rb
  tri_state = Set.new(["New Jersey", "New York"]) # <Set: {"New Jersey", "New York"}>
  tri_state << "Connecticut" #<Set: {"New Jersey", "New York", "Connecticut"}>
  tri_state << "Connecticut"  # second time, no change

  tri_state.add("Connecticut") # always self
  tri_state.add?("Pennsylvania") # return self
  tri_state.add?("Pennsylvania") # return nil if not added
  ```

- merge

  ```rb
  tri_state = Set.new(["New Jersey", "New York"]) # <Set: {"New Jersey", "New York"}>
  states = Set.new(["New York", "Connecticut"]) # <Set: {"New York", "Connecticut"}>
  tri_state.merge(states) # <Set: {"New Jersey", "New York", "Connecticut"}>
  ```

- removing

  ```rb
  tri_state = Set.new(["New Jersey", "New York"]) # <Set: {"New Jersey", "New York"}>
  tri_state.delete("New Jersey") #<Set: {"New York"}>
  tri_state.delete("Connecticut") # nil
  ```

- intersection &

  ```rb
  tri_state = Set.new(["New Jersey", "New York"]) # <Set: {"New Jersey", "New York"}>
  states = Set.new(["New York", "Connecticut"]) # <Set: {"New York", "Connecticut"}>
  tri_state & states # <Set: {"New York"}>
  ```

- union |, +

  ```rb
  tri_state = Set.new(["New Jersey", "New York"]) # <Set: {"New Jersey", "New York"}>
  states = Set.new(["New York", "Connecticut"]) # <Set: {"New York", "Connecticut"}>
  tri_state | states # <Set: {"New Jersey", "New York", "Connecticut"}>
  tri_state + states # <Set: {"New Jersey", "New York", "Connecticut"}>
  ```

- difference -

  ```rb
  tri_state = Set.new(["New Jersey", "New York"]) # <Set: {"New Jersey", "New York"}>
  states = Set.new(["New York", "Connecticut"]) # <Set: {"New York", "Connecticut"}>
  tri_state - states # <Set: {"New Jersey"}>
  ```

- exclusive_or ^

  ```rb
  tri_state = Set.new(["New Jersey", "New York"]) # <Set: {"New Jersey", "New York"}>
  states = Set.new(["New York", "Connecticut"]) # <Set: {"New York", "Connecticut"}>
  tri_state ^ states # <Set: {"New Jersey", "Connecticut"}>
  ```

- subset?, superset?

  ```rb
  small_states = Set.new(["Connecticut", "Delaware", "Rhode Island"])
  tiny_states = Set.new(["Delaware", "Rhode Island"])
  tiny_states.subset?(small_states) # true
  small_states.superset?(tiny_states) # true
  ```

### Enumerable

- predict on array

  ```rb
  # Does the array include Louisiana?
  states.include?("Louisiana") # true
  # Do all states include a space?
  states.all? {|state| state =~ / / } # false
  # Does any state include a space?
  states.any? {|state| state =~ / / } # true
  # Is there one, and only one, state with "West" in its name?
  states.one? {|state| state =~ /West/ } # true
  # Are there no states with "East" in their names?
  states.none? {|state| state =~ /East/ } # true
  ```

- predict on hash

  ```rb
  # Does the hash include the key "Texas"?
  states.key?("Texas") # true
  # alternative include hash key
  states.include?("Louisiana")
  # Do all states have a population greater than 100,000?
  states.all? {|state, population| population > 100000 }
  # Does any state have a population greater than 100,000?
  states.any? {|state, population| population > 100000 }
  # Is there one, and only one, state with a population greater than 100,000?
  states.one? {|state, population| population > 100000 }
  # Are there no states with a population greater than 100,000?
  states.none? {|state, population| population > 100000 }
  # Do all states include a space?
  states.keys.all? {|state| state =~ / / }
  ```

- predict on range

  ```rb
  r = Range.new(1, 10)
  r.one? {|n| n == 5 } # true
  r.none? {|n| n % 2 == 0 } # false


  # But a range between two floats C can’t behave like a finite collection of discrete values. It’s meaningless to produce “each” float inside a range.
  r = Range.new(1.0, 10.0)
  r.one? {|n| n == 5 } # TypeError (can't iterate from Float)

  # But You can use a float as a range’s end point and still get enumeration, as long as the start point is an integer
  r = Range.new(1, 10.3)
  1..10.3
  r.any? {|n| n > 5 } # true
  ```

- find, find_all, select, reject

  ```rb
  states = { "Texas" => 90000, "New York"=>800, "New Jersey"=>600}

  p states.find {|state, popu| popu>500} # result is one element
  p states.find_all {|state, popu| popu>500} # result is array
  p states.select {|state, popu| popu>500} # also a hash
  p states.reject {|s, p| p>1000} # result is also a hash

  => ["Texas", 90000]
  => [["Texas", 90000], ["New York", 800], ["New Jersey", 600]]
  => {"Texas"=>90000, "New York"=>800, "New Jersey"=>600}
  => {"New York"=>800, "New Jersey"=>600}
  ```

- ===, grep

  ```rb
  colors = %w(red orange yellow green blue indigo violet)
  colors.grep(/o/)  # ["orange", "yellow", "indigo", "violet"]
  ```

  something fancy

  ```rb
  miscellany = [75, "hello", 10...20, "goodbye"]

  miscellany.grep(String) # ["hello", "goodbye"]
  miscellany.grep(50..100) # [75]
  ```

  In general, the statement enumerable.grep(expression) is functionally equivalent to this:

  `enumerable.select {|element| expression === element }`

  In other words, it selects for a truth value based on calling ===. In addition, grep can take a block, in which case it yields each element of its result set to the block before returning the results:

  ```sh
  >> colors = %w(red orange yellow green blue indigo violet)
  => ["red", "orange", "yellow", "green", "blue", "indigo", "violet"]
  >> colors.grep(/o/) {|color| color.capitalize }
  => ["Orange", "Yellow", "Indigo", "Violet"]
  ```

  The full grep syntax\
  `enumerable.grep(expression) {|item| ... }`

  thus operates in effect like this:

  `enumerable.select {|item| expression === item}.map {|item| ... }`

- group_by, partition

  ```sh
  >> colors = %w(red orange yellow green blue indigo violet)
  => ["red", "orange", "yellow", "green", "blue", "indigo", "violet"]
  >> colors.group_by {|color| color.size }
  => {3=>["red"], 6=>["orange", "yellow", "indigo", "violet"],
      5=>["green"], 4=>["blue"]}
  ```

  The partition method is similar to group_by, but it splits the elements of the enu- merable into two arrays based on whether the code block returns true for the ele- ment.

  ```rb
  class Person
    attr_accessor :age
    def initialize(options)
      self.age = options[:age]
    end
    def teenager?
      (13..19) === age # cool
    end
  end

  people = 10.step(25,3).map {|i| Person.new(:age => i) }
  teens =  people.partition {|person| person.teenager? } # [[...], [...]]
  ```

## Part 3 Dynamics

```

```
