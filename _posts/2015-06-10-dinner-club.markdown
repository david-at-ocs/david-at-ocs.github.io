---
layout: post
title:  "Dinner Club"
date:   2015-06-10 09:10:57
categories: code 
---
#Dinner Club

Dinner Club is a program that records each time members of DinnerClub go out and which restaurant the went to.  It also records how much each person in the group has paid over the life of the club.

It uses an array to save each event (or each time they go to a restaurant) and a hash to keep a list of members and the total amount they've spent in the club. 

##DinnerClub Class

DinnerClub has a couple of attributes: @dates[] and @dinner_club{}.
The initialize method gets the argument club_members, an array,  which is a list of Dinner Club member names.  It iterates over the array to @dinner_club{} hash using the member's names for the *keys* and sets each of the starting *values* to 0.

```ruby
  def initialize(club_members)
    @dates = []    
    @dinner_club = {}
    
    club_members.each do |a|
      @dinner_club[a] = 0
    end
      
  end
```

@dates is an array I use to hold each event, which includes the restaurant name and the attendees for that particular date (not every member will go out to eat every time).

###Method add_to_member_tab(amount, tip, attendees, payer)

This method's arguments are:

- The total amount of the bill (a float)
- The amount of the tip (an integer)
- An array of who attended this outing
- And a string holding the name of the peron who paid, if there was one attendee picking up the bill.

It creates a new object of CheckSplitter class (more on CheckSplitter later) to figure out the tip, the total amount of the bill with tip added, and what each person owes.

If all attendees were paying, I iterate over the @dinner_club array and update each attendees running total with the total amount per person.

If only one person was paying, I add the total cost of the event to that person's running total.

```ruby
  def add_to_member_tab(amount, tip, attendees, payer)
    dinner_event = CheckSplitter.new(amount, tip, attendees.length)
    if payer.nil?
      attendees.each do |a|
        @dinner_club[a] = @dinner_club[a] + dinner_event.total_per_person
      end
    else
      @dinner_club[payer] = @dinner_club[payer] + dinner_event.total_cost
    end
    
    @dinner_club
  end
```

###Method add_event(info)

add_event takes an array which holds the restaruant name (always in the first slot) and the attendees in after that.  i.e.: ['Dinkers', 'David', 'Thleen', 'Max', 'Olivia']

It adds the that array of each date or outing or event or whatever to array attribute @dates[]. 

```ruby
  def add_event(info)
    dinner_event = DinnerClubEvent.new(info)
    @dates.push(dinner_event)
  end
```

You may be wondering what that DinnerClubEvent class does.  It does next to nothing.  It just returns what it was sent: the array holding the restaurant and attendees.  Why?  I guess I thought maybe in the future we'd be using this for more stuff.

Onto...

##CheckSplitter Class

CheckSplitter figures out the tip and adds it to the total amount of the bill and divides that total by the number of attendees and returns that figure.

It has three arguments:
- amount is the amount of the bill (a float)
- tip is the tip amount (an int, which is converted to a decimal in this class)
- And number of attendees (an int)

It has three attributes.

```ruby
  def initialize(amount, tip, number_of_attendees)
    @party_size = number_of_attendees
    @bill_amount = amount
    @tip = tip
  end
```

##app.rb

This is where I set the list of members and create a few events.

A new instance of DinnerClub passing a list of members to DinnerClub
```ruby
club = DinnerClub.new(['David', 'Thleen', 'Max', 'Mickey', 'Carly', 'Olivia'])
```

An example of event creation.  The first line calls the add_to_member_tab method of the object club we just created above and passes the total bill amount, an array of attendees and sets the payer to nil (all attendees will divide the bill).
The second line calls the add_event method and sends array containing restaurant name and attendees.
```ruby
club.add_to_member_tab(120.00, 20, ['David', 'Thleen'], payer=nil)
club.add_event(['Avoli', 'David', 'Thleen'])
```

Here's an example when one person is paying for then entire party (notice payer is set to 'Olivia'):
```ruby
club.add_to_member_tab(57.34, 15, ['David', 'Thleen', 'Max', 'Olivia'], payer='Olivia')
club.add_event(['Stellas', 'Thleen', 'Max', 'Carly', 'Olivia'])
```