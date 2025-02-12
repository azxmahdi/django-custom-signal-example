# django-custom-signal-example


### Creating a Custom Signal in Django

Django signals allow certain senders to notify a set of receivers when some action has taken place. In this document, we'll go through the steps to create a custom signal in Django.

#### 1. Required Components
To create a custom signal, you will need:

- **Sender**: The model or event that will send the signal.
- **Signal**: The custom signal itself.
- **Receiver**: A function that will receive the signal and perform actions upon it.

#### 2. Steps to Create a Custom Signal

##### Step 1: Import Required Modules
You will begin by importing the required modules from Django.

from django.dispatch import Signal, receiver


##### Step 2: Define Your Custom Signal
Define a custom signal in a separate file, typically within your app's directory (e.g., signals.py).

# signals.py
my_custom_signal = Signal(providing_args=["instance", "created"])


##### Step 3: Create a Receiver Function
Next, create a function that will act as a receiver for your custom signal. This function will receive the signal and perform any desired actions.

# receivers.py
@receiver(my_custom_signal)
def my_signal_handler(instance, created, **kwargs):
    if created:
        print(f'New instance created: {instance}')
    else:
        print(f'Instance updated: {instance}')


##### Step 4: Connect the Signal in Your AppConfig
You need to connect the signal to your application’s startup by overriding the ready method in apps.py.

# apps.py
from django.apps import AppConfig

class MyAppConfig(AppConfig):
    name = 'myapp'

    def ready(self):
        import myapp.signals  # Import and register the signals


Make sure to update your INSTALLED_APPS in settings.py to use MyAppConfig.

INSTALLED_APPS = [
    'myapp.apps.MyAppConfig',
    ...
]


#### 3. Triggering the Custom Signal
To trigger the custom signal, you can use the send() method provided by the signal.

# models.py
from django.db import models
from .signals import my_custom_signal

class MyModel(models.Model):
    name = models.CharField(max_length=100)

    def save(self, *args, **kwargs):
        created = self.pk is None  # Check if this is a new instance
        super().save(*args, **kwargs)  # Call the original save method
        my_custom_signal.send(sender=self.__class__, instance=self, created=created)


#### 4. Example Usage
Now, every time an instance of MyModel is saved, it will trigger the my_custom_signal, and the corresponding handler my_signal_handler will execute.

### Summary
1. **Import necessary modules** and define your custom signal with Signal().
2. **Create a receiver function** using the @receiver decorator to handle signal events.
3. **Connect the signal** in your AppConfig by importing the signals.
4. **Trigger the signal** in your model whenever an instance is saved.

This setup will allow you to effectively use custom signals in your Django application! 🚀
