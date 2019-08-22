# Code-Along: Intro to Working with APIs

## Objectives

1. Understand why programmers need to work with APIs
2. Learn some of the basic ways in which programmers work with APIs

## Define API

An API, or application programming interface, is a manner in which companies and
organizations, like Twitter or the New York City government, expose their data
and/or functionality to the public (i.e. talented programmers like yourself) for
use. APIs allow us to add important data and functionality to the applications
we build. Here's just a few examples of some of the cool things you can do by
using APIs:

* Create an app that allows users to sign up/sign in via
  Facebook/Google/Twitter/Github/etc.
* Use the NYC Open Data API to get and map data––everything from Health
  Department restaurant ratings to public park locations and hours to New York
  City Public Housing repair issues to noise complaints to public school
  construction, you name it!
* Use the Yelp API to find and deliver popular local spots to your users.
* Use the Weather Underground API to give your users up-to-date weather alerts.
* Use the Ticket Master API to inform your users if their favorite musician has
  an upcoming show.
* So much more!

This is just a small sample of what working with APIs allows us to do as
developers. Throughout the course of your programming life, you'll likely be
exposed to working with many different APIs. You'll even learn how to build your
own API later on in this course. This reading seeks to introduce the topic,
emphasize some of the benefits of getting comfortable working with APIs and
offer a brief intro into some of the common methods of working with APIs.

## How to Work with APIs

Different APIs expose their data and functionalities in different ways. However,
there are commonalities among them and there are common approaches that we'll
discuss here. Generally speaking, there are two main uses for APIs––getting data
and adding functionality (i.e. signing in with Facebook or posting to
Instagram). We'll be discussing the "getting data" part of working with APIs
here.

Many APIs are built on what is referred to as a REST-ful framework. That means
that the "endpoints", or URLs to which we can send a request for data, follow
certain conventions. These URLs should allow you to request information, send
information, update information and delete information. Let's focus on the
"getting information" request.

### Retrieving Data from an API

For this walk-through, we'll be working with the NYC Open Data API. Let's say
we've been hired by the city to create an app that will help parents sign their
children up for after-school activities. To connect parents to after-school
clubs around the city, we need a data set of such clubs. Luckily for us, the
city has collected that information and allows the public to access it via their
Open Data API. Let's check out the documentation for their
[after-school club data set][].

[after-school club data set]: https://dev.socrata.com/foundry/data.cityofnewyork.us/szgz-awuh

#### Finding the API Endpoint

Take a few minutes and familiarize yourself with resource linked to above.
Learning how to read API documentation is an important skill. It's okay if you
don't understand everything in the above document. We'll learn much more about
the web later on. For now, just look it over briefly.

The documentation gives us the API endpoint that we are interested in.
**Endpoint** refers to the URL that we are requesting that will return to us the
desired data.

Open up a new tab in your browser and paste in the following URL:
`http://data.cityofnewyork.us/resource/uvks-tn5n.json`

The page brings you to is the desired set of data! Notice that the data is laid
out in what looks like a big array of nested hashes. This is actually a
[JSON](http://json.org/) object, which behaves just like a Ruby hash. Working
with the JSON data returned to you by requests to an API is one of the reasons
why we spent so much time in previous lessons learning how to manipulate and
operate on nested hashes.

**Top Tip:** Once you find the right URL for retrieving your data, test it out
directly in your browser *before* you try to request the data from inside your
program. If pasting the URL into your browser brings you to the right data, you
can move on. This cuts down on debugging time. This way, once you try to request
the data from within your program, if it doesn't work, at least you'll know it's
something wrong with your code, as opposed to something wrong with the API.

#### Sending a Request to an API from a Program or Application

Now that we understand what an API is and have even dealt with a URL that takes
us to a real API endpoint, let's use that same URL to send a request for data
from a Ruby program. Fork and clone this repo locally.

Open up `nyc_api.rb`. Let's take a look at the code here:

```ruby
require 'net/http'
require 'open-uri'
require 'json'

class GetPrograms

  URL = "http://data.cityofnewyork.us/resource/uvks-tn5n.json"

  def get_programs
    uri = URI.parse(URL)
    response = Net::HTTP.get_response(uri)
    response.body
  end

end

programs = GetPrograms.new.get_programs
puts programs

```

We stored our API endpoint URL in a constant at the top of our class. Then, we
have a `get_programs` method that uses the `NET::HTTP` library to send an HTTP
request from our program. `NET::HTTP` is a Ruby library that allows your program
or application to send HTTP requests. We require it at the top of our file with
the `require` statement.  We also require the URI library which helps Ruby to
handle URIs.

Both [NET::HTTP][`net/http`] and [URI][`open-uri`] are classes built into Ruby.
Check out the links at the bottom of this lesson to read more about their usage.
Don't worry too much about NET::HTTP and URI right now, though. Just focus on
getting a basic understanding of and exposure to the tools we can use to request
data from an API from an application.

**A Note on Requirements:** We can use `require` statements to include libraries
of code in our own programs. We can require libraries like `NET::HTTP`, or we
can require gems, like Pry.

Now, in your terminal in the directory of this lab, run `ruby nyc_api.rb`. It
should output the JSON response from the NYC Open Data API!

### Working with API Data

Now that we have all of our data back from the API, we need to be able to
collect and present it within the context of our app. Since we are basically
pros at manipulating nested hashes, we shouldn't have too much trouble. Let's
write a method, `program_school`, that just returns a list of the schools or
organizations that are running our after school programs.

Copy and paste the following code into our GetPrograms class:

```ruby
def program_school
# we use the JSON library to parse the API response into nicely formatted JSON
  programs = JSON.parse(self.get_programs)
  programs.collect do |program|
    program["agency"]  
  end
end
```

At the bottom of the file, comment out:

```ruby
programs = GetPrograms.new.get_programs
puts programs
```

Let's `puts` out a unique list of the schools. Paste in the following two lines
right underneath where you commented out the above two lines:

```ruby
programs = GetPrograms.new
puts programs.program_school.uniq
```

Now, run the program with `ruby nyc_api.rb` in your terminal. You should
something like this:

```bash
Baychester Youth Council
Bronx Arts Ensemble
Bronx River Art Center, Inc.
Casita Maria, Inc.
Childrens Arts & Science Workshops, Inc.
Citizens Advice Bureau, Inc.
Claremont Neighborhood Centers, Inc.
Community Association of Progressive Dominicans
Directions For Our Youth, Inc.
East Side House, Inc.
Episcopal Social Services
Federation Employment & Guidance Services (FEGS)
Good Shepherd Services
Inwood House
Just Us, Inc.
Kips Bay Boys & Girls Club
Latino Pastoral Action Center, Inc.
Learning Thru An Expanded Arts Program, Inc
...
```

So far we've used the NET::HTTP and URI to send a request for data to an API
endpoint, a URL. We've operated on the data that was returned to us, making sure
it was formatted properly as JSON and iterating over that JSON to retrieve the
name of the school hosting each after-school program. That was a lot of work! I
wonder if there is an easier way to work with popular APIs...

### Using Gems to Work with APIs

Many popular APIs have wrappers, i.e. gems that are code libraries that do a lot
of the heavy lifting for you. In fact, we'll use a gem to interact with the
Twitter API in the next lab.

## Conclusion

To recap: APIs generally either provide a user with data or added functionality.
We can use APIs that serve data to get information for our own applications and
projects. To get this data, we need to send a request to the URL of the API and
know how to work with the response we receive. Many APIs serve data in JSON
format, which needs to be parsed before we can use it. Once parsed, it becomes
a hash we can work with and extract data from.

In our example, we were able to retrieve remote information from an API using
the built-in Ruby classes `NET::HTTP` and `URI`. By putting this implementation
inside a class, we can develop highly reuseable code that let's us access all
sorts of information remotely.

The `GetPrograms` class used a hard-coded the URL, stored as a class constant,
and included an instance method called `get_programs`:

```ruby
URL = "http://data.cityofnewyork.us/resource/uvks-tn5n.json"

def get_programs
  uri = URI.parse(URL)
  response = Net::HTTP.get_response(uri)
  response.body
end
```

But we could easily adapt this code to be flexible and accept _any_ URL we pass
in using an `initialize` method and an instance variable. The `get_programs`
method is really just getting the response body of from the requested URL, so we
could name this `get_response_body` to be more accurate. We could replace the
custom `program_school` method with a general `parse_json` method, as well.
Instead of a specific class, we would instead have a class that retrieves
JSON from any provided URL!

## Resources

* [NET::HTTP][`net/http`]
* [Open URI][`open-uri`]

[`net/http`]: https://ruby-doc.org/stdlib-2.6.3/libdoc/net/http/rdoc/Net/HTTP.html
[`open-uri`]: https://ruby-doc.org/stdlib-2.6.3/libdoc/open-uri/rdoc/OpenURI.html

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/code-along-working-with-apis' title='Code Along: Intro to Working with APIs'>Code Along: Intro to Working with APIs</a> on Learn.co and start learning to code for free.</p>
