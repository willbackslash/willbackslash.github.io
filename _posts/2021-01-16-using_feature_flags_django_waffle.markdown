---
layout: post
title:  "Using feature flags with django waffle"
date:   2021-01-16 22:18:56 -0600
categories: django
---
## What's a feature flag?

In simple words a feature flag (feature toggle) is a mechanism to enable or disable a feature on a system without the need of changing environment variables or deploying a new version of the system

## Common use cases of feature flags

Feature flags are used often on projects that follows a continuos integration approach, giving full control of rolling out new features to production. Also are used to experiment with variants of a feature (AB testing) for distinct segments of users

## Feature flags in Django

If you are reading this post probably you already have a Django project where you want to start using features flags.

Recently I did a research of libraries to implement feature flags on django and I found a couple of options but I decided to use django-waffle because the setup it's very simple and you can start creating flags immediatly

## Set up of django waffle

```bash
pip install django-waffle
```

Then add the waffle configuration to your settings file

{% highlight python %}
INSTALLED_APPS = (
    # ...
    'waffle',
    # ...
)

MIDDLEWARE = (
    # ...
    'waffle.middleware.WaffleMiddleware',
    # ...
)
{% endhighlight %}

that's almost what we need to start using waffle

## Setting up a new flag
I will show you how to create a new flag with waffle using the django admin

if you have django admin enabled on your project, try opening it and you'll see a new group on the admin main page called `DJANGO-WAFFLE` where you can find a link to `Flags` 
<img src="/assets/django_waffle_in_admin.png" style="display: block;margin-left: auto;margin-right: auto;width: 50%;margin-bottom:8px;margin-top:8px;"/> Now open the `Flags` link and press the add flag button:
<img src="/assets/add_flag.png" style="display: block;margin-left: auto;margin-right: auto;width: 20%;margin-bottom:8px;margin-top:8px;"/> 
After that you will see a form like this to create your first flag, in this example I will create a feature flag to send a sms verification to the half of users that are singing up to my site

<img src="/assets/new_flag_form.png" style="display: block;margin-left: auto;margin-right: auto;width: 80%;margin-bottom:8px;margin-top:8px;"/>
<img src="/assets/new_flag_form_2.png" style="display: block;margin-left: auto;margin-right: auto;width: 80%;margin-bottom:8px;margin-top:8px;"/>

Let me explain you the most important fields on this form:
- **Name**: the name that you will use to identify a feature flag, keep it descriptive and short
- **Everyone**: True if you want to apply this flag for all your users, False if you want to use a percent, Unknown if you want to choose specific users for this flag
- **Percent**: the percent of users that will be affected by this rule, in my case 50%
- **Superusers**: True if you want to apply this flag to all the superusers
- **Staff**: True if you want to apply this flag to all the staff members
- **Authenticated**: True if you want to apply this flag to authenticated users
- **Notes**: True if you want to apply this flag to authenticated users
- **Groups**: Select specific user groups to apply the flag
- **Users**: Select specific users to apply the apply  

## Identifying if a flag is active in views and templates
The last step is to apply distinct logics if a flag is enabled, to do that waffle give us some helpers to check if a flag is enabled, for example lets say that we have this view that currently is not sending a sms verification code on signup

{% highlight python %}
def signup(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            username = form.cleaned_data.get('username')
            raw_password = form.cleaned_data.get('password1')
            user = authenticate(username=username, password=raw_password)
            login(request, user)
            return redirect('home')
    else:
        form = UserCreationForm()
    return render(request, 'signup.html', {'form': form})
{% endhighlight %}

Now lets apply the flag verification in views using `flag_is_active` helper function:
{% highlight python %}
from waffle import flag_is_active
 
def signup(request):
    send_verification_code_enabled = flag_is_active('sms_verification_code')
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            username = form.cleaned_data.get('username')
            raw_password = form.cleaned_data.get('password1')
            
            if send_verification_code_enabled:
                phone_number = form.cleaned_data.get('phone')
                send_verification_code(phone_number)
                return render(request, 'signup.html', {'form': form})

            user = authenticate(username=username, password=raw_password)
            login(request, user)
            return redirect('home')
    else:
        form = UserCreationForm()
    return render(request, 'signup.html', {'form': form})
{% endhighlight %}

And finally we can check for a flag in a template using `waffle_tags`
{% highlight html %}
{% raw  %}
{% load waffle_tags %}

{% flag "sms_verification_code" %}
    <label for="verification_code">Verification code:</label>
    <input type="text" name="verification_code">
{% else %}
    <p>sms_verification_code not enabled</p>
{% endflag %}
{% endraw  %}
{% endhighlight %}


Check out the [Django Waffle docs][waffle-docs] for more info on how to get the most out of Django Waffle.

[waffle-docs]: https://waffle.readthedocs.io/en/stable/