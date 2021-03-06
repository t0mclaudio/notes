In this post, I will illustrate how I set up our Django application on a production server.

Please note however that as of this writing, we already migrated to a JAM Stack application architecture. The purpose of this post is to help those who can benefit from my experience and a way for me to document this process.

Now let's begin. 

First, I created a virtual private server running Ubuntu 14 on a DigitalOcean droplet. After doing the initial setup and securing ssh access to the server, I installed git to allow me to fetch my Django application from a private repository.

I created a virtual environment where the application and its dependencies will reside. Isolating the app to a virtual environment prevents it from running into conflict with the system installed Python and libraries.

Next, I installed Redis and MySQL and configure Django to use a socket connection to communicate with these services.

At this point, we need a web server to accept HTTP requests and return responses to the clients. The web server I chose for this project is NGINX.

While Django can spawn a web server intended for local testing, it is not recommended for production because it is unable to handle multiple requests and has no security features expected on a production-grade web server. 

Now that we have NGINX installed, we need to configure it to forward requests to Django for processing. However, Nginx can not interface directly with Django. This is where Gunicorn, an application server, comes in. An application server acts as a proxy between Nginx and Django.

By this time, we have already established the request-response route between the client and our application. 

Now we can start the application and the services in the server.

Sometimes applications and services in the server crash or shutdown, and most of the time the remedy is to perform a restart. Doing this manually is a tedious process, ideally, we want this to happen automatically. I use a utility called Supervisored to automate this process of restarting. 

There will always be updates and bug fixes in the life of a software application. I created a Supervisord script to periodically watch for updates in the repository, initiate the process of fetching the updated codebase and restart the application and services to reflect the updates.

Additionally, resources such as photos, videos, etc are hosted on a separate content delivery network provider. This reduces disk usage and network calls to our server decreasing bandwidth consumption, which usually is a limited resource our VPS provider has set for us. 

We also use API services provided by third-party vendors such as for sending transactional emails and logging.

And that's it our Django Application is running on a production server.
