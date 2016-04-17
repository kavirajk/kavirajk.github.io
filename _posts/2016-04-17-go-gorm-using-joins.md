---
layout: post
title: "Go-gorm: JOINS make life easier!"
summary: Make advanced queries in gorm using joins
comments: True
twitter: True
tags: [golang, go, sql, ORM]
---

I have been using [go-lang](http://golang.org/) and [gorm](http://jinzhu.me/gorm/) to build web apps for a while. Though *gorm*
provids [very good documentation](http://jinzhu.me/gorm/), I think it lacks some real-time examples. Here is
what I have learned how to use gorm for advanced queries. Most importantly to get data
from multiple tables that are not directly related. 

Let me try explaining the concepts with three use cases.

Consider following models

{% highlight go %}

type Launguage struct {
	ID uint `gorm:"primary_key"`
	Name string
}
	
type Movie struct {
	ID uint `gorm:"primary_key"`
	Title string
	LaunguageID uint
	Launguage Launguage
}

type Artist struct {
	ID uint `gorm:"primary_key"`
	Name string
	Movies []Movie `gorm:"many2many:artist_movies"`
}
{% endhighlight %}

<!--break-->

The models are simple. `Movie` have *foreign key* to `Launguage` and `Artist`
have *many-to-many* relationship with `Movie` with `artist_movies` as
intermediate table. Important thing to note here is `Artist` have no direct
relationship with `Language` model. Lets populate the db with some data(Im
using postgres here, though you can you any database)

After populating above modes in db, the tables look like below

{% highlight sql %}
gorm=# select * from languages;
 id |  name   
----+---------
  2 | tamil
  3 | hindi
  1 | english
(3 rows)

gorm=# select * from movies;
 id |    title    | language_id 
----+-------------+-------------
  1 | Nayagan     |           2
  2 | Anbe sivam  |           2
  3 | 3 idiots    |           3
  4 | Shamithab   |           3
  5 | Dark Knight |           1
  6 | 310 to Yuma |           1
(6 rows)

gorm=# select * from artists;
 id |       name       
----+------------------
  1 | Madhavan
  2 | Kamal Hassan
  3 | Dhanush
  4 | Aamir Khan
  5 | Amitabh Bachchan
  6 | Christian Bale
  7 | Russell Crowe
(7 rows)

gorm=# select * from artist_movies ;
 artist_id | movie_id 
-----------+----------
         1 |        2
         1 |        3
         2 |        1
         2 |        2
         3 |        4
         4 |        3
         5 |        4
         6 |        5
         6 |        6
         7 |        6
(10 rows)

{% endhighlight %}

Now, the time for actual query. How would you make the following queries

1. Get the list of all artist who acted in "english" movies

2. Get the list the artists for movie "Nayagan" 

3. Get the list of artists for movies "3 idiots", "Shamitab" and "310 to Yuma"


### Get list of all artist who acted in "english" movies:
   
{% highlight go %}
var artists []Artist
if err = db.Joins("JOIN artist_movies on artist_movies.artist_id=artists.id").
	Joins("JOIN movies on movies.id=artist_movies.movie_id").
	Joins("JOIN languages on movies.language_id=languages.id").
	Where("languages.name=?", "english").
	Group("artists.id").Preload("Movies").Find(&artists).Error; err != nil {
	log.Fatal(err)
}
	
for _, ar := range artists {
	fmt.Println(ar.Name)
}

/* output
Kamal Hassan
Madhavan
*/
{% endhighlight %}

### Get the list of all artists who acted in movie "Nayagan"

{% highlight go %}
var artists []Artist
if err = db.Joins("JOIN artist_movies on artist_movies.artist_id=artists.id").
	Joins("JOIN movies on artist_movies.movie_id=movies.id").Where("movies.title=?", "Nayagan").
	Group("artists.id").Find(&artists).Error; err != nil {
		log.Fatal(err)
}

for _, ar := range artists {
	fmt.Println(ar.Name)
}

/* output
Kamal Hassan
*/
{% endhighlight %}

### Get the list of artists who acted in any of the movies "3 idiots", "Shamitab" and "310 to Yuma"

{% highlight go%}
var artists []Artist

if err = db.Joins("JOIN artist_movies on artist_movies.artist_id=artists.id").
	Joins("JOIN movies on artist_movies.movie_id=movies.id").
	Where("movies.title in (?)", []string{"3 idiots", "Shamitabh", "310 to Yuma"}).
	Group("artists.id").Find(&artists).Error; err != nil {
		log.Fatal(err)
}

for _, ar := range artists {
	fmt.Println(ar.Name)
}

/* output
Madhavan
Aamir Khan
Christian Bale
Russell Crowe
*/

{% endhighlight %}


Without `Joins`, the above queries using `gorm` would involve lots of `where`
clause and loops. But using `Joins` its just one simple database query.

<a href="https://gist.github.com/kavirajk/63f369727150455c33c79d0d2ec72c0e" target="_blank">Here</a> is
the link to complete program used in this article.

See you with more real-time examples for more advanced queries using `gorm` next time. Happy
hacking.. !!
