---
layout: post
title:      "StudioB - Rails Project"
date:       2020-10-28 05:10:38 -0400
permalink:  studiob_-_rails_project
---



Continuing to power through the Flatiron Scool curriculum, everything was slowly inching towards the more abstract. More macros, helpers, methods, and a requisite awareness of everything happening "magically" in the background. The first day of this project week, like the projects before, found me utterly clueless as to where to begin. My ideas revolved around my interests yet again; literature and music, but were far from concrete. A quick conversation with my significant other, fortunately, found the words for me. My major in school was sound engineering, and I still hold a passion for the music business, significantly where music and technology overlap. I thought about the current way musicians, artists, producers, and the like, managed their studio bookings when recording an album or any major project. Usually, that relationship is between the talents team and the studio itself, with different studios employing different tactics for managing their books. StudioB, my current project, seeks to break this dynamic by creating a platform where all studio managers, artists, musicians, and producers are available centrally.

I will not lie. I bit off more than I could chew on this one, but it led to some exciting growth as a programmer. The first hurdle was generating ten models and their relationships:

```
class User < ApplicationRecord
  
  has_many :instruments
  has_many :user_genres
  has_many :genres, through: :user_genres
  has_many :user_roles
  has_many :roles, through: :user_roles
  has_many :user_sessions
  has_many :studio_sessions, through: :user_sessions
  has_many :studios, through: :studio_sessions
	
end
```

There was hesitation that was bubbling underneath the surface of my brain. I had never dealt with this many models and their corresponding relationships at once, and doing so seemed intimidating. I didn't want to jinx the remainder of the project by wiring these models incorrectly. Taking notes on each route and its intended purpose while making an ERD  with Lucidchart helped foster an understanding I needed to progress. The most significant discovery that stemmed from this exercise was understanding how a "has many through" relationship would function within these models' boundaries. Specifically, the model User would have many Studios through StudioSessions. Given this model, a User has many StudioSessions. I also wanted to reflect on an entire recording session, which is that StudioSessions have many Users. The session's entirety does not belong to just one User; there are usually artists, producers, session musicians, and studio managers all in the same room. I then created a many to many relationship (User - StudioSession) within a larger many to many relationship (User - Studio). All of this became more manageable when I reiterated one fundamental point: all these macros and relationships are only adding methods. That realization was vital to not feeling overwhelmed by the number of models.

After creating a whole heaping serving of seed data and working through each of the three main controllers, Users, Studio, and StudioSessions, and their actions, the next hill to conquer was implementing a login/logout/signup system. This particular phase proved more difficult than other coding sessions prior. In planning this section, my instinct was to go with Devise, but due to a steep learning curve and issues encountered by other cohort mates in implementation, setting up a vanilla Rails signup/login system was the way to go. It was both a blessing and a curse. I thought not having to jump through hoops with Devise would make the process straightforward, but instead, I discovered something else; a lack of understanding. The project had inadvertently come to a halt. Late night, several tabs open in Chrome, I wanted to comprehend how sessions worked within Rails and how I had quickly gleaned through the reading but somehow retained little information. After a whole day's review of sessions, I was well equipped. I answered some pending questions I made prior, among them: "Why isn't this  form_for working with this session instance variable?" Ultimately I had an excellent login system to show for it in my SessionsController.

```
def create
        user = User.find_by(email: params[:session][:email])
        user = user.try(:authenticate, params[:session][:password])
            
        return redirect_to login_path, notice: "Invalid email/password combination" unless user
        
        session[:user_id] = user.id

        redirect_to user_path(user)
end
```

Proving similarly difficult after a foray into sessions was nested routes. Drawing the routes in config/routes.rb was easy enough. But controlling these routes' flow was another beast, mainly since I had a more restricted user experience. For example, I wanted the User to add studios that, when created, could only be managed by their respective "studio manager."  This studio manager would then add studio sessions belonging to studios they managed and belonging to the Users participating in each session—all great in theory. However, the mess came when trying to find the best strategy to prevent a user from trying to fiddle with the URL directly and edit someone else's studio or sessions. The solution I implemented used a couple of handy before_action filters for different circumstances:

```
def require_login
        redirect_to root_path unless session.include? :user_id
end

def require_manager
        redirect_to root_path unless current_user.roles.include?(Role.find(4))
end

def require_current_user
        redirect_to root_path unless current_user.id == params[:id]
end

def require_managed_studio
        redirect_to root_path unless current_user.id == @studio.studio_manager_id
end

def studio_managed_by_user
        if params[:studio_id]
            redirect_to root_path unless Studio.find_by(id: params[:studio_id]).studio_manager_id == current_user.id
        end
end
```

Overall, this project, more so than the previous two, has been incredibly challenging. However, I am thankful for the gaps in my knowledge that arose during the project's life cycle, allowing me to shed light and reinforce some weak links in the chain while programming something related to a field I love. As a total newcomer, programming has been possibly the most compelling topic I have ever studied. I am thrilled to discover all of the opportunities where music and programming overlap. JavaScript is on the horizon.



