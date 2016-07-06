Last week I assisted to a great speech by [Jordan Kasper](https://jordankasper.com/) at the [Iron Yard DC](https://www.theironyard.com/) about new JS Apis in HTML5.

It was a concise but a perfect introduction to all new features included in HTML5 that developers ***can use and implement now***.

Some of the features introduced I completely forgot about their existence, so I decided that it is time to review them by testing the ones I find more interesting, and write my impressions here.


## Notifications

Today it's the turn of the Web Notifications feature. For the ones that do not know exactly how it works and why it is useful, I suggest to open your your [Skype session in your browser](https://web.skype.com/), and check how the browser notifies you when one of your contacts messages you back on the chat.

We have to take into account that [some browsers](http://caniuse.com/#search=web%20notifications) have special issues related to this feature. For instance, Firefox does not support notifications sent immediately after one another.

### Let's get notified

Unlike the alert event system, notifications allow users to get notified outside of the web page, without being fully interrupted. Users do not need to click on any button to continue the interaction with the web page.

To activate the notification system in the browser, first we need to check if the user gave to the browser the correspondent permission to trigger the Notification (on that web or domain).

In addition, I also suggest to first check for the existance of the Notification API by the browser. To check this support, we can probably use *modernizr* or just type the following:


```javascript
if (window.Notification) {
  // Notifications supported, it is time to use them.
}
```

### A DIV that notifies when clicked on

Once we make sure Notifications API is supported, we should ask the user to give the correspondent permissions to the browser, if initially it is not set. When *window.Notification.permission* equals 'granted', means there is permission already to use the API.  

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

Well, now we can define the function that will be triggered when we make sure it is possible to activate notifications (i.e. browser supports it and user granted the permission). In the exemple, we are going to add a listener to a DIV element that throws the Notificaion when the user clicks on it. 

```javascript
function notificationReady () {
  document.getElementById('your_button').addEventListener('click', function () {
    var notification = new window.Notification('This is your notification...');
  }, false);
}
```

### Image in your notifications

When calling a new Notification, apart from the String title, there is a second optional parameter in the function. It is just a simple JSON object with [some options](https://developer.mozilla.org/en-US/docs/Web/API/notification/Notification), that we can use to modify the aspect of your notification.

Here we are only going to use the URL of my blog image, that will be included in the notification (option key *icon*)

Please, <span id='click_here_button'>**click here**</span>, to trigger a Notification example.

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
