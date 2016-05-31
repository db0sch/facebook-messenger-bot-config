# Facebook Messenger bot configuration on Rails

I started with this gem : https://github.com/hyperoslo/facebook-messenger

You can use this gem on your existing Rails app.
Just follow the following steps :

1. Create a specific file for the bot code

=> `app/bot/bot.rb` (for example)

In this file, you can use the code available there : https://github.com/hyperoslo/facebook-messenger/blob/master/examples/bot.rb

As you can see, you'll have to set the "Page Access Token" and "Verify Token" in your application.yml (for development) or your Heroku credentials (for production).
Be careful to name these credentials in your application.yml/heroku credentials, just like in the ENV[] statement in app/bot/bot.rb.

You don't have the "Page Access Token" and "Verify Token" yet. We will have them soon, when we will create the FB app (on the FB platform website).

2. Add a route on your routes.rb file

=> `config/routes.rb`

Add the following line to your routes file :

`mount Facebook::Messenger::Server, at: 'bot'`

3. Add a `bot.rb` file to your initializers folder

with the following code in it : 
```
unless Rails.env.production?
  Dir["#{Rails.root}/app/bot/**/*.rb"].each { |file| require file }
end
```

As the gem developpers put it : 
> Remember that Rails only eager loads everything in its production environment. In the development and test environments, it only requires files as you reference constants. You'll need to explicitly load app/bot.

And that's what the preivous code does. It explicitly ask rails to load app/bot everywhere in the app.

That's all for your rails app for now.

4. Create an FB app (or use an existing one) and click on "Messenger" on the left panel.

You must specify the FB page of the app (create one if it doesn't already exist).
And you must then create a 'webhook'.
A webhook tells facebook to send the messenger message to the server linked with the webhook.
So you can interact with them.

When you choose the FB page to link with the app, it should give you a "Page access token".
Copy it and paste it in your application.yml like this `ACCESS_TOKEN: $THE_PAGE_ACCESS_TOKEN`.

You should have a "test" version of your facebook app, and a production (normal) version.

First, start with 'test' version.

When you create the webhook, it'll ask you to specify the URL of the server to hook with.
As you're coding on your computer, and you don't want to push your code on Heroku to check every modification, you should user 'ngrok' : https://ngrok.com/

Ngrok can create a tunnel between your `rails s` server (just specify the port it should listen to) on your computer (for local development) and the web. It'll provide you with a http or https url you can use for your test on the FB platform.
Do not forget that we set up a specific route for the bot.
Therefor, the URL for the webhook will be : `https://$YOUR_NGROK_URL/bot`.

(unless you're under the playing plans, Ngrok will change the URL each time you launch it. You'll then have to change the webhook configuration each time you relaunch it).

On the webhook set up page, you'll also have to give a Verify Token. Enter whatever you like. Of course, the more long, complex, unpredictable it is, the better.
At the same time, you can copy/paste this Verify Token on your application.yml.
Like this : `ACCESS_TOKEN: $YOUR-CHOSEN-TOKEN`

Then choose "messages" on the subscription fields, and hit Verify and Save.

The FB app and your server should be linked now.

Then follow the FB indication. 
And try to send messages to you FB page. It everything is set up correctly, it should say `hello human` when you send `hello`.

====

Info importante indiquée sur la doc de FB :
>These ids are page-scoped ids. This means that the ids are unique for a given page.

>If you have an existing Facebook Login integration, user IDs are app-scoped and will not work with the Messenger platform.

Post an issue if you have any question, or need any help.

I'd like to thank Hyper (https://github.com/hyperoslo) for their work on this gem.
It has helped a lot.

PS : i've written this text in order to help beginners like me, who could struggle for their first bot configuration. Hope it will help.
