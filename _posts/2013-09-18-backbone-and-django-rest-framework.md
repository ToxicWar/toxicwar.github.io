---
layout: post
title:  "Backbone.js и Django REST framework"
date:   2013-09-18 00:00:00
categories: django
---

Проходя курс [Connected to the Backbone][course] я использовал [Django REST framework][rest] для построения простого апи, которое затем использовал в backbone приложении.

В backbone приложение мы используем простую модель для примера, а также добавим коллекцию для большей наглядности.

{% highlight js %}
App.Models.Task = Backbone.Model.extend({
    defaults: {
        title: '',
        is_completed: false
    },
    urlRoot: '/tasks/'
});

App.Collections.Tasks = Backbone.Collection.extend({
    model: App.Models.Task,
    url: 'tasks/'
});
{% endhighlight %}

Модель в django приложении будет подоба нашей модели в backbone приложении.

{% highlight python %}
class Task(models.Model):
    title = models.CharField('Title', max_length=1000)
    is_completed = models.BooleanField('Is completed', default=False)
{% endhighlight %}

Теперь нам нужно создать сериализатор для нашей модели задач и отображение для него.

{% highlight python %}
class Task(models.Model):
    title = models.CharField('Title', max_length=1000)
    is_completed = models.BooleanField('Is completed', default=False)
{% endhighlight %}

{% highlight python %}
class TaskViewSet(viewsets.ModelViewSet):
    model = Task
    serializer_class = TaskSerializer
{% endhighlight %}

Ну а затем самое главное, а именно урлы для работы с данными.

{% highlight python %}
router = routers.DefaultRouter()
router.register(r'tasks', views.TaskViewSet)

urlpatterns = patterns('',
    url(r'^$', 'app.views.home', name='Home'),
    url(r'', include(router.urls)),
)
{% endhighlight %}

Далее добавим в базу несколько записей с которыми дальше будем работать.

{% highlight js %}
[
    {'title': 'Begin write this posts', 'is_completed': 'true'},
    {'title': 'Go to the shops', 'is_completed': 'false'},
    {'title': 'Buy a new MacBook Pro', 'is_completed': 'false'}
]
{% endhighlight %}

Теперь можно проверить нашу модель и коллекцию в backbone приложении.

{% highlight js %}
> var task = new App.Models.Task({id: 2});
> task.toJSON();
  Object {id: 2, title: "", is_completed: false}
> task.fetch();
> task.toJSON();
  Object {id: 2, title: "Go to the shops", is_completed: false}

> collections = new App.Collections.Tasks();
> collections.fetch();
> collections.toJSON();
  [Object, Object, Object]
{% endhighlight %}

Исходный код вы можете найти на [гитхабе][repo].

[course]: https://tutsplus.com/course/connected-to-the-backbone/
[rest]: http://django-rest-framework.org/
[repo]: https://github.com/ToxicWar/learn-backbone.js/tree/master/backbone_and_django