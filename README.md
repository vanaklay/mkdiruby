# How to make mkdiruby file to automate Ruby's folder creation

Every time you want to make an app in Ruby, you have plenty to do : 
- lib folder
- Gemfile file and put inside all the gems that you need for you app
- Launch the command : rspec --init to initialize a TDD 
- And more things...

It would be cool to be able to create an command line like `mkdiruby` who takes care of creating all this for us. Isn't it ?

It would work like `mkdir` except that it would take care of everything you need to do when you create a Ruby folder.

Let's do that... 

## Long time ago ... The Westerns Ages !

We need to create all these files and folder : 

1 - Create a folder and go inside
```
  $ mkdir mkdiruby

  $ cd mkdiruby
  -> mkdiruby
```
2 - Create a Gemfile and put all the gems inside it
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
3 - Run Bundle install to install dependencies
```
  -> mkdiruby/ $ bundle install

  -> mkdiruby/ $ ls
  Gemfile Gemfile.lock
```
3 - Don't forget the README.md !
```
  -> mkdiruby/ $ echo "# New app in Ruby" >> README.md

  -> mkdiruby/ $ ls
  Gemfile Gemfile.lock README.md
```
With `echo` it writes what is in the double quote in the README.md file. If README.md doesn't exist, it create it.

4 - Initialize rspec to execute some Test-Driven Development
```
  -> mkdiruby/ $ rspec --init

  -> mkdiruby/ $ ls -a
  Gemfile Gemfile.lock README.md spec/ .rspec
```
5 - Create a lib folder
```
  -> mkdiruby/ $ mkdir lib

  -> mkdiruby/ $ ls -a
  Gemfile Gemfile.lock README.md spec/ .rspec lib/
```
6 - Finally create app.rb and his test app_spec.rb
```
  -> mkdiruby/ $ touch lib/app.rb

  -> mkdiruby/ $ touch spec/app_spec.rb
```

Now we can work on our app ! 

So evertime you create a new app, you need to pass through all this process before starting to code your app...

Let's me say you something my friend, because i ❤️❤️ you ... 
![DRY](https://res.cloudinary.com/teepublic/image/private/s--r5W7iKNZ--/t_Resized%20Artwork/c_fit,g_north_west,h_954,w_954/co_000000,e_outline:48/co_000000,e_outline:inner_fill:48/co_ffffff,e_outline:48/co_ffffff,e_outline:inner_fill:48/co_bbbbbb,e_outline:3:1000/c_mpad,g_center,h_1260,w_1260/b_rgb:eeeeee/c_limit,f_jpg,h_630,q_90,w_630/v1551016863/production/designs/4274629_0.jpg)

## Past 2020 ... The Coding Ages !

1 - (7) Starting from the end of the western ages, we have an app folder ready to be use
```
  -> mkdiruby/ $ ls -a
  Gemfile Gemfile.lock README.md spec/ .rspec lib/

  -> mkdiruby/ $ ls -a lib
  . .. app.rb

  -> mkdiruby/ $ ls -a spec
  . .. app_spec.rb spec_helper.rb
```
2 - Create a folder where we are

The aim is that if we launch this command line `ruby lib/app.rb folder_name_to_create`, we have the ability to create a folder with the given name.

In Ruby it is possible to retrieve the strings entered just after the command, in this case `folder_name_to_create` with the code `ARGV`
```ruby
# ./lib/app.rb

  puts ARGV[0]
```
```
  -> mkdiruby/ $ ruby lib/app.rb new_folder
  new_folder
```
In our code, we filter if the user doesn't enter a folder name or if the user enters folder name seperate by space like that: `folder name to create`
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
With `Dir.mkdir(ARGV[0])`, we create an inplace folder with `ARGV[0]` as a value. Because `ARGV` returns an array we take the first item.

3 - Create the Gemfile
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
With `Dir.chdir`, we change directory to go inside the folder that we just created, it's like `cd your_folder` in command line. `Dir.pwd` is the position of where we are and `ARGV[0]` your app folder.

With this command `File.open("Gemfile", "w")`, we create a new file if it doesn't exist or we just open it with the option to write it. Then we put all the informations that we need, all the gems.

4 - Launch the command line `bundle install` to install all the dependencies
```ruby
# ./lib/app.rb

  def launch_bundle_install
    puts "Start install dependencies"
    sleep 1
    system("bundle install")
    puts "End install dependencies"
  end
```
`system("command_line")` allows us the ability to launch command line code in Ruby.

5 - Launch the command line `rspec --init` to install spec folder and .rspec file
```ruby
# ./lib/app.rb

  def launch_rspec
    puts "Start install rspec"
    sleep 1
    system("rspec --init")
    puts "End install rspec"
  end
```

6 - Create the lib folder to store all your application files
```ruby
# ./lib/app.rb

  def create_lib_folder
    puts "Start creating lib folder"
    sleep 1
    Dir.mkdir("lib")
    puts "End creating lib folder"
  end
```
7 - Create the README.md file to explain what do your application or how we can use it or how we can install it
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

8 - Initialize git
```ruby
# ./lib/app.rb

  def launch_git
    puts "Start initialize git"
    sleep 1
    system("git init")
    puts "End initialize git"
  end
```

9 - Create `.env` and `.gitignore` files
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

10 - Put all in the perform method
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

11 - Create an alias in your `~/.bash_profile` or `~/.zshrc` 
```
  alias mkdiruby="ruby /home/your/path/to/mkdiruby/app.rb"
```

Now you can run `mkdiruby` from anywhere in your computer.

## Author
- [Vanak Lay](https://github.com/vanaklay)


