---
layout: post
title:      "NewsHeadlines CLI Gem!"
date:       2017-11-22 00:54:37 +0000
permalink:  newsheadlines_cli_gem
---

After lots of hard work and struggles I've finally completed my first portfolio project since starting Flatiron's Web Developer program. For this project I had to build a CLI that provides a user access to data obtained from an external source.  This data has to be accessible at least one level deep, such as presenting a list of items from which the user selects an item to view additional information for.  I had the option of either scraping a website for this data or accessing data from an API.  Up until now I'd learned about scraping sites with open-uri and nokogiri but knew nothing of APIs, so perhaps this is why I chose to work with an API.  I probably would've finished a lot sooner had I scraped a site, but I've learned quite a bit from my struggles dealing with an API.  I also tried my hand at building an actual Ruby gem for this project, which also cost me more time but also provided for yet another learning opportunity. 
In short, I've built a news headlines CLI ruby gem that presents users with the latest news headlines from 60 major news sources from around the world.  The CLI does this by accessing the NewsAPI (https://newsapi.org/) and building Source objects and Article objects out this data to then present to the user.  

## Domain Model  & Notable Files
![(https://drive.google.com/file/d/1JBizk_hnLncoAUU_q1YWgpQrphFCujKu/view?usp=sharing)]

For this project I ended up with 4 classes: CLI, Source, Article, and API.
The API class is responsible for accessing the NewsAPI data (using RestClient and Json)  and using this data to create new Source objects and Article objects through object collaboration.

The Source class is responsible for creating new instances of the Source class, keeping track of all its objects, and being able to find Source objects by category.  The Article class does the same, however it finds Article objects by @title. Both this classes collaborate through a has many and belongs to relationship (a Source has many Articles and an Article belongs to a source).  

This being a CLI application, most of the work takes place in the CLI class.  This class is responsible for the interaction between the user and the NewsAPI data.  It does this by creating a CLI object that presents the NewsAPI data to the user and captures user input to determine which data to present.  This being an object oriented application, the data presented to the user are Source and Article objects.

### /bin/console
This is a file that can be used during development to experiment with the app.  It's great because it does a couple things.  It requires all the code necessary for the app to run and then opens up an IRB session.  This means that as soon as the IRB session opens Ruby already knows about all my classes so I can start experimenting with my objects and their methods right away to see how everything is working.  This was by far one of the most useful things I learned to use creating this project.  In the past I found myself copying and pasting increasingly longer lines of code (methods, variables, etc.) into IRB and having a /bin/console file saves so much time.

### /bin/news_headlines
The file that runs the app. Only line of code is to create a new CLI object with the #call method.

### /lib/news_headlines.rb
This file is important, this sets up the application's environment (i.e. this is where all dependencies live).  Anytime `/bin/console` or `/bin/news_headlines` (or bundler when loading this as a gem) are run, they just require one file instead of requiring all files and gems needed to run the application.  This 'environment' file means that all classes can then collaborate with each other. 

### /spec
This directory includes a couple of spec files I wrote to test my Article and Source class.  These tests are very basic and my first attempt at writing tests. They simply test the correct instantiation of objects.


## The Process/Challenges  
When starting this project the first real challenge was knowing how to start from zero, without a directory or file structure having been set up for me.  Up until now pretty much all the code I'd written was done inside lesson directories that were already set up, with `config/environment` files that required all the files need for the code to run and rspec tests to guide me.  Starting with a blank slate presented two challenges. First I had to come up with a domain model on my own(i.e. which objects I would need to create and how they would collaborate with each other). Secondly, I had to create my project directory and all the files needed for it to run. 

The domain model was somewhat simple and followed a lot of what I'd learned in the course. However deciding where to start was the challenge.  I knew I would have a CLI class to deal with the user interaction and presentation of data, and that I would have an API class dealing with getting the actual data.  I also knew I would try to have a class(-es) representing that data but I needed to play with the data first to see what this might look like.  It was at this point that I struggled deciding where to begin.  I figured I could either start building this from the bottom up (start with classes/objects first and finish with my CLI) or from top to bottom (stub out my CLI with fake data and just get it working first, and moving on to building my classes to replace the fake data).  With this question in my mind I decided to play around with my NewsAPI data first. Before I knew it I'd already started building my API class, which led to building my other classes.  I inadvertently went about building this from the bottom up, but looking back I wonder if I would've had an easier time building this by stubbing out my CLI first.  I say this because as I built my CLI I ended up going back and making many changes to my classes to deal with issues that came about from building the CLI.  


Dealing with the directory and file structure was challenging in a different way, mostly because I didn't quite understand the purpose of all the files (aside from those I created for my classes) in a file structure such as this.  Bundler made things easier to start my project because the `bundle gem <gem_name>` command does quite a lot of neat stuff for you.  It creates a directory for the project with all of the basic structure needed to start out, including starting a git repository in this directory.  In addition it generates all the files needed for a gem (.gitignore, gemfile, rakefile, code of conduct, license, gemspec, `bin/console`, `lib/<gem_file>.rb`, `lib/<gem_file>/version.rb`).  While this did help setting up the directory and file structure I did have to spend quite a bit of time understanding exactly what a Gem is and how the gemspec works.  

## Areas for improvement  
As I worked on this project I realized there are a few things I could do to improve or add functionality. The first would be an added feature at the detail level of the news headlines.  I would like to add an option for the user to view the full article on their default browser.  I've seen there is `system` command of some sort to open an url from the terminal in the default browser but I wasn't able to get it to work.  

Another issue my CLI has that could be improved upon is related to the NewsAPI data itself.  I noticed that article hashes contain different values representing `nil` (e.g. `nul`, and `""`) depending on the news source.  Currently my Article #initialize method gets called passing the article hash keys => values as arguments with a default value of `nil` to deal with missing arguments. As such, currently when my API class gets this article data from NewsAPI and uses it to create Article objects, some objects get instantiated with variables set as `nul` or `""` because technically these aren't `nil` in Ruby. 

Finally, my CLI requires an API key from NewsAPI for my API class to access the NewsAPI data.  This means that my user first has to get their own free API key from NewsAPI before they're able to run the CLI.  When building this I had to find a way for the user to be able to 'save' their API key somewhere in my file structure so that it could  be accessed by my API class when getting data from NewsAPI.  My solution for this whole API key issue was to create an API_KEY constant at the highest level of my application, in the NewsHeadlines module where all my classes exist.  When a user first wants to use my CLI they must go and set this constant to a string containing their API key.  I know there are ways for applications to deal with this better but that is beyond the scope of my knowledge at this moment and improving the way my CLI handles API keys is something I intend to revisit once I expand my knowledge on the subject.









