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
