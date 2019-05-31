---
layout: post
title:  Plug Vs. Rack
date:   2017-06-16 09:00:00 -0600
---
I've recently been dabbling a bit into Elixir and Phoenix. The often used analogy is plug is to Phoenix what rack is to Rails. As I was trying to understand plug I often tried to compare it to something that I was a little more familiar with - Rack. Here's a bit about what I learned when I compared them. 

#### What is Plug? 

José Valim, the creator Elixir, wrote that plug is a "generic adapter for different web servers (currently it only supports cowboy)". So it is a contract that the server will understand the data structure used by plug and the server will be able to translate it into a HTTP request/ response. Plug works on the data structure called a struct, and more specifically the connection struct (often abbreviated as `conn`). Each function that is "a plug" takes in a connection and outputs a connection. The function manipulates the struct (ex: changes the response header or response status) and returns a new connection. Going back to my middleware blogpost, Plug is middleware (used in a different way than in my Java Server and Clojure Ring). It's a simple way to go through the HTTP proccess. The beauty of Plugs is the way that they can be easily composable. You can create many different, specific functions that manipulate the connection to return what you want. I found this [blogpost](http://www.brianstorti.com/getting-started-with-plug-elixir/) particularly helpful, which has the image below. 


![Plug image](/assets/plug_image.png)

Here's an example of crafting your own plug in Phoenix. 

```elixir
def call(conn, _opts) do
    conn 
    |> put_resp_content_type("text/plain") 
    |> send_resp(200, "Hello, world!")
  end
end
```

When crafting your own Plug, it must have a `call` method that takes in a `conn` and outputs a `conn`. In this case we are sending a response with the body set as "Hello, world!" to every connection. The neat part is that `put_resp_content_type` and `send_resp` are also plugs. `put_response_content_type` takes in a conn, changes the header key to contain the value "text/plain", and `send_resp` changes the status key to have the value of 200 and the body key to have the value "Hello, world!". This allows us to create a plug pipeline, which is just a chaining of these functions until we have all information we want in the `conn` struct. The Phoenix framework heavily relies on Plugs. In fact, it essentially is what makes up Phoenix. From the Phoenix documentation, they state "core Phoenix components like Endpoints, Routers, and Controllers are all just Plugs internally". 


#### What is Rack? 

Rack is also a middleware. Many popular Ruby frameworks including Rails and Sinatra utilize Rack. It is very similar to plug in the sense that it is a contract that the server will understand the way that rack constructs the HTTP request/ responses and will translate that to bytes accordingly. It defines how a Rails or Sinatra app should communicate with the server. Applications that abide by Rack respond to a `call` method and take in a hash called `env` which represents the components in a HTTP request. They output an array which includes the status, headers, and body, which represents the HTTP response. So the server gives an application that abides by rack an `env` hash and expects the application to output an array representing the HTTP request. Rack middleware can also be chained in different orders just like plug. From the rack website here's an example. 

 
```ruby
require 'rack'
 
app = Proc.new do |env|
    ['200', {'Content-Type' => 'text/html'}, ['A barebones rack app.']]
end
```

When creating your own Rack middleware, you must have an object (`app`) which responds to the call method (by definition of `Proc` you can invoke `app.call`) and take in the `env` hash. Your app must output an array which contains the status, headers, and body. In this example, the app is very simple. But as you can imagine the simplicity of Rack has many benefits. 

#### Comparing the Two

The nature of these middlewares allow you to construct objects or functions that have a specific task and they can be switched around in order, which is one of the biggest benefits of them. To me, the main difference is summed up in the Phoenix documentation. It states "The basic idea of Plug is to unify the concept of a "connection" that we operate on. This differs from other HTTP middleware layers such as Rack, where the request and response are separated in the middleware stack." I found both to be neat to learn about, and Rack and Plug achieve the same result - just architected in different ways. 
 
