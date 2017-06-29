---
layout: post
title:  Including Files in Elixir
date:   2017-06-29 08:51:00 -0600
categories: Elixir
---
When I first created my Elixir Phoenix project, I immediately noticed the wide use of `alias`, `import`, and `use`. While trying to understand the preexisting code, it led me to feel quite confused. Here's a brief summary of what I learned, although for more information see the [documentation](https://elixir-lang.org/getting-started/alias-require-and-import.html)

### alias

`alias` should be used when you want to reference a module by a different name. In the case of my EbayClone app, all my modules start with the app name - EbayClone. If I wanted to refer to `EbayClone.User` as `User`. There's two ways to do this. The first way is write `alias EbayClone.User, as: User` or if you leave out `as:` it automatically sets the alias name to the last part of the module name. So, `alias EbayClone.User as: User` is the same as `alias EbayClone.User`. Now a function call becomes `User.changeset` instead of `EbayClone.User.changeset`


### import

This will import all functions and macros into the current module, so a function call does not have to be prefixed with the module name. For example, I have a module called `EbayClone.ItemCase` with a function `create_item/5` if I `import EbayClone.ItemCase` into my current module then my function call will just become `create_item` instead of `EbayClone.ItemCase.create_item`. `import` also allows you to choose if you only want certain functions to be loaded. 

### use

`use` will call the macro `__using__/1` and will put code into the module you are calling it from. Here's an example from the `EbayClone.ConnCase` which is a standard file that is generated when you create a new Phoenix app.

```elixir
  using do
    quote do
      # Import conveniences for testing with connections
      use Phoenix.ConnTest

      alias EbayClone.Repo
      import Ecto
      import Ecto.Changeset
      import Ecto.Query

      import EbayClone.Router.Helpers

      # The default endpoint for testing
      @endpoint EbayClone.Endpoint
    end
  end
```

As you can see `using` in turn calls `alias`, `import`, and `use`. So this creates creates aliases or imports in the whatever module is calling `use EbayClone.ConnCase`. The main advantage of this is that it cuts down on the excessive and repetitive use of aliasing and importing calls in modules. For example, any module that I call `use EbayClone.ConnCase` I no longer have to alias `EbayClone.Repo` or import functions from `Ecto.Query`, this is already available. 

These are a few of the most common ways I came across for loading files in Phoenix. Although it is something that frequently used across all Elixir code. 
