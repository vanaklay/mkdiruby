# How to automate Ruby's project creation?

Every time you want to write a program using Ruby, you have plenty of things to do : 
- Create a lib folder.
- Create a Gemfile and place inside of it all the gems that you need to run your program
- Run rspec --init on your CLI to initialize a TDD
- And many more...

Wouldn't it be cool to have a command like `mkdiruby` that takes care of all the setup?

It would work just like a `mkdir` command, except that this time the newly created folder would be filled with all the necessary stuff to begin with.

Alright, let's do it!

## Long time ago... The Westerns Ages!

We needed to go step by step to create a folder and fill it up with a bunch of files:

### 1. Create a folder and place yourself inside of it
```
  $ mkdir mkdiruby

  $ cd mkdiruby
  -> mkdiruby
```
### 2. Create a Gemfile and put all the gems inside it
```
  -> mkdiruby/ $ touch Gemfile
```
```ruby
# ./Gemfile
  source "https://rubygems.org"
  ruby '2.7.1'
  gem 'rubocop', '~> 0.57.2'
  gem 'rspec'
  gem 'gem_needed'
```
### 3. Run Bundle install to install dependencies
```
  -> mkdiruby/ $ bundle install

  -> mkdiruby/ $ ls
  Gemfile Gemfile.lock
```
### 4. Don't forget the README.md !
```
  -> mkdiruby/ $ echo "# New app in Ruby" >> README.md

  -> mkdiruby/ $ ls
  Gemfile Gemfile.lock README.md
```
With `echo` it writes what is in the double quote in the README.md file. If README.md doesn't exist, it create it.

### 5. Initialize rspec to execute some Test-Driven Development
```
  -> mkdiruby/ $ rspec --init

  -> mkdiruby/ $ ls -a
  Gemfile Gemfile.lock README.md spec/ .rspec
```
### 6. Create a lib folder
```
  -> mkdiruby/ $ mkdir lib

  -> mkdiruby/ $ ls -a
  Gemfile Gemfile.lock README.md spec/ .rspec lib/
```
### 7. Finally create app.rb and his test app_spec.rb
```
  -> mkdiruby/ $ touch lib/app.rb

  -> mkdiruby/ $ touch spec/app_spec.rb
```

Now we can work on our app ! 

So evertime you create a new app, you need to pass through all this process before starting to code your app...

Let's me say you something my friend, because i ❤️❤️ you ... 
![DRY](https://res.cloudinary.com/teepublic/image/private/s--r5W7iKNZ--/t_Resized%20Artwork/c_fit,g_north_west,h_954,w_954/co_000000,e_outline:48/co_000000,e_outline:inner_fill:48/co_ffffff,e_outline:48/co_ffffff,e_outline:inner_fill:48/co_bbbbbb,e_outline:3:1000/c_mpad,g_center,h_1260,w_1260/b_rgb:eeeeee/c_limit,f_jpg,h_630,q_90,w_630/v1551016863/production/designs/4274629_0.jpg)

## Past 2020 ... The Coding Ages !

### 1. (8) Starting from the end of the western ages, we have an app folder ready to be use
```
  -> mkdiruby/ $ ls -a
  Gemfile Gemfile.lock README.md spec/ .rspec lib/

  -> mkdiruby/ $ ls -a lib
  . .. app.rb

  -> mkdiruby/ $ ls -a spec
  . .. app_spec.rb spec_helper.rb
```
So, let’s use the app.rb program to automate the different steps detailed above!

### 2. Create a folder where we are

The idea is that when you run the command `ruby /home/your/path/to/mkdiruby/app.rb folder_chosen_name`, you create a folder with the chosen name.

In Ruby, it is possible to retrieve the strings entered right after the command typed in the CLI (in our case `folder_chosen_name`), thanks to the code `ARGV`. So let’s get back to our app.rb file:
```ruby
# ./lib/app.rb

  puts ARGV[0]
```
```
  -> mkdiruby/ $ ruby lib/app.rb new_folder
  new_folder
```
In our app.rb file, we now want to program to stop the user in case he doesn't type a folder name at all or types a folder name with spaces such as `folder chosen name`:
```ruby
# ./lib/app.rb

  def check_ARGV
    abort("mkdir: missing input\nYou need to launch this line\n
      ruby lib/app.rb folder_name_to_create\n\nwith only one argument") if ARGV.empty?
    if ARGV.length > 1
      puts "> You need to launch this line\n
      ruby lib/app.rb folder_name_to_create\n\n> with ONLY ONE argument"
    else 
      puts "Start creating folder project"
      sleep 1
      Dir.mkdir(ARGV[0])
      puts "End creating folder project"
    end
  end
```
While running the `Dir.mkdir(ARGV[0])` code, we create a folder on the spot with `ARGV[0]` as a value. Because the `ARGV` code returns an array, we want to take the first item only as the name of our Ruby project folder.

### 3. Create the Gemfile

In our app.rb file, we now want to program the creation of a Gemfile filled with the necessary Gems:
```ruby
# ./lib/app.rb

  def create_gemfile
    puts "Start creating Gemfile"
    sleep 1
    Dir.chdir("#{Dir.pwd}/#{ARGV[0]}")
    file = File.open("Gemfile", "w")
    file.puts("source \"https://rubygems.org\"\nruby '2.7.1'\ngem 'rubocop', '~> 0.57.2'\ngem 'rspec'")
    file.close
    puts "End creating Gemfile"
  end
```
While running the `Dir.chdir` code, we change the directory to go inside the folder that we just created. It would be equivalent to  `cd your_folder` typed in the CLI. Additionally, the `Dir.pwd` code would print your position on the Terminal screen (just like if you typed `pwd` in the CLI).

While running the `File.open("Gemfile", "w")` code, we create a new file if it doesn't exist yet, and/or we open it with the option to write in it. Here, we want to list the Ruby gems that we are going to use for the project. Of course, you are welcome to customize this part depending on which gems you need.

### 4. Run the command line `bundle install` to install all the dependencies

In our app.rb file, we now want to program the run of the command `bundle install`:
```ruby
# ./lib/app.rb

  def launch_bundle_install
    puts "Start install dependencies"
    sleep 1
    system("bundle install")
    puts "End install dependencies"
  end
```
In a nutshell, the `system("command_line")` code enables the Ruby program to run any command line in the CLI.

### 5. Run the command line `rspec --init` to create a `spec` folder and a `.rspec` file

Same logic, back in our app.rb file, we add:
```ruby
# ./lib/app.rb

  def launch_rspec
    puts "Start install rspec"
    sleep 1
    system("rspec --init")
    puts "End install rspec"
  end
```

### 6. Create the lib folder to store all your application files

You now know the drill, let’s automate another action via our app.rb file:
```ruby
# ./lib/app.rb

  def create_lib_folder
    puts "Start creating lib folder"
    sleep 1
    Dir.mkdir("lib")
    puts "End creating lib folder"
  end
```
### 7. Create a README.md file to explain what your application does, how we can use it and so on
```ruby
# ./lib/app.rb

  def create_readme
    puts "Start creating README.md"
    file = File.open("README.md", "w")
    file.puts("# A new application coded with Ruby")
    file.close
    puts "End creating README.md"
  end
```

### 8. Initialize git
```ruby
# ./lib/app.rb

  def launch_git
    puts "Start initialize git"
    sleep 1
    system("git init")
    puts "End initialize git"
  end
```

### 9. Create `.env` and `.gitignore` files
```ruby
# ./lib/app.rb

  def create_env_and_gitignore
    puts "Start creating .env"
    file = File.open(".env", "w")
    file.close
    puts "End creating .env"
    puts "Start creating .gitignore"
    file = File.open(".gitignore", "w")
    file.puts(".env")
    file.close
    puts "End creating .gitignore"
  end
```

### 10. Wrap it all together in a perform method

Nice! We now have scripted all the steps of a Ruby project folder creation. So, let’s put everything together in a perform method:
```ruby
# .lib/app.rb

  def perform
    check_ARGV
    create_gemfile
    launch_git
    create_env_and_gitignore
    launch_bundle_install
    launch_rspec
    create_readme
    create_lib_folder
  end

  perform
```

### 11. Create an alias in your `~/.bash_profile` or `~/.zshrc` 

And there we have the cherry on the cake! To gain even more time and productivity, let’s create a new alias to swiftly run our fresh program into the CLI:
```
  alias mkdiruby="ruby /home/your/path/to/mkdiruby/app.rb"
```

And that’s it! At this point, should you type `mkdiruby folder_chosen_name` from anywhere you are in the CLI, you’ll come up with a fully kitted Ruby folder, ready to be used!

## Author
- [Vanak Lay](https://github.com/vanaklay)
  
## Proofreading
- [Quentin Plaud](https://github.com/kentsbrockman)


