---
layout: project
title: Building Ruby Gems
thumbnail-path: "assets/images/building_ruby_gems/ruby.png"
published: true
---


<p class="w3-center w3-wide w3-opacity-max w3-large"><b> Ruby gems are bundles of fun! </b></p>
<div class="w3-row-padding">

  <div class="w3-half">
  <p><b><span class="w3-xxlarge">R</span></b>uby is a fantastic language. It gets even better when once you discover all the gems/ libraries that are a part of the language. Gems are fundamental to just about every Ruby application. What if you have got an idea that you feel could help other developers? Perhaps you have created some functionality that might be useful to others? If the answer to one of these is yes then follow me on the short explanation of how to create a Ruby gem.
  </p>

  <p><b><span class="w3-xxlarge">P</span></b>rerequisites:</p>
  <p>
    1. You must have Ruby installed. I recommend using <a href="https://rvm.io/"><u>RVM</u></a>. It is a great tool for managing your Ruby packages, including gem sets.
  </p>

  <p>
  2. Bundler gem is required. You can add that like so $ gem install bundler
  </p>

  <p>
  3. A unique gem name. This is a very important step. See <a href="https://rubygems.org"><u>Ruby Gems</u></a>
  </p>


  <p><b><span class="w3-xxlarge">C</span></b>reating a gem for Ruby is actually quite simple</p>

  <b>Step 1:</b>

  <p>By installing Bundler your gem can be bootstrapped. The following command will execute the bootstrapping:</p>

  {% highlight ruby %}
  $ bundle gem your_gem_name_here
  {% endhighlight %}

  <p><b><span class="w3-xxlarge">
  T</span></b>hat command will scaffold a gem directory your_gem_name and initialize a Git repo if git is installed. You will be asked if you would like to include a LICENSE.txt and a CODE_OF_CONDUCT if you are using bundler for the first time. I recommend including both, but it is all up to you. Below is an example of what the directory should look like.
  </p>

  {% highlight ruby %}
  your_gem_name$ ls
  bin lib Gemfile    LICENSE.txt        README.md CODE_OF_CONDUCT.md   Rakefile   your_gem_name.gemspec
  {% endhighlight %}



  <b><span class="w3-xxlarge">T</span></b>here will be several files generated. The gem specification file is the first file to take a look at. This file is where you supply pertinent information for your gem including: author(s) ,gem name, description, homepage if applicable(not required), gem dependencies needs to run the gem. Your file should look similar to the following:

  {% highlight ruby %}
  lib = File.expand_path("../lib", __FILE__)
  $LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
  require “your_gem_name/version”

  Gem::Specification.new do |spec|
    spec.name          = "your_gem_name"
    spec.version       = YourGemName::VERSION
    spec.authors       = [“your name"]
    spec.email         = [“your email"]

    spec.summary       = %q{Summary of your gem.}
    spec.description       = %q{Description of your gem.}
    spec.homepage      = ""
    spec.license       = "MIT"

    # Prevent pushing this gem to RubyGems.org. To allow pushes either set the 'allowed_push_host'
    # to allow pushing to a single host or delete this section to allow pushing to any host.
    # if spec.respond_to?(:metadata)
    #   spec.metadata["allowed_push_host"] = "TODO: Set to 'http://mygemserver.com'"
    # else
    #   raise "RubyGems 2.0 or newer is required to protect against " \
    #     "public gem pushes."
    # end

    spec.files         = `git ls-files -z`.split("\x0").reject do |f|
      f.match(%r{^(test|spec|features)/})
    end
    spec.bindir        = "exe"
    spec.executables   = spec.files.grep(%r{^exe/}) { |f| File.basename(f) }
    spec.require_paths = ["lib"]

    spec.add_development_dependency "bundler", "~> 1.16"
    spec.add_development_dependency "rake", "~> 10.0"
    spec.add_development_dependency "rspec", "~> 3.0"
  end
  {% endhighlight %}
  </div>

  <div class="w3-half">
  <p><b><span class="w3-xxlarge">B</span></b>e sure to comment out everything from “Prevent pushing this gem… to the end below “public gem pushes”</p>

    <p>
    In the lib  directory under lib/your_gem_name you will find a a file version.rb. That is a module responsible for versioning your gem. There is no need to change anything here since this is the first iteration of your gem. You will see something similar to the following:
    </p>

      {% highlight ruby %}
      class YourGemName
        VERSION = "0.1.0"
      end
      {% endhighlight %}

    <p>
      You have the option start writing your code at this point. I would suggest writing test at this stage and allow your test to guide your code solution, however. RSpec was added as a dependency.  You will see the line below in your gem specification file
     <b>your_gem_name.gemspec</b> to the <b>Gem::Specification</b> block.
    </p>

      {% highlight ruby %}
       spec.add_development_dependency "rspec", "~> 3.0”
      {% endhighlight %}

    <p>
      Let's install:{% highlight ruby %} $ bundle install {% endhighlight %}  

      <p><b><span class="w3-xxlarge">B</span></b>y executing this command Bundler will create a Gemfile.lock which ensures that every gem in your library will be available on any system it is being developed on. You should also see a similar output to the following: Using <b>your_gem_name (0.1.0) from source at /path/to/your_gem_name</b>
      That output indicates that Bundler recognizes your gem, loads your gem and bundles your gem.
    </p>

    <p>
      The next command you will want to run is: {% highlight ruby %}rspec —init{% endhighlight %}

     This command will generate some standard files (<b>.rspec</b>, and <b>spec/spec_helper.rb</b>)for an Rspec project.  
    </p>

      Add the following to the top of <b>your_gem_name_spec.rb</b> and <b>spec_helper.rb</b> files:
      {% highlight ruby %}
      require ‘your_gem_name’
      {% endhighlight %}

    <p>
      Your main file <b>your_gem_name.rb</b>  should at least show <b>require ‘bundler/setup</b>, and <b>require ‘your_gem_name/version’</b>. If you have other classes you will need to require those files as well.

      It is crucial, but not required, that your classes and modules  have the same name as your gem. You should update your README file with configuration and usage instructions.

      It is time to build your gem after you have finished implementing your tests and the code to make them pass.

      From your root directory execute the following:
    </p>

    {% highlight ruby %}
    gem build your_gem_name.gemspec
    {% endhighlight %}

    <p>
      To instal your gem locally:
      {% highlight ruby %}
      gem install ./your_gem_name-0.1.0.gem
      {% endhighlight %}

      Now you can test it using IRB:
      {% highlight ruby %}
      $ irb
      {% endhighlight %}

      {% highlight ruby %}
      $ require ‘your_gem_name’
      {% endhighlight %}

      The last thing to do is publish your gem on rubygems.org if you wish to make available to the world.

      You will need a Rubygems account
      Add credentials as per the instructions.

      {% highlight ruby %}
      $ gem push your_gem_name-0.1.0.gem
      {% endhighlight %}
    </p>

    <p>That's it. I hope this helped. Please clap, comment, and share!</p>
    <!-- </div>
  </div> -->
