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
## Simplest Component

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
<%= live_component @socket, ClassComponent, button_state_style: @class1_state, button_text: @class1_text, button_val=@class1_text %>
<%= live_component @socket, ClassComponent, button_state_style: @class1_state, button_text: @class1_text, button_val=@class1_text %>
<%= live_component @socket, ClassComponent, button_state_style: @class1_state, button_text: @class1_text, button_val=@class1_text %>```
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
<%= live_component @socket, ClassComponent, id: @class.id, button_state_style: @class_state, button_text: @class_text, button_val=@class_text %>
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
    <button id="class-<%= @id %>" class="<%= @button_state_style %>" phx-click="btnclk" phx-target="#class-1" phx-value-btnclk="<%= @button_val %>">
        <%= @button_text %>
    </button>
    """
  end
end
```

For a client event to reach a component, the tag must be annotated with a phx-target annotation which must be a query selector to an element inside the component.
---
## https://phoenixphrenzy.com/results

---
## Page Load
<img src="./images/page_load.png"  height="450">

---
## Messages Over Socket
<img src="./images/messages_over_socket.png" height="450">

---
## .leex
<img src="./images/leex_example.png"  height="500">

---
## Bindings

```html
<button phx-click="inc_temperature">+</button>
```
---
## Binding Handler

All via handle_event callback

```elixir
def handle_event("inc_temperature", _value, socket) do
  {:ok, new_temp} = Thermostat.inc_temperature(socket.assigns.id)
  {:noreply, assign(socket, :temperature, new_temp)}
end
```
---
## Binding:Attribute

| Binding  | Attribute |
| ------------- | ------------- |
|Params	|phx-value-*|
|Click Events	|phx-click|
|Focus/Blur Events	|phx-blur, phx-focus, phx-target|
|Form Events	|phx-change, phx-submit, data-phx-error-for, phx-disable-with|

---
| Binding  | Attribute |
| ------------- | ------------- |
|Key Events	|phx-keydown, phx-keyup, phx-target|
|Rate Limiting	|phx-debounce, phx-throttle|
|Custom DOM Patching	|phx-update|
|JS Interop	|phx-hook|
---
## Liveview Use Cases

* Inputs, buttons forms
  - input validation, dynamic forms, autocomplete

* Page and data navigation

---
## Liveview Limitations

* Needs connection reliability
* Needs low connection latency or can tolerate lag
* Animations handled elsewhere, CSS transitions

---
## Strengths of Liveview

* Display interactions pushed to client
  - Chat, monitoring events, sharing interactively

* AI systems where AI is major actor on system

---
## Example Application

https://github.com/meanderingstream/image_anno

---

[Knowfalls.com](https://knowfalls.com/)

###### scottmueller@knowfalls.com

Looking for Founder Engineers

Elixir, Functional Programming, Rails, Experience

---
