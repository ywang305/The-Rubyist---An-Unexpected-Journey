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
