---
layout: project
title: Hocsv
thumbnail-path: "assets/images/building_ruby_gems/ruby.png"
published: true
short-description: "&bull; Hocsv is a Ruby library / gem that takes an array of hashes and parses the data into a csv file."
---

<h3 class="wide w3-center">Summary</h3>

How I built my Ruby gem.

<h3 class="wide w3-center">Explanation</h3>

**Hocsv** is a Ruby library / gem that takes an array of hashes and parses the data into a csv file.

<h3 class="wide w3-center">Problem</h3>

This project was meant to expand upon my backend knowledge and skills, particularly Ruby. Primary object objective was for me to meet all of the predefined user requirements.  

<h3 class="wide w3-center">Requirements</h3>

1. As a developer I want a gem that converts an array of hashes to a CSV file.
2. As a developer, I want the CSV file saved to the current directory.
3. As a developer, I want to create a csv that finds all the possible headers, and assigns the values in correct order, and fills empty spaces.
4. As an author, I want to build the gem.
5. As an author, I want to publish the gem.

<h3 class="wide w3-center">Solution</h3>

My first step was to install the bundler gem:
<center class="highlight">
  {% highlight Ruby %}
    gem install bundler
  {% endhighlight %}
</center>

I searched www.rubygems.org to verify that the name of my gem was not in use before I created the gem.

Because I installed bundler I was able to bootstrap my gem using the following command:

<center class="highlight">
  {% highlight Ruby %}
    bundle gem hocsv
  {% endhighlight %}
</center>

My gem directory was scaffolded as a result of the above mentioned command and development dependencies were added.

<center class="highlight">
  {% highlight Ruby %}
….
  spec.add_development_dependency "bundler", "~> 1.16"
  spec.add_development_dependency "rake", "~> 10.0"
  spec.add_development_dependency "rspec", "~> 3.0”
…
  {% endhighlight %}
</center>

It also initialized the repository for me given that I already had git installed on my machine and provided the a README.md and LICENSES.txt. A spec directory was also created from this command do to Rspec being installed on the system.   That folder houses all files necessary for testing my gem.

The next thing I did was to review my gem specification file and entered all the necessary information for my gem including: author(s) ,gem name, description, homepage if applicable(not required), gem dependencies needs to run the gem. I commented out the section responsible for using my gem to rubygems.org while I build.

<center class="highlight">
  {% highlight Ruby %}
    …
      # Prevent pushing this gem to RubyGems.org. To allow pushes either set the 'allowed_push_host'
      # to allow pushing to a single host or delete this section to allow pushing to any host.
      # if spec.respond_to?(:metadata)
      #   spec.metadata["allowed_push_host"] = "TODO: Set to 'http://mygemserver.com'"
      # else
      #   raise "RubyGems 2.0 or newer is required to protect against " \
      #     "public gem pushes."
      # end
    …
  {% endhighlight %}
</center>

Bundler produces a module for versioning gems called **version.rb** inside the **lib/hocsv**. I modified it into a class to fit my needs.

<center class="highlight">
  {% highlight Ruby %}
    class Hocsv
      VERSION = "0.1.0"
    end
  {% endhighlight %}
</center>

The next step involved writing unit tests for my gem once the foundation was laid. I did this so that the test could guide my code which allowed me to methodically build the functionality for my gem.

The initial step was to create the Hocsv class.

<center class="highlight">
  {% highlight Ruby %}
    require "hocsv/version"

    class Hocsv

    end
  {% endhighlight %}
</center>

Next I created two attributes, that also serves as parameters,  to hold the necessary data.

<center class="highlight">
  {% highlight Ruby %}
  attr_accessor :data, :filename
  {% endhighlight %}
</center>

I created an initialize  method that validates the data types. It also invokes the method to_hocsv responsible for converting the data and file.

<center class="highlight">
  {% highlight Ruby %}
…
  def initialize(data, filename="hocsv.csv")
    # 'data=' Returns the value of the data sent to the 'data' parameter
    self.data = data

    # Returns the value of the 'filename' parameter
    self.filename = filename
    raise InvalidDataError.new if (!data.is_a?(Array)) || (data.empty?.eql?(TRUE)) || (!data.any? {|obj| obj.respond_to?(:keys)}) || (!filename.is_a?(String)) || (filename.empty?.eql?(TRUE))
    to_hocsv
  end

…
  {% endhighlight %}
</center>

<center class="highlight">
  {% highlight Ruby %}
  def to_hocsv
    # adds. .csv to the end of the filename if not present
    filename.concat('.csv') if !filename.include?(".csv")

    #Places uniq keys into an array
    headers = data.flat_map(&:keys).uniq
    # Opens a new csv file in read and write mode with the provided file name or the default file name; adds column formatting
    CSV.open(filename, "w+b", col_sep: ', ') do |csv|

      # pushes headers to the file
      csv <<  headers

      data.each do |hash|
      #Retrieves values at a keys location, and inserts empty space when no value is present
        csv << hash.values_at(\*headers)
      end
   {% endhighlight %}
</center>

An InvalidDataError  class, that inherits from StandardError and produces a custom error message, was created to handle exceptions.  

<center class="highlight">
  {% highlight Ruby %}
class InvalidDataError < StandardError
  # Instantiates when an invalid data error is raised.
  def initialize(response = "Invalid data or file name. Please try again.")
    # Invokes StandardError's initialize with a custom response.
    super(response)
  end
end
  {% endhighlight %}
</center>


To build my gem I simply executed the following command:

<center class="highlight">
  {% highlight Ruby %}
gem build hocsv.gemspec
  {% endhighlight %}
</center>

I executed the following command to install it locally:

<center class="highlight">
  {% highlight Ruby %}
gem install ./hocsv-0.1.0.gem
  {% endhighlight %}
</center>

The final stage involved publishing my gem. To do that:

	1. Created an account on rubygems.org
	2. Setup my computer with my Rubygems account using this command:

                        $ curl -u username_here https://rubygems.org/api/v1/api_key.yaml >
                           ~/.gem/credentials; chmod 0600 ~/.gem/credentials

	3. Supplied my account password
	4. **$ gem push hcsv-0.1.0.gem**

<h3 class="wide w3-center">Results</h3>

User requirements met. Gem working as expected.

<h3 class="wide w3-center">Conclusion</h3>

Building and open sourcing a Ruby gem was fun and easier than anticipated. This was a valuable learning experience.
