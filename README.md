# Sinatra Playlister

##Objectives 

* Solidify your ActiveRecord understanding 
* Build out basic views for all your models
* Create forms for editing and creating a new song that returns a well-structured params hash 


### Overview

In the theme of moving from a simple command line application static website to dynamic web app, it's time to move Playlister to the interwebs using Sinatra. In this lab, you'll be:

  1. Giving our "library" of songs a dynamic web interface
  2. Creating a few complex forms that allow you to create and edit Artists, Songs and Genres.

## File Structure

Your application should use the following file structure. Notice how there are separate controllers for songs, artists, and genres. Separately, we have sub-directories for  views for songs, artists, and genres.

├── app
│   ├── controllers
│   │   ├── application_controller.rb
│   │   ├── artists_controller.rb
│   │   ├── genres_controller.rb
│   │   └── songs_controller.rb
│   ├── models
│   │   ├── artist.rb
│   │   ├── concerns
│   │   │   └── slugifiable.rb
│   │   ├── genre.rb
│   │   ├── song.rb
│   │   └── song_genre.rb
│   └── views
│       ├── artists
│       │   ├── index.erb
│       │   └── show.erb
│       ├── genres
│       │   ├── index.erb
│       │   └── show.erb
│       ├── index.erb
│       ├── layout.erb
│       └── songs
│           ├── edit.erb
│           ├── index.erb
│           ├── new.erb
│           └── show.erb

### Instructions

The first thing you should aim to do is create a Sinatra interface for the data in `db/data`. There is a `LibraryParser` class included in the `lib` folder that you may use, though it may need some tweaking to work with your specific application. Your associations should follow this pattern:

1. An Artist can have multiple songs and multiple genres
2. A Genre can have multiple artists and multiple songs
3. A Song can belong to ONE Artist and multiple genres
4. How would we implement the relationship of a song having many genres and genre having many songs? In order to establish a "many-to-many" relationship, we'll need a join table. You will need a `SongsGenre` class to go along with this table in the database

You should build the following routes:

1. `/songs`
  * This should present the user with a list of all songs in the libaray.
  * Each song should be a clickable link to that particular song's show page.
2. `/genres`
  * This should present the user with a list of all genres in the library.
  * Each genre should be a clickable link to that particular genre's show page.
3. `/artists`
  * This should present the user with a list of all artists in the library.
  * Each artist should be a clickable link to that particular artist's show page.
4. `/songs/:slug`
  * Any given song's show page should have links to that song's artist and the each genre associated with the song.
  * Pay attention to the order of `/songs/new` and `/songs/:slug`. The route `/songs/new` could interpret `new` as a slug if that controller action isn't defined first.
5. `/artists/:slug`
  * Any given artist's show page should have links to each of his or her songs and genres.
6. `/genres/:slug`
  * Any given genre's show page should have links to each of its artists and songs.

To get the data into your database, you will want to figure out how to use your `LibraryParser` class in the `db/seeds.rb` file.


### How to approach this lab

Get the basics of the app working first, which means we have five specs in total and you should first pass all three model specs. 

By typing 

```bash
rspec spec/models/01_artist_spec.rb 
```

in your bash/ command line you will only run that spec. It is important to run the specs in there numeric order. You will notice even after adding a table, model, and controller your specs are still not passing, but the error messages are changing. You have to read every errror message carefully to understand what to do next.

For the last spec `05_song_form_spec.rb` you need to implement the following features:

1. `/songs/new`
  * Be able to create a new song
  * Genres should be presented as checkboxes
  * Be able to enter the Artist's name in a text field (only one Artist per song.)
2. `/songs/:slug/edit`
  * Be able to change everything about a song, including the genres associated with it and its artist.

Think about the custom writer or writers you may need to write to make these features work. 

### Slugs

Having a URL like `/songs/1` sort of sucks. Imagine trying to email that song to a friend. They would literally have no idea what the song would be until they click the link. You could be sending them literally anything. It would be much better to have a URL like `/songs/hot line bling`.

But again, we run into a problem here. We can't have spaces in a URL. In order to make it a proper URL, we have to convert the spaces to `-` in the song name. This is called a slug.

You are going to need to create some slugs in this lab. A slug is used to create a name that is not acceptable as a URL for various reasons (special characters, spaces, etc). This is great because instead of having a route like `/songs/1`, you can have a route `/songs/hotline-bling` which is a much more descriptive route name.

Each class you build will need to have a method to slugify each object's name. This means you'll need to strip out any special characters, and replace all spaces with `-`. 

You'll need to build a method `slug` which takes a given song/artist/genre name and create the "slugified" version".

The `find_by_slug` method should use the `slug` method to retrieve a song/artist/genre from the database and return that entry.

## Check Boxes

In order to create a check box of all the genres on a new song form, you'll need to iterate over all the Genres in the database. From there, you'll want to set the genre name as the ID of the input tag. 

The value attribute should be set to the genre id.

The name attribute should be set to set to `genres[]` because we're dealing with a collection of attributes. This will make the params hash look like this: 
```ruby
params = {
  genres => [ genre1, genre2, genre2]
}

```HTML
<% Genre.all.each do |genre| %>
  <input id="<%= genre.name %>" type="checkbox" name="genres[]" value="<%= genre.id %>">
<% end %>
``` 

## Flash Message

You can add a flash message for when a new instance is created. Let's take a new song creation. The controller action that handles the POST request will look something like this:

```ruby
post '/songs' do
  #code to create a new song and save to DB
  erb :'songs/show', locals: {message: "Successfully created song."}
end
```

The `locals: {message: "Successfully created song."}` will create the message `"Successfully created song."`. To display that on the view, You will need to include this at the top:

```html
views/songs/new.erb
<% unless locals.empty? %>
  <%= message %>
<% end %>
```

This checks to see if the variable `locals` is empty. If it isn't, then it displays message, which we set in the controller to store `"Successfully created song."`

### Resources
* [Clean ULR - Slugs](http://en.wikipedia.org/wiki/Slug_(web_publishing)#Slug)

<a href='https://learn.co/lessons/playlister-sinatra' data-visibility='hidden'>View this lesson on Learn.co</a>