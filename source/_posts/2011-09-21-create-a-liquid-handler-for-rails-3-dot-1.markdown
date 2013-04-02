---
layout: post
title: "Create a Liquid handler for Rails 3.1"
date: 2011-09-21 14:03
comments: true
categories: rails, liquid
---

Back in the days with rails 2 you could easily create a handler for liquid and have access to the instance variables you've created in your controller. 
However, the creators of liquid didn't like this approach and so when Rails 3 came out the functionality didn't get upgraded.

I needed this functionality today for a cms I'm working on so I took some time and headaches to figure this one out.

## First: Add liquid as a dependency for your app
``` ruby Gemfile.rb
gem "liquid"
```

## Second: Create the handler
``` ruby lib/action_view/template/handlers/liquid.rb
class ActionView::Template::Handlers::Liquid
  def self.call(template)
    "ActionView::Template::Handlers::Liquid.new(self).render(#{template.source.inspect}, local_assigns)"
  end

  def initialize(view)
    @view = view
  end
  
  def render(template, local_assigns = {})
    @view.controller.headers["Content-Type"] ||= 'text/html; charset=utf-8'
    
    assigns = @view.assigns
    
    if @view.content_for?(:layout)
      assigns["content_for_layout"] = @view.content_for(:layout)
    end
    assigns.merge!(local_assigns.stringify_keys)

    controller = @view.controller
    filters = if controller.respond_to?(:liquid_filters, true)
                controller.send(:liquid_filters)
              elsif controller.respond_to?(:master_helper_module)
                [controller.master_helper_module]
              else
                [controller._helpers]
              end

    liquid = Liquid::Template.parse(template)
    liquid.render(assigns, :filters => filters, :registers => {:action_view => @view, :controller => @view.controller})
  end

  def compilable?
    false
  end
end
```

## Third: Create an initializer
``` ruby config/initializers/liquid_template_handler.rb
require 'action_view/template/handlers/liquid'
ActionView::Template.register_template_handler :liquid, 
  ActionView::Template::Handlers::Liquid
```

## Fourth: Grab a beer, I just saved you 4 hours of puzzles and headaches :-)

Now you can create files like posts/index.html.liquid.
In our case, we have our views in the database so we can render different views for different websites.
The book [Crafting Rails Applications](http://pragprog.com/book/jvrails/crafting-rails-applications) has an extraordinary chapter that shows you how put views in your database, worth every penny.
