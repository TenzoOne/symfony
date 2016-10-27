# Join Entity Refactoring

In some ways, not much just changed. Before, we had a genus_scientist table with a genus ID, and a user ID. We still have that. Really, the only thing that's changed is that table has a new ID column and a new year study column. In our application, a ton just changed. That means we've actually broken a ton of stuff. Specifically, before, the genus scientist was a collection of user objects, but now it's a collection of genus scientist objects. That means anywhere that we call the get genus scientists, we were expecting a user object, now we're getting back a genus scientist object. The same thing is true on user, if anybody was calling this study genuses to get it or set it, well, they're not doing it correctly anymore. We've got some things in our code that we need to clean up, and we're going to learn some really interesting things along the way.

First, because we just cleared out our database, we have nothing in our system. Open up the fixtures file, and temporarily comment out the genus_scientist property. Because, of course, we can't set user objects on genus_scientist anymore, this now needs genus_scientist objects. We'll fix that in a second. Then go over and run bin console doctrine fixtures load. When that's working, let's at least get one genus in our system in the database that has the new setup. Open up genus controller, and we're once again going to use our fake new action to actually set some data manually in our database.

First, remove these two add genus_scientist lines, because those don't make any sense anymore. You do not pass a user object to add_genus scientist. In fact, the way that you create this, an entry in the table, joint table, is very simple. Genus_scientist equals new genus_scientist. Then we say genus scientist arrow set genus, genus, genus_scientist arrow set user, user, and genus_scientist set years studied 10. Then call E.M. persist on genus scientist.

There's no longer anything fancy going on, it's just its own entity. Now if we go to slash genus slash new, that creates a new genus in the system with a new joint thing, and we can view it. Not surprisingly, that page blows up, because our tweet template code is expecting this genus scientist array to be giving us an array of user objects. Open up the template that's in genus slash show dot html twig, and let's start fixing a few things. Actually, let's start in the genus class itself. If you look for get genus scientist, the is no longer correct. This no longer returns an array of user objects, this returns an array of genus scientist objects.

On that note in our controller, when we loop over genus dot genus scientist, that's not a user anymore, that's a genus scientist. So we need to uplight a couple lines. For one, we need to say genus_scientist.user.fullname, and above on the user show, we don't want that to be the genus scientist id., we want that to be the user's id. That should also me genus_scientist.user.id. Then in the anchor, we'll show off our new year study field by saying, curly curly, genus scientist.yearstudied, years. So we can show that off. We still need to fix the remove link but we'll do that in a second.

Let's go back and refresh, now the page is at least working can we can see our scientist here. Now if we click that to go to the user templates, we have the exact same problem. First, go into the user class, go down to get study genuses, and change that to [inaudible 00:05:03] to say this returns an array of genus_scientist objects. Next, find the show template in user directory, and in the for loop here I'm actually going to rename this variable to be a little bit more clear. I'm gonna say for genus_scientist, because that's what the object is, in user dot study genuses. Then on here for slug, it's going to be genus_scientist.genus.slug. And genus_scientist.genus.name. That's enough to bring this page back to life.

So if we go back to the genus, the other thing I want to fix is this remove link. In the show template for genus, the URL for that is generated down here on this attribute and it passes the genus id and the user id. We know that this is now wrong, it should be genus_scientist.user.id.

Then our controller code actually gets a lot simpler. We find genus controller, scroll all the way down to the bottom, you'll find our remove genus_scientist action endpoint that that's being sent up to. Check this out, we can remove the querys for the genus and the user and just replace them with genus_scientist equals e-m, get repository, app bundle genus_scientist, find one by, pass that user set to user id, and genus set to genus id. So we just go directly to that joint end [inaudible 00:07:10]. Then we're not removing it from a genus or anything like that, we're just deleting that from the database with e-m error remove, genus_scientist, and that is it.

So try that out, go back, refresh. Quick delete that, and it goes away. And if we refresh, it's still gone. So as you can see when you make this adjustment, there are things that need to change. So if you know that you will need extra fields on your joint table from the very beginning, don't use a many many, just go straight to joint ends [inaudible 00:07:51].

Now last we want to do is actually fix our fixtures themselves, because I do want to have that joined still there. We're going to build it up here anymore, we're going to do it just like we did in the PHP code, which means we're just going to set the entity directly down here. So I'll add a new entry down here called app bundle slash entry slash genus scientist, with keys called genus.scientist_1..50 to create 50 records. Then we'll just assign a random user with at user dot aquanauts underscore star. And genus at genus underscore star. Then we'll set a random year study, with numbered between, how about 1 and 30. Perfect.

Head to your terminal, reload the fixtures one last time, then go back and refresh slash genus. Perfect we have some genuses, we click in and we can see the scientist. So our application's fixed right? Well only sort of. If you go to slash admin slash genus, you're gonna probably need to log back in with I like turtles. Our form s still totally broken. It doesn't give us an error but we can no longer control this form with simple check boxes. Our relationship is more complex than that so we need to start talking about form collections.