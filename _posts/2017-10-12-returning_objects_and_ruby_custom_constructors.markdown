---
layout: post
title:      "Returning Objects & (Ruby) Custom Constructors "
date:       2017-10-12 23:57:10 +0000
permalink:  returning_objects_and_ruby_custom_constructors
---



So I've been learning Procedural and Object Oriented Ruby for some time now.
I've gone deep into OO learning all about objects, instance and class methods, struggled wrapping my mind around object collaboration, and beyond.  Yet I've somehow managed to miss truly understanding what class constructors are.  I've had to use them several times by now (albeit, sparingly) and yet I just found myself stuck dealing with a test failure related to a class constructor while building the Music CLI.   After 20 minutes of going over and over my method to find errors and staring at the same test errors, I thought of just Googling Ruby Constructor Methods. Oh I forgot to mention that during those 20 minutes I'd also pry'ed the heck out of that method and was 100% sure my method was performing the task it was required to do (well, 99.99% sure).  Before I get to what my Googling efforts yielded (clarity and understanding) let me briefly describe what my method was required to do and what I had so far…

The test itself was simple,  a class method **.create** should take a *song_name* as an argument and do two things: 
	1)Instantiate a new Song object while setting the *song_name* as an instance variable **@name** 
	2) call a **#save** instance method that will then take that new Song object and push it into the class variable **@@all** (an array containing instances of Song)

My method so far :
```
def self.create(song_name)
  song = self.new(song_name)
  song.save
end
```

This method performed both of the tasks require by my rspec test, yet kept returning the same error below, that apparently the *@@all* array didn't contain the new Song object instantiated.
```
Song .create initializes and saves the song
     Failure/Error: expect(Song.all).to include(created_song)
       expected [#<Song:0x00000002826cf0 @name="Kaohsiung Christmas">] to incl
ude [#<Song:0x00000002826cf0 @name="Kaohsiung Christmas">]
       Diff:
       @@ -1,2 +1,2 @@
       -[[#<Song:0x00000002826cf0 @name="Kaohsiung Christmas">]]
       +[#<Song:0x00000002826cf0 @name="Kaohsiung Christmas">]
```
However, prying over and over showed me that right before my method ended/exited my *@@all* array did in fact contain the object.  Even the error above is confusing as far as I can understand it, because it seems to show that the object is in fact there, (maybe as a nested array? but it still seems to be there).

At this point I decided to see if I just was missing something in general about custom constructors, which is why I decided to Google just that.   After some short reading I discovered what I somehow missed so long ago. It seems that a Ruby constructor must always return the new object/instance that was created.  Return, the new object.  **Facepalm** The fix then was to simply add the following as the last line of code in my method: 

```  
def self.create(song_name)
	song = self.new(song_name)
	song.save
	song
end 
```
In retrospect, I now remember that I had this exact same issue the few times I had to use custom constructors before and had then somehow just figured it out by accident.  Yet, I never really understood what the constructor method was doing.  In short, a custom constructor should always return the instantiated object it just constructed.  Technically, I've learned, a constructor method has 3 jobs, the last of which is: returning the new object.  Part of my ignorance was not realizing that I have been using a class constructor all along: the .new method (as in Object.new).   I never realized the calling that method on class was performing all 3 tasks all along, thus when it came time for me to write my own custom constructors I was unaware that I had to ensure those 3 tasks got completed.  I will try to expand at a later time what I've learned about constructor methods as a whole, including the first of those 2 jobs.  

