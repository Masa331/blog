---
layout: post
title:  "Password-less hassle-free authentication in Rails"
date:   2016-05-21 21:22:49
comments: true
author: Přemysl Donát
---
# Password-less hassle-free authentication in Rails

I think you know the situation pretty good. You want to try some app but then comes the registration. Big form, password requirements, email confirmation... Just why?!

For both users and developers, passwords, registrations, sing-ins and everything related means just trouble.

What i hate as a user:

* Filling registration forms
* Retyping password
* **Fucked up password format requirements**
* Email confirmation
* Password resets
* Not working password resets
* Password managers
* Oauths

What i hate as a developer:

* Implementing logic unrelated to my app's domain
* Third party dependencies(devise, authlogic..)
* Security responsibilities


I always wished there was some simpler way. And finally i found it! Do you know [Asciinema.org](https://asciinema.org)? No? Go check it out and try it's [login](://asciinema.org/login/new). This is it. You just type your email, get your sing-in link and that's all! **No forms, no requirements, no confirmations, no reset, no nothing!**

The workflow is incredibly simple:

1. User fills in his email
2. App sends him email with link with unique token
3. User clicks on link
4. App looks up user by token and signs him in. Token is invalidated so it can't be reused

## Implementing this in Rails

First of all some scaffolds:

~~~
rails new passwordless_authentication
rails g controller users index edit update
rails g model user email:string name:string login_token:string login_token_valid_until:datetime
rails db:migrate

rails g controller logins create
rails g controller sessions create destroy
rails g mailer login login_link
~~~

And code with comments:

{% highlight ruby %}
Rails.application.routes.draw do
  post 'logins/create'
  get 'sessions/create'
  delete 'sessions/destroy'

  resources :users

  root 'users/index'
end

# Here is just some basic example for authenticated/non-authenticated user restrictions
class UsersController < ApplicationController
  before_action :authenticate_user!, only: [:edit, :update]

  def index
    @users = User.all
  end

  def edit
    @user = User.find(params[:id])
  end

  def update
    User.find(params[:id]).update!(name: params[:user][:name])
    redirect_to users_path
  end

  private

  def authenticate_user!
    if current_user.anonymous?
      redirect_to root_path, alert: 'Not authenticated'
    end
  end
end

class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  helper_method :current_user

  def current_user=(user)
    session[:user_id] = user.id
  end

  # If i don't find a user from session i return null object
  def current_user
    User.find_by(id: session[:user_id]) || NullUser.new
  end
end

class LoginsController < ApplicationController
  # This is the action triggered by login form
  #   if we don't find user by given email we create new one
  def create
    user = User.find_or_create_by!(email: params[:email]) do |user|
      user.name = 'Edit me!'
    end
    # Here we set unique login token which is valid only for next 15 minutes
    user.update!(login_token: SecureRandom.urlsafe_base64,
                 login_token_valid_until: Time.now + 15.minutes)

    LoginMailer.login_link(user).deliver

    redirect_to root_path, notice: 'Login link sended to your email'
  end
end

class SessionsController < ApplicationController
  # This is the action triggered by login link
  def create
    # We don't sign in user with token which expired
    user = User.where(login_token: params[:token])
             .where('login_token_valid_until > ?', Time.now).first

    if user
      # Here we nullify login token so it can't be reused
      user.update!(login_token: nil, login_token_valid_until: 1.year.ago)

      self.current_user = user
      redirect_to root_path, notice: 'Signed-in sucesfully'
    else
      redirect_to root_path, alert: 'Invalid or expired login link'
    end
  end

  # Simple sign-out. Just set current user to NullUser
  def destroy
    self.current_user = NullUser.new
    redirect_to root_path, notice: 'Sucesfully signed-out'
  end
end

class User < ApplicationRecord
  def anonymous?
    false
  end
end

class NullUser
  def anonymous?
    true
  end

  def id
    nil
  end
end

class LoginMailer < ApplicationMailer
  def login_link(user)
    @user = user

    mail to: @user.email, subject: 'Sign-in into someapp.com'
  end
end
{% endhighlight %}

That's pretty much it without views. Views are just some tables and forms. Check them out in the [example app repo]().

## Conclusion

With this passwordless login you don't need to bother yourself and your users with:

* Registration forms
* Rassword requirements
* Forcing your users to remember yet another password
* Confirmation of emails
* Password storage security
* Resetting and changing passwords logic
* Using third party libs and gems

### Update 24.5.2016

Some cons were mentioned in discussion under the post. I consider these two the worst:

* Long email delivery time
* Users not used to such systems

## Links

* [My repo with example app](https://github.com/Masa331/rails_passwordless_authentication)
* [Asciinema.org repo](https://github.com/asciinema/asciinema.org)
