# Template Examples

## Twig Templates

### Add a user to a Klaviyo List

```html
<form method="POST">
  <input type="hidden" name="action" value="/klaviyoconnect/api/track" />
  <!-- Add to the list -->
  <input type="hidden" name="list" value="FOO123" />
  <label>
    Email
    <input type="email" name="email" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

_Using a Klaviyo List Field from a global entry_

```twig
<form method="POST">
    <input type="hidden" name="action" value="/klaviyoconnect/api/track" />
    <input type="hidden" name="list" value="{{ global.myKlaviyoList.id }}" />
    <label>
      Email
      <input type="email" name="email" />
    </label>
    <input type="submit" value="Submit" />
</form>
```

### Add a user to a Klaviyo List in accordance with that list’s settings

The difference with the above is that if double opt-in is enabled for the list, the user won’t be added right away, but only after they’ve confirmed their subscription by email.

```html
<form method="POST">
  <input type="hidden" name="action" value="/klaviyoconnect/api/track" />
  <!-- Add to the list -->
  <input type="hidden" name="list" value="FOO123" />
  <input type="hidden" name="useSubscribeEndpoint" value="1" />
  <label>
    Email
    <input type="email" name="email" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

### Add a user to multiple Klaviyo Lists

_Using the Klaviyo Lists Field from a global entry_

```twig
<form method="POST">
    <input type="hidden" name="action" value="/klaviyoconnect/api/track" />
    {% for id, name in global.myKlaviyoLists %}
      <input type="hidden" name="lists[]" value="{{ id }}" />
    {% endfor %}
    <label>
      Email
      <input type="email" name="email" />
    </label>
    <input type="submit" value="Submit" />
</form>
```

### Track Event

```html
<form method="POST">
  <input type="hidden" name="action" value="/klaviyoconnect/api/track" />
  <!-- Event to track -->
  <input type="hidden" name="event[name]" value="Event Foo" />
  <input type="hidden" name="event[event_id]" value="a1b2c3" />
  <input type="hidden" name="event[value]" value="Foobar" />
  <input type="hidden" name="event[FooBar]" value="Foo Bar" />
  <!-- Profile Details -->
  <label>
    Email
    <input type="email" name="email" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

### Tracking an event with a custom timestamp

```html
<form method="POST">
  <input type="hidden" name="action" value="/klaviyoconnect/api/track" />
  <input type="hidden" name="event[name]" value="My Event" />
  <input type="hidden" name="event[event_id]" value="some-id" />
  <!-- The timestamp to set the event to in Klaviyo -->
  <input type="hidden" name="event[timestamp]" value="2019-12-02T00:30:00" />
  <label>
    Email
    <input type="email" name="email" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

### Track an event and add a user to a Klaviyo List

```html
<form method="POST">
  <input type="hidden" name="action" value="/klaviyoconnect/api/track" />
  <!-- Add to the list -->
  <input type="hidden" name="list" value="FOO123" />
  <!-- Event to track -->
  <input type="hidden" name="event[name]" value="Event Foo" />
  <input type="hidden" name="event[event_id]" value="a1b2c3" />
  <input type="hidden" name="event[value]" value="Foobar" />
  <input type="hidden" name="event[FooBar]" value="Foo Bar" />
  <!-- Profile Details -->
  <label>
    Email
    <input type="email" name="email" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

### Identify a user

```html
<form method="POST">
  <input type="hidden" name="action" value="/klaviyoconnect/api/identify" />
  <label>
    Email
    <input type="email" name="email" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

### Extra profile properties

```html
<form method="POST">
  <input type="hidden" name="action" value="/klaviyoconnect/api/track" />
  {{ redirectInput('/thankyou') }}
  <!-- Add to the list -->
  <input type="hidden" name="list" value="foo123" />
  <!-- Profile properties -->
  <label>
    Email
    <input type="email" name="email" />
  </label>
  <label>
    First Name
    <input type="text" name="profile[first_name]" />
  </label>
  <label>
    Last Name
    <input type="text" name="profile[last_name]" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

### Forward a POST request after Klaviyo Connect has identified a user

```twig
<form method="POST">
    <input type="hidden" name="action" value="/klaviyoconnect/api/identify" />
    <input type="hidden" name="forward" value="/commerce/cart/update-cart" />
    {{ redirectInput('/commerce/cart') }}
    <label>
      Email
      <input type="email" name="email" />
    </label>
    <input type="hidden" name="shippingAddressId" value="{{ address.id }}" />
    <input type="hidden" name="sameAddress" value="1" />
    <input type="submit" value="Submit" />
</form>
```

## Klaviyo Email Templates

### Profile Details and Cart/Order Items

Assuming the event triggering the Klaviyo flow is one of Started Checkout, Placed Order or Updated Cart:

```twig
<p>Hi {{ first_name }} {{ last_name }},</p>

<p>Your cart is waiting:</p>

{% for item in event.Items %}
  <p>
    <img alt="item.SKU" src="{{ item.ImageURL }}" />
    <a href="{{ item.ProductURL }}">
      {{ item.ProductName }}
    </a>
  </p>
{% endfor %}

<p>
  <a href="https://mysite.com/actions/klaviyoconnect/cart/restore?number={{ event.OrderNumber }}">Go to your cart</a>
</p>

```

## API Templates

You can use the Craft API in parallel with Klaviyo Connect to trigger Klaviyo events.  
_\*You'll need to write a function to talk directly with the Craft API_

### Adding a User to a List

```javascript
fetch(
  '/klaviyoconnect/api/track',
  {
    list: 'FOO123',
    email: 'YOUR USER EMAIL',
  },
  {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Accept: 'application/json',
    },
  }
);
```

_Pass the list ID and user email to the track endpoint_

### Tracking a User Event

```javascript
fetch(
  '/klaviyoconnect/api/track',
  {
    event: {
      name: 'Started checkout', // required
      // add custom properties to event for further targeting
      cart: {
        {
          title: 'Foo Product',
          brand: 'Bar Co.',
          sku: 'FooBar123',
        }
      }
    },
    email: 'YOUR USER EMAIL',
  },
  {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Accept: 'application/json',
    },
  }
);
```

_Pass the user email and an event object to the track endpoint. The event must have a \*name property_
