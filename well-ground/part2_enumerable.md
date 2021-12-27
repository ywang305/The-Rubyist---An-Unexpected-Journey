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

- first
  ```sh
  >> [1,2,3,4].first
  => 1
  >> (1..10).first
  => 1
  >> {1 => 2, "one two" => "three"}.first
  => [1, 2]
  ```
- take , drop, take_while, drop_while

  ```sh
  >> states = %w(NJ NY CT MA VT FL NT)

  >> states.take(2)
  => ["NJ", "NY"]
  >> states.drop(2)
  => ["CT", "MA", "VT", "FL", "NT"]

  >> states.take_while {|s| /N/.match(s) }
  => ["NJ", "NY"]
  >> states.drop_while {|s| /N/.match(s) }
  => ["CT", "MA", "VT", "FL", "NT"]
  ```

- min, min_by, minmax, minmax_by

  ```rb
  %w(Ruby C APL Perl Smalltalk).min # "APL"
  %w(Ruby C APL Perl Smalltalk).min {|a,b| a.size <=> b.size } # "C"
  ```

  A more streamlined block-based approach, though, is to use min_by or max_by, which perform the comparison implicitly:

  ```rb
  %w{ Ruby C APL Perl Smalltalk }.min_by {|lang| lang.size } # "C" No need to compare two parameters explicitly in code block
  ```

  ```rb
  %w{ Ruby C APL Perl Smalltalk }.minmax # ["APL", "Smalltalk"]
  %w{ Ruby C APL Perl Smalltalk }.minmax_by {|lang| lang.size } # ["C", "Smalltalk"]
  ```

  In the case of hashes, min and max use the keys to determine ordering.

  ```rb
  state_hash = {"New York" => "NY", "Maine" => "ME", "Alaska" => "AK", "Alabama" => "AL" }
  state_hash.min # ["Alabama", "AL"]
  state_hash.min_by {|name, abbr| name } # ["Alabama", "AL"]
  state_hash.min_by {|name, abbr| abbr } # ["Alaska", "AK"]
  ```

- reverse_each
  ```rb
  [1,2,3].reverse_each {|e| puts e * 10 }
  ```
- each_index, each_with_index, each.with_index (better)

  ```rb
  [1,2,3].each_with_index {|e, i| puts e * 10 + i }
  %w(a b c).each_index {|i| puts i } # 0 1 2

  array = %w{ red yellow blue }
  array.each.with_index do |color, i| # i start from 0
    puts "#{i}: #{color}"
  end
  # 0: red ...

  names.each.with_index(1) do |color, i| #  start from 1
    puts "#{i}: #{color}"
  end
  # 1: red ...
  ```

- each_slice , each_cons

  ```rb
  [1,2,3,4,5,6,7,8,9,10].each_slice(3) {|slice| puts slice.join(",") }
  # 1,2,3
  # 4,5,6
  # 7,8,9
  # 10
  ```

  `each_cons` is similar to `each_slice`, but it takes a number of elements at a time.

  ```rb
  [1,2,3,4,5,6,7,8,9,10].each_cons(3) {|cons| puts cons.join(",") }
  # 1,2,3
  # 2,3,4
  # 3,4,5
  # 4,5,6
  # 5,6,7
  # 6,7,8
  # 7,8,9
  # 8,9,10
  ```

- slice_before, slice_after, slice_when

  ```rb
  parsed_report = ["Top Secret Report", "Eyes Only", "=====", "Title: The Meaning of Life"]
  parsed_report.slice_before(/=/).to_a
  # => [["Top Secret Report", "Eyes Only"],[ "=====", "Title: The Meaning of Life"]]

  [1,2,3,3,4,5,6,6,7,8,8,8,9,10].slice_when { |i,j| i == j }.to_a
  # => [[1,2,3],[3,4,5,6],[6,7,8],[8],[8,9,10]]
  ```

- cycle

  cycle(1) is, in effect, another way of saying each.

  ```rb
  [1,2,3].cycle(2) {|e| puts e } # 1 2 3 1 2 3
  ```

- inject, reduce

  ```rb
  [1,2,3].inject(0) {|acc, cur| acc + cur } # 6
  [1,2,3].reduce(0) {|acc, cur| acc + cur } # 6
  ```

  if you don't pass an initial value, the first element is used as the initial value.

  In fact, the preceding example is purposefully verbose to describe how an accumulator works. We can simplify the expression:

  ```rb
  [1,2,3,4].inject(:+) # 10
  ```

- map, collect
  ```rb
  names.map {|name| name.upcase }
  names.map(&:upcase)
  ```
- Strings as quasi-enumerables

  each_char, each_codepoint, each_line, each_byte, each_char, each_codepoint, each_line, each_byte

  ```rb
  str = "abcde"
  str.each_byte {|b| puts b } # 97 98 99 100 101
  str.each_char {|c| puts c } # a b c d e

  str = "100\u20ac" # 100?
  str.each_codepoint {|cp| p cp } # 49 48 48 8364

  str = "This string\nhas three\nlines"
  str.each_line {|l| puts "Next line: #{l}" }

  str.bytes # arry of bytes
  str.chars # array of chars
  str.codepoints # array of codepoints
  str.lines # array of lines
  ```

- sort class spaceship operator

  ```rb
  class Painting
    attr_reader :price
    def initialize(price)
      @price = price
    end
    def to_s
      "My price is #{price}."
    end
    def <=>(other_painting) # spaceship comparator
      self.price <=> other_painting.price
    end
  end

  paintings = 5.times.map { Painting.new(rand(100..900)) }
  paintings.sort # it happens immutably
  paintings.sort! # it happens mutably
  ```

- sort block on the fly

  ```rb
  year_sort = paintings.sort do |a,b|
    a.year <=> b.year
  end

  ["2",1,5,"3",4,"6"].sort {|a,b| a.to_i <=> b.to_i }

  # 3. concise sort_by
  ["2",1,5,"3",4,"6"].sort_by {|a| a.to_i }
  ["2",1,5,"3",4,"6"].sort_by(&:to_i)
  ```

- sort class with comparable module

  ```rb
  class Painting
    include Comparable # see *
    attr_reader :price
    def initialize(price)
      @price = price
    end
    def to_s
      "My price is #{price}."
    end
    def <=>(other_painting) # must def cause the include
      self.price <=> other_painting.price
    end
  end

  # the Comparable allows you to use > <
  pa1 > pa2 # false
  pa1 < pa2 # true
  pa3 = Painting.new(300)
  pa2.between?(pa1, pa3) # true

  cheapest, priciest = [pa1, pa2, pa3].minmax
  Painting.new(1000).clamp(cheapest, priciest).object_id == priciest.object_id
  ```

- map.with_index

  map here is a enumerator, any enumerator (select, each_byte...) can followed by with_index

  ```rb
  ('a'..'z').map.with_index {|letter,i| [letter, i] }
  # [["a", 0], ["b", 1], ...]
  ```

- lazy enumerator

  `(1..Float::INFINITY).select {|n| n % 3 == 0 }.first(10)` But this line of code runs forever. The select operation never finishes, so the chained-on first command never gets executed.

  ```rb
  (1..Float::INFINITY).lazy # <Enumerator::Lazy: 1..Infinity>

  (1..Float::INFINITY).lazy.select {|n| n % 3 == 0 }.first(10) # [3, 6, 9, 12, 15, 18, 21, 24, 27, 30]

  ```
