# Object individuation

- extend <<
  ![image](https://user-images.githubusercontent.com/24782000/147417191-03c91c29-8706-4c01-b8ac-bfaa28966c10.png)

- mixin a module in a singleton class

```rb
class C
  def talk
    puts "Hi from original class!"
  end
end

module M
  def talk
    puts "Hello from module!"
  end
end

c = C.new
c.talk #  Hi from original class!

class << c
  include M
end
c.talk # Hello from module!
```

# Callable and Runnable

- Proc objects
  > self-contained that you can create, store, pass around as method arguments, and, when you wish, execute with the call method.
- lambdas
  > Lambdas are similar to Proc objects. Truth be told, a lambda is a Proc object, but one with slightly special internal engineering.
- method objects.
  > methods extracted into objects that you can, similarly, store, pass around, and execute.

## Proc

### create a proc

```rb
pr = Proc.new { puts "Inside a Proc's block" }
pr.call #  => Inside a Proc's block

# The Kernel#proc method is an alias for Proc.new. We can achieve the same result as above by writing the following:
pr = proc { puts "Inside a Proc's block" }
```

### block to proc

- block-to-proc conversion

  ```rb
  # block parameter
  def call_a_proc(&block) # conversion: Proc.new { puts "Inside the block" }
    block.call # now block is actually a proc
  end

  # block paramter
  call_a_proc { puts "I'm the block...or Proc...or something." }

  # proc parameter works as well
  p = Proc.new { puts "I'm the block...or Proc...or something." }
  call_a_proc(&p)

  ## == another example ==
  p = Proc.new {|x| puts x.upcase }
  %w{ David Black }.each(&p)
  %w{ David Black }.each{|x| puts x.upcase }
  ```

- you still need the **&**
  ```rb
  call_a_proc(p) # ArgumentError
  call_a_proc(p.to_proc) # ArgumentError
  ```
  > Thus the & in capture_block(&p) does two things: it triggers a call to p’s to_proc method, and it tells Ruby that the resulting Proc object is serving as a code block stand-in. And because to_proc is a method, it’s possible to use it in a more general way.

### Hash#to_proc

```rb
albums = { 1988 => "Straight Outta Compton", 1993 => "Midnight Marauders",
           1996 => "The Score", 2004 => "Madvillainy",
           2015 => "To Pimp a Butterfly" }

# implicit call to Hash#to_proc, inside which does: albums[year]
[1988,1996].map(&albums)
# => ["Straight Outta Compton", "The Score"]

# range
(1990..1999).map(&albums).compact
# => ["Midnight Marauders", "The Score"]
```

### Class to_proc

In theory, you can define to_proc in any class or for any object, and the & technique will then work for the affected objects.

the three classes where to_proc is most useful are Proc and Hash (discussed earlier) and Symbol (discussed in the next section), and to_proc behavior is already built into those classes.

```rb
class Person
  attr_accessor :name
  def self.to_proc
     Proc.new {|person| person.name }
  end
end
d = Person.new
d.name = "David"
j = Person.new
j.name = "Joe"
puts [d,j].map(&Person) # call the Person.to_proc
```

### Symbol#to_proc

```rb
%w{ david black }.map(&:capitalize)

# override Symbol's to_proc
class Symbol
  def to_proc
    Proc.new {|obj| obj.public_send(self) }
  end
end
```
