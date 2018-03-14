---
layout: post
title: Blocipedia
thumbnail-path: "img/home.png"
short-description: Blocipedia is a production quality SaaS web application which allows users to create public and private Markdown based wikis and share them with other collaborators.

---

{:.center}
![]({{ site.baseurl }}/img/blocipedia.png)

## Explanation

Blocipedia is a CRUD application built using Ruby on Rails. This build uses some helpful gems including Stripe, Devise, Pundit, Redcarpet, and Faker.

## Problem

The aim for this project was to further my backend knowledge and skills of Ruby on Rails and gain greater exposure to some fantastic Ruby gems. Another objective was for me to meet all of the predefined user requirements.

## Objectives

* Create basic Rails user scheme
* Giver user the ability to sign up for app using Devise
* Create CRUD routes and resources for Rails app
* Authenticate and authorize users
* Integrate Stripe for payments
* Integrate Redcarpet Markdown gem for rendering

#### User Requirements

1. Allow users to sign up for a free account by providing a user name, password and email
2. Allow users to sign in and out of Blocipedia
3. Allow users with a standard account to create, read, update, and delete public wikis
4. Provide three user roles: admin, standard, or premium
5. Allow user to upgrade account from a free to a paid plan
6. Allow premium user to create private wikis
7. Allow user to edit wikis using Markdown syntax
8. Allow premium user to add and remove collaborators for my private wikis

## Solution

The initial stages of the app involved generating a new Rails app configuring git and Github for collaboration and adding default gems for the project.

{% highlight Ruby %}

$ rails _4.2.5_ new new-rails-project —skip-test-unit
$ cd new-rails-project
{% endhighlight %}
From there I generated a Welcome controller and related views, using HTML and CSS, which gave users a landing page to welcome them to the app. To sign users up for the app I and add user authentication I incorporated Devise. I then created a Devise User model.

{% highlight Ruby %}
$ rails g devise user
{% endhighlight %}

Sendgrid was integrated into the app which allowed the app to send confirmation emails. To securely configure Sendgrid username and password the Figaro gem was used. I find myself using this gem quite a bit.  For sign-in sign-out capabilities I used the Devise helper method user_signed_in? which determined if a user was signed in and rendered a particular view based on the response. The navigation links Edit Profile and Sign Out indicated a user was signed in. The user would see the Sign-up or Sign In navigation links if not signed in.   

To enable users to Create, Read, Update, and Delete public wikis I generated a wiki model that references the user, a controller, and views for the resource.

{% highlight Ruby %}
$ rails g model Wiki title:string body:text private:boolean user:references:index
{% endhighlight %}

For authorization I used the Pundit gem. I defined three user roles: standard, premium, admin using an enum attribute on the User model. The after_initialize callback implemented standard as the default value for users. For this project I wanted to allow users to edit any public wiki unlike Bloccit where the user had to be an admin. To do so the **#update** method for application policy was modified to:
{% highlight Ruby %}
_app/policies/application_policy.rb_

  def update?
    user.present?
  end
    {% endhighlight %}
An inner Scope class was added to the wiki_policy regulate which wikis populated on the index page. The scope was then used to modify the **#index** action in the wikis_controller to show the right wikis.

{% highlight Ruby %}
  -  def index
  -   @wikis = Wiki.all
  -  end

   +  def index
   +   @wikis = policy_scope(Wiki)
   +  end  
   {% endhighlight %}
   
Stripe was later integrated for payment processing. This feature was necessary to allow users a path to upgrading to a premium account. To create charges a ChargesController was generated with a **#create** action and a **#new** action. This controller initializes and then creates Charge objects after receiving to params: stripeToken and amount , ultimately sending them through Stripes API to complete the transaction. I also designed a user flow for down grading or reverting back to a standard account.

To handle private wikis I implemented some privacy controls on wikis that checked to see if the users role was admin or  premium  before allowing a private wiki to be edited. The control included in wikis partial app/views/wikis/ \_form.html.erb presented a checkbox only viewable by premium users or an admin.

In order to parse Markdown syntax Redacarpet was integrated.  

The last resource generated was the Collaborator. A collaborator model was built to allow users to add and remove collaborators from private wikis from the wiki’s edit page. A relationship between wikis and users was established through the collaborators model by implementing a has many through relationship.  





## Results

>All user requirements met and working as expected.

## Conclusion

I could have implemented authentication and authorization mechanisms from scratch which is unnecessary, but a very valuable learning tool. I could have generated the views for this app using HAML instead of ERB. I could have added a real-time Markdown editor for wikis.I am always looking to grow so these are definitely features to add to in the future or to future projects. This was a really fun project. Rails is incredible and I love it.
