#Gitscrub
Git Your Game On

##About
Gitscrub is designed to give you a practical way of learning git.  It has a series of levels, each utilizing git commands to ensure a correct answer.

##Installation
To install Gitscrub

    gem install gitscrub

After the gem is installed, you can run `gitscrub` where you will be prompted to create a directory.

##Commands

Gitscrub has 3 commands:
 * play - This is the default command and it will check your solution for the current level.
 * hint - Gives you a hint (if available) for the current level
 * reset - Reset the current level


##Contributing

 Get yourself on the [contributors list](https://github.com/Gazler/gitscrub/contributors) by doing the following:

  * Fork the repository
  * Make a level using the DSL (covered below)
  * Add your level to the LEVELS array inside `lib/gitscrub/level.rb` in a position that makes sense (the "commit" level after the "add" and "init" levels for example)
  * Make sure your level works (covered below)
  * Submit a pull request

##DSL

Gitscrub has a DSL for writing levels

An example level:

    difficulty 1
    description "There is a file in your folder called README, you should add it to your staging area"

    setup do
      repo.init
      FileUtils.touch("README")
    end

    solution do
      return false unless repo.status.files.keys.include?("README")
      return false if repo.status.files["README"].untracked
      true
    end

    hint do
      puts "You can type `git` in your shell to get a list of available git commands"
    end

 `difficulty`, `description` and `solution` are required.

 **note** Because `solution` is a Proc, you cannot prematurely return out of it and as a result, must put an implicit return on the last line of the solution block.


    solution do
      solved = false
      solved = true if repo.valid?
      solved
    end

 By default, `setup` will remove all files from the game folder.  You do not need to include a setup method if you don't want an initial git repository (if you are testing `git init` or only checking an answer.)
 
 You can call `repo.init` to initialize an empty repository with a .gitignore file. It takes a single parameter of false if you want to skip the initial commit of the .gitignore file.

 All methods called on `repo` are sent to the grit gem if the method does not existyou want to skip the initial commit of the .gitignore file.

 All methods called on `repo` are sent to the [grit gem](https://github.com/mojombo/grit) if the method does not exist, and you can use that for most git related commands (`repo.add`, `repo.commit`, etc.)


Another method exists called `init_from_level` and it is used like so:

    setup do
      init_from_level
    end

This will copy the contents of a repository specified in the levels folder for your level.  For example, if your level is called "merge" then it will copy the contents of the "merge" folder.  it is recommended that you do the following steps:

 * mkdir "yourlevel"
 * cd "yourlevel"
 * git init
 * some git stuff
 * **important** rename ".git" to ".gitscrub" so it does not get treated as a submodule
 * cd "../"
 * git add "yourlevel"

After doing this, your level should be able to copy the contents from that git repository and use those for your level.  You can see the "blame" level for an example of this.