---
layout: post
title: Blocmarks
thumbnail-path: "img/blocMarks/homePostSignout.png"
short-description: Build a bookmarking application that allows users to bookmark URLs via email, view other user’s bookmark and keep a personal list of bookmarks.
---

{:.center}
![]({{ site.baseurl }}/img/blocMarks/homePostSignOut.png)




## Summary
Build a bookmarking application that allows users to bookmark URLs via email, view other user’s bookmark and keep a personal list of bookmarks.

## Explanation

Bookmarking is easy, but keeping a bookmark collection tidy and manageable is no so easy. Sharing bookmarks from your browser is not so easy either. Blockmarks addresses and resolves the issues mentioned by categorizing bookmarks according to topics. Blockmarks also makes bookmarks public so that other users can discover and add bookmarks to their profile.    

## Problem
This project was meant to expand upon my backend knowledge and skills, particularly Ruby on Rials. Primary objective was for me to meet all of the predefined user requirements.  


## User Requirements

1. Allow user to sign up for a free account by providing a user name, password and email
2. Allow users to sign in and out of Blockmarks
3. Allow users to email a URL to Blocmarks and have it saved in the Blocmarks database
4. Allow users to see an index of all topics and their bookmarks
5. Allow users to create, read, update, and delete bookmarks
6. Allow users to be the only one allowed to delete and update my bookmarks
7. Allow users to "like and unlike" bookmarks created by other users
8. Allow users to see a list of bookmarks on my personal profile that I've added or liked


## Solution
a new Rails app was generated to start the project . Next I configured the default gems for my project and generated a welcome controller for a landing page which welcomes users to the application. I added _Bootstrap_ to make styling the application easier . Finally I configured _git_ and _Github_ workflow for collaboration. The aforementioned steps are the initial steps for every project of course.

Once the foundation for the app was laid I incorporated and configured _Devise_ for authentication. This step involved generating a Devise User model.
```
rails g devise user
```


**Rails** generated a migration file as a result. To run the migrations I executed :
```
rake db:migrate
```
This allowed users to sign up for the application and send emails for confirmation.


I used the _Devise_ helper method `user_signed_in?` for sign-in sign-out capabilities.  This determined if a user was signed in and rendered a particular view based on a _boolean_ response. The navigation links <span style="color:blue">Edit Profile</span> and <span style="color:blue">Sign Out</span> indicated a user was signed in.

![Edit Profile](/img/blocMarks/editUser.png)

The user would see the <span style="color:blue">Sign Up</span> or <span style="color:blue">Sign In</span> navigation links if not signed in.  I used a _Devise_ filter method to redirect users that are not signed in to the login page  . The `except [:index, :about]` syntax removes login requirement to see the index and about pages.


app/controllers/application_controller.rb  
```
before_action :authenticate_user!, except: [:index, :about]
```

I generated a UsersController which included a **#show** action to allow signed-in users to see their profile page .  I updated the root path in **routes.rb** to map to the users show view to redirect the user to their show view after <span style="color:blue">Sign In</span>.

My next task was to allow a user to see an index of all Topics and their bookmarks. A **Topic** model that associated with a user was generated to address this need.  
```
$ rails g model Topic name:string user:references:index
```

By referencing the **User** model a ` belongs_to :user` relationship was created on the **Topic** model.

**app/model/topic.rb**
```
class Topic < ActiveRecord::Base
  belongs_to :user
...

end
```

An `has_many :topics` relationship was established on the **User** model.


```
app/models/user.rb

class User < ActiveRecord::Base
...

   has_many :topics
end
```


Next I generated a **Bookmark** model and associated bookmarks with topics.
```
$ rails g model Bookmark url:string topic:references:index
```
```
app/models/bookmark.rb

class Bookmark < ActiveRecord::Base
  belongs_to :topic
  ...
end
```

I generated a **Topics** controller with **#index**, **#show**, **#new**, **#edit** actions.
```
$ rails g controller Topics index show new edit
```

![New Topic](/img/blocMarks/newTopic.png)

I implemented <span style="color:red">C</span>reat<span style="color:red">R</span>ead<span style="color:red">U</span>pdate<span style="color:red">D</span>estroy</span> for topics frm there.

I then implemented <span style="color:red">CRUD</span> for bookmarks. I generated a **Bookmarks** controller with **#show**, **#new**, **#edit** actions. No index action was created for the **BookmarksController** given that the topics show view handles the rendering.
```
$ rails g controller Bookmarks show new edit
```

It was important to nest **Bookmarks** resource under the **Topics** resource to make this work.  

**routes.rb**
```
...
  resources :topics ...
    resources :bookmarks, except: [:index] ...
  ...
  end
...  
```

I implemented <span style="color:red">CRUD</span> for bookmarks after completing the above actions to ensure users could create new bookmarks, view, update and delete created bookmarks.

My next focus was allowing users to email a <span style="color:blue">URL</span>s to **Blocmarks** and have it saved in the **Blocmarks** database.I implemented _Mailgun_ to accomplish this task. I modified the **#create** action in **IncomingController** to process incoming emails and convert them to bookmarks .

I integrated _Pundit_ into the application to handle authorization. This was necessary to only allow  users to update or delete bookmarks belonging to them. This meant adding the _Pundit_ gem to my **Gemfile** and bundling the gem.

**Gemfile**
```
...
  gem 'pundit'
...
```

```
$ bundle
```

I included Pundit in the ApplicationController.

**app/controllers/application_controller.rb**
```
class ApplicationController < ActionController::Base
   include Pundit
   ...
```

I generated a default policy file with the following command:

`
$ rails g pundit:install
`

Next I defined **#create**, **#update#**, and **#destroy** actions in the **application\_policy.rb** policy file

**app/policies/application_policy.rb**
```
   def create?
     user.present?
   end

   def update?
     user.present? && (record.user == user)
   end

   def destroy?
     user.present? && (record.user == user)
   end
```
I created a similar policy file for bookmarks and added authorization to **BookmarksController** and its controller views.


I created a like model to give users the ability to like a bookmark. Because liked bookmarks had to be associated with the user who flagged them and the bookmarks that were  marked as a like, a separate **Like** model was generated with **bookmark** and **user** references. The **like table** served as a a **join table**, which represents the relationship between the two objects/ tables **user** and **bookmark**.  I associated **Likes** with **Users** and **Bookmarks** by adding a `has_many` to the **user** and **bookmark** models **_user.rb_** and **_bookmark.rb_** respectively. I added a `dependent: :destroy` option for **user** and **bookmark** models to ensure that an instance of the **Like** model was associated.

I created a **#liked**  method to determine if a user has liked a bookmark. It allows users to toggle between <span style="color:blue">like</span> and <span style="color:blue">unlike</span> links in views.

app/models/user.rb
```
...
   def liked(bookmark)
     likes.where(bookmark_id: bookmark.id).first
   end
...
```
This method receives a **bookmark** object and produces a **like** object if there is a record in the **likes table** that is properly identified with a **user_id** and a  **bookmark_id**. **Nil** is returned otherwise.


A likes controller was generated next.
```
$ rails g controller Likes index
```

Required routes were added to routes.rb.

**config/routes.rb**
```
   resources :bookmarks, except: [:index] do
     resources :likes, only: [:index, :create, :destroy]
```

A likes  index was created to render a list of all bookmarks.


I built a partial to display links for liking bookmarks in the **app/views/likes/** folder. I added the following **erb** syntax to my views to render the partial :

```
     <%= render partial: 'likes/like', locals: { bookmark: @bookmark } %>
```

Create and destroy methods were added to the LikesController.

**app/controllers/likes_controller.rb**
```
   def create
     @bookmark = Bookmark.find(params[:bookmark_id])
     like = current_user.likes.build(bookmark: @bookmark)

     if like.save
       # Add code to generate a success flash and redirect to @bookmark
     else
       # Add code to generate a failure flash and redirect to @bookmark
     end
   end

   def destroy
     # Get the bookmark from the params
     # Find the current user's like with the ID in the params

     if like.destroy
       # Flash success and redirect to @bookmark
     else
       # Flash error and redirect to @bookmark
     end
   end
```

A policy for likes was then added. Authorization for the **LikeContorller** was added in addition to an authorized like prior to the **IF**  statements in the **#create** and **#destroy**  actions. Authorization had to be added to the partial as well.  

**app/views/likes/\_like.html.erb**
```
 <% if policy(Like.new).create? %>
   <div>
     <% if like = current_user.liked(bookmark) %>
       <%= link_to [bookmark, like], class: 'btn btn-danger', method: :delete do %>
         <i class="glyphicon glyphicon-star-empty"> </i>&nbsp; Unlike
       <% end %>
     <% else %>
       <%= link_to [bookmark, Like.new], class: 'btn btn-primary', method: :post do %>
         <i class="glyphicon glyphicon-star"> </i>&nbsp; like
       <% end %>
     <% end %>
   </div>
 <% end %>
```
Lastly, I generated a **Users controller** to allow users to see a list of added or liked bookmarks on their profile pages.

```
$ rails g controller Users show
```
Required routes were also added.

**config/routes.rb**
```
devise_for :users
 resources :users, only: [:show]
```

A **#show** action was added to the UsersController to produce instance variables containing bookmarks the user has created and liked.

I gave users the ability to unlike bookmarks on the personal page as a final touch.

## Results
> All user requirements met and working as expected.

## Conclusion

I had fun with this project.  I have made several apps using Rails to this point. I cannot get enough of Rails. Incorporating rake was very interesting. I could have made the users show page private. I will do this knowing I would want this feature as a user. I could have implemented user authentication from scratch instead of using a gem. I could have automated delete Rake tasks to run daily.
