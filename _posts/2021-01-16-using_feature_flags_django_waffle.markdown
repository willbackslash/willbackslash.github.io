---
layout: post
title:  "Using feature flags with django waffle"
date:   2021-01-16 22:18:56 -0600
categories: django
---
## What's a feature flag?

In simple words a feature flag (feature toggle) is a mechanism to enable or disable a feature on a system without the need of changing environment variables or deploying a new version of the system

## Common use cases of feature flags

Feature flags are used often on projects that follows a continuos integration approach, giving full control of the rolling out new features to production or to experiment with variants of a feature for distinct segments of users

## Feature flags in Django

If you are reading this post probably you already have a Django project where you want to start using features flags.

Recently I did a research of libraries to implement feature flags on django and I found a couple of options but I decided to use django-waffle because the setup it's very simple and you can start creating flags imediatly

## Set up of django waffle

```bash
pip install django-waffle
```

Then add waffle to your installed apps in settings file

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

Check out the [Django Waffle docs][waffle-docs] for more info on how to get the most out of Django Waffle.

[waffle-docs]: https://waffle.readthedocs.io/en/stable/