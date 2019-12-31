---
title: Movie Tracking with Shortcuts and Data Jar
layout: post
date: 2019-12-31
---


I like to keep track of movies I'd like to watch and movies I've watched. I know it's not very useful information, but it's pretty easy to track using Shortcuts, and it helps me select movies to watch whenever I feel like it.

In this post, I'll explain my movie tracking setup which is built on Shortcuts, [Toolbox Pro](https://apps.apple.com/us/app/toolbox-pro-for-shortcuts/id1476205977) and Data Jar (which is still in beta), with the help of [Scriptable](https://apps.apple.com/us/app/scriptable/id1405459188).

The setup consists of 3 main Shortcuts:
- [Add movie to my watchlist](https://www.icloud.com/shortcuts/c78ea6f943b6426b90ccc561409fc189)
- [Show movie details](https://www.icloud.com/shortcuts/9c51a06bd4ee4c01b01bbee5be1a5e41)
- [Mark a movie as watched](https://www.icloud.com/shortcuts/b301135e87584e7c826a1261a002c34b)

All the data is stored locally in Data Jar, automatically gathered and displayed using Toolbox Pro's actions in Shortcuts.

Here's how it all works:




### Add Movie to my Watchlist

We only input the movie name, which the Shortcut uses as a query to search using Toolbox Pro's `Find Movies` action, which searches [TMDb](https://www.themoviedb.org/). Usually this results in multiple movies, which are displayed for us to pick from, alongside their poster and release year, all in a nice list built from Toolbox Pro's `Menu Items`. The index of each movie is stored in `Field 1` of the Menu Item, for recall in the next steps.
Once we select a movie, we get the item from the `Movies` list which corresponds the index stored in `Field 1` of the Menu Item.
There's a small hack here: if we attempt the `Get Item from List` right after getting the index, the resulting object looks like a number instead of the `Movie` object returned by Toolbox Pro, which contains the details we're gonna need in the next steps, and there's no way to tell Shortcuts to use this value as a `Movie` object. To fix this, we need to have a list of movies right before getting the item from list.

We build out a dictionary containing all the details we want to save, and save it as the value of `Movie.{ID}`. We're using the movie's ID as the key and the dictionary containing the details we want to save as its value. 
I like storing movies based on their ID so saving multiple movies with the same name doesn't accidentally overwrite anything, and I prefer it over arrays because they are easier to find this way, when we need to update or show their details.

We also use `Get Contents of URL` to download the poster thumbnail and full resolution image and store them with the movie details in Data Jar.

Here is how it looks running it:
![add to watchlist](/assets/img/2019-12-25-movie-tracking/add.jpeg)
### Show Movie Details
This Shortcut relies heavily on this Scriptable script, so I'll explain it first:

```javascript
function isAccepted(movie) {
  type = args.shortcutParameter["type"]
  switch (type) {
    case "All":
      return true
    case "Watchlist":
      return !movie.Watched
    case "Watched":
      return movie.Watched
  }
}

movies = args.shortcutParameter["movies"]

output = Object.keys(movies)
            .map(function(key) { return movies[key] })
            .filter(isAccepted)
            .sort(function(a,b) { return b.Rating - a.Rating })

Script.setShortcutOutput({ "movies": output })
Script.complete()
```
The script accepts a dictionary from Shortcuts containing two pieces of information:
1. The list of movies
2. The type of filter

The list of movies is understood by Scriptable as a dictionary, so we have to use `Object.keys` to get an array of the keys of the dictionary, and map them to their respective values, using `map` with a simple function that returns the value of its input key from the `movies` dictionary.
The result of these two operations is an actual array of movie details, which we filter using the `isAccepted` function, based on the type of filter. I only built three different filters: all, watchlist, and watched. Other filters can be built by adding them as cases in the switch on line 3 and filtering based on whatever criteria you like. We then sort this array. I chose to sort based on movie ratings, which can also be changed based on your preferences, by changing the sort function.
The resulting array is returned to Shortcuts as a part of a dictionary, containing only the array's value. This is to overcome a limitaion in which we can't return an array directly from Scriptable to Shortcuts, instead we have to wrap it up in a dictionary.
Lastly, we call `Scriptable.complete()` on line 21 to end the script. This is not necessary, but it does speed up the proccess.

Now, back to Shortcuts:
The Shortcut starts by asking which filter we'd like to use, then we get the full `Movie` dictionary from Data Jar, which contains all of our movies.
We build a dictionary containing the filter type and add the movies to it, this is passed to the previously explained Scriptable script. The resulting dictionary has a movies value containing a filtered, sorted list of movies.
We iterate over that list, getting the thumbnail of the poster of each movie from Data Jar, which would be stored at `Movie.{ID}.Thumbnail`. Using the thumbnail and movie details, we build out Toolbox Menu Items to pick from. We save the ID of each movie in `Field 1` of the Menu Item just like we did in the previous Shortcut with indices.

Once a movie is picked, we extract the ID from `Field 1` of the Menu Item, and we get the details of the selected movie from Data Jar.

Here, I'm differentiating between watched movies and movies I haven't watched yet. This is to display less information if I hadn't watched the movie yet, to avoid any kind of spoiler, and to add a `Watched` button which calls the next Shortcut with the selected movie's ID. We build a summary of the movie's information, which could also be customized by changing the text.

Finally, we get the movie's poster from Data Jar, build a list containig the poster and the summary we created, and pass it to Toolbox's `Preview` action, which builds a nice looking preview of our movie's details and poster.
![movie details](/assets/img/2019-12-25-movie-tracking/details.jpeg)

### Mark a Movie as Watched
If this Shortcut receives a movie ID, it immediately sets its `Watched` value to true, and set its `Watch time` value to the current date.

Otherwise it works exactly like the previous one: We get the movies list, pass it to Scriptable with "Watchlist" filter type and build out our menu items. Once a movie is picked, we get its ID from the menu item, just like before, and then we set its `Watched` and `Watch time` values.

![movie watched](/assets/img/2019-12-25-movie-tracking/watched.jpeg)
## Some Slight Shortcommings
I'd like to get more details from Toolbox Pro's `Find Movies` action, like movie runtime and genre, which is supposed to be there, but I'm always getting what appears to be an empty string in its place.

I might try to use TMDb's API directly without going through Toolbox Pro for getting the movie details, which might give me some more info, but I haven't looked into that yet.

I'd love it if all these Shortcuts would run faster, specifically, Toolbox Pro's Menu Items take a relatively long time to build.

Other than these small nitpicks, I've been enjoying this setup so much, I might replicate it for other complex stuff I like to track, such as apps and books.