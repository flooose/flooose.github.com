---
title: Another reason why being a programmer is awesome
layout: post-03242013
published: true
---

## Another reason why being a programmer is awesome

I'm writing this because I have to share the excitement I once again felt when I
found myself fixing a problem that non-programmers probably wouldn't have been
able to solve. It goes as follows:

Like most people, when I take pictures with my digital camera or phone, I use a
program to import and organize them. In my case the program is called
[Shotwell](http://yorba.org/shotwell/), and a configuration mistake and a missing
feature resulted in shotwell importing pictures into my home folder. While I
wanted a structure similar to `~/Pictures/2010/03/01/*.jpg`,
`~/Pictures/2012/12/25/*.jpg`, etc., what I actually got was
`~/2010/03/01/*.jpg`, `~/2012/12/25/*.jpg`, i.e. without the "Pictures" prefix.
Obviously, as the years go by, I'd end up with a lot of folders directly in my
home directory while I intended them to be in `~/Pictures`.

The missing feature in shotwell is that there is no built in way to migrate
these folders to `~/Pictures/*`.

So what does a programmer do? SHe starts looking for where and how the metadata
of the images is stored. This led me to a hidden folder in my home directory
where I found a `photo.db` file.

Step 1:

    $ less ~/.local/share/shotwell/data/photo.db
    "/home/chris/.local/share/shotwell/data/photo.db" may be a binary file.  See it anyway?

"Hmm, so not a csv file or something simple like that. I can't imagine this program having a dependency on Postgres or MySQL, but sqlite?..."

Step 2:

    $ sqlite3 ~/.local/share/shotwell/data/photo.db
    SQLite version 3.7.15.2 2013-01-09 11:53:05
    Enter ".help" for instructions
    Enter SQL statements terminated with a ";"
    sqlite> .tables
    EventTable                    SavedSearchDBTable_Rating
    PhotoTable                    SavedSearchDBTable_Text
    SavedSearchDBTable            TagTable
    SavedSearchDBTable_Date       TombstoneTable
    SavedSearchDBTable_Flagged    VersionTable
    SavedSearchDBTable_MediaType  VideoTable
    sqlite>

"Score!!!"

At this point it was just a matter of exploring which tables contained what. It
turned out that the appropriately named "PhotoTable" had an appropriately named
"filename" column that had an absolute path to the file's location.

Since it had over 7000 entries, I wrote the following script:

    $ cat photo-migration.rb
            require 'sqlite3'

    connection = SQLite3::Database.new('photo.db')

        results = connection.execute('SELECT id, filename FROM PhotoTable')
        total_files = connection.execute('SELECT count(*) FROM PhotoTable')
        job = 1

        results.each do |result|
    id = result[0]
      filename = result[1]

      # Special case for files in ~/pictures
      if filename.match('/home/chris/pictures(.*)')
        suffix = filename.gsub('/home/chris/pictures', '')
        puts "renaming #{filename} to /home/chris/Pictures#{suffix} # #{id}:#{filename}"
        connection.execute(
          "UPDATE PhotoTable set filename = :filename WHERE id = :id", {'id' => id, 'filename' => "/home/chris/Pictures#{suffix}"}
        )
        puts "finished job: #{job} of: #{total_files}"
      # move files from /home/chris/*/foo/bar/ to /home/chris/Pictures/*/foo/bar
      elsif filename.match('/home/chris/(.*)')
        suffix = filename.gsub('/home/chris', '')
        puts "renaming #{filename} to /home/chris/Pictures#{suffix} # #{id}:#{filename}"
        connection.execute(
          "UPDATE PhotoTable set filename = :filename WHERE id = :id", {'id' => id, 'filename' => "/home/chris/Pictures#{suffix}"}
        )
        puts "done with job: #{job} of: #{total_files}"
      end
      job = job + 1
    end

Without worrying too much about efficiency and such, I wrote and tested this
against copies of the database. It took me about 20 minutes and after finishing
it and running it against the real database, I was able to move all of my photos
into the new "Pictures" folder without shotwell complaining about a thing or
mixing up any of the metadata.

Yay for programming!
