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

### Proc parameters and arguments

> Procs differ from methods, with respect to argument handling, in that they don’t care whether they get the right number of arguments. A one-argument proc like

```rb
>> pr = Proc.new {|x| p x }
=> #<Proc:0x000001029a8960@(irb):1>

# can be called with any number of arguments, including none. If it’s called with no arguments, its single parameter gets set to nil:
>> pr.call
nil

# If it’s called with more than one argument, the single parameter is bound to the first argument, and the remaining arguments are discarded:
>> pr.call(1,2,3)
1
```

## lambda

> the lambda method returns a Proc object

### create lambda

```rb
# lambda keyword
lambda{ |x| p x }.call("hi")

# stabby -> no param
-> { puts "hi" }.call

# stabby ->(param)
mult = ->(x,y) { x * y }
mult.call(3,4)
```

### lambda v.s. Proc

- lambda-flavored procs don’t like being called with the wrong number of arguments. Unlike traditional procs, they’re fussy:

  ```rb
  lam = lambda {|x| p x }
  #<Proc:0x000001029901f8@(irb):3 (lambda)>

  lam.call(1) # OK
  lam.call # ArgumentError (wrong number of arguments (given 0, expected 1))
  lam.call(1,2,3) # ArgumentError (wrong number of arguments (given 3, expected 1))
  ```

- lambdas require explicit creation, no implicate conversion like Proc objects
  ```rb
  def m(&block)  # block is always a proc, never a lambda
  
  def m( lambda_param )   # just like common parateter
  ```
- return
  > Finally, lambdas differ from procs in how they treat the return keyword.
  > return inside a lambda triggers an exit from the body of the lambda.
  > return inside a proc triggers a return from the method in which the proc is being executed. Here’s an illustration of the difference:
  ```rb
  def return_test
    l = lambda { return }
    l.call # return inside lambda
    puts "Still here!"
    p = Proc.new { return }
    p.call # return the method, no run below
    puts "You won't see this message!"
  end
  return_test
  ```

## Concurrency

### Thread.new

```rb
t = Thread.new do
  puts "Starting the thread"
  sleep 1
  puts "At the end of the thread"
end
# note t has already run

puts "Outside the thread"
t.join

=> Starting the thread
=> Outside the thread
=> At the end of the thread
```

## System Command

### system

```rb
>> system("date")
Sun Apr 22 08:49:11 EDT 2018
=> true
>> system("cat")
I'm typing on the screen for the cat command. I'm typing on the screen for the cat command.
=> true
>> system('grep "D"')
one
two
David
David

```

> The $? variable is thread local: if you call a program in one thread, its return value affects only the $? in that thread:

```rb
>> $?
=> #<Process::Status: pid 28046 exit 0>
>> Thread.new { system("datee"); p $? }.join
#<Process::Status: pid 28047 exit 127>
=> #<Thread:0x3af840 dead>
>> $?
=> #<Process::Status: pid 28046 exit 0>
```

### CALLING SYSTEM PROGRAMS WITH BACKTICKS

```rb
>> d = `date`
=> "Sun Apr 22 08:49:11 EDT 2018\n"
>> puts d
Sun Apr 22 08:49:11 EDT 2018
=> nil
>> output = `cat`
I'm typing into cat. Since I'm using backticks,
I won't see each line echoed back as I type it.
Instead, cat's output is going into the
variable output.
=> I'm typing into cat. Since I'm using backticks,\nI won't etc.

>> puts output
I'm typing into cat. Since I'm using backticks,
I won't see each line echoed back as I type it.
Instead, cat's output is going into the
variable output.
```
