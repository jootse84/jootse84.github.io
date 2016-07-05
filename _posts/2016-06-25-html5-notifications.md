Last week I assisted to a great speech by [Jordan Kasper](https://jordankasper.com/) at the [Iron Yard DC](https://www.theironyard.com/) about new JS Apis in HTML5.

It was a concise but a perfect introduction to all new features included in HTML5 that developers ***can use and implement now***.

Some of the features introduced I completely forgot about their existence, so I decided that it is time to review them by testing the ones I find out more interesting, and write my impressions here.


## Notifications

Today it's the turn of the Web Notifications feature of HTML5. For the ones that don't know exactly how it works, I suggest to open your your [Skype session in your browser](https://web.skype.com/), and check how the browser notifies once some of your contacts writes you on the chat.

We have to take into account that [some browsers](http://caniuse.com/#search=web%20notifications) have special issues related to this feature. For instance, Firefox does not support notifications sent immediately after one another.

### Let's get notified

Unlike the alert event system, notifications allow users to get notified outside of the web page, without being fully interrupted. Users does not need to to click on any button, to continue the interaction with the web page.

To activate the notification system in the browser, first we need to check if the user gave to the browser the correspondent permissions to trigger notifications (on that web, domain).

In addition, I suggest to first check for the Notifications API support by the browser. For this check, we can probably use *modernizr*, or just type the following:


```javascript
if (window.Notification) {
  // Notifications supported, it is time to use them.
}
```

### A DIV that notifies when clicked on

Once we make sure Notifications API is supported, we can ask the user the give the correspondent permissions . When *window.Notification.permission* equals 'granted', means there is permission already to use the API.  

If it is not 'granted', then we have to request that permission by using *requestPermission()*. This function works as a promise, so we can set a callback that can execute some code depending if the permission it is or it is not granted by the user. Here the example:

```javascript
if (window.Notification) {
  // Notifications supported, it is time to use them.
  if (window.Notification.permission === 'granted') {
    // There is permission to use the notifications.
  } else {
    window.Notification.requestPermission().then(function(result) {
      if (result === 'denied') {
        // permission not granted
        return;
      }
      if (result === 'default') {
        // permission dismissed
        return;
      }
      if (result === 'granted') {
        // permission granted.
        // There is permission to use the notifications.
        return;
      }
    });
  }
}
```

Well, now we can define the function that will be triggered when we make sure it is possible to activate notifications (browser supports it && user granted the permission). In the exemple, we are going to add a listener to a DIV element that notifies the user when clicks on it. 

```javascript
function notificationReady () {
  document.getElementById('your_button').addEventListener('click', function () {
    var notification = new window.Notification('This is your notification...');
  }, false);
}
```

### Image in your notifications

When calling a new Notification, apart from the title, there is a second optional parameter, a JSON object with [some options](https://developer.mozilla.org/en-US/docs/Web/API/notification/Notification), that you can use to modify the aspect of your notification.

The following example, we are going to use the URL of my image, that will be included in the notification (option key *icon*)

Please, <span id='click_here_button'>**click here**</span>, to trigger a notification.

<script>
window.onload = function () {
  var notificationReady = function () {
    document.getElementById('click_here_button').addEventListener('click', function () {
      var notification = new window.Notification('Hi my friend!',
        { icon: '/assets/profile.jpg'}
      );
    }, false);
  };

  if (window.Notification) {
    if (window.Notification.permission === 'granted') {
      notificationReady();
    } else {
      window.Notification.requestPermission().then(function(result) {
        if (result === 'denied') {
          return;
        }
        if (result === 'default') {
          return;
        }
        if (result === 'granted') {
          notificationReady();
          return;
        }
      });
    }
  }

}
</script>
