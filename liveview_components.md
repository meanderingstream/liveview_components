---
title: Liveview Components
theme: moon
revealOptions:
    transition: 'fade'
---
## Intro

Scott Mueller

### Knowfalls.com
---
## Tampa Elixir

Looking for Presenters


---
## Overview of Phoenix Liveview

* Purpose
* Simplest Component
* Code Snippets
* Liveview Use Cases
* Liveview Limitations
* AI and Liveview
* Strengths of Liveview

---
## Purpose

Components are a mechanism to compartmentalize state, markup, and events in LiveView

Defined by using Phoenix.LiveComponent

Called by Phoenix.LiveView.live_component/3

---
## Simplest LiveComponent

```elixir
defmodule ClassComponent do
  use Phoenix.LiveComponent

  def render(assigns) do
    ~L"""
    <button class="<%= @button_state_style %>" phx-click="btnclk" phx-value-btnclk="<%= @button_val %>">
        <%= @button_text %>
    </button>
    """
  end
end
```

Define render/1
---
## Invoke Component - Stateless

```elixir
<%= live_component @socket, ClassComponent, button_state_style: @class1_state,
 button_text: @class1_text, button_val=@class1_text %>
<%= live_component @socket, ClassComponent, button_state_style: @class1_state,
 button_text: @class1_text, button_val=@class1_text %>
<%= live_component @socket, ClassComponent, button_state_style: @class1_state,
 button_text: @class1_text, button_val=@class1_text %>
```
---
## Stateless Lifecycle

```elixir
mount(socket) -> update(assigns, socket) -> render(assigns)
```
A stateless component is always mounted, updated, and rendered whenever the parent template changes. That's why they are stateless: no state is kept after the component.

---
## Stateful Component Invoke

```elixir
<%= live_component @socket, ClassComponent, id: @class.id, button_state_style: 
@class_state, button_text: @class_text, button_val=@class_text %>
```

Add an id field
---
## handle_event/3

Stateful components can also implement the handle_event/3 callback that works exactly the same as in LiveView.

---
## Stateful Component

```elixir
defmodule UserComponent do
  use Phoenix.LiveComponent

  def render(assigns) do
    ~L"""
    <button id="class-<%= @id %>" class="<%= @button_state_style %>" 
    phx-click="btnclk" phx-target="#class-1" phx-value-btnclk=
    "<%= @button_val %>">
        <%= @button_text %>
    </button>
    """
  end
end
```

For a client event to reach a component, the tag must be annotated with a phx-target annotation which must be a query selector to an element inside the component.
---
## Update

Every time a stateful component is rendered, both preload/1 and update/2 are called.

```elixir
def update(assigns, socket) do
  user = Repo.get! User, assigns.id
  {:ok, assign(socket, :user, user)}
end
```

rendering multiple user components in the same page via update, 
you have a N+1 query problem
---
## Preload

invoked with a list of assigns for all components of the same type

```elixir
def preload(list_of_assigns) do
  list_of_ids = Enum.map(list_of_assigns, & &1.id)

  users =
    from(u in User, where: u.id in ^list_of_ids, select: {u.id, u})
    |> Repo.all()
    |> Map.new()

  Enum.map(list_of_assigns, fn assigns ->
    Map.put(assigns, :user, users[assigns.id])
  end)
end
```

return an updated list_of_assigns, keeping the assigns in the same order as they were given
---
## Liveview Source of Truth

LiveView will be responsible for fetching all of the cards in a board

```elixir
<%= for card <- @cards do %>
  <%= live_component CardComponent, card: card, board_id: @id %>
<% end %>
```

---
## LiveComponent handle_event

Pass event on to parent Liveview

```elixir
defmodule CardComponent do
  ...
  def handle_event("update_title", %{"title" => title}, socket) do
    send self(), {:updated_card, %{socket.assigns.card | title: title}}
    {:noreply, socket}
  end
end
```
---
## Liveview handle_info

```elixir
defmodule BoardView do
  ...
  def handle_info({:updated_card, card}, socket) do
    # update the list of cards in the socket
    {:noreply, updated_socket}
  end
end
```
---
## PubSub to parent Liveview

```
defmodule CardComponent do
  ...
  def handle_event("update_title", %{"title" => title}, socket) do
    message = {:updated_card, %{socket.assigns.card | title: title}}
    Phoenix.PubSub.broadcast(MyApp.PubSub, board_topic(socket), message)
    {:noreply, socket}
  end

  defp board_topic(socket) do
    "board:" <> socket.assigns.board_id
  end
end
```

Distributed updates out of the box
---
## LiveComponent source of truth

```elixir
<%= for card_id <- @card_ids do %>
  <%= live_component CardComponent, card_id: card_id, board_id: @id %>
<% end %
```

Liveview doesn't fetch to database.  Component queries the database via preload and update
---
## Blog Post

http://blog.pthompson.org/liveview-livecomponents-introduction

---
## Stateless Component Application

https://github.com/antew/planning_poker

---

## Example Application

https://github.com/meanderingstream/image_anno

---

[Knowfalls.com](https://knowfalls.com/)

###### scottmueller@knowfalls.com

Looking for Founder Engineers

Elixir, Functional Programming, Rails, Experience

---
