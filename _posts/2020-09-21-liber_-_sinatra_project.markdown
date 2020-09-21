---
layout: post
title:      "Liber - Sinatra Project"
date:       2020-09-21 22:07:25 +0000
permalink:  liber_-_sinatra_project
---


Finishing up the eighth week of coursework at the Flatiron School proved a daunting but supremely rewarding task. I found myself neck-deep in what seemed to be a conundrum of HTTP requests, CRUD, and routes. After conquering the week, relatively unscathed, I had a lingering idea for what this project should accomplish. As a sound engineering student, I frequently envisioned an open music exchange, where one could trade physical music (vinyl, cassettes, CDs) at no cost except shipping. Where users were free to exchange recordings, however, this already exists, to a certain extent. Discogs is a platform that allows such transactions, albeit from a more eBay-like model. Casting that idea aside, I took on my next interest: literature. An exchange where users who had books they've never read sitting on their shelves, or who had titles read far too many times, could exchange books with users interested in those titles for books they find interesting. I hunkered down and got to work.

I set up an initial file structure using the Corneal gem and wrote migrations to create a book and owner's table. Next was seeding some dummy data and laying the groundwork for the fundamental CRUD routes, establishing their corresponding view .erb files and routes in their respective controllers, remembering separation of concerns. I checked on the models to make sure their associations were functional, by using the has_many and belongs_to macros in ActiveRecord; a book belongs to an owner, an owner has many books. 

```
class Book < ActiveRecord::Base
    belongs_to :owner
end
```

Corneal, along with Sinatra itself, are potent tools. With the use of Corneal's file structure generation and Sinatra's library in play, the project was already about halfway done. After making sure the routes were functional, responding to HTTP requests appropriately, and using the has_secure_password macro for hashing and salting passwords, all that was left was writing the views and using complex form associations. Upon finishing each .erb file for its relevant class; new.erb, show.erb, edit.erb, and index.erb, validations, and conditional form display proved to be very interesting. For example, permitting users to create and edit books in their library, view other's libraries, and conditionally display form elements if signed in, and if the books and show.erb view displayed belonged to them.

```
<% if logged_in? && current_user.id == @owner.id %>
    <form action="/books/new" method="GET">
        <input type="submit" value="New Book" name="new_book">
    </form>
    <br>
    <form action="/owners/<%= current_user.id %>/edit" method="GET">
        <input type="submit" value="Edit Your Account" name="account_edit">
    </form>
    <br>  
<% end %>
```

After reaching a minimum viable product, it was time to refactor, squash some bugs, and perhaps fit in a stretch goal or two. Interested in the users having a more detailed overview of the books they wanted to explore, I wrote a migration to add a synopsis column to the books table and then used Wikipedia to seed some synopsis data for each book in the database. I appended the synopsis to the books show.erb view, and thus it was born. My preferred challenge was adding the user's ability to modify the owner of books in their library. Adding this feature made this assignment feel less like a project and more a viable application for exchange.

```
<label for="change_owner">Change Owner: </label>
    <br>
    <br>
    <% Owner.all.each do |owner| %>
      <input type="radio" name="book[owner_id]" id="<%= owner.id %>" value="<%= owner.id %>" <%='checked' if @book.owner == owner %>><%= owner.name %><br>
    <% end %>
```

There's still plenty of functionality I would add in the future; the ability to message users directly, initiate trade requests, and search/sort by particular attributes. Ultimately this project proved challenging but extremely rewarding. As the complexity of this curriculum grows, so does my ability to effectively assess and respond to said challenges. On to Ruby on Rails. 
